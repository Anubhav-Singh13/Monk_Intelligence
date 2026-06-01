# Day 12 — Grover's Algorithm — Quantum Search

> **Today's one idea:** Grover's algorithm finds a marked item in an unsorted list of N items in roughly √N steps — a quadratic speedup over the N steps required classically — by using amplitude amplification to systematically boost the target's probability.
> **Reading time:** ~40 min · **Prereqs:** Day 11
> **Primary source for today:** Rieffel & Polak, *Quantum Computing: A Gentle Introduction*, Chapter 6 (MIT Press, 2011)

---

## The hook

A library has a million books, shelved in completely random order. You want one specific book. Classically, you have no choice but to check books one by one — in the worst case, checking all million before finding it. On average, you check 500,000. There's no smarter approach when there's no structure to exploit.

Now suppose you could pick up half a million books at once, hold them in a kind of quantum superposition, and run a "spotlight" that gradually brightens over the right book while dimming all the others. After about 1,000 rounds of brightening, the right book glows so brightly that you'd pick it every time.

That is Grover's algorithm. The "spotlight" is amplitude amplification: a repeated sequence of two operations that, each iteration, rotates the amplitude distribution slightly toward the target. After ~√N iterations, the target amplitude is near 1, and measurement finds it reliably.

---

## Building the intuition

### The setup

You have N items, one of which is "marked" (the correct answer). You have access to an oracle — a black box that can tell you whether a given item is the marked one. Classically, you need O(N) oracle calls to find the marked item. Grover achieves O(√N).

For N = 1,000,000: classical needs ~500,000 queries; Grover needs ~785.
For N = 10^18 (one quintillion): classical needs ~5×10^17 queries; Grover needs ~10^9. Still large, but trillions of times fewer.

### Amplitude amplification — the geometry

Start all N items in equal superposition: each has amplitude 1/√N, so each has probability 1/N.

The target item's amplitude starts at 1/√N — the same as every other item. Grover's algorithm applies two operations repeatedly:

**Operation 1 — Oracle reflection:** The oracle flips the *sign* of the target item's amplitude. Everything else is unchanged.

```
Before oracle:       After oracle (target = item 3):
  item 1:  +1/√N      item 1:  +1/√N
  item 2:  +1/√N      item 2:  +1/√N
  item 3:  +1/√N  →   item 3:  −1/√N   ← sign flipped
  item 4:  +1/√N      item 4:  +1/√N
  ...                 ...
```

This sign flip is invisible to direct measurement (|−1/√N|² = |+1/√N|²). The target is still indistinguishable by measurement. But the *sign* marks it for the next operation.

**Operation 2 — Diffusion (inversion about the mean):** Reflect all amplitudes around their average value.

```
Average before diffusion ≈ (1/√N) − (2/N√N) ≈ 1/√N (slightly less because one was flipped)

Inversion about the mean:
  new amplitude = 2 × (average) − (old amplitude)

  item 3: 2 × (mean) − (−1/√N) = 2×mean + 1/√N  ← target gets boosted above mean
  all others: 2 × (mean) − (1/√N) ≈ slightly below previous amplitude
```

After one iteration: the target's amplitude is slightly larger; all others are slightly smaller.
After ~√N iterations: the target's amplitude is close to 1; measurement finds it almost certainly.

### The geometric picture

Think of it as rotating a vector in a 2D plane. The state of the quantum computer can be described as a superposition of "target" and "everything else." Grover's algorithm rotates this vector toward the target by a fixed angle each iteration.

```
     ↑ target direction
     │
     │       ←  final state after √N steps
     │      /
     │     /    (each step rotates by angle ~2/√N)
     │    /
     │   /
     │  /
     │ /
     │/ ← initial state (equal superposition, tiny component along target)
     └─────────────────────────────────────────→ "everything else" direction
```

Each Grover iteration rotates by approximately 2/√N radians. Starting near horizontal, you need roughly π/(4/√N) = π√N/4 ≈ √N/4 iterations to reach vertical (full amplitude on target).

### What Grover can and cannot search

Grover needs an oracle — a function that checks whether a given item is the answer. This exists for many real problems:

- Is this password the right one? (check: hash and compare)
- Does this key decrypt this ciphertext? (check: decrypt and verify)
- Does this variable assignment satisfy this constraint? (check: evaluate the constraint)

Grover doesn't help when:
- There's no clear way to *verify* a candidate answer
- The search space has structure that classical algorithms already exploit (sorted lists, indexed databases)
- The speedup (√N) is insufficient for the problem scale

---

## The formal picture

**Grover's operator G = D · O:**
- O (oracle): flips the phase of the marked state: |target⟩ → −|target⟩, others unchanged
- D (diffusion): inversion about the average, implemented as D = 2|+⟩⟨+| − I

Applied k times, the probability of measuring the target is:

```math
P(\text{target after } k \text{ steps}) = \sin^2\!\left((2k+1)\arcsin\frac{1}{\sqrt{N}}\right)
```

This is maximized at k ≈ (π/4)√N, giving P ≈ 1 − 1/N (essentially certain).

**Lower bound:** It has been proven (Bennett et al., 1997) that no quantum algorithm can search an unstructured database in fewer than Ω(√N) oracle calls. Grover is optimal.

**The quadratic speedup:** O(N) → O(√N). This is quadratic, not exponential. For cryptographic key search, this means doubling the key length (e.g., AES-128 → AES-256) restores classical security levels against quantum attack. Unlike Shor's algorithm, Grover doesn't break symmetric cryptography — it just halves the effective key length.

---

## Where it breaks / what it is not

**"Grover breaks all encryption."**
No. Grover gives a quadratic speedup on key search. Symmetric encryption (AES) uses key lengths specifically chosen to remain secure even with √N speedup — 256-bit keys are standard precisely for this reason. RSA (asymmetric) is broken by Shor's algorithm (Day 13), not Grover's.

**"Grover speeds up all search problems."**
Only unstructured search — where you have no information about where the answer is. Classical algorithms that exploit structure (binary search on sorted lists, indexed lookup) are already O(log N) — far faster than Grover's O(√N). Grover is irrelevant for them.

**"You just run Grover once and get the answer."**
Almost — but not quite. The algorithm maximizes probability at exactly √N/4 iterations. Run fewer: probability is too low. Run more: probability drops back down (the rotation overshoots). You need to know N (the search space size) to set the iteration count correctly, or use a variant with an adaptive stopping rule.

---

## Try it yourself

**1. Check understanding.**
A classical exhaustive search of a database of 1 billion items requires 500 million queries on average. How many oracle calls does Grover's algorithm require?

<details>
<summary>Answer</summary>
√(10^9) ≈ 31,623 queries. Grover achieves about a 15,000× reduction in oracle calls compared to classical average-case search.
</details>

**2. Apply.**
A cybersecurity team wants to use Grover's algorithm to brute-force a 128-bit symmetric encryption key. How many oracle calls does classical brute force require (average)? How many does Grover require? Does this break AES-128?

<details>
<summary>Answer</summary>
Classical: ~2^127 queries (average: half the keyspace).
Grover: ~√(2^128) = 2^64 queries — about 18 quintillion.
This is vastly fewer than classical, but 2^64 quantum oracle calls at any foreseeable gate speed (even 1 GHz) would take ~585 years. AES-128 isn't immediately broken, but AES-256 (Grover: 2^128 calls) is considered safe. This is why post-quantum standards recommend doubling symmetric key lengths.
</details>

**3. Stretch.**
Grover's algorithm performs exactly √N/4 iterations to maximize success probability. What happens if you run √N/2 iterations? √N iterations?

<details>
<summary>Answer</summary>
The probability follows sin²((2k+1)arcsin(1/√N)). At k ≈ π√N/4, probability ≈ 1 (maximum). At k ≈ π√N/2, probability ≈ 0 (the state has rotated past the target and back to "everything else"). At k ≈ 3π√N/4, probability ≈ 1 again. The oscillation continues — Grover "bounces" between the target and the uniform superposition. Running too many iterations is just as bad as too few. In practice, you need to know N or use a quantum counting subroutine to find it.
</details>

---

## Connect it back

Grover showed quadratic speedup on search — broadly useful but not exponential. Tomorrow, Shor's algorithm (Day 13) delivers an *exponential* speedup on a very specific problem: integer factoring. The contrast between these two speedups — quadratic vs. exponential — is crucial for understanding which quantum algorithms are genuinely transformative and which are merely better.

**The question you should now be able to answer:** Why doesn't Grover's algorithm break AES-256 encryption?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Rieffel & Polak, *Quantum Computing: A Gentle Introduction*, Chapter 6, Section 6.1 (MIT Press, 2011). Pages 131–150. The geometric picture of amplitude amplification, with clear diagrams of the rotation toward the target state.

**If you want the deep version:**
- Lov K. Grover, "A Fast Quantum Mechanical Algorithm for Database Search," *Proceedings of STOC 1996*, pp. 212–219. The original paper. Sections 1–3 (pages 1–5) are readable without heavy math and outline the core insight of inversion about the mean.
- Aaronson, *Quantum Computing Since Democritus*, Lecture 10, pages 8–14. Aaronson's discussion of Grover vs. classical probabilistic search and the cryptographic implications is sharper than any other accessible source.

---

## Navigation

← **Previous:** [Day 11 — Deutsch's Problem — The First Quantum Speedup](./day-11-deutsch-problem.md)
→ **Next:** [Day 13 — Shor's Algorithm — Why Cryptographers Worry](./day-13-shors-algorithm.md)
