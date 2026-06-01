# Bibliography — Quantum Computing Course Shelf

Full annotated sources for the course. Each entry includes: full citation, what it covers, whose intuition it favors, where it's hardest, and which days rely on it most.

---

## Foundational Books

### 1. Computing with Quantum Cats: From Colossus to Qubits
**John Gribbin.** Bantam Press, 2013. ISBN: 978-0552779319.

A narrative history and conceptual tour of quantum computing written for the general reader. Gribbin grounds every idea in the history of physics — you learn about quantum mechanics the same way physicists discovered it, which makes the strangeness feel earned rather than arbitrary. Chapters 1–4 cover the quantum weirdness that underlies everything; Chapters 7–9 reach into quantum cryptography and computing applications.

- **Whose intuition it favors:** Storytellers and historically-minded readers. Gribbin explains the *why* of each discovery before the *what*.
- **Where it's hardest:** The final chapters assume you've followed the earlier narrative; they can feel rushed if read in isolation.
- **Days it anchors:** Days 1, 2, and 19. The spine of Module 1.

---

### 2. Quantum Computing: An Applied Approach (2nd Edition)
**Jack Hidary.** Springer, 2021. ISBN: 978-3030832742.

The most practical book at the L1–L2 boundary. Hidary covers the full stack: from qubit physics to circuit construction to hardware platforms to near-term algorithms. Chapter 2 on physical qubit implementations and Chapters 3–5 on error, hardware, and NISQ computing are the most relevant for this course.

- **Whose intuition it favors:** Engineers and systems thinkers who want to understand how things are *built*.
- **Where it's hardest:** Later chapters include Python/Cirq code, which you can skip at L1 without losing the conceptual thread.
- **Days it anchors:** Days 7, 15, 16, 17. The spine of Module 3.

---

### 3. Quantum Computing: A Gentle Introduction
**Eleanor Rieffel and Wolfgang Polak.** MIT Press, 2011. ISBN: 978-0262526678.

The gold standard for algorithmic understanding without graduate-level quantum mechanics. Rieffel and Polak are meticulous: every step is justified, every claim is proven at just enough depth. Chapters 4–7 cover gates, circuits, and the landmark algorithms (Deutsch, Grover, Shor) with unusual pedagogical care.

- **Whose intuition it favors:** Careful, methodical readers who want to understand *why* each step works, not just *that* it works.
- **Where it's hardest:** Chapter 3 (linear algebra) requires comfort with vectors and matrices. At L1, you can read the surrounding chapters and treat the linear algebra as scaffolding you don't need to fully follow.
- **Days it anchors:** Days 5, 8–13. The spine of Module 2.

---

### 4. Q is for Quantum
**Terry Rudolph.** Terian Books, 2017. ISBN: 978-1974000227. Freely available at: [http://www.qisforquantum.org](http://www.qisforquantum.org) [VERIFY link currency].

An entirely diagrammatic treatment of quantum computing — no equations, just carefully designed visual puzzles. Rudolph builds up the full formalism using only black and white circles and lines. It sounds gimmicky but is genuinely rigorous: you arrive at the same understanding as a textbook reader, via pictures.

- **Whose intuition it favors:** Visual thinkers who bounce off notation.
- **Where it's hardest:** The visual notation takes ~20 minutes to learn. Once it clicks, it's remarkably efficient.
- **Days it anchors:** Days 3, 4, and 10 as alternative visual intuition. Particularly good for superposition and measurement.

---

## Landmark Papers

### 5. "Simulating Physics with Computers"
**Richard P. Feynman.** *International Journal of Theoretical Physics*, Vol. 21, No. 6/7, pp. 467–488, 1982.

The paper that started the field. Feynman argues that classical computers cannot efficiently simulate quantum mechanical systems, and proposes that a computer built on quantum principles could. Short, readable, and still electrifying. Feynman's argument is the foundation of Day 20.

- **Days it anchors:** Day 1 (the original motivation), Day 20 (quantum simulation).

---

### 6. "Algorithms for Quantum Computation: Discrete Logarithms and Factoring"
**Peter W. Shor.** *Proceedings of the 35th Annual Symposium on Foundations of Computer Science (FOCS 1994)*, pp. 124–134. IEEE, 1994.

The paper that made quantum computing a serious concern for cryptographers and governments. Shor demonstrates polynomial-time quantum algorithms for factoring and discrete logarithm — both problems believed intractable for classical computers. You do not need to follow the math to appreciate the argument of Day 13.

- **Days it anchors:** Day 13.

---

### 7. "A Fast Quantum Mechanical Algorithm for Database Search"
**Lov K. Grover.** *Proceedings of the 28th Annual ACM Symposium on Theory of Computing (STOC 1996)*, pp. 212–219. ACM, 1996.

The second landmark algorithm, demonstrating quadratic speedup for unstructured search. Grover's paper is unusually readable: the algorithm is short and the argument is elegant. Day 12 is built on this paper's core insight.

- **Days it anchors:** Day 12.

---

### 8. "Quantum Supremacy Using a Programmable Superconducting Processor"
**Frank Arute et al. (Google AI Quantum).** *Nature*, Vol. 574, pp. 505–510, October 2019. DOI: [10.1038/s41586-019-1666-5](https://doi.org/10.1038/s41586-019-1666-5).

The paper that coined "quantum supremacy" as a realized milestone. Google's Sycamore processor performed a specific sampling task in 200 seconds that they claimed would take 10,000 years on the best classical supercomputer. (IBM contested the classical estimate.) The paper and the controversy around it are precisely what Day 18 dissects.

- **Days it anchors:** Day 18.

---

### 9. "Quantum Computing in the NISQ Era and Beyond"
**John Preskill.** *Quantum*, Vol. 2, p. 79, 2018. arXiv: [1801.00862](https://arxiv.org/abs/1801.00862).

The paper that defined the current moment. Preskill coins "NISQ" (Noisy Intermediate-Scale Quantum), characterizes what today's devices can and cannot do, and gives a clear-eyed assessment of the path to fault-tolerant quantum computing. Essential reading for understanding Days 18–22. Freely available on arXiv.

- **Days it anchors:** Days 18, 21, 22. The spine of Module 4's realism.

---

## Video Lectures and Courses

### 10. "Quantum Computing for Computer Scientists"
**Andrew Helwer.** Microsoft Research, 2018. YouTube: search "Microsoft Research Quantum Computing Computer Scientists Helwer" (runtime: ~1 hour).

The single best video introduction to quantum gates and circuits for someone without physics training. Helwer uses linear algebra as the bridge — but explains the necessary math as he goes, so prior exposure helps but isn't required. Watch between Days 8 and 11 for the highest payoff.

- **Best moments:** 12:00–28:00 for superposition and gates; 38:00–52:00 for circuit intuition.
- **Days it anchors:** Days 8–11 (alternative visual explanation of gates and Deutsch's algorithm).

---

### 11. "Quantum Mechanics and Quantum Computation" (CS 191x)
**Umesh Vazirani.** edX / UC Berkeley, 2012–2015. Course ID: BerkeleyX CS-191x. (Search edX or YouTube for lecture recordings.)

Vazirani is one of the field's great teachers. His delivery is intuition-first, and he has a gift for making the formalism feel inevitable rather than arbitrary. The early lectures (weeks 1–3) cover superposition, entanglement, and measurement at exactly the right depth for this course.

- **Best lectures:** Weeks 1–3 for L1 purposes.
- **Days it anchors:** Days 3–10 as a supplementary track.

---

### 12. Scott Aaronson — "Quantum Computing Since Democritus" (Lecture Notes)
**Scott Aaronson.** University of Texas at Austin. Freely available at: [https://www.scottaaronson.com/democritus/](https://www.scottaaronson.com/democritus/).

Lecture notes from Aaronson's course, later published as a book (Cambridge University Press, 2013). Unlike every other source on this shelf, Aaronson's primary interest is in what quantum computing *means* — philosophically, mathematically, computationally. Lectures 9 and 10 on quantum computing proper are outstanding. Lecture 9 is required reading after Day 1.

- **Best lectures:** Lecture 9 ("Quantum," after Day 1), Lecture 10 ("Quantum Computing," after Day 11).
- **Days it anchors:** Days 1–2 (motivation and philosophy), Day 21 (QML skepticism).

---

## Optional Deeper Dives

**Scott Aaronson.** *Quantum Computing Since Democritus* (book). Cambridge University Press, 2013. ISBN: 978-0521199568.
The expanded, polished version of the lecture notes above. If this course sparks genuine interest and you want to cross into L2 territory, this is the natural next read. Chapters 9–10 are the core quantum computing content; the surrounding chapters on complexity theory reward patient readers.

**IBM Quantum Learning.** [https://quantum.ibm.com/learning](https://quantum.ibm.com/learning).
Interactive quantum circuits in the browser, free to use. After Day 9 (Circuits), spending 20 minutes on IBM Quantum's composer — dragging and dropping gates onto qubits — makes the abstractions viscerally real. No account required for the basic tutorials.

**Stephanie Wehner, David Elkouss, and Ronald Hanson.** "Quantum Internet: A Vision for the Road Ahead." *Science*, Vol. 362, No. 6412, 2018. DOI: [10.1126/science.aam9288](https://doi.org/10.1126/science.aam9288).
A readable overview of the quantum internet — using entanglement to build networks that are secure by physical law. Supplements Day 19 on quantum cryptography and gives a longer horizon view of quantum networking.
