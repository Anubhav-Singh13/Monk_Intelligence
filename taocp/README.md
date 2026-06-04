# The Art of Computer Programming — A 45-Day Guided Course

> **The promise:** By the end of this course you will be able to read any algorithm in TAOCP, name its load-bearing idea, analyse its cost, and explain why it is correct — using the same tools Knuth uses.

## Who this course is for

This course targets **L1 Practitioner** depth. It assumes comfort with mathematics (sums, induction, basic probability), hands-on programming experience (Python and/or COBOL), and no prior formal study of algorithms. You do not need to own TAOCP to follow the course, though having Volumes 1 and 3 nearby will deepen every day. You should *not* take this course if you want to implement MIX assembly programs or grind through every exercise — this path distils the essential ideas and skips the machinery where it does not add understanding.

The single outcome this course optimises for: **you can pick up any volume of TAOCP, read a section, and follow Knuth's reasoning without getting lost.**

## The arc at a glance

On Day 1, the reader meets Euclid's algorithm and learns to ask: *what exactly is a procedure, and when does it deserve to be called an algorithm?* By Day 8 they can count an algorithm's steps, name its growth rate, and follow an inductive correctness argument — the three instruments Knuth uses on every page of every volume. The middle arc (Days 9–42) traces the same pattern Knuth does: first *how to store things* (structures), then *how to compute with numbers* (seminumerical), then the two great algorithmic problems of practical computing — sorting and searching. Each module builds directly on the last: trees earned in Module 2 reappear as heaps in Module 4 and as BSTs in Module 5; the random-number intuition from Module 3 powers both quicksort's average-case analysis and hashing's collision math.

The single story tying the course together is **invariants as a design tool**. Euclid's GCD has one. Loop invariants prove insertion sort correct. The heap property is an invariant. The AVL balance condition is an invariant. The loaded-slot invariant governs open-address hashing. By Day 45 the reader does not just know these algorithms — they can read any new algorithm Knuth presents and immediately ask "what is the invariant here, and how does the code maintain it?" That question is the master key to TAOCP.

## How to use this course

- **Rhythm:** one page per day, 30–45 minutes of focused reading. Do not binge.
- **Exercises:** Strongly recommended. Each day has 2–4 short exercises; the stretch exercise is optional for L1 but do not skip the first two.
- **Bibliography:** Consult `bibliography.md` when a day's "Suggested readings" section names a source — full citations and annotations live there. Keep moving if you don't have the source; the page is self-contained.
- **Rest and synthesize days:** Days 7, 17, 24, 34, and 42 introduce no new material. Use them to re-derive, re-draw, and re-code. They are not optional.
- **Falling behind:** If life intervenes, re-read the previous day's "Connect it back" paragraph before picking up. Never skip a rest day to catch up.

## The learning path

| Day | Title | One idea | Module |
|-----|-------|----------|--------|
| [1](modules/01-foundations/days/day-01-what-is-an-algorithm.md) | What Is an Algorithm? | Euclid's GCD is the archetype of a finite, unambiguous procedure | Foundations |
| [2](modules/01-foundations/days/day-02-mathematical-toolkit.md) | Knuth's Mathematical Toolkit | Floor, ceiling, mod, and sum notation are the shared language of all analysis | Foundations |
| [3](modules/01-foundations/days/day-03-asymptotic-analysis.md) | Asymptotic Analysis — Big O from First Principles | O/Ω/Θ names how growth behaves, not how fast a particular machine runs | Foundations |
| [4](modules/01-foundations/days/day-04-mix-machine.md) | How Knuth Describes Machines | MIX/MMIX is an idealized computer — enough to read Knuth's cost-counting | Foundations |
| [5](modules/01-foundations/days/day-05-induction-and-invariants.md) | Proof by Induction and Loop Invariants | A loop invariant is an inductive hypothesis wearing work clothes | Foundations |
| [6](modules/01-foundations/days/day-06-generating-functions.md) | Generating Functions — A First Glimpse | A generating function turns a sequence into an algebraic object you can manipulate | Foundations |
| [7](modules/01-foundations/days/day-07-rest-synthesize-1.md) | **Rest & Synthesize I** | Consolidate; re-derive O(n²) for bubble sort from scratch | Foundations |
| [8](modules/01-foundations/days/day-08-numbers-and-information.md) | Numbers and Information | The byte, word, and field are the atoms of every data structure Knuth builds | Foundations |
| [9](modules/02-information-structures/days/day-09-stacks-and-queues.md) | Stacks and Queues | A stack is a disciplined way to remember "where you came from" | Information Structures |
| [10](modules/02-information-structures/days/day-10-linked-lists.md) | Linked Lists — The Pointer Idea | A pointer breaks the tyranny of contiguous memory | Information Structures |
| [11](modules/02-information-structures/days/day-11-doubly-linked-circular.md) | Doubly Linked and Circular Lists | Bidirectionality buys O(1) deletion anywhere | Information Structures |
| [12](modules/02-information-structures/days/day-12-trees.md) | Trees — Definitions and Traversal | A tree is a linked list that branches; traversal order is the design choice | Information Structures |
| [13](modules/02-information-structures/days/day-13-binary-trees.md) | Binary Trees and Representation | Any ordered forest maps to a binary tree — one universal representation | Information Structures |
| [14](modules/02-information-structures/days/day-14-tree-traversal.md) | Tree Traversal Algorithms | Preorder, inorder, postorder each answer a different question | Information Structures |
| [15](modules/02-information-structures/days/day-15-memory-allocation.md) | Memory Allocation — Pools and Boundary Tags | Memory is itself a data structure; fragmentation is unavoidable without a policy | Information Structures |
| [16](modules/02-information-structures/days/day-16-strings-and-pattern-matching.md) | Strings and Pattern Matching — The Setup | Naïve string search is O(nm) and that is a provable waste | Information Structures |
| [17](modules/02-information-structures/days/day-17-rest-synthesize-2.md) | **Rest & Synthesize II** | Draw a doubly-linked circular list from scratch; trace an inorder traversal | Information Structures |
| [18](modules/03-seminumerical-algorithms/days/day-18-what-is-random.md) | Random Numbers — What "Random" Means | A PRNG is a deterministic machine pretending to be a coin | Seminumerical |
| [19](modules/03-seminumerical-algorithms/days/day-19-linear-congruential.md) | Linear Congruential Generators | One multiply, one add, one mod — and why it can fail | Seminumerical |
| [20](modules/03-seminumerical-algorithms/days/day-20-testing-randomness.md) | Testing Randomness — Statistical Eyes | No generator passes every test; the question is which failures matter | Seminumerical |
| [21](modules/03-seminumerical-algorithms/days/day-21-floating-point.md) | Floating-Point Arithmetic | Floating-point is not real arithmetic; rounding error is an algorithm input | Seminumerical |
| [22](modules/03-seminumerical-algorithms/days/day-22-euclid-deeper.md) | Euclid's Algorithm — Deeper | The extended Euclidean algorithm reveals number theory in a loop | Seminumerical |
| [23](modules/03-seminumerical-algorithms/days/day-23-large-number-arithmetic.md) | Arithmetic on Large Numbers | Grade-school multiplication is O(n²); better is possible | Seminumerical |
| [24](modules/03-seminumerical-algorithms/days/day-24-rest-synthesize-3.md) | **Rest & Synthesize III** | Implement an LCG in Python; verify with a chi-square test | Seminumerical |
| [25](modules/04-sorting/days/day-25-lower-bounds.md) | Why Sorting Is Hard — Lower Bounds | Any comparison sort needs at least ⌈log₂(n!)⌉ comparisons | Sorting |
| [26](modules/04-sorting/days/day-26-insertion-sort.md) | Insertion Sort — The Invariant in Action | Insertion sort's invariant is the clearest "sorted prefix" reasoning | Sorting |
| [27](modules/04-sorting/days/day-27-shell-sort.md) | Shell Sort — Breaking O(n²) | Diminishing gap sequences exploit the cheap end of insertion sort | Sorting |
| [28](modules/04-sorting/days/day-28-merge-sort.md) | Merge Sort — Divide, Conquer, Combine | The recurrence T(n)=2T(n/2)+n yields O(n log n) cleanly | Sorting |
| [29](modules/04-sorting/days/day-29-heapsort.md) | Heaps and Heapsort | A heap is a tree hiding in an array; heapsort is O(n log n) in-place | Sorting |
| [30](modules/04-sorting/days/day-30-quicksort.md) | Quicksort — Expected Performance | Quicksort is O(n²) worst but O(n log n) expected — probability explains why | Sorting |
| [31](modules/04-sorting/days/day-31-radix-sort.md) | Radix Sort — Sorting Without Comparisons | Sorting by digits sidesteps the comparison lower bound | Sorting |
| [32](modules/04-sorting/days/day-32-external-sorting.md) | External Sorting — When Data Doesn't Fit | Merge on tape/disk requires thinking about I/O cost, not comparisons | Sorting |
| [33](modules/04-sorting/days/day-33-sorting-networks.md) | Optimal Sorting Networks | A sorting network is a fixed circuit that sorts any input | Sorting |
| [34](modules/04-sorting/days/day-34-rest-synthesize-4.md) | **Rest & Synthesize IV** | Implement merge sort and heapsort; time both at n=10⁵ | Sorting |
| [35](modules/05-searching/days/day-35-sequential-search.md) | Sequential Search and Its Costs | The baseline: scan every element; self-organising heuristics help | Searching |
| [36](modules/05-searching/days/day-36-binary-search.md) | Binary Search — Deceptively Tricky | Binary search is easy to state and hard to implement correctly | Searching |
| [37](modules/05-searching/days/day-37-binary-search-trees.md) | Binary Search Trees | Insert/search/delete in O(h); h is O(log n) average, O(n) worst | Searching |
| [38](modules/05-searching/days/day-38-balanced-trees.md) | Balanced Trees — AVL and the Height Guarantee | Rotations maintain balance; AVL turns O(n) worst into O(log n) | Searching |
| [39](modules/05-searching/days/day-39-hashing.md) | Hashing — The Birthday Paradox at Work | A hash function maps keys to buckets; collisions are inevitable | Searching |
| [40](modules/05-searching/days/day-40-collision-resolution.md) | Collision Resolution — Chaining vs. Open Addressing | Two strategies, different load-factor trade-offs | Searching |
| [41](modules/05-searching/days/day-41-tries.md) | Tries and Radix Search Trees | A trie uses key digits as a path — O(key length) regardless of n | Searching |
| [42](modules/05-searching/days/day-42-rest-synthesize-5.md) | **Rest & Synthesize V** | Build a hash table with open addressing; measure probe lengths | Searching |
| [43](modules/06-combinatorial-and-synthesis/days/day-43-permutations-combinations.md) | Permutations and Combinations | Counting and enumerating are different; backtracking enumerates | Combinatorial |
| [44](modules/06-combinatorial-and-synthesis/days/day-44-bit-manipulation.md) | Bit Manipulation — Knuth's Bitwise Toolkit | Many combinatorial operations reduce to O(1) bitwise tricks | Combinatorial |
| [45](modules/06-combinatorial-and-synthesis/days/day-45-capstone.md) | **Capstone — The Algorithmic Lens** | Synthesise everything: analyse a real problem from scratch | Combinatorial |

## The Course Shelf — Top 5

| # | Source | Why |
|---|--------|-----|
| 1 | Knuth, *TAOCP* Vols 1–4B | The spine of every single day |
| 2 | Graham, Knuth, Patashnik, *Concrete Mathematics* (2nd ed., 1994) | Mathematical backbone for Days 2–6 and all analysis |
| 3 | Cormen et al., *CLRS* (4th ed., MIT Press, 2022) | Modern clean second voice for Days 25–42 |
| 4 | Sedgewick & Wayne, *Algorithms* (4th ed., 2011) | Executable intuition alongside Knuth's pseudocode |
| 5 | MIT 6.006 lectures, Demaine & Devadas (Fall 2011, MIT OCW) | Best video companion for sorting and searching modules |

Full annotated shelf → [bibliography.md](bibliography.md)

## The capstone

Day 45 presents a single unseen problem — designing and analysing a small algorithm from scratch. "Done" means: you have stated the problem precisely, identified the right data structure, written pseudocode with a named invariant, derived the time complexity, and argued correctness. No looking up; no partial credit for "it runs." The capstone is a Feynman test applied to yourself.

## Glossary

→ [glossary.md](glossary.md)

## Meta

- **Depth level:** L1 Practitioner
- **Estimated total hours:** ~28 hours active reading + ~10 hours exercises = ~38 hours
- **Last updated:** 2026-06-04
- **Feedback / corrections:** Raise an issue or note in the margin of your copy.
