# Day 1 — Why Quantum at All?

> **Today's one idea:** Classical computers have a fundamental ceiling that physics itself imposes — and quantum computers are the only known way past it for certain problems.
> **Reading time:** ~35 min · **Prereqs:** None
> **Primary source for today:** John Gribbin, *Computing with Quantum Cats*, Chapter 1 (Bantam Press, 2013)

---

## The hook

It takes an average of 12 years and $2.6 billion to bring a single drug to market. A huge part of why: before you run a clinical trial on a molecule, you need to know how it behaves — how it folds, how it bonds to a protein, how stable it is. And to know that, you need to simulate it on a computer.

Here's the problem: even a small molecule like caffeine has 24 atoms and 102 electrons. Simulating its quantum behavior on a classical computer requires tracking roughly 2^102 possible states — that's more states than there are atoms in the observable universe. No classical computer, ever, could hold that in memory.

This isn't a hardware shortfall that a faster chip will fix. It's a mathematical wall. And it's the wall that motivated Richard Feynman, in 1982, to ask: *"What if we built a computer out of quantum mechanics itself?"*

That question is why this course exists.

---

## Building the intuition

### Classical computers and the bits they're built on

A classical computer represents everything as bits — switches that are either OFF (0) or ON (1). To store a number between 0 and 7, you need 3 bits (because 2³ = 8 possibilities). To represent N possible states, you need log₂(N) bits of memory.

This is enormously efficient for most problems. A photo, a spreadsheet, a chess game — all can be represented compactly in bits and processed step by step.

### The exponential wall

Some problems don't compress. Consider simulating a physical system — say, a molecule. The molecule isn't in one state. Quantum mechanics says it exists in a *superposition* of all possible configurations simultaneously (more on this in Day 3). To simulate this on a classical computer, you must track every possible configuration explicitly.

For a system with N quantum particles, that's 2^N states to track. Double the number of particles → square the number of states → the work grows *exponentially*.

```
 Particles | States to simulate
 ----------|-------------------
     10    | 1,024
     20    | 1,048,576
     50    | ~1 quadrillion (1 petabyte of RAM just to store the state)
    100    | more than atoms in the universe
```

This exponential growth is not a software problem. It's not a RAM problem. It's a *mathematical* property of quantum systems. No amount of classical engineering crosses this wall.

### Why this matters beyond chemistry

Exponential hardness shows up in several economically important problems:

| Problem | Why it's exponentially hard | Who cares |
|---------|----------------------------|-----------|
| Simulating molecules for drug discovery | Quantum states of electrons scale as 2^N | Pharma, biotech |
| Optimizing logistics at massive scale | Search space grows exponentially with variables | Shipping, finance |
| Breaking RSA encryption | Factoring large numbers (more on Day 13) | Cybersecurity |
| Training certain AI models | Some energy landscapes are hard to navigate | ML research |

The key word is *certain*. Quantum computers won't help with everything — most everyday computing (running apps, browsing the web, video calls) has no known quantum speedup and never will.

### Feynman's insight

In 1982, physicist Richard Feynman made a simple observation: if classical computers can't simulate quantum systems efficiently, maybe the solution is to build a computer *that is itself quantum mechanical*. Let nature compute nature.

This is not a metaphor. A quantum computer is a physical device that exploits quantum phenomena — superposition, entanglement, interference — to represent and manipulate quantum states directly. Instead of approximating 2^N states with exponentially growing memory, it *is* a quantum system of N particles, and computation is just letting that system evolve.

---

## The formal picture

**Computational complexity** is the branch of computer science that classifies problems by how hard they are to solve as inputs grow larger.

- **Polynomial time (P):** Work grows as N^k for some fixed k. Manageable. Finding a name in a sorted list, sorting a million items, computing a route on a map.
- **Exponential time:** Work grows as 2^N or worse. Intractable beyond small inputs. Simulating quantum systems, finding the optimal solution among all possible combinations.

The promise of quantum computing: some problems that are exponential for classical computers appear to be polynomial (or at least dramatically faster) on quantum computers. Grover's algorithm is one example (Day 12). Shor's algorithm is the most famous (Day 13).

The crucial caveat — stated now so it never surprises you: **not all hard problems have known quantum speedups.** The class of problems where quantum helps is specific and well-studied. Quantum computers are not general-purpose magic.

---

## Where it breaks / what it is not

**"Quantum computers are just faster computers."**
No. For problems like email, word processing, or video streaming, classical computers are already optimal. A quantum computer would be *slower* for these tasks.

**"We'll have quantum computers that solve everything soon."**
No. Today's quantum computers (the NISQ era, Day 18) are small, noisy, and error-prone. The problems they can solve better than classical computers are still very narrow.

**"Classical computers will eventually catch up with more transistors."**
No — for the problems at the exponential wall. Adding more transistors scales linearly. Exponential growth outruns any linear scaling eventually. The wall is mathematical, not engineering.

**"Quantum computing is just a physics curiosity."**
No. The US government has declared it a national security priority. IBM, Google, Microsoft, Amazon, and dozens of startups have active quantum hardware programs. It is both real and consequential.

---

## Try it yourself

**1. Retrieval — close the page.** Write down in one sentence: what is the exponential wall that classical computers face when simulating quantum systems — and is it a hardware shortfall or something more fundamental? Open only after writing your answer.

<details>
<summary>Answer</summary>
Classical computers must explicitly track all 2^N configurations of an N-particle quantum system. For N=100, this exceeds the number of atoms in the observable universe. This is a mathematical wall imposed by quantum mechanics — not a hardware shortfall that faster chips can cross.
</details>

**2. Check understanding.**
If a classical simulation of a 50-qubit system requires 2^50 values stored in memory, roughly how many gigabytes is that? (Hint: 2^30 bytes ≈ 1 GB.)

<details>
<summary>Hint</summary>
2^50 = 2^30 × 2^20 ≈ 1 GB × 1,048,576 ≈ 1 petabyte. Most laptops have 16 GB RAM; a petabyte is about 60,000 laptops' worth.
</details>

<details>
<summary>Answer</summary>
2^50 complex numbers × 16 bytes each ≈ 16 × 10^15 bytes ≈ 16 petabytes. This exceeds the total storage of most data centers. For 100 qubits, the number is incomprehensible.
</details>

**3. Apply.**
Name three tasks you do every day on your phone or laptop that would NOT benefit from quantum computing. Explain briefly why for each.

<details>
<summary>Answer (example)</summary>
(1) Sending a WhatsApp message — sequential, no combinatorial explosion. (2) Playing a video — decoding is polynomial-time, well-suited to classical. (3) Searching Google — classical search is already near-optimal for this task structure.
</details>

**4. Stretch.**
Feynman's 1982 paper said: "Nature isn't classical, dammit, and if you want to make a simulation of nature, you'd better make it quantum mechanical." What do you think he meant by "nature isn't classical"? What observation was he responding to?

<details>
<summary>Answer</summary>
Feynman observed that quantum systems (electrons, molecules) do not follow classical probability rules — they exhibit interference and superposition. Any attempt to simulate this behavior on a classical computer requires explicitly tracking exponentially many states. The failure of classical simulation isn't a gap in our algorithms; it's a fundamental property of what classical bits can represent.
</details>

---

**Transfer — apply it (all levels):** Name a problem from your own work or daily life that involves searching across a large space of possibilities — finding a configuration, a schedule, or a set of parameters. Estimate the search-space size. Write one sentence: is it large enough that a quantum speedup would matter, or does classical heuristic search already find good-enough answers?

---

## Connect it back

You started today with a puzzle: why does drug discovery cost $2.6 billion? Now you have a partial answer — the underlying physics of molecules is exponentially hard to simulate classically. Tomorrow, we'll go one level deeper: *what is it about tiny things that makes them behave so differently from everyday objects?* The answer will be stranger than you expect.

**The question you should now be able to answer:** Why can't we just build a faster classical supercomputer to simulate molecules?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Gribbin, *Computing with Quantum Cats*, Chapter 1 ("Cat in a Box"). Gribbin sets the historical scene — from Turing to Feynman — and makes the motivation visceral. Read it for the story, not the formalism.

**If you want the deep version:**
- Scott Aaronson, *Quantum Computing Since Democritus*, Lecture 9 ("Quantum"). Available free at scottaaronson.com/democritus/lec9.html. Aaronson's first few pages on why quantum mechanics forces us to rethink computation are the clearest philosophical treatment available. Read the first 4 pages.
- Richard Feynman, "Simulating Physics with Computers," *International Journal of Theoretical Physics*, 21(6/7):467–488, 1982. The original paper. The first 3 pages are readable without physics training and electrifying.

---

## Navigation

← **Back to course overview:** [README](../../../README.md)
→ **Next:** [Day 2 — The Strangeness of the Quantum World](./day-02-quantum-strangeness.md)
