# FraudOps — Multi-Tenancy

> **Version:** 1.1 | **Last updated:** 2026-05-21

## Table of Contents

1. [Overview](#1-overview)
2. [Tenant Resolution](#2-tenant-resolution)
3. [Connection String Lifecycle](#3-connection-string-lifecycle)
4. [Database Isolation](#4-database-isolation)
5. [Per-Tenant Feature Flags](#5-per-tenant-feature-flags)
6. [Tenant Infrastructure Provisioning](#6-tenant-infrastructure-provisioning)
7. [Tenant Onboarding Sequence](#7-tenant-onboarding-sequence)
8. [Endpoints Exempt from Tenant Resolution](#8-endpoints-exempt-from-tenant-resolution)

---

## 1. Overview

FraudOps uses a **database-per-tenant** isolation model. Each tenant has:

- An isolated **FraudOps SQL Server database** (application data: investigations, referrals, parties, screening, workflow)
- An isolated **Intel SQL Server database** (intelligence hub data)
- Isolated **Azure Blob Storage containers** (cases, intelligence, library)
- Dedicated **Azure AI Search indexes** (SQL + Blob)

A shared **master database** (`TenantDBContext`) holds tenant metadata and encrypted connection strings. A shared **Azure Key Vault** holds the encryption key and Azure credentials used during provisioning.

```mermaid
graph TB
    subgraph master["Shared Master DB - TenantDBContext"]
        T["Tenants table
        - Domain: routing key
        - Encrypted FraudOps connection string
        - Encrypted Intel connection string
        - Feature flags: TPAMode, AIMode, etc."]
    end

    subgraph tenantA["Tenant A - isolated resources"]
        A_DB[("FraudOps DB A
        FraudOps_DBContext")]
        A_Intel[("Intel DB A
        sqldbintelhubdevContext")]
        A_Blob["Blob Storage A
        3 containers"]
        A_Search["AI Search A
        5 indexes"]
    end

    subgraph tenantB["Tenant B - isolated resources"]
        B_DB[("FraudOps DB B
        FraudOps_DBContext")]
        B_Intel[("Intel DB B
        sqldbintelhubdevContext")]
        B_Blob["Blob Storage B
        3 containers"]
        B_Search["AI Search B
        5 indexes"]
    end

    T -->|resolved per request| A_DB
    T -->|resolved per request| B_DB
    T -.-> A_Intel
    T -.-> A_Blob
    T -.-> A_Search
    T -.-> B_Intel
    T -.-> B_Blob
    T -.-> B_Search
```

---

## 2. Tenant Resolution

### 2.1 Middleware

**Class:** `TenantResolver` (`FraudOps.UI/Middleware/TenantResolver.cs`)
**Position in pipeline:** After authentication/authorisation, before endpoint execution.

```csharp
public async Task InvokeAsync(HttpContext context, ITenantResolverService tenantResolverService)
```

### 2.2 Resolution Priority

The middleware resolves the tenant identifier from the incoming request in this order:

1. **`X-Tenant` request header** — explicit tenant override (used by integrations and internal tooling)
2. **Request host / subdomain** — `context.Request.Host.Host` (e.g. `acme.fraudops.ai`)
3. **400 Bad Request** — returned if neither yields a tenant identifier

### 2.3 Resolution Flow

```mermaid
flowchart TD
    A([Incoming HTTP request]) --> B{"X-Tenant header present?"}
    B -->|Yes| C[Use header value as domain]
    B -->|No| D[Use request host as domain]
    C --> E{"IMemoryCache hit? key: tenant-domain"}
    D --> E
    E -->|HIT| F["Populate ITenantResolverService
    from CachedTenantInfo"]
    E -->|MISS| G["Query TenantDBContext.Tenants
    WHERE Domain = domain"]
    G --> H{"Tenant found?"}
    H -->|No| I([Return 400 Bad Request])
    H -->|Yes| J["EncryptionHelper.Decrypt
    both connection strings"]
    J --> K["Cache as CachedTenantInfo
    sliding expiry: 1 hour"]
    K --> F
    F --> L([Request continues to controller])
```

### 2.4 Cached Tenant Info

`CachedTenantInfo` (stored in `IMemoryCache`, key `tenant:{domain}`) contains:

```csharp
string FraudOpsConnectionString
string IntelConnectionString
Guid   TenantId
bool   TPAMode
bool   AIMode
string TenantName
bool   EnableCaseNotesSharing
```

Sliding expiration is configured via `ConnectionStringsManagement:CacheDurationHours` (default: 1 hour). Invalidation is not event-driven — tenant config changes take up to 1 hour to propagate without a restart.

---

## 3. Connection String Lifecycle

### 3.1 Storage

Connection strings are stored **encrypted** in the `Tenant` entity columns:

- `Tenant.ConnectionString` — FraudOps database
- `Tenant.IntelConnectionString` — Intel database

Encryption uses AES (via `EncryptionHelper`) with the key sourced from Azure Key Vault secret `ConnectionStringEncryptionKey`.

### 3.2 Write Path (Provisioning) and Read Path (Runtime)

```mermaid
sequenceDiagram
    participant P as TenantInfraProvisioner
    participant KV as Azure Key Vault
    participant M as Master DB (TenantDBContext)
    participant C as IMemoryCache
    participant DB as FraudOps_DBContext

    Note over P,M: Write path — provisioning time
    P->>KV: Read ConnectionStringEncryptionKey
    KV-->>P: Encryption key
    P->>P: EncryptionHelper.Encrypt(plaintext connection string)
    P->>M: Tenant.ConnectionString = ciphertext
    M-->>P: SaveChangesAsync OK

    Note over M,DB: Read path — per request
    DB->>C: Check cache (key: "tenant:{domain}")
    alt Cache miss
        C-->>DB: Not found
        DB->>M: SELECT ConnectionString FROM Tenants WHERE Domain = @domain
        M-->>DB: Ciphertext
        DB->>DB: EncryptionHelper.Decrypt(ciphertext, key from appsettings)
        DB->>C: Store CachedTenantInfo (sliding 1h expiry)
    end
    C-->>DB: FraudOpsConnectionString (plaintext)
    DB->>DB: OnConfiguring → UseSqlServer(connectionString)
```

### 3.3 DbContext Injection

`FraudOps_DBContext` and `sqldbintelhubdevContext` are registered as scoped DbContexts. Their `OnConfiguring` override dynamically selects the connection string at query time:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    // Explicit connection string (e.g. migrations, provisioner)
    if (!optionsBuilder.IsConfigured && !string.IsNullOrEmpty(_connectionString))
        optionsBuilder.UseSqlServer(_connectionString);

    // Tenant-resolved connection string (normal request path)
    if (_tenantResolverService != null &&
        !string.IsNullOrEmpty(_tenantResolverService.ConnectionString))
        optionsBuilder.UseSqlServer(_tenantResolverService.ConnectionString);
}
```

`ITenantResolverService` is scoped, so every request gets its own resolver instance with the correct tenant's connection string. No explicit routing or sharding logic is needed in service or repository code.

### 3.4 Azure Functions (FraudOps.Functions)

Functions do not participate in the HTTP middleware pipeline. `TenantConnectionService` performs direct decryption:

```csharp
Task<string?> GetConnectionStringByTenantIdAsync(Guid tenantId)
// SELECT ConnectionString FROM Tenants WHERE Id = @Id AND IsActive = 1 AND IsDeleted = 0
// → EncryptionHelper.Decrypt(result, _encryptionKey)
```

---

## 4. Database Isolation

### 4.1 Data Segregation

| Scope | Database | DbContext | Isolation level |
| ----- | -------- | --------- | --------------- |
| Cross-tenant (master) | Tenant registry DB | `TenantDBContext` | Shared — one instance, all tenants |
| Per-tenant (application) | FraudOps DB | `FraudOps_DBContext` | Fully isolated — separate SQL Server database per tenant |
| Per-tenant (intelligence) | Intel Hub DB | `sqldbintelhubdevContext` | Fully isolated — separate database per tenant |

### 4.2 What Lives in the Master DB (TenantDBContext)

```text
Tenants                  — tenant registry (name, domain, connection strings, feature flags)
DataProtectionKeys       — ASP.NET Core data protection keys
Integrations             — tenant-level API integration config
IntegrationGroups        — grouping of integrations
TenantSubscriptions      — subscription associations
IntegrationAudits        — audit log for integration calls
Plans                    — subscription plan definitions
Agents                   — AI agent registrations
AgentAudits              — AI agent call audit log
TenantInbounds           — inbound email/message tracking
Subscriptions            — subscription details
MasterDataTables         — shared reference/lookup data
```

### 4.3 What Lives in Each Tenant DB (FraudOps_DBContext)

All application data is fully isolated per-tenant:

- **Identity & access:** `RefreshTokens`, `UserSessions`, `Features`, `Privileges`
- **Core workflow:** `ClaimsReferral`, `Referrals`, `Investigation`, `InvestigationStatus`, `InvestigationParty`, `InvestigationAttachments`
- **Parties:** `InsuredPersonal`, `InsuredCommercial`, `ThirdPartyPersonal`, `ThirdPartyCommercial` and related address/contact tables
- **Intelligence:** `IntelligenceReferral`, `IntelligenceOutcome`, `IntelligenceReport`
- **Screening:** screening result tables
- **Configuration:** `ClientConfig`, `Config`, `TaskType`, `FormFieldConfig`, `ApiConfig`, `IntegrationList` (API keys per provider)
- **Workflow engine:** `Workflow`, `WorkflowNodes`, `WorkflowExecution`, `NodeExecutionLogs`
- **Audit:** `AuditLogs` (automatic via `AspNetCoreHero.EntityFrameworkCore.AuditTrail`)

---

## 5. Per-Tenant Feature Flags

Feature flags are stored on the `Tenant` entity in the master DB and propagated into `ITenantResolverService` and into JWT claims at login:

| Flag | Type | Default | Effect |
| ---- | ---- | ------- | ------ |
| `TPAMode` | bool | `false` | Third-party account mode — alters investigation and referral workflows |
| `AIMode` | bool | `false` | Enables AI agent features (key evidence, objectives, case summary, smart probability) |
| `IsIntegrationActive` | bool | `false` | Enables third-party screening API integrations |
| `EnableCaseNotesSharing` | bool | `true` | Allows case notes to be shared across users within the tenant |
| `IsScreeningEnabled` | bool | — | Controls access to the screening module |
| `IsQuickInvestigation` | bool | — | Enables the quick investigation workflow |
| `IsTrigageSetup` | bool | `false` | Whether triage workflow is configured |
| `ModeOfReceivingReferral` | string | `"MANUAL"` | How the tenant receives referrals (`"MANUAL"` or email-based auto-create) |
| `PreferredMfaProvider` | string | `"Email"` | MFA method: `"Email"`, `"Authenticator"`, or `"None"` |

Flags are injected into JWT claims at token issue time so Angular guards and .NET permission checks can read them without an additional DB call per request.

---

## 6. Tenant Infrastructure Provisioning

Provisioning is handled by `FraudOps.TenantInfraProvisioner` — an HTTP-triggered Azure Function that orchestrates all Azure resource creation for a new tenant.

**Trigger:** `GET / POST /resources/{tenantId:Guid}`

### 6.1 Provisioning Sequence

```mermaid
flowchart TD
    Start([POST /resources/tenantId]) --> Auth

    Auth["1. Authenticate with Azure
    Read Azure AD tenantId + subscriptionId from Key Vault
    Create ArmClient via DefaultAzureCredential"]
    Auth --> Validate

    Validate{"2. Validate Tenant
    Look up by tenantId
    in TenantDBContext"}
    Validate -->|Not found| Err400([HTTP 400 Bad Request])
    Validate -->|Found| Suffix

    Suffix["3. Derive tenant suffix
    First 3 chars of Tenant.Name lowercased
    e.g. Zodiac becomes zod"]
    Suffix --> BlobStep

    BlobStep["4. Create Blob Storage
    StorageAccountService.CreateBlobStorageAccount
    - 1 storage account
    - 3 containers: Cases, Intelligence, Library
    Returns: BlobStorageKeysModel"]
    BlobStep --> SQLStep

    SQLStep["5. Create SQL Server + Databases
    DatabaseService.CreateSQLServerAndDBsAsync
    - SQL Server, existence-checked before creation
    - FraudOps DB e.g. fops_zod
    - Intel DB e.g. intel_zod
    - Apply EF migrations, views, stored procs
    Returns: both connection strings"]
    SQLStep --> EncStep

    EncStep["6. Encrypt + Persist Connection Strings
    Read ConnectionStringEncryptionKey from Key Vault
    EncryptionHelper.Encrypt on both strings
    Save Tenant.ConnectionString and Tenant.IntelConnectionString
    TenantDBContext.SaveChangesAsync"]
    EncStep --> SearchStep

    SearchStep["7. Create Azure AI Search Indexes
    SearchIndexService.CreateAISearchServiceAsync
    - FraudOps SQL index with soft-delete policy
    - Intel SQL index with 60-min refresh schedule
    - Cases Blob index, Intelligence Blob index, Library Blob index
    Persist keys and URIs to ClientConfig table"]
    SearchStep --> Done

    Done([HTTP 200 OK])
```

### 6.2 Idempotency Notes

- SQL Server creation is guarded with an existence check before creation.
- EF migrations are applied via migration runner — applying the same migration twice is safe.
- AI Search index creation is **not** currently idempotent — re-running provisioning for an existing tenant will attempt to recreate indexes. This is a known gap.

### 6.3 State Model

```csharp
CreateSearchIndexesModel
{
    string TenantSuffix              // e.g. "zod"
    string FOpsSqlConnectionString   // plaintext (used within provisioner only)
    string IntelDBSqlConnectionString
    string AzureStorageConnectionString
    Tenant TenantData
}
```

---

## 7. Tenant Onboarding Sequence

```mermaid
sequenceDiagram
    participant Admin as Platform Admin
    participant API as FraudOps.UI API
    participant M as Master DB
    participant P as TenantInfraProvisioner
    participant Az as Azure Resources

    Admin->>API: POST /api/tenant/createtenant\n(Name, Domain, feature flags)
    Note over API: Exempt from TenantResolver middleware
    API->>M: INSERT Tenants row\n(ConnectionString = null)
    M-->>Admin: Tenant created (tenantId)

    Admin->>P: POST /resources/{tenantId}
    P->>Az: Steps 1–7 (see §6.1)
    Az-->>P: Resources provisioned
    P->>M: Update Tenant with encrypted connection strings
    P-->>Admin: HTTP 200 OK

    Admin->>API: Configure feature flags via admin panel\n(AIMode, IsIntegrationActive, etc.)
    Admin->>API: Configure API integrations\n(IntegrationList rows per provider)
    API->>M: Save tenant config
    M-->>Admin: Config saved

    Admin->>API: POST /api/user/invite-user\n(first admin user email)
    API->>API: Send invitation email (Postmark)
    API-->>Admin: Invitation sent

    Note over Admin: User sets password + enrols MFA\nTenant is live
```

---

## 8. Endpoints Exempt from Tenant Resolution

The following endpoints bypass `TenantResolver` because they operate at the platform level or handle callbacks from external systems that cannot provide tenant context:

| Endpoint | Reason |
| -------- | ------ |
| `/api/tenant/createtenant` | Creates the tenant record before any tenant identity exists |
| `/api/tenant/tenantsubscriptions` | Platform-level subscription management |
| `/api/Investigation/ProcessSmsCallback` | SMS provider webhook — no tenant header available |
| `/api/Investigation/CLWebhookResponse` | ClearSpeed webhook callback — no tenant header available |
| `/postmark/inbound` | Postmark inbound email webhook — tenant resolved from email address within the handler |
