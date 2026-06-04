# Day 1 — What Is an Algorithm?

> **Today's one idea:** An algorithm is not just "a method" — it is a finite, unambiguous procedure that is guaranteed to halt and produce the right answer. Euclid's GCD is the archetypal example.
> **Reading time:** ~35 min · **Prereqs:** None
> **Primary source:** Knuth, *TAOCP* Vol. 1, §1.1 "Algorithms" (pp. 1–9, 3rd ed.)

---

## The hook

You have written code before. COBOL programs, Python scripts — procedures that follow steps. But have you ever wondered: what *exactly* makes a sequence of steps an *algorithm*, rather than just a recipe, a ritual, or a wish?

Here is a recipe that fails the test: *"Keep stirring until the sauce is perfect."*  
Here is one that passes: *"Stir for exactly 3 minutes, then reduce heat."*

The first is ambiguous. The second is finite and unambiguous. But there is more to it than just being clear. Consider this "procedure": start at 1, keep adding 1, and stop when you reach the largest prime number. This is perfectly unambiguous — but it never halts, because there is no largest prime.

An algorithm must:
1. Be **finite** — it ends after a bounded number of steps.
2. Be **definite** — every step is precisely specified, with no room for interpretation.
3. Have **input** — zero or more values drawn from a specified set.
4. Have **output** — one or more values that bear a defined relationship to the input.
5. Be **effective** — every step is basic enough that a person with pencil and paper can carry it out exactly.

Knuth calls these the five properties of an algorithm. He takes them seriously enough to spend the first nine pages of a 3,000-page work establishing them. That is your first signal about what kind of book this is.

---

## Building the intuition

Meet Euclid's algorithm. It computes the greatest common divisor (GCD) of two positive integers — the largest number that divides both without a remainder.

**The idea in plain English:** if you have two numbers, the GCD of the pair is the same as the GCD of the smaller number and the remainder when you divide the larger by the smaller. Keep replacing the pair this way until the remainder is zero. The non-zero number you have at that point is the answer.

**Traced by hand for gcd(12, 8):**

```
Step 1: 12 ÷ 8 = 1 remainder 4   →  new pair: (8, 4)
Step 2:  8 ÷ 4 = 2 remainder 0   →  remainder is 0, stop
Answer: 4
```

**And for gcd(252, 105):**

```
Step 1: 252 ÷ 105 = 2 remainder 42  →  (105, 42)
Step 2: 105 ÷  42 = 2 remainder 21  →  (42, 21)
Step 3:  42 ÷  21 = 2 remainder  0  →  done
Answer: 21
```

Check: 21 divides 252 (= 12 × 21) and 105 (= 5 × 21). ✓

Notice: the inputs get strictly *smaller* at each step (the remainder is always less than the divisor). That is what guarantees the algorithm terminates — we cannot shrink a positive integer forever.

**Formally, as Knuth writes it (Algorithm E):**

> **E1.** [Find remainder.] Divide m by n and let r be the remainder (0 ≤ r < n).  
> **E2.** [Is it zero?] If r = 0, the algorithm terminates; n is the answer.  
> **E3.** [Reduce.] Set m ← n, n ← r, and go back to step E1.

Knuth labels every step. That labelling is not cosmetic — it lets him count how many times each step executes, which is how he measures cost. You will see this pattern in every volume.

---

## The formal picture

A **computational method** is a quadruple (Q, I, Ω, f) where:
- Q is the set of all possible *states* the computation can be in.
- I ⊆ Q is the set of *input* states.
- Ω ⊆ Q is the set of *output* states.
- f : Q → Q is the *transition function* (what happens at each step).

An **algorithm** is a computational method that terminates: for every x ∈ I, repeated application of f eventually reaches an element of Ω.

For Euclid's algorithm:
- A state is a pair (m, n) of non-negative integers plus a "which step am I at" marker.
- Input states: all (m, n) with m, n > 0.
- Output states: states of the form (0, d) where d > 0 is the answer.
- The transition is: compute the remainder, shift the pair.

The termination argument is the key: at each transition, the second number n strictly decreases (it becomes the remainder, which is < n). A strictly decreasing sequence of positive integers must reach zero.

> **Annotating symbols back to intuition:**  
> Q = "all the situations the computation can find itself in" (not just the numbers, but the *position* in the procedure).  
> f = "what the next step does."  
> Termination = "f can't cycle forever because something measurable keeps shrinking."

---

## Where it breaks / what it is not

**Misconception 1: "Any loop with a stopping condition is an algorithm."**  
No. If the stopping condition can never be reached (like "stop when the sauce is perfect"), you have a computational method but not an algorithm. Termination must be *provable*, not just plausible.

**Misconception 2: "Euclid's algorithm is obvious — why dwell on it?"**  
Because it is the *simplest* non-trivial example of every property. Later algorithms will be vastly more complex, but the checklist (finite, definite, input, output, effective) applies to all of them. Internalise it now.

**Misconception 3: "The five properties are just philosophy."**  
They are actually a design constraint. When Knuth later shows that certain problems have no algorithm (they are *undecidable*), he is using this exact definition. "No algorithm" means "no finite, effective procedure satisfying these five properties."

**Edge case:** What about algorithms that produce probabilistic output, or algorithms that can run forever on purpose (like a web server)? Knuth acknowledges these exist and calls them *computational methods*. They are outside the scope of TAOCP's main analysis, which focuses on procedures that provably halt.

---

## Try it yourself

**Exercise 1 — Check understanding:** Apply Algorithm E to gcd(35, 15). Trace every step, writing the pair (m, n) and remainder r at each stage. How many steps does it take?

<details>
<summary>Hint</summary>
35 ÷ 15 = ? What is the remainder? Keep going until r = 0.
</details>

<details>
<summary>Solution</summary>

```
(35, 15): 35 ÷ 15 = 2 r 5  →  (15, 5)
(15,  5): 15 ÷  5 = 3 r 0  →  done
Answer: gcd(35, 15) = 5
```

Two steps. Note 35 = 7 × 5 and 15 = 3 × 5. ✓
</details>

---

**Exercise 2 — Apply:** Write a Python function implementing Algorithm E exactly as Knuth labels it. Use a `while` loop. Add a counter that tracks how many times step E1 executes.

<details>
<summary>Solution</summary>

```python
def euclid_gcd(m: int, n: int) -> tuple[int, int]:
    """Returns (gcd, step_count) for gcd(m, n) via Knuth's Algorithm E."""
    steps = 0
    while True:
        r = m % n          # E1: find remainder
        steps += 1
        if r == 0:         # E2: done?
            return n, steps
        m, n = n, r        # E3: reduce

# Try it:
for pair in [(12, 8), (252, 105), (35, 15), (1000000, 999983)]:
    result, count = euclid_gcd(*pair)
    print(f"gcd{pair} = {result}, steps = {count}")
```
</details>

---

**Exercise 3 — Stretch:** How many steps does euclid_gcd take for gcd(F(n+1), F(n)) where F(k) is the k-th Fibonacci number? Try n = 5, 10, 15. What do you notice? (Knuth addresses this in §1.2.8 — this is a famous result.)

<details>
<summary>Hint</summary>
Consecutive Fibonacci numbers have a special relationship with Euclid's algorithm. The answer is related to n itself.
</details>

<details>
<summary>Solution</summary>

```python
def fib(k):
    a, b = 1, 1
    for _ in range(k - 1):
        a, b = b, a + b
    return a

for n in [5, 10, 15]:
    _, steps = euclid_gcd(fib(n + 1), fib(n))
    print(f"n={n}: {steps} steps")
# Output: n=5: 5 steps, n=10: 10 steps, n=15: 15 steps
```

Consecutive Fibonacci numbers are the *worst case* for Euclid's algorithm — they require the maximum number of steps for numbers of their size. This is Lamé's theorem, which Knuth proves in §1.2.8.
</details>

---

## Connect it back

You entered today not knowing what distinguished an algorithm from a vague procedure. You leave with Euclid's five-property checklist and a concrete example you traced by hand and implemented in code. The termination argument — *something strictly decreasing guarantees halting* — is your first loop invariant. You will use exactly this pattern on Day 5 when we formalise invariants, and then on every sorting and searching algorithm in Modules 4 and 5.

**Tomorrow:** We equip the mathematical toolkit — the notation for sums, floors, and modular arithmetic that Knuth uses to count steps precisely. Without it, "Algorithm E takes O(log n) steps" is a sentence you can believe but not verify.

**One sharp question you can answer now that you couldn't this morning:**  
*Why does Euclid's algorithm always terminate?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:**  
Knuth, *TAOCP* Vol. 1, §1.1, pp. 1–9. Read Knuth's own prose introduction to Algorithm E and the formal definition. Pay attention to how he labels steps — that convention reappears on every page of the series.

**If you want the deep version:**
- Knuth, *TAOCP* Vol. 1, §1.2.1 "Mathematical Induction" (pp. 11–21) — introduces the inductive style that will pay off fully on Day 5. Read the first three pages now as a preview.
- *Concrete Mathematics* (Graham, Knuth, Patashnik), Ch. 1 "Recurrent Problems," pp. 1–17 — the Tower of Hanoi and Josephus problem as alternative first examples of algorithm analysis. Excellent if today's material felt too quick.

---

## Navigation

← **Back to course overview:** [README](../../../README.md)  
→ **Next:** [Day 2 — Knuth's Mathematical Toolkit](day-02-mathematical-toolkit.md)
