# Glossary

Alphabetical. Each entry: plain-English definition → formal definition → introduced on Day N.

---

**Algorithm, Quantum**
Plain: A step-by-step recipe designed specifically to run on a quantum computer, exploiting superposition and interference to solve certain problems faster than any classical recipe.
Formal: A sequence of unitary operations (gates) and measurements applied to a quantum register, designed to solve a computational problem with a provable speedup over classical algorithms for that problem class.
Introduced: Day 9.

---

**Amplitude**
Plain: The "weight" assigned to each possible outcome in a superposition — like a probability, except it can be negative or complex, which allows for interference.
Formal: A complex number α such that |α|² gives the probability of a given outcome upon measurement. The set of all amplitudes for a quantum state must satisfy Σ|αᵢ|² = 1.
Introduced: Day 3.

---

**Bell State**
Plain: The simplest entangled state of two qubits — they are maximally correlated, with no way to describe either qubit independently.
Formal: One of four two-qubit states, e.g. |Φ+⟩ = (|00⟩ + |11⟩)/√2, that cannot be written as a tensor product of individual qubit states.
Introduced: Day 4.

---

**Bell's Theorem**
Plain: A mathematical proof (1964) that the correlations in entangled quantum systems cannot be explained by any pre-agreed "hidden" classical information — quantum randomness is genuinely irreducible.
Formal: No local hidden-variable theory can reproduce all predictions of quantum mechanics. Verified experimentally via violation of Bell inequalities (Aspect et al., 1982; loophole-free tests, 2015).
Introduced: Day 4.

---

**Bloch Sphere**
Plain: A visual tool: picture a globe where the north pole is |0⟩, the south pole is |1⟩, and any point on the surface represents a valid qubit state (superposition). Quantum gates are rotations on this globe.
Formal: A unit sphere in ℝ³ parameterizing the pure states of a single qubit: |ψ⟩ = cos(θ/2)|0⟩ + e^(iφ)sin(θ/2)|1⟩, where θ ∈ [0, π] and φ ∈ [0, 2π).
Introduced: Day 3.

---

**Coherence / Coherence Time**
Plain: How long a qubit can maintain its quantum superposition before interactions with the environment destroy it. The longer the coherence time, the more computation you can do before losing the quantum state.
Formal: The timescale over which the off-diagonal elements of the qubit's density matrix decay to zero due to environmental interaction (decoherence).
Introduced: Day 15.

---

**Decoherence**
Plain: The process by which a qubit's quantum superposition is destroyed through interaction with the surrounding environment — the central engineering obstacle in quantum hardware.
Formal: The irreversible loss of quantum coherence as a quantum system becomes entangled with its environment, causing its density matrix to evolve from a pure state toward a mixed state.
Introduced: Day 15.

---

**Deutsch's Algorithm**
Plain: The first quantum algorithm, solving a toy problem (is a function constant or balanced?) with one query instead of two — proving that quantum computers can be fundamentally faster, even on simple problems.
Formal: A quantum algorithm that determines whether a function f: {0,1} → {0,1} is constant (both outputs equal) or balanced (outputs differ) using a single oracle call, versus two calls required classically.
Introduced: Day 11.

---

**Entanglement**
Plain: A connection between two or more qubits such that measuring one instantly tells you something about the other, no matter how far apart they are — and this correlation cannot be explained by pre-agreed information.
Formal: A quantum state of a multi-qubit system that cannot be written as a tensor product of individual qubit states; measuring one subsystem collapses the joint state non-locally.
Introduced: Day 4.

---

**Error Correction, Quantum**
Plain: A technique for protecting quantum information against errors without ever directly reading the qubit — using redundancy encoded across many physical qubits to represent one reliable "logical" qubit.
Formal: A class of protocols (e.g. surface codes) that encode one logical qubit into multiple physical qubits and use syndrome measurements to detect and correct errors without collapsing the logical qubit's state.
Introduced: Day 16.

---

**Fault Tolerance**
Plain: A quantum computer that is reliable enough to run long algorithms despite hardware errors. Requires quantum error correction at scale — currently not achieved.
Formal: The ability to perform arbitrarily long quantum computations with logical error rates below a threshold, achievable when physical error rates fall below a platform-specific fault-tolerance threshold (~1%).
Introduced: Day 16.

---

**Gate, Quantum**
Plain: An operation applied to one or more qubits that changes their state — the quantum analog of a logic gate in a classical circuit. Unlike classical gates, all quantum gates are reversible.
Formal: A unitary matrix U acting on the Hilbert space of n qubits. Common single-qubit gates: Hadamard (H), Pauli-X (bit flip), Pauli-Z (phase flip). Common two-qubit gate: CNOT.
Introduced: Day 8.

---

**Grover's Algorithm**
Plain: A quantum search algorithm that finds a marked item in an unsorted list of N items in roughly √N steps — quadratically faster than the N steps required classically.
Formal: A quantum algorithm achieving O(√N) oracle calls to find a marked element in an unstructured database of N elements, versus O(N) classically. Uses amplitude amplification.
Introduced: Day 12.

---

**Hadamard Gate**
Plain: The most important single-qubit gate — it transforms a definite |0⟩ or |1⟩ state into an equal superposition of both, and vice versa. The "on ramp" into quantum computation.
Formal: H = (1/√2) [[1, 1], [1, -1]]. Applied to |0⟩, produces (|0⟩ + |1⟩)/√2; applied to |1⟩, produces (|0⟩ - |1⟩)/√2.
Introduced: Day 8.

---

**Interference, Quantum**
Plain: The phenomenon where amplitudes of different computational paths add together (constructive) or cancel (destructive), allowing quantum algorithms to "steer" toward correct answers and away from wrong ones.
Formal: The addition of complex probability amplitudes from multiple computational paths before measurement, analogous to wave interference. Enables amplitude amplification of target states.
Introduced: Day 5.

---

**Logical Qubit**
Plain: A reliable, error-protected qubit encoded across many physical qubits. Building logical qubits is the main milestone between today's NISQ devices and fault-tolerant quantum computers.
Formal: A qubit encoded in the code space of a quantum error-correcting code, protected against local errors on the constituent physical qubits. Typically requires ~1,000–10,000 physical qubits per logical qubit.
Introduced: Day 16.

---

**Measurement**
Plain: The act of reading out a qubit's value — which collapses its superposition to a definite 0 or 1, with probabilities determined by the amplitudes. You only get one result; the superposition is gone.
Formal: A projective measurement in the computational basis {|0⟩, |1⟩} collapses state α|0⟩ + β|1⟩ to |0⟩ with probability |α|² or |1⟩ with probability |β|², destroying the superposition.
Introduced: Day 10.

---

**NISQ (Noisy Intermediate-Scale Quantum)**
Plain: The current era of quantum computing: devices with 50–1000+ qubits that are real but too noisy and error-prone to run most useful quantum algorithms. Coined by Preskill in 2018.
Formal: Quantum processors with O(10²–10³) physical qubits, operating without quantum error correction, with gate fidelities insufficient for fault-tolerant computation. Error rates ~0.1–1% per gate.
Introduced: Day 18.

---

**Physical Qubit**
Plain: An actual physical system — an electron, a photon, a superconducting loop — used to represent a qubit in hardware. Many physical qubits are needed to make one reliable logical qubit.
Formal: A two-level quantum system used as the hardware implementation of a qubit, subject to decoherence and gate errors. Distinguished from a logical qubit, which is error-protected.
Introduced: Day 7.

---

**Post-Quantum Cryptography**
Plain: Classical cryptographic algorithms redesigned to remain secure even if large-scale quantum computers exist — because Shor's algorithm would break most of today's public-key encryption.
Formal: Cryptographic schemes (e.g., lattice-based, hash-based, code-based) believed resistant to known quantum attacks, including Shor's algorithm. NIST standardized first post-quantum algorithms in 2024.
Introduced: Day 13.

---

**Quantum Advantage**
Plain: The point at which a quantum computer solves a *practically useful* problem faster or cheaper than any classical computer. Distinct from "quantum supremacy," which only requires beating classical on *any* task.
Formal: Demonstrable superpolynomial or exponential speedup of a quantum algorithm over the best known classical algorithm on a problem with real-world value, verified under fair experimental conditions.
Introduced: Day 18.

---

**Quantum Circuit**
Plain: A diagram representing a quantum computation: horizontal lines are qubits, boxes on the lines are gates, and the whole thing is read left to right, ending with a measurement.
Formal: A model of quantum computation consisting of a sequence of unitary gates acting on n qubits, followed by a measurement. Equivalent in computational power to the quantum Turing machine model.
Introduced: Day 9.

---

**Quantum Key Distribution (QKD)**
Plain: A method for two parties to share a secret encryption key using quantum mechanics — if anyone eavesdrops, the quantum state is disturbed in a detectable way, making interception detectable.
Formal: A cryptographic protocol (e.g., BB84) using quantum states to establish a shared secret key with information-theoretic security guaranteed by the no-cloning theorem and quantum measurement disturbance.
Introduced: Day 19.

---

**Quantum Supremacy**
Plain: A narrow milestone: a quantum computer completes a specific task faster than any classical computer could — even if that task has no practical use. Google claimed this in 2019.
Formal: Computational quantum supremacy is demonstrated when a quantum device solves a well-defined sampling or decision problem in time that is intractable for classical simulation (typically > 10⁴ classical core-hours).
Introduced: Day 18.

---

**Qubit**
Plain: The basic unit of quantum information — like a classical bit, but instead of being definitely 0 or 1, it can exist in a superposition of both until measured.
Formal: A two-dimensional quantum system with basis states |0⟩ and |1⟩, whose general state is |ψ⟩ = α|0⟩ + β|1⟩ where α, β ∈ ℂ and |α|² + |β|² = 1.
Introduced: Day 3.

---

**Shor's Algorithm**
Plain: A quantum algorithm that factors large numbers exponentially faster than any known classical method — threatening RSA encryption, which relies on factoring being hard.
Formal: A quantum algorithm running in O((log N)³) time that finds the prime factors of an integer N, versus the best classical algorithm (general number field sieve) running in sub-exponential but super-polynomial time.
Introduced: Day 13.

---

**Superposition**
Plain: A qubit's ability to be in a combination of |0⟩ and |1⟩ simultaneously — not "we don't know which," but genuinely both, with the two possibilities carrying amplitudes that can interfere.
Formal: A quantum state that is a linear combination of basis states: |ψ⟩ = α|0⟩ + β|1⟩. The principle of superposition follows from the linearity of quantum mechanics (Schrödinger equation).
Introduced: Day 3.

---

**Wave Function**
Plain: The mathematical object that completely describes a quantum system — it encodes every possible state and how likely each is. "Collapse" happens when you measure it.
Formal: A vector |ψ⟩ in a Hilbert space H that completely specifies the quantum state of a system. Its squared modulus |⟨x|ψ⟩|² gives the probability density for outcome x upon measurement.
Introduced: Day 2.
