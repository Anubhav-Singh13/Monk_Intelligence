# Day 21 — Quantum Machine Learning — Hype vs. Reality

> **Today's one idea:** Quantum machine learning claims require the same scrutiny as any other proposed quantum speedup — the speedup must be over the best classical algorithm, on a problem that can't be easily dequantized, using hardware that doesn't yet exist.
> **Reading time:** ~35 min · **Prereqs:** Days 12, 13, 18
> **Primary source for today:** John Preskill, "Quantum Computing in the NISQ Era and Beyond," Section 4, arXiv:1801.00862 (2018)
> **Before you start:** Recall Day 20's load-bearing idea — one sentence, no looking. Why is quantum simulation Feynman's original motivation — and what makes it naturally suited to quantum hardware?

---

## The hook

In 2017, a paper published in Nature claimed that a quantum algorithm could speed up machine learning exponentially — slashing the time to solve certain linear algebra problems from O(N) to O(log N). The result was mathematically correct.

Within two years, a classical algorithm was found that achieved nearly the same speedup — without quantum hardware. The quantum "advantage" had been dequantized.

This wasn't a scandal. It was science working correctly. But it illustrates a recurring pattern in quantum machine learning: claimed speedups that look impressive until you examine the fine print — the data access assumptions, the comparison baseline, or the hidden classical workarounds.

Quantum machine learning (QML) is a real research area. But as of 2026, there is no demonstrated quantum advantage for any practically relevant machine learning task. Understanding why requires applying everything you've learned about what quantum speedups actually require.

---

## Building the intuition

### What QML is

Quantum machine learning broadly covers two kinds of work:

1. **Quantum algorithms for classical ML tasks:** Using quantum computers to speed up operations that appear in classical machine learning — linear algebra (matrix multiplication, SVD), sampling, optimization.
2. **Classical ML applied to quantum systems:** Using classical machine learning to help understand, control, or error-correct quantum computers. (This second direction is less hyped but more immediately useful — and outside our scope today.)

Today's focus is on category 1: can quantum computers speed up ML?

### The three obstacles

**Obstacle 1: The data loading problem (QRAM)**

Most proposed quantum ML speedups work on a dataset encoded in a quantum state — a superposition representing all N data points simultaneously. The claim: quantum operations on this superposition run in O(log N) instead of O(N).

The catch: to load N classical data points into a quantum superposition, you need a Quantum Random Access Memory (QRAM) — a hardware device that creates the superposition in O(log N) time. QRAM doesn't exist in practical form. Building it requires O(N) hardware components — roughly as much as storing the data classically. The speedup in the quantum algorithm is canceled by the overhead of loading the data.

If you already have a classical dataset (which you do, in all real ML problems), the data-loading step is a bottleneck that quantum algorithms generally fail to address.

**Obstacle 2: Dequantization**

Ewin Tang (as a 2018 undergraduate) showed that the quantum linear algebra speedups (HHL algorithm, quantum recommendation systems) could be "dequantized" — matched by classical algorithms using classical sampling techniques. The key insight: if your data has a low-rank structure that quantum computers exploit, classical algorithms can sample from the data's structure efficiently too.

This doesn't mean all QML speedups will be dequantized. But it established that the mere existence of a quantum speedup claim requires scrutiny — what structure is being exploited, and can a clever classical algorithm exploit it too?

**Obstacle 3: End-to-end analysis**

Even genuine quantum speedups on subroutines don't always translate to end-to-end ML speedup. If the quantum subroutine is one step in a pipeline where classical steps dominate, the quantum speedup has no practical effect. This is Amdahl's law applied to quantum computing.

### What might actually have a quantum speedup in ML

The honest 2026 picture:

| QML claim | Status |
|---|---|
| Quantum support vector machines | Dequantized for important cases (Tang 2021) |
| Quantum principal component analysis | Dequantized (Tang 2018) |
| Quantum recommendation systems | Dequantized (Tang 2018) |
| Quantum neural networks | No known advantage; often worse than classical |
| Quantum Boltzmann machines | Possible speedup for sampling; not demonstrated |
| Optimization (QAOA) | Small problems only; classical often competitive |
| Quantum kernel methods | Possible exponential advantage for specific structured kernels; no practical demonstration |

The most credible remaining candidates for quantum ML advantage:
- **Quantum kernel methods** for classification problems whose kernel function has a classically hard-to-compute structure — but this requires the problem to naturally exhibit quantum structure, which most practical ML problems don't.
- **Quantum generative models** for tasks whose natural output is a quantum probability distribution (relevant for quantum chemistry, not image generation).

### The useful filter — three questions for QML claims

When you see a QML speedup claim, apply these:

1. **Is the speedup over the best known classical algorithm or a naive baseline?** Many claims compare to O(N) when the best classical method is O(N log N) or better.
2. **Does the speedup survive end-to-end analysis including data loading?** QRAM is often assumed without counting its cost.
3. **Has the speedup been demonstrated on fault-tolerant hardware?** NISQ demonstrations of QML are typically dominated by hardware noise — they don't demonstrate the quantum part working, only the circuit running.

---

## The formal picture

**The HHL algorithm (Harrow, Hassidim, Lloyd, 2009):** A quantum algorithm for solving linear systems Ax = b in O(log N) time — vs. classical O(N) for dense matrices. Spawned enormous excitement about quantum ML (linear algebra is the backbone of ML).

Limitations that largely negate the speedup:
- Requires QRAM to load the data (O(N) hardware).
- Only outputs a quantum state proportional to x — extracting all of x classically requires O(N) measurements, losing the speedup.
- Works best when A has low condition number — classical iterative methods (conjugate gradient) also work well in this regime.
- Subject to dequantization for low-rank matrices.

The HHL algorithm is theoretically interesting but practically neutered by these constraints. It's a useful illustration of how quantum speedups can look dramatic in isolation and modest end-to-end.

**Quantum kernel methods** (Havlíček et al., 2019): Use a quantum feature map to compute a kernel function between data points that is classically intractable. If the kernel captures something about the data's structure that's classically hard to compute, a quantum SVM using this kernel can outperform classical SVMs.

The open question: for what practical datasets does such a quantum-hard kernel provide a useful classification advantage? No convincing practical example has been demonstrated.

---

## Where it breaks / what it is not

**"QML will make AI exponentially more powerful."**
No demonstrated path to this. AI's bottlenecks (data, compute for training, inference at scale) are largely in classical matrix multiplication and gradient descent — neither of which has known quantum speedup that survives end-to-end analysis.

**"Google/IBM is investing in QML, so it must work."**
Investment reflects speculative potential, not demonstrated advantage. The field is exploring the possibility of QML speedups — most researchers are honest that none have been proven useful yet.

**"Dequantization means QML is dead."**
No — dequantization of specific algorithms (like quantum recommendation systems) simply means those specific problems don't require quantum hardware. QML is a broad area; dequantization of some claims is scientifically healthy. The remaining open questions are genuine.

---

## Try it yourself

**1. Retrieval — close the page.** Write down in one sentence: what is the data loading problem in quantum ML, and which specific proposed quantum speedup did dequantization undermine? Open only after writing your answer.

<details>
<summary>Answer</summary>
The data loading problem: loading N classical data points into a quantum superposition requires O(N) QRAM hardware, which cancels the O(log N) speedup in the quantum algorithm. Dequantization (Ewin Tang, 2018) showed that quantum recommendation systems and quantum PCA — which claimed exponential speedup over classical linear algebra — could be matched by classical algorithms using quantum-inspired sampling, because the same low-rank structure the quantum algorithms exploit is accessible classically.
</details>

**2. Check understanding.**
What is the "data loading problem" in quantum machine learning, and why does it threaten most proposed quantum ML speedups?

<details>
<summary>Answer</summary>
Most quantum ML speedups work on data encoded in a quantum superposition (all N data points in one state). Achieving this efficiently requires QRAM — hardware that can load the superposition in O(log N) time. But building QRAM requires O(N) physical components (one for each data point). So while the quantum algorithm runs in O(log N) once the data is loaded, the loading step costs O(N) in hardware. The end-to-end advantage is eliminated or severely reduced. Since real ML problems have classical data (not pre-existing quantum states), data loading is always a bottleneck.
</details>

**3. Apply.**
A company claims their quantum neural network (QNN) achieves 95% accuracy on image classification, matching a classical neural network. They say this proves "quantum advantage." Evaluate this claim.

<details>
<summary>Answer</summary>
This is not quantum advantage. Matching classical accuracy is not better than classical. Quantum advantage requires the quantum approach to be faster, cheaper, or more accurate than the best classical method — not merely equivalent. Additionally: (1) What classical baseline was used? A small classical network or a state-of-the-art ResNet? (2) Was this on a NISQ device, dominated by noise? (3) What is the QNN's circuit depth and qubit count vs. the classical network's parameter count and training cost? Matching accuracy on a small benchmark with undisclosed comparison methodology is not a meaningful result.
</details>

**4. Stretch.**
Ewin Tang's dequantization result showed that if quantum algorithms exploit "low-rank structure" in data, classical algorithms using "quantum-inspired" sampling can exploit the same structure. What does this imply about the kinds of ML problems that might genuinely require quantum hardware?

<details>
<summary>Answer</summary>
Tang's result implies that any speedup based solely on efficient sampling from low-rank classical data is achievable classically. For a genuine quantum ML advantage, the problem likely needs to:
(1) Involve data that is inherently quantum in origin (e.g., quantum states from a quantum experiment — no classical loading bottleneck).
(2) Exploit a kernel or feature map that is provably classically hard to compute (not just that it's convenient to compute quantumly).
(3) Require correlations or symmetries that classical algorithms cannot efficiently approximate even approximately.
The most credible candidates are classification problems whose underlying structure reflects quantum phenomena — perhaps in quantum chemistry or quantum materials — not general computer vision, NLP, or tabular data ML.
</details>

---

**Transfer — apply it (all levels):** Your organization is evaluating a QML vendor's claim. Apply today's three questions: (1) Is the claimed speedup over the best classical algorithm or a naive baseline? (2) Does the speedup survive end-to-end analysis including data loading? (3) Has it been demonstrated on fault-tolerant hardware? Write one sentence per question, applied to a real or plausible QML claim you've encountered.

---

## Connect it back

QML is the most hyped — and most contested — application of quantum computing. The honest answer is that no genuine quantum ML advantage has been demonstrated for any practically relevant task, and the theoretical foundations of several popular approaches have been seriously challenged. This doesn't mean QML is worthless, but it demands the skepticism you've now earned.

Tomorrow (Day 22) synthesizes everything into a coherent picture of where the field stands and what to watch in the coming decade.

**The question you should now be able to answer:** What is "dequantization," and why does it matter for evaluating quantum machine learning claims?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Preskill, "Quantum Computing in the NISQ Era and Beyond," Section 4.2 ("Machine learning"), arXiv:1801.00862. Preskill's sober 2018 assessment of what QML might and might not deliver — still the clearest honest treatment.

**If you want the deep version:**
- Aaronson, *Quantum Computing Since Democritus* lecture notes, Lecture 10, final section on "Quantum speedups for AI" (scottaaronson.com/democritus/lec10.html). Aaronson's 2021 blog post "The Limits of Quantum Speedup" (search scottaaronson.blog) is an outstanding calibration exercise.
- Ewin Tang, "A Quantum-Inspired Classical Algorithm for Recommendation Systems," *STOC 2019*, arXiv:1807.04271. The dequantization paper itself — the introduction is accessible and explains the key insight without the full mathematical proof.

---

## Navigation

← **Previous:** [Day 20 — Quantum Simulation — The Original Killer App](./day-20-quantum-simulation.md)
→ **Next:** [Day 22 — The Road Ahead — Timelines, NISQ, and Fault Tolerance](./day-22-road-ahead.md)
