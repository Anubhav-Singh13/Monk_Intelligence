# Day 7 — Rest & Synthesize I

> **Today's one idea:** Understanding is not the same as remembering. Today you consolidate Days 1–6 by re-deriving, re-drawing, and re-coding — without looking things up.
> **Reading time:** ~35 min (active work, not passive reading) · **Prereqs:** Days 1–6
> **Primary source:** Your own notes and memory. No new TAOCP sections today.

---

## What this day is for

Knuth opens TAOCP with a warning: "The reader should not … expect to read [this] quickly." He means that each idea must be *metabolised*, not just encountered.

Rest days are not optional padding. Research on how people learn complex material consistently shows that spaced retrieval — trying to recall and reconstruct without cues — builds far stronger understanding than re-reading. Today you attempt to reconstruct the core ideas cold. The goal is not to produce perfect answers; it is to find where your mental model is fuzzy.

**Rule for today:** attempt each task before expanding the hint. An honest wrong answer is more valuable than a looked-up right one.

---

## Synthesis tasks

Work through these in order. Allow yourself roughly 5–7 minutes per task.

---

### Task 1 — Re-state the five properties

Without looking at Day 1: write down Knuth's five properties that distinguish an algorithm from a mere computational method. For each one, give a counterexample — a procedure that fails *that specific property* but satisfies all the other four.

<details>
<summary>Check your answer</summary>

1. **Finiteness** — terminates after a bounded number of steps. *Fails:* "add 1 repeatedly forever."
2. **Definiteness** — each step is precisely specified. *Fails:* "stir until done."
3. **Input** — zero or more values from a specified domain. *Fails:* a procedure that reads from an undefined or unbounded source.
4. **Output** — one or more values with a defined relationship to the input. *Fails:* a procedure that prints random noise unrelated to input.
5. **Effectiveness** — every step is basic enough to carry out exactly. *Fails:* "find the largest prime" (not finitely checkable).
</details>

---

### Task 2 — Notation cold-recall

Compute without a calculator or reference:

a) $\lfloor 11/3 \rfloor$  
b) $\lceil 11/3 \rceil$  
c) $23 \bmod 7$  
d) $\sum_{k=1}^{5} (2k - 1)$  
e) $\lfloor \log_2 100 \rfloor$

<details>
<summary>Check your answers</summary>

a) 3 · b) 4 · c) 2 · d) 1+3+5+7+9 = **25** (sum of first 5 odd numbers = 5²) · e) 6 (since 2⁶=64 ≤ 100 < 128=2⁷)
</details>

---

### Task 3 — Big O from first principles

State whether each of the following is True or False, and give one-line justification:

a) $n^2 + n = O(n^2)$  
b) $\log_2 n = O(\sqrt{n})$  
c) $2^n = O(n^{100})$  
d) $n \log n = \Omega(n)$  
e) $5n + 3 = \Theta(n)$

<details>
<summary>Check your answers</summary>

a) **True.** $n^2 + n \leq 2n^2$ for $n \geq 1$.  
b) **True.** Any polylog is dominated by any polynomial root — logarithm grows slower than any power of n.  
c) **False.** Exponential grows faster than any polynomial. $2^n / n^{100} \to \infty$.  
d) **True.** $n \log n \geq n$ for $n \geq 2$.  
e) **True.** $5n+3 \leq 6n$ for $n \geq 3$ (O); $5n+3 \geq 5n$ (Ω). Both constants exist, so Θ.
</details>

---

### Task 4 — MIX cost exercise

A loop runs $n$ times. Each iteration executes:
- 1 load (2u), 1 add (2u), 1 store (2u), 1 conditional jump not-taken (1u), 1 comparison (2u)

After the loop, one final conditional jump taken (1u).

Express the total cost as a linear function of $n$, then state its asymptotic class.

<details>
<summary>Check your answer</summary>

Per iteration: 2+2+2+1+2 = **9u**  
n iterations: 9n u  
Final jump: 1u  
**Total: 9n + 1 u = O(n)**
</details>

---

### Task 5 — Re-derive a loop invariant

Without looking at Day 5: write a Python function that computes the sum of a list, and prove it correct using a loop invariant. Name all three parts (Initialisation, Maintenance, Termination) explicitly.

<details>
<summary>Reference solution</summary>

```python
def list_sum(A: list[int]) -> int:
    total = 0
    for i in range(len(A)):
        total += A[i]
    return total
```

**Invariant:** At the start of iteration i, `total` = $\sum_{k=0}^{i-1} A[k]$.

- **Init (i=0):** total=0 = empty sum. ✓
- **Maintenance:** total += A[i] makes total = $\sum_{k=0}^{i} A[k]$. ✓
- **Termination:** loop ends with i=len(A); total = $\sum_{k=0}^{n-1} A[k]$ = sum of whole list. ✓
</details>

---

### Task 6 — Generating function recognition

Match each sequence to its generating function:

| Sequence | GF candidates |
|----------|--------------|
| $0, 1, 2, 3, 4, \ldots$ | $\frac{1}{1-z}$ |
| $1, 1, 1, 1, \ldots$ | $\frac{z}{(1-z)^2}$ |
| $1, 0, 0, 0, \ldots$ | $1$ |
| $0, 1, 1, 2, 3, 5, \ldots$ (Fibonacci) | $\frac{z}{1-z-z^2}$ |

<details>
<summary>Check your answers</summary>

- $0, 1, 2, 3, \ldots$ → $\frac{z}{(1-z)^2}$ (from Day 6 Exercise 2)
- $1, 1, 1, \ldots$ → $\frac{1}{1-z}$ (geometric series)
- $1, 0, 0, \ldots$ → $1$ (only the $z^0$ term is non-zero)
- Fibonacci → $\frac{z}{1-z-z^2}$ (derived in Day 6)
</details>

---

### Task 7 — The big picture (5 min free-write)

Without looking at any notes, write a short paragraph (3–5 sentences) answering: **what is the connection between loop invariants (Day 5) and mathematical induction (Day 5)? How does this connection appear in Euclid's algorithm?**

There is no answer key for this one — the act of writing it is the learning. If you find yourself stuck, that is your signal to re-read Day 5 before continuing to Day 8.

---

## What to carry forward

The concepts that will recur most in Days 8–45:

| Tool | First use | Recurs in |
|------|-----------|-----------|
| Floor/ceiling | Day 2 | Every tree height argument, Days 12–14, 29, 37–38 |
| Big O / Θ | Day 3 | Every single algorithm analysis |
| Loop invariant | Day 5 | Insertion sort (Day 26), binary search (Day 36), AVL (Day 38) |
| Modular arithmetic | Day 2 | LCG (Day 19), hashing (Days 39–40) |
| Generating functions | Day 6 | Fibonacci analysis, quicksort average case (Day 30) — background only |

If any of the tasks above revealed a gap, re-read that day's page before proceeding. Day 8 starts a new module and assumes Days 1–6 are solid.

---

## Navigation

← **Previous:** [Day 6 — Generating Functions](day-06-generating-functions.md)  
→ **Next:** [Day 8 — Numbers and Information](day-08-numbers-and-information.md)
