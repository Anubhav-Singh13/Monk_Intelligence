# Day 9 — Quantum Circuits — Wiring Gates Together

> **Today's one idea:** A quantum circuit is a visual recipe for a quantum computation — horizontal lines are qubits, boxes are gates applied left-to-right, and the whole thing terminates with a measurement that extracts a classical result.
> **Reading time:** ~35 min · **Prereqs:** Day 8
> **Primary source for today:** Rieffel & Polak, *Quantum Computing: A Gentle Introduction*, Chapter 4, Sections 4.4–4.6 (MIT Press, 2011)
> **Before you start:** Recall Day 8's load-bearing idea — one sentence, no looking. What makes a quantum gate fundamentally different from a classical logic gate — name the key distinction?

---

## The hook

Before you bake a cake, you write a recipe: gather ingredients in this order, mix them this way, bake at this temperature for this long. The recipe is a precise, reproducible sequence of operations.

A quantum circuit is a recipe for a quantum computation. It tells you:
- How many qubits to start with and in what state
- Which gate to apply to which qubit at each step
- When and how to measure

Without this notation, describing a quantum algorithm would require pages of mathematical formulas. With it, you can communicate an entire computation in a single diagram — and the diagram can be translated directly into hardware instructions.

The circuit model is the language of quantum computing. Everything else — gates, superposition, interference — is vocabulary. The circuit is the sentence.

---

## Building the intuition

### Reading a quantum circuit

A quantum circuit reads left to right, like a score of music. Each horizontal line is a qubit (like a musical staff). Each box on a line is a gate applied to that qubit (like a note). Time flows left to right.

Here is the simplest meaningful circuit: creating a Bell state (entangled pair) from two qubits starting in |0⟩|0⟩.

```
q0: ──[H]──●──────  → measure
            │
q1: ────────⊕──────  → measure

Legend:
  [H]  = Hadamard gate
  ●    = control qubit of CNOT
  ⊕    = target qubit of CNOT
```

Read this circuit:
1. Start: both qubits are |0⟩.
2. Apply Hadamard to q0 → q0 becomes (1/√2)(|0⟩ + |1⟩). q1 is still |0⟩.
3. Apply CNOT with q0 as control, q1 as target → creates entanglement.
4. Measure both qubits → always get either (0,0) or (1,1), with 50/50 probability.

Four lines of diagram. One entangled pair. This is what makes circuits invaluable.

### Circuit structure — the anatomy

```
        Gates              Measurement
           │                    │
q0: |0⟩──[H]──●────────────────[M]──→  0 or 1
               │
q1: |0⟩───────⊕────────────────[M]──→  0 or 1
        ↑               ↑
     qubit wire      barrier: no
     (time flows    classical info
      left to right) crosses here
```

**Key elements:**
- **Qubit wire:** horizontal line, represents one qubit through time
- **Gate box:** square with a letter or symbol, applied at that point in time
- **Control dot (●):** the "if" of a controlled gate — activates the gate only when the control qubit is |1⟩
- **Target (⊕ or a box):** the qubit being operated on
- **Measurement (M or a meter symbol):** collapses the qubit to a classical bit (0 or 1)
- **Classical wire (double line):** carries the classical output after measurement

### A slightly larger example: 3-qubit GHZ state

The Greenberger-Horne-Zeilinger (GHZ) state is the 3-qubit generalization of a Bell state — all three qubits maximally entangled:

|GHZ⟩ = (1/√2)(|000⟩ + |111⟩)

Circuit to create it:

```
q0: |0⟩──[H]──●──────●──────────[M]
               │      │
q1: |0⟩───────⊕──────│──────────[M]
                      │
q2: |0⟩──────────────⊕──────────[M]
```

Steps:
1. H on q0 → superposition
2. CNOT (q0→q1) → entangle q0 and q1
3. CNOT (q0→q2) → entangle q0 and q2
4. Measure all three → always get (0,0,0) or (1,1,1)

### Circuit depth and width

- **Circuit width:** number of qubits (vertical dimension)
- **Circuit depth:** number of gate layers from left to right (horizontal dimension)

Depth matters because each gate layer takes time (and introduces noise). A deeper circuit is harder to run reliably on real hardware. Quantum algorithm designers often work hard to reduce depth without changing the computation's outcome.

Gates on *different qubits* at the same time can be parallelized — they run simultaneously, reducing depth.

---

## The formal picture

A quantum circuit implements a sequence of unitary operations U₁, U₂, ..., Uₖ on a starting state |ψ₀⟩ = |0...0⟩:

```math
|\psi_f\rangle = U_k \cdots U_2 U_1 |0 \cdots 0\rangle
```

The final state |ψ_f⟩ is then measured. The measurement probabilities are determined by the amplitudes in |ψ_f⟩.

**Circuit complexity:** The resources needed to run a circuit are characterized by:
- **T (T-gate count or total gate count):** number of individual gate operations
- **D (depth):** number of sequential gate layers (accounting for parallelism)
- **W (width):** number of qubits

For practical hardware, depth is often the binding constraint because it determines total time in the face of decoherence.

**Ancilla qubits:** Many circuits use extra "helper" qubits called ancilla qubits. They start in |0⟩, participate in the computation to store intermediate results, and are returned to |0⟩ at the end (to preserve reversibility). They add to circuit width but can reduce depth.

---

## Where it breaks / what it is not

**"A larger quantum circuit is always a better algorithm."**
No — more gates mean more noise and more time, both of which fight decoherence. A quantum circuit that is unnecessarily deep may fail more often on real hardware than a shallower classical alternative.

**"You can insert a measurement anywhere in the circuit to check intermediate results."**
Technically yes, but it collapses the superposition. Mid-circuit measurements are used in some advanced protocols (like quantum error correction), but in most algorithms, measurement is only at the end — inserting it earlier destroys the computation.

**"The circuit model is the only model of quantum computation."**
No. There are alternatives: measurement-based quantum computing (MBQC) starts with a large entangled state and drives computation entirely through measurements; adiabatic quantum computing evolves a system slowly toward a ground state. IBM, Google, and IonQ all use the circuit model, which is why this course focuses on it.

---

## Try it yourself

**1. Retrieval — close the page.** Write down in one sentence: what is circuit depth, and why does it constrain real quantum hardware more than total gate count? Open only after writing your answer.

<details>
<summary>Answer</summary>
Circuit depth is the number of sequential gate layers — how many rounds of operations must run one after another. It constrains hardware more than gate count because depth determines how long the computation takes, and time is the enemy of coherence. Gates on different qubits can run in parallel (same layer), so depth can be much smaller than total gate count — algorithm designers work to minimize depth specifically.
</details>

**2. Check understanding.**
What does "circuit depth" mean, and why does it matter for real quantum hardware?

<details>
<summary>Answer</summary>
Circuit depth is the number of sequential gate layers in a circuit — how many "rounds" of operations must be executed one after another. It matters because real qubits have limited coherence times: the longer the circuit takes, the more likely decoherence will destroy the quantum state before computation finishes. Deeper circuits = more time = more errors. Algorithm designers try to minimize depth while preserving correctness.
</details>

**3. Apply.**
Draw (in text notation) the circuit for creating a 2-qubit Bell state from |0⟩|0⟩, then describe what measurement outcome you'd expect and what it tells you about entanglement.

<details>
<summary>Answer</summary>
Circuit:
```
q0: |0⟩──[H]──●──[M]
               │
q1: |0⟩───────⊕──[M]
```
Expected measurement: 50% chance of (0,0), 50% chance of (1,1), 0% chance of (0,1) or (1,0).
This tells you the qubits are entangled: their outcomes are perfectly correlated, but which outcome occurs is random. The correlation cannot be explained by them independently choosing the same classical value.
</details>

**4. Stretch.**
A circuit has 5 qubits and 3 layers of gates. In layer 1: H is applied to all 5 qubits. In layer 2: CNOT between q0-q1, q2-q3 (applied simultaneously). In layer 3: CNOT between q1-q2, q3-q4 (applied simultaneously). What is the circuit's width? What is its depth? How many total gate operations are there?

<details>
<summary>Answer</summary>
Width: 5 qubits.
Depth: 3 layers (even though layer 1 has 5 parallel operations, they happen simultaneously in one layer).
Total gate count: Layer 1: 5 gates (H × 5). Layer 2: 2 CNOT gates. Layer 3: 2 CNOT gates. Total: 9 gates.
The depth is 3, not 9, because the H gates in layer 1 all run in parallel.
</details>

---

**Transfer — apply it (all levels):** Think of a workflow you execute regularly — a data pipeline, a deployment process, a test suite. Does it have a notion of "depth" (operations that must happen sequentially) vs. "width" (operations that can run in parallel)? Write one sentence connecting it to circuit depth and width, and one sentence on what the "coherence constraint" equivalent would be in your workflow.

---

## Connect it back

Gates (Day 8) are the words. Circuits (today) are the sentences. Tomorrow (Day 10), we study the punctuation mark that ends every circuit: measurement — the act of looking that collapses the quantum state into a classical result. After that, Days 11–13 give you three complete sentences: the first quantum algorithms.

**The question you should now be able to answer:** Why does circuit depth matter more than circuit gate count when running on real hardware?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Rieffel & Polak, *Quantum Computing: A Gentle Introduction*, Chapter 4, Sections 4.4–4.5 (MIT Press, 2011). Pages 106–120. Circuit notation, controlled gates, and circuit depth — explained with exceptional care.

**If you want the deep version:**
- IBM Quantum Learning (quantum.ibm.com/learning): the Circuit Composer is an interactive drag-and-drop circuit builder. Build the Bell state circuit from exercise 2 and run it on IBM's real quantum hardware or simulator. Seeing your circuit execute — with real measurement results — makes everything click.
- Andrew Helwer, "Quantum Computing for Computer Scientists," Microsoft Research, 2018 (YouTube: 38:00–52:00). Helwer's coverage of quantum circuits and how they map to algorithms is the clearest video treatment available at this level.

---

## Navigation

← **Previous:** [Day 8 — Quantum Gates — Operations on Qubits](./day-08-quantum-gates.md)
→ **Next:** [Day 10 — Measurement — The Act of Looking](./day-10-measurement.md)
