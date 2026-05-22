# FraudOps Code Review

**Date:** 2026-05-22
**Reviewer:** Claude Code
**Scope:** Full codebase — Python AI agents (ClaimAdminAgent, IntelligenceAgent, CaseSummaryAgent), .NET Core (FraudOps.Core, FraudOps.UI), CI/CD pipelines, configuration
**Verdict:** Request Changes — 5 blocking issues must be resolved before any production release

---

## Overall Design Assessment

The architecture is coherent for its problem domain: a multi-tenant .NET 8 API backend, Angular SPA, and a set of Azure Functions Python AI agents orchestrated via LangChain/LangGraph. The layering (Core → Models → UI) is sensible, the standardised `ApiResponse<T>` wrapper is consistent, and the ARCHITECTURE.md is genuinely useful. The Python agents follow a clean pattern: Pydantic validation → LangGraph/LCEL chain → structured response.

That said, there are **five blocking issues** — two are security-critical, three are correctness-critical — that must be resolved before this branch ships to any non-local environment.

---

## BLOCKING — Security

### 1. Secrets committed to source control

**Files:** `appsettings.json`, `appsettings.Development.json`, `appsettings.FraudOpsDev.json`, `appsettings.FraudOpsProduction.json`, `appsettings.Production.json`

Secrets found in plaintext across multiple appsettings files:
- Azure OpenAI API key (in base `appsettings.json`)
- Postmark API key (`c6e9fcc3-bad0-4e61-b95c-...`)
- Brevo (Sendinblue) API key
- SQL Server password: `Password=Sridevi` in `appsettings.Development.json`
- JWT `SecretKey` / `EncryptionKey`
- Azure Cognitive Search admin keys
- EventGrid topic keys

If this repository is or ever was connected to a remote, these secrets are likely in git history even after removal. **All of these credentials should be considered compromised and rotated immediately.**

**Fix:** Move all secrets to Azure Key Vault. Use `dotnet user-secrets` locally. Reference via `@Microsoft.KeyVault(...)` URI syntax in App Service config. The `appsettings.*.json` files should only contain non-sensitive configuration (feature flags, endpoints without keys, log levels).

---

### 2. Python AI agents have no authentication

**Files:** `FraudOps.ClaimAdminAgent/function_app.py`, `FraudOps.AI/FraudOps.IntelligenceAgent/function_app.py`, `FraudOps.AI/FraudOps.CaseSummaryAgent/function_app.py`

All three function apps expose HTTP-triggered endpoints with `AuthLevel.ANONYMOUS` (the default if no `auth_level` is specified and no `x-functions-key` check is done in the handlers). There is no token validation, no tenant check, and no IP restriction in the function code itself.

Anyone who discovers the Azure Function URL can POST arbitrary claim JSON to the LLM pipeline, incurring Azure OpenAI costs and potentially leaking case data through crafted prompts.

**Fix:** At minimum, set `auth_level=AuthLevel.FUNCTION` in every `@app.route()` decorator and pass the function key from the .NET backend. Better: add a middleware check that validates the caller presents a shared secret or a Managed Identity token from the .NET App Service.

---

## BLOCKING — Correctness

### 3. Duplicate `FraudOps.ClaimAdminAgent` — deployment target is ambiguous

Two copies of the agent exist:
- `FraudOps.ClaimAdminAgent/` (root) — described in ARCHITECTURE.md as the canonical, more mature copy
- `FraudOps.AI/FraudOps.ClaimAdminAgent/` — described as an incomplete migration

The CI/CD workflows do not clearly indicate which copy is deployed. If the wrong one reaches production, the more mature logic (phone normalizer with 115 country codes, TPA mode handling, referral graph workflows) is silently dropped.

**Fix:** Delete `FraudOps.AI/FraudOps.ClaimAdminAgent/` entirely, or add an explicit comment and CI-time assertion (`if directory exists, fail build`). Update `deploy.yml` to reference the canonical path explicitly. This should happen before the next release.

---

### 4. LangGraph version mismatch between agents

| Agent | LangGraph version |
|---|---|
| `FraudOps.ClaimAdminAgent` | `1.0.9` |
| `FraudOps.AI/FraudOps.IntelligenceAgent` | `0.2.0` |
| `FraudOps.AI/FraudOps.CaseSummaryAgent` | `1.0.9` |

LangGraph 1.x introduced breaking changes from 0.2.x (state schema, graph compilation API, node wiring). If code from one agent is ever copied to the other — which happens given the shared code duplication — it will silently behave differently or throw at runtime. The two agents cannot share graph patterns while on different major versions.

**Fix:** Pin IntelligenceAgent to `langgraph==1.0.9`. Verify the `build_graph()` + `@lru_cache` pattern in IntelligenceAgent is compatible with the 1.x API. Run a smoke test against all three endpoints after upgrading.

---

### 5. `time.sleep()` inside async Azure Function handlers

**File:** `FraudOps.AI/FraudOps.IntelligenceAgent/src/services/` (the `_invoke_chain()` retry logic)

The retry wrapper uses `time.sleep(1)` between retries. Azure Functions Python v2 runs async handlers on an event loop. Calling `time.sleep()` blocks the entire event loop thread for 1 second per retry — no other requests can be served during that time. Under load this becomes a reliability problem.

**Fix:** Replace `time.sleep(1)` with `await asyncio.sleep(1)` and ensure `_invoke_chain()` is `async def`.

---

## SUGGEST

### 6. Python shared code is copy-pasted, not packaged

`shared/llm_client.py` and `shared/tracing.py` exist in both `FraudOps.ClaimAdminAgent/shared/` and `FraudOps.AI/FraudOps.IntelligenceAgent/src/shared/`. CaseSummaryAgent has its own `src/services/llm.py` that is structurally identical. Any bug fix or config change must be applied in all three places and is likely to diverge over time.

The fix is to extract a `fraudops_agents_common` Python package (a local `.whl` or a path-referenced `setup.py`) and have all three agents depend on it. This would unify LLM client creation, trace ID handling, and retry logic in one place.

---

### 7. `NotificationService` swallows exceptions silently

**File:** `FraudOps.Core/Services/NotificationService.cs`

```csharp
catch (Exception)
{
    // silent
}
```

When a notification fails to persist (DB unavailable, constraint violation, etc.), the failure is completely invisible. No log entry, no metric, no retry. Since notifications drive user-visible task assignment, silent failures mean users miss work items with no indication something went wrong.

**Fix:** At minimum, `_logger.LogError(ex, "Failed to create notification for {ReferenceId}", referenceId)`. Ideally rethrow or return a result type so callers can decide whether to surface the failure.

---

### 8. `DateTime.Now` instead of `DateTime.UtcNow` in NotificationService

**File:** `FraudOps.Core/Services/NotificationService.cs`

`DateTime.Now` produces local time, which is server-timezone-dependent. In Azure (UTC by default) this works by accident, but it will break if the App Service timezone is ever changed and creates inconsistencies in audit trails that mix `Now` and `UtcNow`.

**Fix:** Use `DateTime.UtcNow` consistently across all services. Run a grep for `DateTime.Now` and fix in the same pass.

---

### 9. `SaveChanges()` is synchronous in `NotificationService`

**File:** `FraudOps.Core/Services/NotificationService.cs`

The interface method is `async Task CreateNotification(...)` but the implementation calls `_dbContext.SaveChanges()` (synchronous). This means it occupies a thread from the async pool during the DB round-trip without yielding.

**Fix:** `await _dbContext.SaveChangesAsync(cancellationToken)`.

---

### 10. Commented-out `attachment_enrichment_node` in production code

**File:** `FraudOps.ClaimAdminAgent/workflows/nodes/attachment_enrichment_node.py`

The node body is commented out. `referral_graph.py` references it via conditional routing, but the logic is disabled. If this is incomplete work it should either be tracked in a ticket and the dead code removed, or completed before merging.

---

### 11. Missing ApplicationInsights telemetry in CaseSummaryAgent

**File:** `FraudOps.AI/FraudOps.CaseSummaryAgent/host.json`

IntelligenceAgent has Application Insights sampling configured in `host.json`. CaseSummaryAgent has `"isDefaultHostConfig": true` and no sampling configuration. CaseSummaryAgent runs the most complex LangGraph pipeline and is the most important agent to observe — yet it emits no structured telemetry.

**Fix:** Add the same Application Insights block from IntelligenceAgent's `host.json` to CaseSummaryAgent.

---

### 12. No rate limiting on AI agent endpoints

The Python agents have no rate limiting at the function level. A misbehaving or malicious caller can exhaust Azure OpenAI quota and incur significant cost. At minimum, the Azure API Management layer (if it sits in front of the Functions) should apply rate limits per tenant/IP. If APIM is not in the path, add a simple token bucket or per-IP throttle check in each handler.

---

## NITS

**N1.** `FraudOpsIntelReIndex` breaks the project naming convention (`FraudOps.<Name>`). Should be `FraudOps.IntelReIndex`. Low urgency, but creates confusion in solution explorer and CI logs.

**N2.** `IntelligenceOutcome.cs` in `FraudOps.Core/Models/Enum/` is an empty placeholder class. Either implement it or remove it — empty types communicate intent ambiguously.

**N3.** `Intel_Party.cs` uses `UPPER_CASE` for all field names (`INTEL_PARTY_ID`, `FIRST_NAME`, etc.), inconsistent with C# naming conventions and every other model in the codebase. These are likely mapped from a DB view with uppercase column names, but the model layer should normalise to PascalCase properties with `[Column("FIRST_NAME")]` attributes.

**N4.** `TempTokenDurationInMinutes=5` — verify temp tokens are invalidated on first use and are not reusable. If they are single-use, document that constraint explicitly in the `ITokenService` interface.

**N5.** `FraudOps.UI.Models/` and `FraudOps.Core.Models/` have overlapping namespaces (DTO, Enum, Shared, Referral). The rule is stated in ARCHITECTURE.md but not enforced. Consider adding a Roslyn analyser or at minimum a comment at the top of each namespace to guide future contributors.

---

## Praise

**AppContextMiddleware** is clean. Extracting JWT claims once at the request boundary and propagating via `HttpContext.Items` avoids scatter-gun `ClaimsPrincipal` reads across controllers and keeps auth logic centralised.

**LoB rubric system in CaseSummaryAgent** is excellent design. Dropping a JSON file into `src/prompts/lob_rubrics/` adds a new line of business with zero code changes. This is the right separation of configuration from logic.

**Data caps in CaseSummaryAgent** (`max_case_notes=50`, `max_documents=20`, etc.) are thoughtful. They prevent runaway token consumption when a case has thousands of notes and keep response times predictable.

**`@lru_cache` on LLM client and settings singletons** is correct for Azure Functions — avoids re-initialising `AzureChatOpenAI` on every cold invocation.

**Pydantic `AliasChoices` for camelCase/PascalCase** in CaseSummaryAgent inputs means the .NET backend can POST either format and the agent handles both — good defensive parsing at the boundary.

**Trace ID propagation** (`x-correlation-id` header → all agent responses) is properly threaded through all three agents. This will make cross-service debugging tractable.

**ARCHITECTURE.md is current** (updated 2026-05-21) and includes known structural issues as explicit callouts. This is rare and valuable — most teams let architecture docs rot.

**Standardised `ApiResponse<T>`** is used consistently across all .NET controllers, making client-side error handling predictable.

---

## Issue Priority Summary

| # | Severity | Area | Issue |
|---|---|---|---|
| 1 | **BLOCKING — Security** | Config | Secrets committed to source control |
| 2 | **BLOCKING — Security** | Python agents | No auth on HTTP endpoints |
| 3 | **BLOCKING — Correctness** | Python agents | Duplicate ClaimAdminAgent, ambiguous deploy target |
| 4 | **BLOCKING — Correctness** | Python agents | LangGraph version mismatch (0.2.0 vs 1.0.9) |
| 5 | **BLOCKING — Correctness** | Python agents | `time.sleep()` blocks async event loop |
| 6 | Suggest | Python agents | Shared code duplicated, not packaged |
| 7 | Suggest | .NET Core | Silent exception swallow in NotificationService |
| 8 | Suggest | .NET Core | `DateTime.Now` → `DateTime.UtcNow` |
| 9 | Suggest | .NET Core | `SaveChanges()` → `SaveChangesAsync()` |
| 10 | Suggest | Python agents | Commented-out node in production code |
| 11 | Suggest | Python agents | Missing App Insights in CaseSummaryAgent |
| 12 | Suggest | Python agents | No rate limiting on agent endpoints |
| N1 | Nit | Project structure | `FraudOpsIntelReIndex` naming convention break |
| N2 | Nit | .NET Core | Empty `IntelligenceOutcome` enum |
| N3 | Nit | .NET Core | `Intel_Party.cs` UPPER_CASE field names |
| N4 | Nit | .NET Core | Temp token single-use not documented |
| N5 | Nit | .NET Core | UI.Models / Core.Models overlap unenforced |

---

## Recommended Fix Order

1. Rotate all committed secrets → move to Azure Key Vault
2. Add `AuthLevel.FUNCTION` (or Managed Identity) to all Python agent routes
3. Delete `FraudOps.AI/FraudOps.ClaimAdminAgent/` and pin the deploy path
4. Align LangGraph to `1.0.9` across all agents
5. Replace `time.sleep()` with `await asyncio.sleep()` in retry logic
6. Extract shared Python code into a common package
7. Fix `NotificationService` (logging, UtcNow, SaveChangesAsync)
8. Add App Insights to CaseSummaryAgent host.json
9. Address nits in a cleanup PR

---

## Module × Functionality Summary

| # | Module | Functionality | Issue | Severity | Change Complexity |
| --- | --- | --- | --- | --- | --- |
| 1 | `FraudOps.UI` — appsettings | Secrets & Configuration Management | API keys, DB passwords, JWT secrets committed to source control | **Critical** | Medium — Key Vault wiring, user-secrets setup, CI env var migration |
| 2 | All Python Agents (`ClaimAdminAgent`, `IntelligenceAgent`, `CaseSummaryAgent`) | HTTP Endpoint Authentication | All function routes exposed with no auth; any caller can invoke LLM pipeline | **Critical** | Low — Add `auth_level=AuthLevel.FUNCTION` per route + pass key from .NET |
| 3 | `FraudOps.ClaimAdminAgent` + CI/CD (`deploy.yml`) | Deployment Pipeline | Duplicate agent at root and `FraudOps.AI/`; which copy deploys is ambiguous | **High** | Low — Delete duplicate directory, update deploy path reference |
| 4 | `FraudOps.AI/FraudOps.IntelligenceAgent` | LangGraph Agent Pipeline | LangGraph `0.2.0` vs `1.0.9` — breaking API differences across agents | **High** | Medium — Pin version, verify graph compilation API compatibility, smoke test |
| 5 | `FraudOps.AI/FraudOps.IntelligenceAgent` | LLM Retry Logic | `time.sleep(1)` in async Function handler blocks event loop under load | **High** | Low — Replace with `await asyncio.sleep(1)`, mark function `async def` |
| 6 | All Python Agents | Shared Infrastructure (LLM Client, Tracing) | `llm_client.py` and `tracing.py` copy-pasted across three agents; divergence risk | Medium | High — Extract `fraudops_agents_common` package, update all three requirements.txt |
| 7 | `FraudOps.Core` — `NotificationService` | Notification Persistence | Exceptions silently swallowed; failed notifications invisible to callers and logs | Medium | Low — Add `_logger.LogError`, optionally rethrow |
| 8 | `FraudOps.Core` — `NotificationService` | Timestamp Consistency | `DateTime.Now` used instead of `DateTime.UtcNow`; timezone-dependent | Low | Low — Single-line fix; grep for other occurrences |
| 9 | `FraudOps.Core` — `NotificationService` | Async Correctness | `SaveChanges()` (sync) called inside `async Task` method; blocks thread pool | Medium | Low — Replace with `await SaveChangesAsync()` |
| 10 | `FraudOps.ClaimAdminAgent` — `workflows/nodes/` | Referral Workflow (LangGraph) | `attachment_enrichment_node` body commented out; incomplete workflow in production branch | Medium | Low — Remove dead code or complete and wire the node |
| 11 | `FraudOps.AI/FraudOps.CaseSummaryAgent` | Observability & Telemetry | `host.json` missing Application Insights config; most complex agent has no structured telemetry | Medium | Low — Copy App Insights block from IntelligenceAgent `host.json` |
| 12 | All Python Agents | Rate Limiting / Cost Control | No per-tenant or per-IP rate limiting; runaway caller can exhaust OpenAI quota | Medium | Medium — Add APIM policy or token-bucket middleware in each function handler |
| 13 | `FraudOpsIntelReIndex` (project) | Project Structure & Naming | Breaks `FraudOps.<Name>` naming convention; causes CI log confusion | Low | Low — Rename project and update solution references |
| 14 | `FraudOps.Core` — `Models/Enum/` | Domain Enum Definitions | `IntelligenceOutcome.cs` is an empty class; intent unclear to future contributors | Low | Low — Implement values or delete |
| 15 | `FraudOps.Models` — `Intel_Party.cs` | Intelligence Data Model | All properties named in `UPPER_CASE`; inconsistent with C# PascalCase convention | Low | Medium — Add `[Column("...")]` attributes and rename properties to PascalCase |
| 16 | `FraudOps.Core` — `TokenService` / `ITokenService` | Authentication — Temp Tokens | Temp token single-use constraint not documented in interface or code | Low | Low — Add XML doc comment to `ITokenService`; verify invalidation on use |
| 17 | `FraudOps.UI/Models/` + `FraudOps.Core/Models/` | Model Layer Organisation | Overlapping namespaces (DTO, Enum, Shared, Referral) with no enforced boundary | Low | Low — Add Roslyn analyser rule or namespace-level comments; no code restructure needed |
