# Day 3 — Superposition — Both and Neither

> **Today's one idea:** Superposition is not "we don't know which state the qubit is in." It is a physical system genuinely in a blend of states — and that blend carries amplitudes that can interfere, which is something ordinary probability can never do.
> **Reading time:** ~40 min · **Prereqs:** Day 2
> **Primary source for today:** Terry Rudolph, *Q is for Quantum*, Chapter 1 (Terian Books, 2017)
> **Before you start:** Recall Day 2's load-bearing idea — one sentence, no looking. What does the double-slit experiment prove about quantum objects — and why does the "it secretly went through one slit" explanation fail?

---

## The hook

Toss a coin into the air. While it's spinning, it isn't heads or tails — it's in some undetermined intermediate state. The moment it lands, it becomes one. You had a 50/50 chance of either outcome.

A qubit sounds like this coin. But there's a crucial difference that makes all the difference.

The coin's 50/50 chance is just ignorance. Before it lands, the physics of gravity and spin have already determined the outcome — you just don't know it yet. If you could measure everything about the coin's trajectory, you could predict exactly which side it would land on.

A qubit in superposition is not like this. The probability is not due to ignorance. There is no hidden trajectory that has already determined the outcome. Before measurement, there genuinely is no fact of the matter about which state it's in. And here's the thing that makes a superposition fundamentally different from a coin flip: the two possibilities don't just have *probabilities* — they have *amplitudes*. And amplitudes can be negative.

That difference — amplitudes instead of probabilities — is the seed of everything quantum computing does.

---

## Building the intuition

### From bits to qubits

A classical bit is a switch: definitely 0 or definitely 1.

A qubit can be in a **superposition**: some amount of |0⟩ *and* some amount of |1⟩ simultaneously. We write it like this:

|ψ⟩ = α|0⟩ + β|1⟩

Read this as: "the qubit is in state ψ (psi), which is a blend of |0⟩ and |1⟩, weighted by α (alpha) and β (beta)."

The symbols |0⟩ and |1⟩ are just labels for the two possible outcomes — they could represent a photon's polarization, an electron's spin direction, or anything else with two states.

### What are α and β?

Here is the critical point. α and β are **amplitudes** — not probabilities. The distinction matters:

| Property | Probability (classical) | Amplitude (quantum) |
|----------|------------------------|---------------------|
| Range | 0 to 1 | Any complex number |
| Can be negative? | No | Yes |
| Can interfere? | No | Yes |
| Converts to probability by | — | squaring: p = \|α\|² |

The probability of measuring |0⟩ is |α|², and the probability of measuring |1⟩ is |β|². These must sum to 1: |α|² + |β|² = 1.

But α and β themselves can be negative (or even complex). This is what allows destructive interference — two amplitudes of opposite sign cancel each other out, driving the probability of an outcome to zero. This cannot happen with ordinary probabilities.

### Concrete example: the equal superposition

The most common superposition in quantum computing is the equal superposition:

|+⟩ = (1/√2)|0⟩ + (1/√2)|1⟩

Here α = β = 1/√2 ≈ 0.707. Check: (1/√2)² + (1/√2)² = 1/2 + 1/2 = 1. ✓

If you measure this state, you get |0⟩ with probability 50% and |1⟩ with probability 50%. So far it looks like a fair coin flip.

Now consider the other equal superposition:

|−⟩ = (1/√2)|0⟩ − (1/√2)|1⟩

Same probabilities — 50/50 — if you measure directly. But this state and the |+⟩ state are *physically different*. The negative sign on β means these two states will interfere differently with other operations. This is invisible to a direct measurement, but becomes decisive in quantum algorithms.

### The Bloch sphere — a picture of all qubit states

Imagine a globe. Every point on its surface represents a valid qubit state.

```
        |0⟩ (north pole)
          │
          │
          ●  ← a qubit state somewhere on the surface
         /│\
        / │ \
       /  │  \
──────────┼──────────── equator (equal superpositions: |+⟩, |−⟩, and others)
       \  │  /
        \ │ /
         \│/
          │
        |1⟩ (south pole)
```

- North pole: |0⟩ (definitely 0)
- South pole: |1⟩ (definitely 1)
- Any point on the equator: an equal superposition (like |+⟩ or |−⟩)
- Any other point on the surface: a weighted superposition

Quantum gates (Day 8) are rotations of this sphere. Measurement collapses the state to either the north or south pole, with probability proportional to how close the point was to each.

The Bloch sphere only works for a *single* qubit. For multiple qubits, the picture becomes much more complex — but the intuition of "rotating a state" carries forward.

---

## The formal picture

The quantum state of a single qubit is a unit vector in a 2-dimensional complex vector space (a Hilbert space):

```math
|\psi\rangle = \alpha|0\rangle + \beta|1\rangle, \quad \alpha, \beta \in \mathbb{C}, \quad |\alpha|^2 + |\beta|^2 = 1
```

- α and β are *amplitudes* (complex numbers).
- |α|² and |β|² are *probabilities* of each outcome.
- Measuring in the computational basis yields outcome 0 with probability |α|² and outcome 1 with probability |β|², and the state collapses to |0⟩ or |1⟩ respectively.

**The no-cloning theorem:** You cannot make a perfect copy of an unknown quantum state. If you could, you could copy a qubit before measuring it, measure the copy, and preserve the original — but this turns out to violate the laws of quantum mechanics. This has profound consequences for quantum cryptography (Day 19).

**Superposition ≠ mixture:** A qubit in superposition (α|0⟩ + β|1⟩) is different from a *mixed state* (a classical probability distribution over |0⟩ and |1⟩). The difference is visible in interference experiments. Superposition can interfere; a mixture cannot.

---

## Where it breaks / what it is not

**"A qubit stores infinite information because α can be any real number."**
This is a common and seductive mistake. Yes, α can be any value in [0, 1]. But you can only *extract* one bit of information from a qubit measurement — it collapses to 0 or 1. The power of superposition is not in storage; it's in the computation you can do *before* measuring.

**"Superposition means the qubit is secretly one or the other and we just don't know."**
No. This is the hidden-variable picture that Bell's theorem ruled out (Day 2). In a superposition, there is no hidden fact about the outcome — until measurement, both possibilities are physically real, as evidenced by interference.

**"Quantum computers try all answers at once by superposing them, then read the right one."**
Partially right, but dangerously incomplete. Yes, a superposition can represent all possible inputs simultaneously. But measuring a uniform superposition gives a *random* answer — no better than guessing. The trick is interference (Day 5): you need to manipulate the amplitudes before measuring so the right answer has high probability. Superposition alone doesn't compute anything.

---

## Try it yourself

**1. Retrieval — close the page.** Write down in one sentence: what is the difference between a qubit amplitude and a classical probability — and why does that difference matter for quantum computing? Open only after writing your answer.

<details>
<summary>Answer</summary>
A classical probability is always a positive number between 0 and 1. A quantum amplitude is a complex number — it can be negative or imaginary — which means two amplitudes can cancel each other out. This cancellation is interference: the mechanism by which quantum algorithms suppress wrong answers and amplify right ones.
</details>

**2. Check understanding.**
If α = 3/5 and β = 4/5 for a qubit |ψ⟩ = α|0⟩ + β|1⟩, what is the probability of measuring |0⟩? What is the probability of measuring |1⟩? Do they sum to 1?

<details>
<summary>Answer</summary>
P(|0⟩) = |α|² = (3/5)² = 9/25 = 36%. P(|1⟩) = |β|² = (4/5)² = 16/25 = 64%. Sum: 9/25 + 16/25 = 25/25 = 1. ✓
</details>

**3. Apply.**
State |+⟩ = (1/√2)|0⟩ + (1/√2)|1⟩ and state |−⟩ = (1/√2)|0⟩ − (1/√2)|1⟩ both give a 50/50 measurement result in the standard basis. Yet they are physically different states. How could you tell them apart experimentally?

<details>
<summary>Hint</summary>
Think about what happens if you apply a Hadamard gate (which puts |0⟩ into |+⟩ and |1⟩ into |−⟩) to each state before measuring.
</details>

<details>
<summary>Answer</summary>
Apply a Hadamard gate (Day 8) before measuring. The Hadamard maps |+⟩ → |0⟩ and |−⟩ → |1⟩. After the gate, measuring gives 0 for |+⟩ and 1 for |−⟩ — perfectly distinguishable. The two states interfere differently with this operation, revealing the sign difference in the amplitude.
</details>

**4. Stretch.**
Why does the no-cloning theorem follow intuitively from superposition? (You don't need to know the formal proof — just reason from what you've learned today.)

<details>
<summary>Answer</summary>
To clone a qubit, you'd need to know its exact amplitudes α and β. But the only way to learn anything about the state is to measure it — and measurement collapses the superposition and gives you only a single classical outcome (0 or 1). You cannot recover α and β from one measurement. Without knowing α and β, you can't faithfully recreate the state. Hence cloning is impossible.
</details>

---

**Transfer — apply it (all levels):** Think of a domain where two signals, forces, or contributions can reinforce or cancel each other — electrical signals, market trends, competing priorities. Write one sentence connecting it to quantum amplitude interference, and one sentence on the key difference: what does quantum interference do that classical cancellation cannot?

---

## Connect it back

Yesterday you saw that quantum objects exist in superposition until observed. Today you gave that concept a precise name and structure: a quantum state with amplitudes, not just probabilities. The key word is *amplitude* — it can be negative, it can cancel, and this turns out to be the weapon quantum computers wield.

On Day 4, we'll see what happens when two qubits become linked: entanglement. On Day 5, we'll put all three ideas together to see how interference actually makes computation faster.

**The question you should now be able to answer:** What is the difference between a qubit in superposition and a classical bit with a 50/50 random value?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Rudolph, *Q is for Quantum*, Chapter 1. Rudolph uses a purely visual notation (no equations) to build superposition from scratch. If the α and β notation feels abstract, Rudolph's pictures will ground it.

**If you want the deep version:**
- Rieffel & Polak, *Quantum Computing: A Gentle Introduction*, Chapter 2, Sections 2.1–2.2 (MIT Press, 2011). The most careful written treatment of single-qubit states and the Bloch sphere for a non-physicist. Pages 24–40.
- Vazirani, *Quantum Mechanics and Quantum Computation* (CS 191x), Week 1, Lecture 1. Available on YouTube: search "Vazirani CS191x Lecture 1." The first 20 minutes cover qubits and superposition with outstanding clarity.

---

## Navigation

← **Previous:** [Day 2 — The Strangeness of the Quantum World](./day-02-quantum-strangeness.md)
→ **Next:** [Day 4 — Entanglement — Correlated by Nature](./day-04-entanglement.md)
