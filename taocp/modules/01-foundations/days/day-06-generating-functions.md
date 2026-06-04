# Day 6 — Generating Functions — A First Glimpse

> **Today's one idea:** A generating function packages an entire sequence of numbers into a single algebraic object — so that operations on the sequence (shift, convolve, sum) become ordinary algebra on the function.
> **Reading time:** ~38 min · **Prereqs:** Day 2
> **Primary source:** Knuth, *TAOCP* Vol. 1, §1.2.9 "Generating Functions" (pp. 87–96, 3rd ed.)

---

## The hook

Here is a problem. You have a recurrence:

$$F_0 = 0, \quad F_1 = 1, \quad F_n = F_{n-1} + F_{n-2} \quad \text{for } n \geq 2$$

You want a closed-form formula — something you can plug n into directly, without computing all the prior terms. Working with the recurrence directly is like trying to describe a song by listing each note. What if you could describe the whole song as a single chord?

That chord is the **generating function**: a formal power series whose coefficients *are* the sequence. Once you have it, algebra does the work that would otherwise require clever tricks.

You encountered the Fibonacci sequence in Days 1 and 5. Today you see how to derive its closed form systematically — and more importantly, you understand *why* this technique keeps appearing in TAOCP's analyses.

---

## Building the intuition

### Packaging a sequence

The ordinary generating function (OGF) of a sequence $a_0, a_1, a_2, \ldots$ is:

$$G(z) = a_0 + a_1 z + a_2 z^2 + a_3 z^3 + \cdots = \sum_{n=0}^{\infty} a_n z^n$$

Think of $z$ not as a number you will substitute, but as a *label*. The coefficient of $z^n$ is "the answer for n." The series is a filing cabinet: drawer $z^n$ holds $a_n$.

**The simplest example:** the sequence $1, 1, 1, 1, \ldots$ has generating function:

$$G(z) = 1 + z + z^2 + z^3 + \cdots = \frac{1}{1-z}$$

This is the geometric series formula. It converges for $|z| < 1$, but in combinatorics we treat it as a formal algebraic identity regardless of convergence.

---

### Why algebra on G(z) = operations on the sequence

Multiplying G(z) by z shifts the sequence right by one position:

$$z \cdot G(z) = 0 + a_0 z + a_1 z^2 + a_2 z^3 + \cdots$$

Coefficient of $z^n$ in $z \cdot G(z)$ is $a_{n-1}$. So "multiply by z" = "shift the sequence index by 1." This is the key manipulation.

Adding two generating functions adds their sequences coefficient by coefficient. The recurrence $F_n = F_{n-1} + F_{n-2}$ says the sequence satisfies a linear relationship — and that translates to an equation between the shifted generating functions.

---

### Deriving the Fibonacci formula

Let $F(z) = \sum_{n=0}^{\infty} F_n z^n = 0 + z + z^2 + 2z^3 + 3z^4 + 5z^5 + \cdots$

The recurrence $F_n = F_{n-1} + F_{n-2}$ means (for $n \geq 2$):

$$F(z) = z \cdot F(z) + z^2 \cdot F(z) + \underbrace{z}_{\text{correct for }F_1 = 1\text{, }F_0=0}$$

Solving for $F(z)$:

$$F(z) - z\,F(z) - z^2\,F(z) = z$$

$$F(z)(1 - z - z^2) = z$$

$$F(z) = \frac{z}{1 - z - z^2}$$

Now use partial fractions. The denominator $1 - z - z^2 = -(z^2 + z - 1) = -(z - \frac{-1+\sqrt{5}}{2})(z - \frac{-1-\sqrt{5}}{2})$.

The roots are $z = 1/\phi$ and $z = -\phi$ where $\phi = (1+\sqrt{5})/2$. After partial fractions (the algebra is in Knuth §1.2.8):

$$F_n = \frac{\phi^n - \hat{\phi}^n}{\sqrt{5}} \quad \text{where } \hat{\phi} = \frac{1-\sqrt{5}}{2} \approx -0.618$$

Since $|\hat{\phi}| < 1$, $\hat{\phi}^n \to 0$ as $n \to \infty$, and:

$$F_n = \left\lfloor \frac{\phi^n}{\sqrt{5}} + \frac{1}{2} \right\rfloor \quad \text{(nearest integer to } \phi^n/\sqrt{5}\text{)}$$

This is **Binet's formula**. A sequence defined by integer arithmetic has a closed form involving the irrational $\phi = (1+\sqrt 5)/2$.

---

## The formal picture

The three-step generating function method:

```mermaid
flowchart LR
    A["Recurrence\nfor aₙ"] --> B["Write G(z) = Σ aₙzⁿ\nApply recurrence\nto get equation for G(z)"]
    B --> C["Solve algebraically\nfor G(z)"]
    C --> D["Extract coefficients\n(partial fractions,\nexpansion)"]
    D --> E["Closed form\nfor aₙ"]
    style A fill:#4a9,color:#fff
    style E fill:#4a9,color:#fff
```

**Where this appears in TAOCP:**

| Algorithm | Generating function used for |
|-----------|------------------------------|
| Euclid GCD analysis (§1.2.8) | Counting steps via Fibonacci GF |
| Sorting lower bounds (§5.3) | Counting permutations |
| Hashing analysis (§6.4) | Expected probe length |
| Quicksort analysis (§5.2.2) | Expected comparisons |

You will not need to derive these yourself at L1. But you *will* need to read sentences like "by the generating function argument of §1.2.9..." and know what kind of object that is.

---

## Where it breaks / what it is not

**Misconception: Generating functions are about convergence.**  
In combinatorics, the series $G(z) = \sum a_n z^n$ is treated as a *formal* power series — the variable $z$ is a placeholder, not a number you substitute. Convergence questions are irrelevant. The algebra works identically.

**Misconception: You need generating functions to understand sorting.**  
You do not. Modules 4 and 5 are fully accessible without them. Generating functions appear in the *analysis* sections (average-case proofs) that L1 readers can skip on a first pass. Today's page earns you the ability to recognise what is happening in those proofs rather than feeling excluded.

**Misconception: Binet's formula is numerically useful.**  
For large n, computing $\phi^n / \sqrt{5}$ accumulates floating-point error. The iterative recurrence is more accurate for computation. Binet's formula is analytically useful — it tells you the *asymptotic growth rate* of $F_n$ is $\Theta(\phi^n)$.

---

## Try it yourself

**Exercise 1 — Check understanding:** What is the generating function of the sequence $2, 2, 2, 2, \ldots$ (constant 2)? Express it as a simple fraction.

<details>
<summary>Solution</summary>

$G(z) = 2 + 2z + 2z^2 + \cdots = 2 \cdot \frac{1}{1-z} = \frac{2}{1-z}$
</details>

---

**Exercise 2 — Apply:** The sequence defined by $a_0 = 0$, $a_n = a_{n-1} + 1$ (simply: $a_n = n$) has generating function $G(z) = \sum_{n=0}^{\infty} n z^n$. Show that $G(z) = \frac{z}{(1-z)^2}$.

*(Hint: differentiate $\frac{1}{1-z} = \sum z^n$ with respect to z, then multiply by z.)*

<details>
<summary>Solution</summary>

$\frac{d}{dz}\left(\frac{1}{1-z}\right) = \frac{1}{(1-z)^2} = \sum_{n=0}^{\infty} (n+1)z^n$

Multiply both sides by z: $\frac{z}{(1-z)^2} = \sum_{n=0}^{\infty} (n+1)z^{n+1} = \sum_{n=1}^{\infty} n z^n = G(z)$ ✓
</details>

---

**Exercise 3 — Stretch:** Use Binet's formula to verify $F_{10} = 55$. Compute $\phi^{10}/\sqrt{5}$ in Python and round to the nearest integer.

```python
import math
phi = (1 + math.sqrt(5)) / 2
print(round(phi**10 / math.sqrt(5)))  # Should print 55
```

Now try $F_{50}$. Do you get the correct answer (12586269025)? What happens for $F_{75}$? Why?

<details>
<summary>Solution</summary>

```python
import math
phi = (1 + math.sqrt(5)) / 2
for n in [10, 50, 75]:
    approx = round(phi**n / math.sqrt(5))
    print(f"F({n}) ≈ {approx}")
```

For n=75, Python's float (64-bit IEEE 754) lacks sufficient precision — $\phi^{75}$ is ~$2 \times 10^{15}$, right at the limit of float's 15-16 significant digits. The rounding fails. Use Python's `decimal` module with higher precision, or the iterative recurrence, for large n. This is your first encounter with the floating-point problem that Day 21 will formalise.
</details>

---

## Connect it back

Today's idea — packaging a sequence as an algebraic object — is an instance of a much larger pattern in mathematics: represent a hard discrete problem as a continuous (or algebraic) one, solve it there, and translate back. Fourier transforms do this for signals. Z-transforms do this for digital systems. Generating functions do this for integer sequences.

In TAOCP, every time you see an analysis that arrives at a beautiful closed form ($\phi^n/\sqrt{5}$, $n \ln n$, $H_n = 1 + 1/2 + \cdots + 1/n$), there is almost always a generating function argument underneath. You now have enough to follow the outline of that argument.

**Tomorrow:** Rest and Synthesize — Day 7. No new material. You re-derive, re-draw, and re-code everything from Days 1–6.

**One sharp question you can answer now:**  
*What does multiplying a generating function by $z$ do to the underlying sequence?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:**  
Knuth, *TAOCP* Vol. 1, §1.2.9 "Generating Functions," pp. 87–96. Read the first four pages and the Fibonacci example. The rest of the section (exponential generating functions) is optional for L1.

**If you want the deep version:**
- *Concrete Mathematics* Ch. 7 "Generating Functions," pp. 320–380 — the definitive treatment. §7.1 (pp. 320–335) covers everything today's page touched. This is the chapter to read if generating functions genuinely excited you today.
- Herbert Wilf. *generatingfunctionology*, 2nd ed. Academic Press, 1994. Freely available at math.upenn.edu/~wilf/gfology2.pdf — an entire book devoted to this technique, readable and enthusiastic.

---

## Navigation

← **Previous:** [Day 5 — Proof by Induction and Loop Invariants](day-05-induction-and-invariants.md)  
→ **Next:** [Day 7 — Rest & Synthesize I](day-07-rest-synthesize-1.md)
