# Learning Path

Full rationale and module overview. Each day links to its page.

## Module 1 — Foundations (Days 1–8)

**What this module earns you:** The three instruments Knuth uses on every page — the ability to count steps, name growth rates, and follow an inductive correctness argument. Without these, every subsequent module is just pattern-memorisation.

| Day | Title | Load-Bearing Idea | Why It Comes Now | Prereqs |
|-----|-------|-------------------|-----------------|---------|
| [1](modules/01-foundations/days/day-01-what-is-an-algorithm.md) | What Is an Algorithm? | Euclid's GCD is the archetype of a finite, unambiguous procedure | Opens everything; plants "what makes a procedure an algorithm?" | None |
| [2](modules/01-foundations/days/day-02-mathematical-toolkit.md) | Knuth's Mathematical Toolkit | Floor, ceiling, mod, and sum notation are the language every later proof speaks | All analysis uses these; earn them once | Day 1 |
| [3](modules/01-foundations/days/day-03-asymptotic-analysis.md) | Asymptotic Analysis — Big O | O/Ω/Θ names growth behaviour, not machine speed | Every algorithm comparison from Day 8 on uses this | Day 2 |
| [4](modules/01-foundations/days/day-04-mix-machine.md) | How Knuth Describes Machines | MIX/MMIX is Knuth's idealised computer — enough to read his cost-counting | Unlocks step-count analysis throughout TAOCP | Day 3 |
| [5](modules/01-foundations/days/day-05-induction-and-invariants.md) | Proof by Induction and Loop Invariants | A loop invariant is an inductive hypothesis wearing work clothes | Knuth proves every algorithm with invariants | Days 2–4 |
| [6](modules/01-foundations/days/day-06-generating-functions.md) | Generating Functions — A First Glimpse | A generating function turns a sequence into an algebraic object | Appears in Vol. 1 §1.2.9; recurs in sorting analysis | Day 2 |
| [7](modules/01-foundations/days/day-07-rest-synthesize-1.md) | **Rest & Synthesize I** | Consolidate; re-derive O(n²) for bubble sort from scratch | Before structures begin | Days 1–6 |
| [8](modules/01-foundations/days/day-08-numbers-and-information.md) | Numbers and Information | The byte, word, and field are the atoms of every data structure | Bridges math to concrete structures | Day 4 |

---

## Module 2 — Information Structures (Days 9–17)

**What this module earns you:** A mental model of how data lives in memory — as arrays, as pointer-linked nodes, and as trees — and the ability to trace any of Knuth's structure diagrams by hand.

| Day | Title | Load-Bearing Idea | Why It Comes Now | Prereqs |
|-----|-------|-------------------|-----------------|---------|
| [9](modules/02-information-structures/days/day-09-stacks-and-queues.md) | Stacks and Queues | A stack is a disciplined way to remember "where you came from" | Simplest dynamic structure; motivates linked allocation | Day 8 |
| [10](modules/02-information-structures/days/day-10-linked-lists.md) | Linked Lists — The Pointer Idea | A pointer breaks the tyranny of contiguous memory | The central data-structure primitive | Day 9 |
| [11](modules/02-information-structures/days/day-11-doubly-linked-circular.md) | Doubly Linked and Circular Lists | Bidirectionality buys O(1) deletion anywhere | Natural extension; used in allocators | Day 10 |
| [12](modules/02-information-structures/days/day-12-trees.md) | Trees — Definitions and Traversal | A tree is a linked list that branches | Vol. 1 §2.3; trees appear in almost every later algorithm | Day 11 |
| [13](modules/02-information-structures/days/day-13-binary-trees.md) | Binary Trees and Representation | Any ordered forest maps to a binary tree — one universal representation | Knuth's "natural correspondence" | Day 12 |
| [14](modules/02-information-structures/days/day-14-tree-traversal.md) | Tree Traversal Algorithms | Preorder, inorder, postorder each answer a different question | Inorder traversal *is* sorted order — precedes BSTs | Day 13 |
| [15](modules/02-information-structures/days/day-15-memory-allocation.md) | Memory Allocation — Pools and Boundary Tags | Memory is itself a data structure | Vol. 1 §2.5; grounds the abstract pointer model | Days 10–11 |
| [16](modules/02-information-structures/days/day-16-strings-and-pattern-matching.md) | Strings and Pattern Matching — The Setup | Naïve string search is O(nm) and that is a provable waste | Sets up KMP; motivates cleverness in search | Day 8 |
| [17](modules/02-information-structures/days/day-17-rest-synthesize-2.md) | **Rest & Synthesize II** | Draw a doubly-linked circular list; trace an inorder traversal | Before seminumerical algorithms | Days 9–16 |

---

## Module 3 — Seminumerical Algorithms (Days 18–24)

**What this module earns you:** An understanding of how randomness and arithmetic are *designed*, not assumed — and the probabilistic intuition that underpins quicksort and hashing in later modules.

| Day | Title | Load-Bearing Idea | Why It Comes Now | Prereqs |
|-----|-------|-------------------|-----------------|---------|
| [18](modules/03-seminumerical-algorithms/days/day-18-what-is-random.md) | Random Numbers — What "Random" Means | A PRNG is a deterministic machine pretending to be a coin | Randomness underlies average-case analysis | Day 3 |
| [19](modules/03-seminumerical-algorithms/days/day-19-linear-congruential.md) | Linear Congruential Generators | One multiply, one add, one mod — most-used RNG, and why it can fail | Vol. 2 §3.2 | Days 2, 18 |
| [20](modules/03-seminumerical-algorithms/days/day-20-testing-randomness.md) | Testing Randomness — Statistical Eyes | No generator passes every test; failures reveal structure | Vol. 2 §3.3 | Day 19 |
| [21](modules/03-seminumerical-algorithms/days/day-21-floating-point.md) | Floating-Point Arithmetic | Floating-point is not real arithmetic; rounding error is an algorithm input | Vol. 2 §4.2 | Day 2 |
| [22](modules/03-seminumerical-algorithms/days/day-22-euclid-deeper.md) | Euclid's Algorithm — Deeper | The extended Euclidean algorithm reveals number theory in a loop | Vol. 2 §4.5; full circle from Day 1 | Days 1–3 |
| [23](modules/03-seminumerical-algorithms/days/day-23-large-number-arithmetic.md) | Arithmetic on Large Numbers | Grade-school multiplication is O(n²); better is possible | Vol. 2 §4.3 | Day 3 |
| [24](modules/03-seminumerical-algorithms/days/day-24-rest-synthesize-3.md) | **Rest & Synthesize III** | Implement an LCG in Python; verify with a chi-square test | Before sorting | Days 18–23 |

---

## Module 4 — Sorting (Days 25–34)

**What this module earns you:** A clear understanding of *why* the main sorting algorithms are designed as they are, including the proven lower bound that separates the clever from the impossible.

| Day | Title | Load-Bearing Idea | Why It Comes Now | Prereqs |
|-----|-------|-------------------|-----------------|---------|
| [25](modules/04-sorting/days/day-25-lower-bounds.md) | Why Sorting Is Hard — Lower Bounds | Any comparison sort needs ≥ ⌈log₂(n!)⌉ comparisons | Motivates every clever sort that follows | Day 3 |
| [26](modules/04-sorting/days/day-26-insertion-sort.md) | Insertion Sort — The Invariant in Action | Insertion sort's invariant is the clearest "sorted prefix" reasoning | Concrete warm-up before complex sorts | Days 5, 25 |
| [27](modules/04-sorting/days/day-27-shell-sort.md) | Shell Sort — Breaking O(n²) | Diminishing gap sequences exploit the cheap end of insertion sort | Bridge between simple and fast | Day 26 |
| [28](modules/04-sorting/days/day-28-merge-sort.md) | Merge Sort — Divide, Conquer, Combine | T(n)=2T(n/2)+n yields O(n log n); the recurrence is the idea | Cleanest divide-and-conquer example | Days 3, 5 |
| [29](modules/04-sorting/days/day-29-heapsort.md) | Heaps and Heapsort | A heap is a tree hiding in an array | O(n log n) in-place; heap also = priority queue | Days 12–14 |
| [30](modules/04-sorting/days/day-30-quicksort.md) | Quicksort — Expected Performance | O(n²) worst, O(n log n) expected — probability explains why | Hoare's great idea; connects to Vol. 2 | Days 18–20, 28 |
| [31](modules/04-sorting/days/day-31-radix-sort.md) | Radix Sort — Sorting Without Comparisons | Digit-by-digit sort sidesteps the lower bound | Shows the lower bound has a hidden assumption | Day 25 |
| [32](modules/04-sorting/days/day-32-external-sorting.md) | External Sorting — When Data Doesn't Fit | I/O cost dominates; the tape-merge model | COBOL background makes this immediately concrete | Days 28–31 |
| [33](modules/04-sorting/days/day-33-sorting-networks.md) | Optimal Sorting Networks | A sorting network is a fixed circuit that sorts any input | Parallel model illuminates what "sorting" really is | Days 25, 28 |
| [34](modules/04-sorting/days/day-34-rest-synthesize-4.md) | **Rest & Synthesize IV** | Implement merge sort and heapsort; time at n=10⁵ | Before searching | Days 26–33 |

---

## Module 5 — Searching (Days 35–42)

**What this module earns you:** A complete picture of dictionary data structures — from the simplest scan to the cleverest hash — and the ability to choose the right one given a set of constraints.

| Day | Title | Load-Bearing Idea | Why It Comes Now | Prereqs |
|-----|-------|-------------------|-----------------|---------|
| [35](modules/05-searching/days/day-35-sequential-search.md) | Sequential Search and Its Costs | The baseline; self-organising heuristics help | The "boring" case reveals what cleverness buys | Day 3 |
| [36](modules/05-searching/days/day-36-binary-search.md) | Binary Search — Deceptively Tricky | Easy to state, hard to implement correctly; the invariant is everything | Case study in the gap between idea and correct code | Days 5, 35 |
| [37](modules/05-searching/days/day-37-binary-search-trees.md) | Binary Search Trees | O(h) for all operations; h is O(log n) average but O(n) worst | Dynamic counterpart to binary search | Days 13–14, 36 |
| [38](modules/05-searching/days/day-38-balanced-trees.md) | Balanced Trees — AVL | Rotations maintain balance; AVL turns worst-case O(n) into O(log n) | Structural invariant maintained through every operation | Days 5, 37 |
| [39](modules/05-searching/days/day-39-hashing.md) | Hashing — The Birthday Paradox | A hash function maps keys to buckets; collisions are inevitable | Connects Vol. 2 probability to structure design | Days 18, 35 |
| [40](modules/05-searching/days/day-40-collision-resolution.md) | Collision Resolution — Chaining vs. Open Addressing | Different load-factor trade-offs; analysis hinges on RNG intuition | Completes the hashing picture | Days 19, 39 |
| [41](modules/05-searching/days/day-41-tries.md) | Tries and Radix Search Trees | O(key length) search regardless of n | Connects to radix sort (Day 31) | Days 13, 31 |
| [42](modules/05-searching/days/day-42-rest-synthesize-5.md) | **Rest & Synthesize V** | Build an open-addressing hash table; measure load vs. probe length | Before combinatorial | Days 35–41 |

---

## Module 6 — Combinatorial Algorithms and Synthesis (Days 43–45)

**What this module earns you:** Exposure to Vol. 4A's combinatorial thinking and, on Day 45, the integrated experience of applying the whole course toolkit to a fresh problem.

| Day | Title | Load-Bearing Idea | Why It Comes Now | Prereqs |
|-----|-------|-------------------|-----------------|---------|
| [43](modules/06-combinatorial-and-synthesis/days/day-43-permutations-combinations.md) | Permutations and Combinations | Counting and enumerating are different; backtracking enumerates | Vol. 4A §7.2.1; underpins algorithm analysis throughout | Day 6 |
| [44](modules/06-combinatorial-and-synthesis/days/day-44-bit-manipulation.md) | Bit Manipulation — Knuth's Bitwise Toolkit | Many combinatorial ops reduce to O(1) bitwise tricks | Vol. 4A §7.1; COBOL background makes word-level contrast vivid | Day 8 |
| [45](modules/06-combinatorial-and-synthesis/days/day-45-capstone.md) | **Capstone — The Algorithmic Lens** | Synthesise everything: analyse a real problem from scratch | The Feynman test applied to yourself | Days 1–44 |
