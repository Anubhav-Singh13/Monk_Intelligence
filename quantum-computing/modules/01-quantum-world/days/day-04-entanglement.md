# Day 4 — Entanglement — Correlated by Nature

> **Today's one idea:** Entanglement is a correlation between qubits that is deeper than any classical correlation — the two particles share a single quantum state that cannot be decomposed into individual states, and measuring one instantly determines what you'll get when measuring the other.
> **Reading time:** ~40 min · **Prereqs:** Day 3
> **Primary source for today:** Terry Rudolph, *Q is for Quantum*, Chapter 2 (Terian Books, 2017)
> **Before you start:** Recall Day 3's load-bearing idea — one sentence, no looking. What is an amplitude, and what can it do that a classical probability cannot?

---

## The hook

Imagine you have a pair of magic gloves. You put one in a box without looking, seal it, and ship it to Mars. You keep the other. The moment you open your box and see a left glove, you know instantly — faster than light — that the Mars glove is a right glove.

Is that entanglement? It sounds like it. But it isn't.

The gloves were left and right from the moment they were packed. There's a pre-existing, definite fact — you just didn't know it. The information traveled with the box, not instantaneously.

Entanglement is stranger. In the quantum version, the "handedness" of each glove isn't determined when you pack them. It isn't determined on the journey. It is only determined the moment one of them is opened — and at that instant, the other one's fate is fixed too, no matter how far apart they are.

And here is the key point that took physicists 30 years to settle: this is not because of some hidden tag inside the box that you missed. The randomness is genuine, and the correlation is real — and John Bell proved mathematically in 1964 that no "pre-agreed information" story can explain it.

---

## Building the intuition

### Two classical coins vs. two entangled qubits

Consider two coins.

**Classical version:** You flip both and they both land randomly. Sometimes they're the same (both heads, or both tails), sometimes different. The probability of matching is 50%.

**Classically correlated version:** You pick two coins, put them in boxes, and shake each independently — but you guarantee they'll always match. Maybe you used identical shaking mechanisms. The outcome is: always matching, but determined when you shook them.

**Entangled version:** The "matching" is not determined until the first one is opened. Before that, neither coin is heads or tails. The moment you open one box, both outcomes are fixed — to be the same. But which outcome (heads or heads, or tails or tails) is determined randomly in that instant.

These three situations produce identical statistics if you only look at correlation counts. But Bell's theorem shows that the entangled version makes *different predictions* from both classical versions in certain carefully designed experiments — predictions that nature matches, ruling out the classical stories.

### What makes entanglement non-classical

Bell's key insight: if the outcomes were pre-determined (like hidden tags), then the correlation between measurements at different angles would follow a certain mathematical inequality. In 1972, John Clauser tested this. In 1981, Alain Aspect performed a loophole-free version. Nature violated the inequality — matching quantum mechanical predictions, not the hidden-variable prediction.

In 2022, Aspect, Clauser, and Anton Zeilinger won the Nobel Prize in Physics for this work.

### One state, not two

Here is the deepest way to state it: two entangled qubits don't each have their own state. They share a *single* joint state that cannot be separated into individual parts.

If I give you qubit A from an entangled pair, you cannot write down a wave function for A alone. The complete description of the system requires describing both qubits together.

This is profoundly different from a classical system. Two classical coins each have their own state (heads or tails). Two entangled qubits do not — there is only one state, and it belongs to the pair.

### What entanglement is NOT

It is not a signal. Measuring qubit A in London and getting a result doesn't let you send information to qubit B in Tokyo. The result you see in London is random — you can't control it. Only when you compare notes over a classical channel do you discover the correlation. The correlation is real; the communication is not.

---

## The formal picture

A two-qubit system has four basis states: |00⟩, |01⟩, |10⟩, |11⟩.

The most famous entangled state — a Bell state — is:

```math
|\Phi^+\rangle = \frac{1}{\sqrt{2}}\bigl(|00\rangle + |11\rangle\bigr)
```

Reading this: the system is in an equal superposition of "both qubits are 0" and "both qubits are 1." When measured:
- 50% chance: both qubits are 0.
- 50% chance: both qubits are 1.
- 0% chance: one is 0 and the other is 1.

The results are perfectly correlated. But which outcome occurs (both-0 or both-1) is random and determined only at the moment of measurement.

**Test for entanglement:** A two-qubit state is *separable* (not entangled) if it can be written as a product of two independent single-qubit states: |ψ⟩ = |a⟩ ⊗ |b⟩. If it cannot, it is entangled. The Bell state |Φ+⟩ cannot be factored this way — try it and you'll find no values of α, β, γ, δ that work.

**The four Bell states:**

| State | Formula | Correlation |
|-------|---------|-------------|
| \|Φ+⟩ | (1/√2)(|00⟩ + |11⟩) | Both same, random which |
| \|Φ−⟩ | (1/√2)(|00⟩ − |11⟩) | Both same (with phase) |
| \|Ψ+⟩ | (1/√2)(|01⟩ + |10⟩) | Always opposite |
| \|Ψ−⟩ | (1/√2)(|01⟩ − |10⟩) | Always opposite (with phase) |

---

## Where it breaks / what it is not

**"Entanglement allows faster-than-light communication."**
No. This is the single most common misconception. The outcomes of measurements are random — you can't choose what result you get, so you can't use entanglement to send a message. The correlation only becomes apparent when you compare results over a classical channel. No information travels between the qubits.

**"The glove analogy is good enough."**
No. The gloves are classically correlated — their "handedness" was fixed at packing. Bell's theorem shows that quantum entanglement is fundamentally different: the outcomes are not predetermined. The analogy is useful for introducing the concept but breaks at exactly the point where quantum mechanics diverges from classical mechanics.

**"Entanglement means the particles are close to each other."**
No. Entangled particles can be separated by any distance. The entanglement persists as long as neither particle has been measured or disturbed. (In practice, decoherence — Day 15 — destroys entanglement if particles interact with their environment.)

**"Entanglement lets quantum computers try all inputs simultaneously."**
Partially. Entanglement links qubits so that operations on one affect the state of others — this enables quantum gates to act on correlated systems efficiently. But the computational power comes from entanglement working *together* with interference (Day 5), not from entanglement alone.

---

## Try it yourself

**1. Retrieval — close the page.** Write down in one sentence: in what specific way does quantum entanglement differ from classical correlation — and what did Bell's theorem establish? Open only after writing your answer.

<details>
<summary>Answer</summary>
Classical correlation is pre-determined: two objects have correlated properties decided at the moment of creation, like gloves packed in separate boxes. Quantum entanglement is a shared state with no definite individual values until measurement. Bell's theorem proved — and experiments confirmed — that the observed correlations are stronger than any pre-determined hidden-variable story can produce.
</details>

**2. Check understanding.**
Is the state |01⟩ entangled? What about (1/√2)(|01⟩ + |10⟩)?

<details>
<summary>Answer</summary>
|01⟩ is not entangled — it's a product state: qubit 1 is definitely |0⟩ and qubit 2 is definitely |1⟩. You can write it as |0⟩ ⊗ |1⟩.
(1/√2)(|01⟩ + |10⟩) is entangled (this is the Bell state |Ψ+⟩). You cannot write it as any product |a⟩ ⊗ |b⟩ — there's no combination of single-qubit amplitudes that gives 50% probability of 01 and 50% probability of 10 with 0% probability of 00 and 11.
</details>

**3. Apply.**
Alice and Bob share a Bell state |Φ+⟩. Alice measures her qubit in London and gets 0. Before Bob measures his qubit in Tokyo, what is his qubit's state? After Bob measures, what result will he get?

<details>
<summary>Answer</summary>
Before Alice measures, Bob's qubit has no definite state — it's part of the joint entangled system. The moment Alice measures and gets 0, the joint state collapses to |00⟩. Bob's qubit is now in state |0⟩. When Bob measures, he will always get 0. (If Alice had gotten 1, Bob would always get 1.)
</details>

**4. Stretch.**
Bell's theorem rules out "local hidden variables." What does "local" mean here, and why does it matter?

<details>
<summary>Answer</summary>
"Local" means that a particle's behavior can only be influenced by things in its immediate surroundings — information can't travel faster than light to affect a measurement result. The "local" in "local hidden variables" rules out any story where qubit B in Tokyo "knows" what's happening to qubit A in London before Alice's measurement could have reached it (at the speed of light). Bell's theorem shows that even without faster-than-light signaling, the correlations in entangled systems are stronger than any local predetermined strategy can produce. This is what makes the correlations genuinely quantum.
</details>

---

**Transfer — apply it (all levels):** Name a system in your work where two components are correlated — two microservices, two sensors, two financial assets. Is the correlation pre-determined at setup (classical) or does it emerge dynamically from interaction? Write one sentence on the analogy with entanglement, and one sentence on where the analogy breaks down.

---

## Connect it back

Superposition (Day 3) gave us a way for a single qubit to be in multiple states at once. Entanglement gives us a way for *multiple qubits* to be in a joint state that can't be factored apart — exponentially more expressive than any collection of independent qubits. Tomorrow, we'll see how these two phenomena combine with a third — interference — to make quantum algorithms actually *work*. The three pillars are finally all in place.

**The question you should now be able to answer:** Why is the "magic gloves" analogy an incomplete description of entanglement?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Rudolph, *Q is for Quantum*, Chapter 2. Rudolph builds entanglement purely visually — no equations. Particularly good at showing why the Bell state cannot be factored into individual qubit states.

**If you want the deep version:**
- Rieffel & Polak, *Quantum Computing: A Gentle Introduction*, Chapter 2, Section 2.3 (MIT Press, 2011). "Multiple Qubits and Entanglement." Pages 41–55. The most careful written treatment of multi-qubit states and what entanglement means formally.
- Gribbin, *Computing with Quantum Cats*, Chapter 4 (Bantam Press, 2013). Gribbin's account of Bell's theorem and the Aspect experiments is the most readable narrative available for a non-physicist.

---

## Navigation

← **Previous:** [Day 3 — Superposition — Both and Neither](./day-03-superposition.md)
→ **Next:** [Day 5 — Interference — How Quantum Computers "Aim"](./day-05-interference.md)
