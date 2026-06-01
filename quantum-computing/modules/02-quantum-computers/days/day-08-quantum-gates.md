# Day 8 — Quantum Gates — Operations on Qubits

> **Today's one idea:** Quantum gates are reversible operations that rotate a qubit's state on the Bloch sphere — they are the quantum equivalent of logic gates, but with one crucial difference: every quantum gate is undoable.
> **Reading time:** ~40 min · **Prereqs:** Days 5, 7
> **Primary source for today:** Rieffel & Polak, *Quantum Computing: A Gentle Introduction*, Chapter 4, Sections 4.1–4.3 (MIT Press, 2011)

---

## The hook

In a classical computer, a NOT gate flips a bit: 0 → 1, 1 → 0. An AND gate takes two bits and outputs 1 only if both inputs are 1. These gates process information one way — you can't run them backwards and recover the inputs from the output. Feed two different inputs into an AND gate, get 0 both times, and you've lost information permanently.

Quantum gates are fundamentally different in one way: **every quantum gate is reversible.** Given the output, you can always recover the input. No information is ever destroyed.

This isn't a design choice — it's a law of physics. Quantum mechanics requires that the evolution of a quantum state be *unitary* (reversible). Any operation that irreversibly destroys information is, by definition, a measurement — and measurements behave very differently from gates.

This reversibility constraint shapes everything about how quantum circuits are built.

---

## Building the intuition

### Gates as rotations on the Bloch sphere

Recall from Day 3: a single qubit's state is a point on the surface of a globe (the Bloch sphere). North pole = |0⟩, south pole = |1⟩, equator = equal superpositions.

A quantum gate is a *rotation* of that globe. Apply a gate, and the qubit's point moves to a new location on the sphere. Apply it again, and it moves again.

This is exact, not metaphorical. Every single-qubit quantum gate corresponds to a specific rotation of the Bloch sphere — by a specific angle, around a specific axis.

```
Before gate:             After NOT gate (X gate):
    |0⟩ (north)              |1⟩ (south)
      ●                          
      │                       │
      │           →            │
      │                        ●
    |1⟩ (south)             |0⟩ (north)

The X gate flips the qubit: 180° rotation around the X-axis.
```

### The four most important gates

**1. The X gate (Pauli-X / "quantum NOT")**

Flips |0⟩ to |1⟩ and |1⟩ to |0⟩. On the Bloch sphere: 180° rotation around the X-axis. Classical analog: the NOT gate.

**2. The Hadamard gate (H)**

Puts a qubit in equal superposition. |0⟩ → |+⟩ = (1/√2)(|0⟩ + |1⟩). |1⟩ → |−⟩ = (1/√2)(|0⟩ − |1⟩). On the Bloch sphere: 180° rotation around the diagonal axis between X and Z.

The Hadamard is the most important gate in quantum computing — it's the "on-ramp" from a definite state into superposition. Almost every quantum algorithm starts by applying Hadamard to all qubits.

```
Hadamard applied to |0⟩:
                         1
|0⟩  ──[H]──  ─────── (|0⟩ + |1⟩)
                        √2

Read as: "measuring now gives 0 or 1 with 50/50 probability."
```

**3. The Z gate (Pauli-Z / "phase flip")**

Leaves |0⟩ unchanged. Flips the sign (phase) of |1⟩: |1⟩ → −|1⟩. Does NOT change measurement probabilities — measuring Z(|+⟩) still gives 50/50. But it changes the state in a way that affects future interference.

This is the gate that makes signs matter. The difference between |+⟩ and |−⟩ is exactly a Z gate.

**4. The CNOT gate (Controlled-NOT)**

A two-qubit gate. It has a "control" qubit and a "target" qubit. If the control is |0⟩, the target is unchanged. If the control is |1⟩, the target is flipped (like an X gate).

| Control | Target in | Target out |
|---------|-----------|------------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

The CNOT gate is crucial for creating entanglement. Apply it to a superposition:

```
Start: (1/√2)(|0⟩ + |1⟩) ⊗ |0⟩ = (1/√2)(|00⟩ + |10⟩)
Control = first qubit, Target = second qubit.

CNOT: flip target only when control is |1⟩:
  |00⟩ → |00⟩  (control is 0, target unchanged)
  |10⟩ → |11⟩  (control is 1, target flipped)

Result: (1/√2)(|00⟩ + |11⟩) = Bell state |Φ+⟩
```

The CNOT gate, combined with Hadamard, creates entanglement from nothing. This is how entangled pairs are produced in practice.

### Gate fidelity — the real-world limitation

Every physical gate introduces small errors. The *fidelity* of a gate is the probability that it performs its intended operation correctly.

- State-of-the-art superconducting single-qubit gate fidelity: ~99.9%
- State-of-the-art two-qubit gate fidelity: ~99.5%
- Trapped-ion two-qubit gate fidelity: ~99.9%

A 99.5% fidelity sounds high. But run 1000 gates, and the probability that all of them succeeded is 0.995^1000 ≈ 0.7% — almost certain to have errors. This is why quantum error correction (Day 16) is essential.

---

## The formal picture

A quantum gate is a **unitary matrix** U acting on the Hilbert space of n qubits.

Unitary means: U†U = I (the dagger denotes conjugate transpose). This is the mathematical statement that the gate is reversible — its inverse is its conjugate transpose. Apply U, then U†, and you're back where you started.

**Key single-qubit gates as matrices** (reading each: the columns tell you where |0⟩ and |1⟩ go):

```
X (NOT):   [0  1]    Z (phase):  [1   0]    H (Hadamard):  [1   1] × 1/√2
           [1  0]                [0  -1]                    [1  -1]
```

**How to apply:** Multiply the gate matrix by the state vector. For |0⟩ = [1, 0] and |1⟩ = [0, 1]:

H|0⟩ = (1/√2)[1, 1; 1, -1] × [1, 0] = (1/√2)[1, 1] = (1/√2)|0⟩ + (1/√2)|1⟩ = |+⟩ ✓

**Universal gate sets:** Just as any classical computation can be built from NAND gates alone, quantum computation can be built from a small set of gates. A common universal set is {H, CNOT, T-gate}. Any quantum algorithm can be decomposed into these.

---

## Where it breaks / what it is not

**"Quantum gates are just quantum logic gates — same idea as classical."**
Structurally similar, but with two key differences: (1) quantum gates operate on amplitudes, not just 0/1 values, so they can create and manipulate superpositions; (2) all quantum gates must be reversible, while most classical gates (AND, OR, NAND) are irreversible.

**"The Hadamard gate makes a qubit 'random.'"**
No — "random" implies you don't know the outcome. A qubit after Hadamard is in a precise, deterministic superposition state. The randomness only appears when you measure. The state itself is fully known.

**"You can measure the qubit at any point during the circuit."**
You can, but it collapses the superposition — destroying any amplitude not yet converted to a definite answer. Measurement is final. This is why in almost all quantum circuits, measurement happens only at the very end.

---

## Try it yourself

**1. Check understanding.**
Apply the Hadamard gate twice to |0⟩. What do you get?

<details>
<summary>Hint</summary>
H|0⟩ = |+⟩ = (1/√2)|0⟩ + (1/√2)|1⟩. Now apply H again to each component.
</details>

<details>
<summary>Answer</summary>
H|+⟩ = H[(1/√2)|0⟩ + (1/√2)|1⟩] = (1/√2)H|0⟩ + (1/√2)H|1⟩
= (1/√2)|+⟩ + (1/√2)|−⟩
= (1/√2)(1/√2)(|0⟩ + |1⟩) + (1/√2)(1/√2)(|0⟩ − |1⟩)
= (1/2)(|0⟩ + |1⟩ + |0⟩ − |1⟩) = |0⟩.
HH = I (Hadamard is its own inverse). You're back to |0⟩.
</details>

**2. Apply.**
Starting from |0⟩|0⟩ (two qubits, both in state 0), create the Bell state |Φ+⟩ = (1/√2)(|00⟩ + |11⟩). What two gates do you apply, in what order, to which qubits?

<details>
<summary>Answer</summary>
Step 1: Apply H to the first qubit → (1/√2)(|0⟩ + |1⟩)|0⟩ = (1/√2)(|00⟩ + |10⟩).
Step 2: Apply CNOT with first qubit as control, second as target:
  |00⟩ → |00⟩, |10⟩ → |11⟩.
Result: (1/√2)(|00⟩ + |11⟩) = |Φ+⟩. ✓
</details>

**3. Stretch.**
Why must quantum gates be reversible (unitary) while classical gates don't have to be? What physical law enforces this?

<details>
<summary>Answer</summary>
In quantum mechanics, physical evolution is governed by the Schrödinger equation, which is linear and time-reversible. Any allowed evolution of a quantum state must be expressible as a unitary operation — one that preserves the total probability (|α|² + |β|² = 1) and is reversible. An irreversible operation would mean information was destroyed, which violates unitarity. The only irreversible thing in quantum mechanics is measurement — and that's not a gate; it's a different kind of operation entirely (collapse).
</details>

---

## Connect it back

Yesterday qubits became physical objects. Today they became *manipulable* — through gates that rotate their states in controlled, reversible ways. Tomorrow (Day 9) we wire gates together into circuits, giving us the full language needed to describe quantum algorithms. Day 10 completes the picture by explaining what happens when the circuit ends and we measure.

**The question you should now be able to answer:** What is the Hadamard gate, and why is it the most important gate in quantum computing?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Rieffel & Polak, *Quantum Computing: A Gentle Introduction*, Chapter 4, Sections 4.1–4.2 (MIT Press, 2011). Pages 79–105. The clearest formal treatment of single-qubit gates, Bloch sphere rotations, and the Hadamard gate.

**If you want the deep version:**
- Andrew Helwer, "Quantum Computing for Computer Scientists," Microsoft Research, 2018. Watch 12:00–28:00 on YouTube (search "Microsoft Research Quantum Computing Computer Scientists Helwer"). Helwer's visual treatment of the Bloch sphere and gates is outstanding — especially the Hadamard animation.
- IBM Quantum Learning (quantum.ibm.com/learning): the Circuit Composer lets you drag gates onto qubits and see their matrix representation side-by-side. 20 minutes of hands-on use here is worth an hour of reading.

---

## Navigation

← **Previous:** [Day 7 — Qubits in the Real World](./day-07-qubits-real-world.md)
→ **Next:** [Day 9 — Quantum Circuits — Wiring Gates Together](./day-09-quantum-circuits.md)
