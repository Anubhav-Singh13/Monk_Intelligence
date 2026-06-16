# Day 11 — Deutsch's Problem — The First Quantum Speedup

> **Today's one idea:** Deutsch's algorithm solves a simple but carefully constructed problem in one quantum query where any classical algorithm requires two — proving, for the first time, that quantum computers are fundamentally more powerful than classical ones for certain questions.
> **Reading time:** ~40 min · **Prereqs:** Days 9, 10
> **Primary source for today:** Rieffel & Polak, *Quantum Computing: A Gentle Introduction*, Chapter 5, Sections 5.1–5.2 (MIT Press, 2011)
> **Before you start:** Recall Day 10's load-bearing idea — one sentence, no looking. What happens when you measure a qubit in superposition — and what information can you recover from a single measurement?

---

## The hook

Here is a puzzle. I have a sealed black box. Inside it is a function f that takes one bit (0 or 1) as input and produces one bit (0 or 1) as output. The function is one of four possible kinds:

| Name | f(0) | f(1) |
|------|------|------|
| Constant-0 | 0 | 0 |
| Constant-1 | 1 | 1 |
| Identity | 0 | 1 |
| Flip | 1 | 0 |

"Constant" means both outputs are the same. "Balanced" means the outputs are different.

Your task: determine whether f is **constant** (same output for both inputs) or **balanced** (different output for both inputs). You can query the box — feed it a bit and get the output — as many times as you like.

**Classically:** You must query twice. One query tells you f(0) or f(1) — not both. You need both to know whether they match. No way around it.

**Quantum mechanically:** David Deutsch showed in 1985 that one query suffices. You put the input in superposition, query the function *once*, and interference gives you the answer.

This is the first proven quantum speedup. It's a toy problem — but its logic is the seed of every quantum algorithm that came after.

---

## Building the intuition

### The problem's structure

The key insight: you don't need the individual outputs f(0) and f(1). You only need to know *whether they're equal*. That's a global property of the function — a property about the relationship between both outputs, not either output alone.

Classically, you have to evaluate both outputs to compare them. Quantum mechanically, you can query both simultaneously (via superposition) and use interference to extract the global property *without* ever learning the individual outputs.

This is a general theme in quantum algorithms: **quantum speedups often come from learning a global property of a function without querying every input.**

### The circuit

The Deutsch circuit uses two qubits: one for the input (the "query register") and one auxiliary qubit (the "answer register").

```
Query: |0⟩──[H]──────[Uf]──[H]──[M]──→ 0 if balanced, 1 if constant
                       │
Answer: |1⟩──[H]──────┘
```

Step by step:

**Step 1:** Initialize query = |0⟩, answer = |1⟩.

**Step 2:** Apply Hadamard to both:
- Query → |+⟩ = (1/√2)(|0⟩ + |1⟩)
- Answer → |−⟩ = (1/√2)(|0⟩ − |1⟩)

**Step 3:** Apply the oracle Uf (the black box, implemented quantumly). This operation encodes f into the phase of the query register. Through a mechanism called **phase kickback**, the function's behavior gets "written" into the sign of the query qubit's amplitude — not into its 0/1 value.

What happens:
- If f is **constant**: phase kickback leaves query as |+⟩ (or −|+⟩, same thing up to a global phase that doesn't affect measurement)
- If f is **balanced**: phase kickback flips the relative phase, turning query into |−⟩

**Step 4:** Apply Hadamard to query again:
- |+⟩ → |0⟩ (constructive interference on |0⟩, destructive on |1⟩)
- |−⟩ → |1⟩ (constructive interference on |1⟩, destructive on |0⟩)

**Step 5:** Measure query:
- Result **0**: f is constant
- Result **1**: f is balanced

One query. Definitive answer. Zero chance of being wrong.

### Why interference is doing the work

After the oracle, the query register's amplitudes encode f's global property in their *signs*, not their magnitudes. Both outcomes (input 0 and input 1) were queried simultaneously. The final Hadamard gate acts as an interference filter:

```
Constant f:  amplitudes for |0⟩ reinforce (+ + = constructive)
             amplitudes for |1⟩ cancel    (+ − = destructive)
             → measure 0 with certainty

Balanced f:  amplitudes for |0⟩ cancel    (+ − = destructive)
             amplitudes for |1⟩ reinforce (+ + = constructive)
             → measure 1 with certainty
```

The interference converts the phase difference (invisible to direct measurement) into a probability difference (visible to measurement). This is the Hadamard gate acting as a decoder.

### Phase kickback — the general mechanism

The answer register starts in |−⟩ = (1/√2)(|0⟩ − |1⟩). When the oracle flips the answer qubit based on f(x), the minus sign in |−⟩ propagates *backwards* to the phase of the query qubit. This is **phase kickback**: a controlled gate kicks a phase back to the control qubit.

Phase kickback is used in almost every quantum algorithm. It's how the oracle's information gets encoded into the query register's amplitudes in a form that interference can then extract.

---

## The formal picture

**The oracle Uf:** For a function f: {0,1} → {0,1}, the quantum oracle is:

```math
U_f |x\rangle|y\rangle = |x\rangle|y \oplus f(x)\rangle
```

Where ⊕ is XOR (addition modulo 2). This is a reversible, unitary gate.

**Phase kickback mechanism:** When the target register is in state |−⟩ = (1/√2)(|0⟩ − |1⟩):

```math
U_f |x\rangle|{-}\rangle = (-1)^{f(x)} |x\rangle|{-}\rangle
```

The answer register is unchanged (it stays |−⟩), but the query qubit's amplitude picks up a factor of (−1)^{f(x)} — a phase. This phase encodes f(x).

**Final state before measurement:**

- Constant f: query ends in |0⟩ → measurement gives 0
- Balanced f: query ends in |1⟩ → measurement gives 1

The result is deterministic — no probability involved.

**Deutsch-Jozsa generalization:** Deutsch's algorithm generalizes to n-bit inputs. For a function f: {0,1}^n → {0,1} that is *either* constant or balanced, the quantum algorithm determines which in one query. Classically, you'd need 2^(n-1) + 1 queries in the worst case. For n=100: 1 query vs. more than 10^29 queries.

---

## Where it breaks / what it is not

**"Deutsch's algorithm proves quantum computers are exponentially faster in general."**
No. The speedup is for a very specific problem structure (constant vs. balanced functions). Most real-world problems don't have this structure. Deutsch's algorithm is significant because it *proves* quantum speedup is possible — not because the problem it solves is practically important.

**"We could solve Deutsch's problem classically with probability — just query once and guess."**
A classical probabilistic algorithm querying once has a 50% chance of being right (if it queries f(0) and guesses based on that, f(1) is unknown). Deutsch's quantum algorithm is always right after one query. The quantum advantage is exact, not probabilistic.

**"The algorithm tells you f(0) and f(1)."**
No. It tells you only whether they're equal — a global property. You don't learn the individual output values. This "learning a global property without learning the parts" is characteristic of quantum speedups.

---

## Try it yourself

**1. Retrieval — close the page.** Write down in one sentence: why does Deutsch's algorithm need only one oracle query when a classical algorithm needs two — name the mechanism? Open only after writing your answer.

<details>
<summary>Answer</summary>
Deutsch's algorithm uses phase kickback: the oracle's behavior is encoded into the phase (sign) of the query qubit's amplitude rather than its 0/1 value. A final Hadamard gate then converts this phase difference into a measurable probability difference. Both input values f(0) and f(1) are queried simultaneously via superposition, and interference extracts the global property (constant vs. balanced) in one shot.
</details>

**2. Check understanding.**
Deutsch's algorithm queries the oracle exactly once. A classical algorithm needs two queries. But both run on a function with only two inputs. Why doesn't the classical algorithm just "guess" after one query?

<details>
<summary>Answer</summary>
After one classical query, say f(0) = 0, you still don't know f(1). f could be constant-0 (if f(1) = 0) or identity (if f(1) = 1). Both are consistent with the one data point you have. You must query f(1) to decide. Guessing has a 50% error rate. Deutsch's quantum algorithm is 100% correct after one oracle call because interference extracts the global property — not a single output value.
</details>

**3. Apply.**
Deutsch's algorithm uses the answer register initialized to |1⟩ (then put in |−⟩ by Hadamard), not |0⟩. What would happen if the answer register started in |0⟩ instead?

<details>
<summary>Hint</summary>
Phase kickback requires the target register to be in |−⟩ to propagate phases back to the query register. What happens if the target is |0⟩ instead of |−⟩?
</details>

<details>
<summary>Answer</summary>
If the answer register is |0⟩ (not |−⟩), phase kickback doesn't work. The oracle would flip the answer qubit based on f(x), but no phase would propagate to the query register. The query register would remain in equal superposition |+⟩ regardless of whether f is constant or balanced. Measuring would give 50/50 — random noise. The |−⟩ initialization of the answer register is essential to the algorithm's function.
</details>

**4. Stretch.**
The Deutsch-Jozsa problem on n bits achieves an exponential classical-to-quantum speedup: 1 quantum query vs. 2^(n-1)+1 classical queries. But this speedup is over a *deterministic* classical algorithm. What if you allow a classical probabilistic algorithm that can be wrong 1% of the time?

<details>
<summary>Answer</summary>
A classical probabilistic algorithm for Deutsch-Jozsa can achieve high confidence with O(1) queries (constant, not exponential) — just query a random sample of inputs and check for consistency. If f is constant, any two queries will match. If f is balanced, random queries will mismatch with probability 1/2. After k queries, a balanced function is identified with probability 1 − (1/2)^k. So with ~7 queries, you get 99% confidence. This largely eliminates the exponential quantum speedup! This is why Deutsch-Jozsa is a proof-of-concept, not a practically important speedup — the classical probabilistic version is nearly as efficient.
</details>

---

**Transfer — apply it (all levels):** Think of a binary question you need to answer about a system or dataset — "Is there a race condition in this code?", "Does this dataset contain the target pattern?", "Is this configuration valid?" Write one sentence naming the question and describing what the "oracle" (the checking function) would be. Is the global property you want (constant vs. balanced) closer to "does any instance exist?" or "do all instances share a property?"

---

## Connect it back

Deutsch's algorithm showed it was possible. Tomorrow, Grover's algorithm (Day 12) shows it's useful — achieving a quadratic speedup on one of the most general problems in computing: searching an unsorted database. The mechanism is the same (superposition + interference), but the payoff is dramatically larger and applies to real-world problems.

**The question you should now be able to answer:** Why does Deutsch's algorithm only need one oracle query when a classical algorithm needs two?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Rieffel & Polak, *Quantum Computing: A Gentle Introduction*, Chapter 5, Sections 5.1–5.2 (MIT Press, 2011). Pages 109–125. The clearest written derivation of Deutsch's algorithm, with full step-by-step algebra that you can follow without a physics degree.

**If you want the deep version:**
- Aaronson, *Quantum Computing Since Democritus*, Lecture 10 ("Quantum Computing"), pages 1–8 (scottaaronson.com/democritus/lec10.html). Aaronson's framing of oracle separations and why Deutsch-Jozsa matters conceptually — even though it's practically weak — is outstanding.
- David Deutsch, "Quantum Theory, the Church–Turing Principle and the Universal Quantum Computer," *Proceedings of the Royal Society A*, 400(1818):97–117, 1985. The original paper. Section 5 describes the algorithm. It's dense but historically electrifying.

---

## Navigation

← **Previous:** [Day 10 — Measurement — The Act of Looking](./day-10-measurement.md)
→ **Next:** [Day 12 — Grover's Algorithm — Quantum Search](./day-12-grovers-algorithm.md)
