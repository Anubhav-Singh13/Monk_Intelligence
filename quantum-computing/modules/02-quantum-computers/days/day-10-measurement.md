# Day 10 — Measurement — The Act of Looking

> **Today's one idea:** Measurement is the irreversible act that collapses a quantum superposition into a single classical outcome — probabilistically, permanently, and in a way that cannot be undone or cheated.
> **Reading time:** ~35 min · **Prereqs:** Days 3, 9
> **Primary source for today:** Terry Rudolph, *Q is for Quantum*, Chapter 3 (Terian Books, 2017)
> **Before you start:** Recall Day 9's load-bearing idea — one sentence, no looking. What is circuit depth, and why does it constrain real quantum hardware more than total gate count?

---

## The hook

Imagine you've spent days carefully arranging a house of cards — 10 floors, precisely balanced. Then someone walks in and asks: "Is it stable?" The only way to answer is to blow on it. If it stays up, it was stable. If it collapses, you've learned something — but the experiment is over, and the house of cards is gone.

Measuring a quantum state is exactly like this. The superposition — which might encode the answer to a complex computation — collapses the moment you look. You get one classical bit of information (0 or 1). The richly structured quantum state is gone.

This is why quantum computing is such a delicate art: you must engineer the *entire computation* so that the answer is already sitting in a high-amplitude state *before* you measure. The measurement itself does nothing clever — it just reads whatever the circuit has prepared.

---

## Building the intuition

### What measurement does

Suppose a qubit is in state |ψ⟩ = α|0⟩ + β|1⟩. Measuring it:

1. Produces outcome **0** with probability |α|²
2. Produces outcome **1** with probability |β|²
3. **Collapses** the qubit to |0⟩ or |1⟩ respectively — the superposition is destroyed

There is no "reading the amplitude." You get a single classical bit. You cannot recover α or β from the result.

If you want to know α and β with high precision, you'd need to prepare the *same* state thousands of times and measure each copy — a process called quantum state tomography. Even then, you're reconstructing the state statistically, not reading it directly.

### Why you run quantum circuits many times

On real quantum hardware, you don't run a circuit once. You run it hundreds or thousands of times ("shots"), because:

1. **Noise:** Real qubits have gate errors. Running many times lets you identify the most frequent outcome (which should be the correct answer if the algorithm worked).
2. **Probability:** Even a perfect circuit gives a probabilistic outcome for some algorithms. Grover's algorithm gives the right answer with high probability — but "high" means 99.9% after enough iterations, not 100%.
3. **Statistics:** You often need the full distribution of outcomes to understand what happened.

A typical quantum experiment runs 1,000–10,000 shots and reports the histogram of results.

### The non-cloning theorem revisited

You cannot copy a quantum state (Day 3). This connects directly to measurement: if you could clone a qubit before measuring it, you could clone it thousands of times, measure each clone, and reconstruct the amplitudes precisely. Measurement would become non-destructive. But cloning is forbidden by quantum mechanics — so measurement is always destructive.

### Basis — what you're actually measuring

"Measurement" always happens relative to a *basis* — a chosen set of states you're checking against. The default is the **computational basis** {|0⟩, |1⟩} — you're asking "is the qubit |0⟩ or |1⟩?"

But you can also measure in the **Hadamard basis** {|+⟩, |−⟩} — asking "is the qubit |+⟩ or |−⟩?" This requires applying a Hadamard gate before measuring in the computational basis.

The choice of measurement basis changes what information you extract. This is not trivial — quantum cryptography (Day 19) exploits this fact: an eavesdropper who measures in the wrong basis gets random noise, and their intrusion is detectable.

### Partial measurement — measuring only some qubits

In a multi-qubit system, you can measure just one qubit. This collapses that qubit's state but leaves the others in a new (possibly entangled) state.

Example: Bell state |Φ+⟩ = (1/√2)(|00⟩ + |11⟩).
Measure only the first qubit:
- Outcome 0 (probability 50%): second qubit collapses to |0⟩
- Outcome 1 (probability 50%): second qubit collapses to |1⟩

The second qubit is now in a definite state — but which one depends on the measurement outcome of the first.

---

## The formal picture

**The Born rule** (Max Born, 1926): The probability of outcome x when measuring quantum state |ψ⟩ is:

```math
P(x) = |\langle x | \psi \rangle|^2
```

Where ⟨x|ψ⟩ is the inner product of the measurement basis state |x⟩ with the state |ψ⟩. For computational basis measurement of |ψ⟩ = α|0⟩ + β|1⟩:
- P(0) = |⟨0|ψ⟩|² = |α|²
- P(1) = |⟨1|ψ⟩|² = |β|²

**Post-measurement state (collapse):** After measuring and obtaining outcome x, the state becomes |x⟩. The pre-measurement state is irretrievably gone.

**Projective measurement:** Each measurement outcome corresponds to a projector Pₓ = |x⟩⟨x|. Measurement "projects" the state onto the subspace of the measured outcome. In 2+ dimensions, partial measurements project onto subspaces and renormalize the remaining state.

**Deferred measurement principle:** Any mid-circuit measurement can be moved to the end of the circuit without changing the distribution of final measurement outcomes. This is a useful proof technique — it means you can always reason about a circuit as if all measurements happen at the very end.

---

## Where it breaks / what it is not

**"If I measure quickly enough, I can catch the qubit before it fully collapses."**
No. Collapse is instantaneous (in the non-relativistic quantum mechanics used in computing). There's no "partially measured" state — the measurement either happens or it doesn't.

**"Measuring a qubit in a superposition gives me information about both outcomes."**
No. You get exactly one classical bit of information: 0 or 1. The amplitudes α and β are not accessible from a single measurement. This is the fundamental information bottleneck of quantum computing.

**"Quantum computers output a distribution, not an answer."**
Partially true. Some quantum algorithms (like quantum sampling algorithms) are designed to output a probability distribution, and the output is genuinely a histogram of outcomes. But the landmark algorithms — Grover, Shor, Deutsch — are designed so that the correct answer appears with overwhelming probability, making the output effectively deterministic.

**"More shots always gives more information."**
More shots reduce statistical uncertainty, but you cannot extract information that isn't encoded in the amplitudes. If the algorithm failed to amplify the right answer (interference step failed), more shots just give you more random noise.

---

## Try it yourself

**1. Retrieval — close the page.** Write down in one sentence: what happens when you measure a qubit in superposition — and what information can you recover from a single measurement? Open only after writing your answer.

<details>
<summary>Answer</summary>
Measurement collapses the superposition irreversibly: the qubit becomes |0⟩ or |1⟩ with probabilities |α|² and |β|² respectively, and the original state is gone. From a single measurement you recover exactly one classical bit (0 or 1) — you cannot recover α or β. Reconstructing the amplitudes requires preparing the same state thousands of times and measuring each copy.
</details>

**2. Check understanding.**
A qubit is in state |ψ⟩ = (√3/2)|0⟩ + (1/2)|1⟩. What is the probability of measuring 0? Of measuring 1? What is the state after measuring 0?

<details>
<summary>Answer</summary>
P(0) = |√3/2|² = 3/4 = 75%.
P(1) = |1/2|² = 1/4 = 25%.
After measuring 0: state collapses to |0⟩. The |1⟩ component is gone.
Check: 3/4 + 1/4 = 1. ✓
</details>

**3. Apply.**
You run a quantum circuit 1,000 times. The results are: 743 times you measure 0, 257 times you measure 1. Estimate the amplitude α of the |0⟩ component in the output state.

<details>
<summary>Answer</summary>
P(0) ≈ 743/1000 = 0.743. Since P(0) = |α|², α ≈ √0.743 ≈ 0.862.
(This assumes the circuit is deterministic and noise-free — in practice, measurement errors would blur this estimate.)
</details>

**4. Stretch.**
The deferred measurement principle says you can move all measurements to the end of a circuit without changing the outcome distribution. Why is this principle useful when thinking about quantum error correction?

<details>
<summary>Answer</summary>
Quantum error correction requires mid-circuit measurements to detect errors (syndrome measurements) without collapsing the logical qubit. At first, this seems to violate the rule that measurement destroys superposition. The deferred measurement principle clarifies the math: you can reason about these mid-circuit measurements as if they happen at the end, which makes it easier to prove that error detection doesn't disturb the encoded information. In practice, the measurements do happen mid-circuit — but the principle confirms they can be designed not to collapse the logical qubit's state.
</details>

---

**Transfer — apply it (all levels):** In your field, what is an irreversible observation — one where the act of measuring changes or destroys the thing being measured? (Examples: a destructive materials test, a load test that crashes the server, a survey that primes the respondent.) Write one sentence connecting it to quantum measurement collapse, and one sentence on where the analogy breaks down.

---

## Connect it back

Now the full quantum circuit model is complete: you prepare qubits (Day 7), apply gates (Day 8) in a circuit (Day 9), and measure at the end (today). The measurement is where quantum information becomes classical information — a single irreversible read-out that captures whatever the circuit has engineered.

Tomorrow (Day 11), we see this complete model used for the first time in a real algorithm: Deutsch's problem. The whole machine — superposition, gates, interference, measurement — works together to solve a problem in one query that classically requires two.

**The question you should now be able to answer:** Why do quantum computers need to run many "shots" of the same circuit, rather than reading all the information from a single run?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Rudolph, *Q is for Quantum*, Chapter 3 (Terian Books, 2017). Rudolph's visual treatment of measurement — especially the idea that "looking" forces a choice — is the most intuitive available. No equations required.

**If you want the deep version:**
- Rieffel & Polak, *Quantum Computing: A Gentle Introduction*, Chapter 2, Section 2.2 ("Measurement") and Chapter 3, Section 3.3 (MIT Press, 2011). Pages 30–38 and 65–72. The most careful mathematical treatment of the Born rule, partial measurement, and the deferred measurement principle at this level.
- Vazirani, *Quantum Mechanics and Quantum Computation* (CS 191x), Week 2, Lecture 3. The section on "Why measurement is destructive" uses concrete numerical examples that make the probability mechanics visceral.

---

## Navigation

← **Previous:** [Day 9 — Quantum Circuits — Wiring Gates Together](./day-09-quantum-circuits.md)
→ **Next:** [Day 11 — Deutsch's Problem — The First Quantum Speedup](./day-11-deutsch-problem.md)
