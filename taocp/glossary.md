# Glossary

Alphabetical. Each entry: plain-English definition | formal definition | introduced Day N.

---

**Algorithm**
An algorithm is a finite, unambiguous, step-by-step procedure that takes an input and produces an output, and is guaranteed to halt.
*Formal:* A finite sequence of well-defined instructions operating on a finite set of inputs, with each step determinately following from the previous, that terminates after a finite number of steps.
*Introduced:* Day 1

**Asymptotic notation (O, Ω, Θ)**
O(f) means "grows no faster than f"; Ω(f) means "grows at least as fast as f"; Θ(f) means "grows at the same rate as f", all in the limit as the input grows large.
*Formal:* f(n) = O(g(n)) iff ∃ c > 0, n₀ such that f(n) ≤ c·g(n) for all n ≥ n₀.
*Introduced:* Day 3

**AVL tree**
A binary search tree that maintains a balance condition: for every node, the heights of its two subtrees differ by at most 1. This guarantees O(log n) height.
*Formal:* A height-balanced BST satisfying |height(left) − height(right)| ≤ 1 at every node, maintained through rotations on insert/delete.
*Introduced:* Day 38

**Binary search tree (BST)**
A binary tree where every node's left subtree holds only smaller keys and every node's right subtree holds only larger keys.
*Formal:* A binary tree T where for each node x: all keys in left(x) < key(x) < all keys in right(x).
*Introduced:* Day 37

**Byte / Word / Field**
A byte is the smallest addressable unit of memory (8 bits in modern hardware). A word is the natural unit of a processor (e.g., 64 bits). A field is a named sub-sequence of bits within a word.
*Formal:* Defined by Knuth relative to MIX: a byte is 6 bits in MIX notation; a word is 5 bytes + sign = 31 bits.
*Introduced:* Day 8

**Ceiling (⌈x⌉)**
The smallest integer greater than or equal to x.
*Formal:* ⌈x⌉ = min { n ∈ ℤ : n ≥ x }.
*Introduced:* Day 2

**Circular list**
A linked list where the last node's pointer points back to the first node, forming a ring.
*Formal:* A linked structure L where next(last(L)) = first(L).
*Introduced:* Day 11

**Collision (hashing)**
A collision occurs when two different keys map to the same bucket in a hash table.
*Formal:* Keys k₁ ≠ k₂ collide under hash function h if h(k₁) = h(k₂).
*Introduced:* Day 39

**Comparison-based sort**
A sorting algorithm that orders elements solely by pairwise comparisons (less-than, equal, greater-than) between keys.
*Formal:* A sort whose decision tree has n! leaves, requiring Ω(n log n) comparisons in the worst case.
*Introduced:* Day 25

**Doubly linked list**
A linked list where each node holds a pointer to both the next and the previous node.
*Formal:* A structure where each node x has fields key(x), next(x), prev(x), and next(prev(x)) = x for all non-sentinel nodes.
*Introduced:* Day 11

**Extended Euclidean algorithm**
A version of Euclid's algorithm that, given a and b, also computes integers x and y such that ax + by = gcd(a, b).
*Formal:* Computes (d, x, y) where d = gcd(a, b) and ax + by = d, via back-substitution on the Euclidean remainder sequence.
*Introduced:* Day 22

**Floor (⌊x⌋)**
The largest integer less than or equal to x.
*Formal:* ⌊x⌋ = max { n ∈ ℤ : n ≤ x }.
*Introduced:* Day 2

**Floating-point number**
A finite-precision representation of a real number as a significand times a power of a base (usually 2).
*Formal:* A floating-point number has the form ±m × β^e where m is the significand, β is the base, and e is the exponent, each bounded.
*Introduced:* Day 21

**Generating function**
A formal power series whose coefficients encode a sequence of numbers; algebraic manipulation of the series corresponds to operations on the sequence.
*Formal:* The ordinary generating function of sequence (a₀, a₁, a₂, …) is G(z) = Σ aₙ zⁿ.
*Introduced:* Day 6

**GCD (Greatest Common Divisor)**
The largest positive integer that divides both a and b without remainder.
*Formal:* gcd(a, b) = max { d ∈ ℤ⁺ : d|a and d|b }.
*Introduced:* Day 1

**Hash function**
A function that maps keys from a large domain to a smaller range (the bucket array indices).
*Formal:* h : U → {0, 1, …, m−1} where U is the key universe and m is the table size.
*Introduced:* Day 39

**Heap (data structure)**
A binary tree stored in an array where every node is larger (max-heap) or smaller (min-heap) than its children.
*Formal:* An array A[1..n] satisfying the heap property: A[⌊i/2⌋] ≥ A[i] for all 1 < i ≤ n (max-heap).
*Introduced:* Day 29

**Invariant (loop)**
A condition that is true before the loop begins, remains true after each iteration, and at termination implies the algorithm's correctness.
*Formal:* A predicate P such that P holds before the loop and P ∧ ¬(loop-condition) implies the post-condition after the loop.
*Introduced:* Day 5

**Linear congruential generator (LCG)**
A pseudorandom number generator defined by the recurrence Xₙ₊₁ = (aXₙ + c) mod m.
*Formal:* A sequence X₀, X₁, … defined by Xₙ₊₁ = (aXₙ + c) mod m with multiplier a, increment c, modulus m, and seed X₀.
*Introduced:* Day 19

**Linked list**
A sequence of nodes where each node stores a value and a pointer to the next node in the sequence.
*Formal:* A structure where each node x has fields key(x) and next(x), with next(last) = NIL.
*Introduced:* Day 10

**Load factor (hashing)**
The ratio of the number of stored keys to the total number of buckets in a hash table.
*Formal:* α = n / m where n is the number of keys and m is the table size.
*Introduced:* Day 40

**MIX / MMIX**
Knuth's hypothetical computer architectures used to give concrete, machine-level meaning to algorithm step-counts.
*Formal:* MIX is a byte-addressable machine with a 5-byte word, defined in TAOCP Vol. 1 §1.3. MMIX is its 64-bit RISC successor, defined in Vol. 1 supplement (2005).
*Introduced:* Day 4

**Open addressing**
A hash table collision-resolution strategy where, on a collision, the algorithm probes a sequence of alternative buckets in the same array.
*Formal:* On collision at h(k), probe positions h(k, 0), h(k, 1), … until an empty slot is found.
*Introduced:* Day 40

**Pointer**
A value that stores the memory address of another value, enabling structures whose nodes need not be contiguous.
*Formal:* A pointer p is a value in the address space such that the node it refers to is stored at location p.
*Introduced:* Day 10

**Pseudorandom number generator (PRNG)**
A deterministic algorithm that produces a sequence of numbers that pass statistical tests for randomness.
*Formal:* A function G : {0,1}ˢ → {0,1}ⁿ (seed to output) whose output is computationally indistinguishable from a truly random sequence.
*Introduced:* Day 18

**Queue**
A data structure supporting insert at the rear and remove from the front (FIFO — First In, First Out).
*Formal:* An abstract data type with operations enqueue(x) and dequeue(), maintaining the invariant that dequeue returns the element enqueued earliest among those present.
*Introduced:* Day 9

**Radix sort**
A non-comparison sort that processes keys digit by digit, from least to most significant (LSD) or vice versa (MSD).
*Formal:* Sort n keys of d digits in base r using d passes of a stable sort on individual digits, total cost O(d(n + r)).
*Introduced:* Day 31

**Rotation (tree)**
A local restructuring of a BST that changes the tree's shape while preserving the BST ordering property, used to restore balance.
*Formal:* A left-rotation on node x replaces x with its right child y, making x the left child of y, while preserving in-order sequence.
*Introduced:* Day 38

**Sorting network**
A fixed circuit of comparators that sorts any input of a given size, with all comparisons determined in advance (data-oblivious).
*Formal:* A sequence of comparator pairs (i, j) applied to positions of an array, where each comparator places min at position i and max at j, sorting all inputs.
*Introduced:* Day 33

**Stack**
A data structure supporting insert and remove from the same end (LIFO — Last In, First Out).
*Formal:* An abstract data type with operations push(x) and pop(), maintaining the invariant that pop returns the most-recently-pushed element.
*Introduced:* Day 9

**Tree (rooted)**
A connected, acyclic graph with a designated root node; equivalently, a node with zero or more child subtrees.
*Formal:* A rooted tree T is either empty or consists of a root r and a finite set of disjoint rooted trees T₁, …, Tₖ (the subtrees of r).
*Introduced:* Day 12

**Trie**
A tree where each path from root to a node spells out a prefix of some key; branching is determined by key digits.
*Formal:* A rooted tree where each node at depth d stores one digit of a key; the path to a leaf spells the full key.
*Introduced:* Day 41
