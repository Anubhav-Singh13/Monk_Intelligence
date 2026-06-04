# Day 2 — Knuth's Mathematical Toolkit

> **Today's one idea:** Floor, ceiling, modular arithmetic, and summation notation are not decorations — they are the *language* in which every algorithm analysis is written. Earn them once and they become transparent.
> **Reading time:** ~35 min · **Prereqs:** Day 1
> **Primary source:** Knuth, *TAOCP* Vol. 1, §1.2.2–1.2.4 (pp. 21–46, 3rd ed.)

---

## The hook

In Day 1 you saw that Euclid's algorithm terminates because the remainder is always *strictly less than* the divisor. You wrote that in words. But Knuth writes it as:

$$0 \leq r < n$$

And he counts the number of steps as something involving $\lfloor \log_\phi n \rfloor$ where $\phi = (1+\sqrt{5})/2$.

If those symbols feel like static, the rest of TAOCP will feel like a foreign film without subtitles. Today's job is to make them transparent — not by memorising rules, but by understanding what each symbol is *asking*.

---

## Building the intuition

### Floor and Ceiling

You already know integer division. In Python, `17 // 5 = 3`. That answer — "round down to the nearest integer" — has a name:

$$\lfloor 17/5 \rfloor = 3$$

The **floor** of x, written $\lfloor x \rfloor$, is the largest integer *not exceeding* x. Think of it as "which integer does x land on when you drop it to the number line?"

```
Number line:  ...  2  ___  3  ___  4  ...
                        ↑
                     3.7 drops to 3  →  ⌊3.7⌋ = 3
                    -3.7 drops to -4  →  ⌊-3.7⌋ = -4   (watch the sign!)
```

The **ceiling** of x, written $\lceil x \rceil$, is the smallest integer *at least* x — round up.

$$\lceil 3.2 \rceil = 4 \qquad \lceil 5 \rceil = 5 \qquad \lceil -3.7 \rceil = -3$$

**Why they matter for algorithms:** When you split an array of n elements in half, one half has $\lfloor n/2 \rfloor$ elements and the other has $\lceil n/2 \rceil$. When a binary tree of n nodes has height h, you know $h \geq \lfloor \log_2 n \rfloor$. These are not coincidences — they are the geometry of discrete structures.

---

### Modular Arithmetic

$m \bmod n$ is the remainder when m is divided by n. You have used this as `%` in Python and `MOD` in COBOL.

$$17 \bmod 5 = 2 \qquad 20 \bmod 4 = 0 \qquad 7 \bmod 10 = 7$$

Knuth writes $m \equiv r \pmod{n}$ to mean "m and r have the same remainder when divided by n," or equivalently, "n divides m − r."

$$17 \equiv 2 \pmod{5} \qquad 22 \equiv 2 \pmod{5} \qquad 7 \equiv 7 \pmod{10}$$

The word **congruence** for this relationship is not snobbery — it names something deep: two numbers that are congruent mod n are indistinguishable in any arithmetic that "wraps around" at n. Clocks are mod 12. Days of the week are mod 7. On Day 19, random number generators will live and die by mod arithmetic.

---

### Summation Notation

The sigma:

$$\sum_{k=1}^{n} k = 1 + 2 + 3 + \cdots + n$$

Read it as: "sum over all values of k from 1 to n of the expression k." The variable k is a dummy — it exists only inside the sum and its name does not matter.

**The one sum you must know cold:**

$$\sum_{k=1}^{n} k = \frac{n(n+1)}{2}$$

*Why:* pair the first and last terms (1 + n), the second and second-to-last (2 + (n−1)), and so on. Each pair sums to n+1, and there are n/2 pairs. This is the $O(n^2)$ you will count in insertion sort on Day 26 — it is the sum of an arithmetic progression.

**The sum you will meet in sorting:**

$$\sum_{k=1}^{n} \log_2 k \approx n \log_2 n - n \log_2 e$$

This comes from $\log_2(n!)$, and it is the lower bound for comparison-based sorting that Day 25 derives. You do not need to prove it today — just recognise the shape.

---

### Logarithms

Knuth uses $\log$ to mean $\log_2$ throughout TAOCP unless specified otherwise (computer scientists count bits). The key identity:

$$\log_b(xy) = \log_b x + \log_b y$$

This is why an algorithm that halves its problem at each step takes $O(\log n)$ time: $\log_2(n/2^k) = 0$ when $k = \log_2 n$.

Change of base: $\log_a x = \frac{\log_b x}{\log_b a}$. All log bases differ only by a constant factor — this is why $O(\log_2 n) = O(\ln n) = O(\log_{10} n)$ in asymptotic analysis.

---

## The formal picture

The four tools with their Knuth-notation forms:

| Symbol | Name | Meaning | Python equivalent |
|--------|------|---------|-------------------|
| $\lfloor x \rfloor$ | Floor | Largest integer ≤ x | `math.floor(x)` or `int(x)` for x≥0 |
| $\lceil x \rceil$ | Ceiling | Smallest integer ≥ x | `math.ceil(x)` |
| $m \bmod n$ | Modulo | Remainder of m ÷ n | `m % n` |
| $\sum_{k=a}^{b} f(k)$ | Summation | Add f(k) for k = a, a+1, …, b | `sum(f(k) for k in range(a, b+1))` |

**Important identities Knuth uses constantly:**

$$\lfloor n/2 \rfloor + \lceil n/2 \rceil = n \quad \text{(splitting a list in two)}$$

$$\lfloor \lfloor x/m \rfloor / n \rfloor = \lfloor x/(mn) \rfloor \quad \text{(nested floors collapse)}$$

$$\sum_{k=0}^{n-1} 2^k = 2^n - 1 \quad \text{(sum of a geometric series — binary trees)}$$

---

## Where it breaks / what it is not

**Misconception: $\lfloor -3.2 \rfloor = -3$.**  
No. Floor rounds *toward negative infinity*, not toward zero. $\lfloor -3.2 \rfloor = -4$. This trips up programmers who think "truncation" and "floor" are the same — they are, for positive numbers only.

**Misconception: $m \bmod n$ is always in $[0, n)$.**  
In mathematics, yes. In Python 3, yes. But in C, Java, and COBOL, `%` on negative numbers may return a negative result. When Knuth writes $m \bmod n$ he always means the non-negative remainder. Be careful in code.

**Misconception: Summation notation is just shorthand.**  
It is also a *calculation tool*. You can split sums, swap the order of summation, and substitute variables. These algebraic manipulations are how Knuth derives closed-form costs (e.g., that bubble sort does exactly $n(n-1)/2$ comparisons in the worst case).

---

## Try it yourself

**Exercise 1 — Check understanding:** Compute each of the following without a calculator:

a) $\lfloor 7.9 \rfloor$  
b) $\lceil 7.1 \rceil$  
c) $\lfloor -2.3 \rfloor$  
d) $17 \bmod 5$  
e) $\sum_{k=1}^{4} k^2$

<details>
<summary>Solution</summary>

a) 7 · b) 8 · c) −3 · d) 2 · e) 1 + 4 + 9 + 16 = **30**
</details>

---

**Exercise 2 — Apply:** The number of nodes in a complete binary tree of height h is $2^{h+1} - 1$. Use this and the floor formula to show that a complete binary tree with n nodes has height $\lfloor \log_2 n \rfloor$.

<details>
<summary>Hint</summary>
A complete binary tree of height h has at least $2^h$ nodes and at most $2^{h+1}-1$. Take $\log_2$ of both sides of the inequality.
</details>

<details>
<summary>Solution</summary>

$2^h \leq n \leq 2^{h+1} - 1 < 2^{h+1}$

Taking $\log_2$: $h \leq \log_2 n < h+1$

So $h = \lfloor \log_2 n \rfloor$. The floor is exactly the height. ∎
</details>

---

**Exercise 3 — Stretch:** Verify the identity $\sum_{k=1}^{n} k = n(n+1)/2$ using the "pairing" argument for n = 6 (write out all 6 terms, pair them, count the pairs). Then write a one-line Python expression that verifies it for n = 1000.

<details>
<summary>Solution</summary>

For n=6: (1+6) + (2+5) + (3+4) = 7+7+7 = 21 = 6×7/2. ✓

```python
assert sum(range(1, 1001)) == 1000 * 1001 // 2  # no output means it passed
```
</details>

---

## Connect it back

Yesterday you traced Euclid's algorithm and argued informally that it terminates. Today you have the notation to say it precisely: the remainder $r = m \bmod n$ satisfies $0 \leq r < n$, and since $n$ strictly decreases at each step, after at most $n$ steps we must reach $r = 0$. You have just written your first quantified termination argument.

**Tomorrow:** Asymptotic notation — Big O, Ω, and Θ. These are built on top of today's toolkit: O(f) is a statement about limits involving the $\leq$ relation, and the summation $\sum_{k=1}^{n} k = n(n+1)/2 = \Theta(n^2)$ will be your first worked example.

**One sharp question you can answer now:**  
*What is $\lfloor \log_2 1000 \rfloor$, and why does it tell you how many times you can halve 1000 before reaching 1?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:**  
Knuth, *TAOCP* Vol. 1, §1.2.2 "Numbers, Powers, and Logarithms" and §1.2.3 "Sums and Products," pp. 21–36. Knuth's own derivations of the closed forms for $\sum k$ and $\sum k^2$ are models of careful calculation.

**If you want the deep version:**
- *Concrete Mathematics* Ch. 2 "Sums," pp. 21–72 — the most complete treatment of summation manipulation in the literature. Even reading pp. 21–30 (the basic techniques) will pay dividends throughout this course.
- *Concrete Mathematics* Ch. 3 "Integer Functions," pp. 67–101 — floors and ceilings in depth, with beautiful identities. §3.1 (pp. 67–78) covers everything today's page touched.

---

## Navigation

← **Previous:** [Day 1 — What Is an Algorithm?](day-01-what-is-an-algorithm.md)  
→ **Next:** [Day 3 — Asymptotic Analysis](day-03-asymptotic-analysis.md)
