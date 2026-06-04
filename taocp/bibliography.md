# Bibliography — Course Shelf

## Foundational Books

### 1. The Art of Computer Programming (TAOCP)
**Donald E. Knuth.** Vols. 1–4B. Addison-Wesley.
- Vol. 1: *Fundamental Algorithms*, 3rd ed., 1997 (reprinted 2011). ISBN 978-0-201-89683-1.
- Vol. 2: *Seminumerical Algorithms*, 3rd ed., 1998. ISBN 978-0-201-89684-8.
- Vol. 3: *Sorting and Searching*, 2nd ed., 1998. ISBN 978-0-201-89685-5.
- Vol. 4A: *Combinatorial Algorithms, Part 1*, 1st ed., 2011. ISBN 978-0-201-03804-0.
- Vol. 4B: *Combinatorial Algorithms, Part 2*, 1st ed., 2023. ISBN 978-0-201-03806-4.

**Role:** Spine for all 45 days. Every page of this course distils a section of TAOCP. The course follows Knuth's own sequencing: Vol. 1 §1–2 (Days 1–17), Vol. 2 §3–4 (Days 18–24), Vol. 3 §5–6 (Days 25–42), Vol. 4A §7 (Days 43–44). Where Knuth is dense, we unpack; where he is terse, we slow down.

**Hardest parts:** Vol. 2's number theory (§4.5) and Vol. 3's external sort analysis (§5.4) are the steepest slopes. Do not be discouraged — the course scaffolds both.

---

### 2. Concrete Mathematics: A Foundation for Computer Science
**Ronald L. Graham, Donald E. Knuth, Oren Patashnik.** 2nd ed. Addison-Wesley, 1994. ISBN 978-0-201-55802-9.

**Role:** Mathematical backbone for Days 2–6 and every analysis section thereafter. Chapter 2 (sums) and Chapter 7 (generating functions) are the two sections this course draws on most. If Knuth's analysis notation ever feels opaque, open *Concrete Mathematics* first.

**Hardest parts:** Chapter 6 (special numbers: Bernoulli, Stirling, Fibonacci) is beautiful but not required for L1. Read it only if generating functions on Day 6 light a fire.

---

### 3. Introduction to Algorithms (CLRS)
**Thomas H. Cormen, Charles E. Leiserson, Ronald L. Rivest, Clifford Stein.** 4th ed. MIT Press, 2022. ISBN 978-0-262-04630-5.

**Role:** Modern complement throughout Days 25–42 (sorting and searching modules). When Knuth's notation or MIX-based argument feels heavy, CLRS provides a clean parallel treatment. Particularly useful: Ch. 6 (heapsort), Ch. 7 (quicksort), Ch. 11 (hashing), Ch. 12–13 (BSTs and red-black trees as an extension of Day 38's AVL introduction).

**Hardest parts:** Ch. 15–16 (dynamic programming, greedy) and Part VI (graph algorithms) are out of scope for this course but excellent next steps.

---

### 4. Algorithms
**Robert Sedgewick & Kevin Wayne.** 4th ed. Addison-Wesley, 2011. ISBN 978-0-321-57351-3.

**Role:** Practitioner's complement for Days 26–41. Java implementations give executable intuition alongside Knuth's pseudocode. Directly maps: Ch. 2 (sorting) ↔ Days 26–33; Ch. 3 (searching/symbol tables) ↔ Days 35–41. The online booksite (algs4.cs.princeton.edu) has runnable code for every algorithm.

**Hardest parts:** Ch. 4–6 (graphs, strings, context) are beyond this course's scope but natural extensions.

---

## Landmark Papers

### P1. Quicksort
**C. A. R. Hoare.** "Algorithm 64: Quicksort." *Communications of the ACM* 4(7):321, 1961. DOI: [10.1145/366622.366644](https://doi.org/10.1145/366622.366644)

**Role — Day 30.** One page. Read it after Day 30 to see an idea as it was born. Notice how Hoare names the partition invariant without calling it that.

---

### P2. AVL Trees
**G. M. Adelson-Velsky & E. M. Landis.** "An Algorithm for the Organization of Information." *Proceedings of the USSR Academy of Sciences* 146:263–266, 1962. English translation in *Soviet Mathematics Doklady* 3:1259–1263, 1962.

**Role — Day 38.** The rotation idea in its original form. Knuth's §6.2.3 follows it closely. Reading the original alongside Day 38 reveals how much clarity Knuth added.

---

### P3. KMP String Matching
**Donald E. Knuth, James H. Morris, Vaughan R. Pratt.** "Fast Pattern Matching in Strings." *SIAM Journal on Computing* 6(2):323–350, 1977. DOI: [10.1137/0206024](https://doi.org/10.1137/0206024)

**Role — Day 16 setup / Day 43 context.** The paper that shows naïve O(nm) search is avoidable. Day 16 plants this seed; the paper rewards reading after completing Module 6.

---

### P4. Heapsort / Treesort
**Robert W. Floyd.** "Algorithm 245: Treesort 3." *Communications of the ACM* 7(12):701, 1964. DOI: [10.1145/355588.365103](https://doi.org/10.1145/355588.365103)

**Role — Day 29.** The origin of heapsort in one page. Knuth credits Floyd; this is the source. Read it immediately after Day 29 to feel the gap between the original flash of insight and Knuth's full analysis.

---

## Video Lectures and Courses

### V1. Knuth's Stanford Lectures — "Musings and Findings"
**Donald E. Knuth.** Stanford University Computer Science lecture series (ongoing). Search YouTube: *"Knuth Stanford lecture"* or *"Knuth All Questions Answered."*

**Role — motivational companion throughout.** Knuth's own voice on why these problems matter. His "All Questions Answered" talk (Stanford, 2018, ~90 min) is a good companion to Days 1–3. His "Pi and the Art of Computer Programming" lecture illuminates the spirit of Vol. 2.

---

### V2. MIT 6.006 Introduction to Algorithms (Fall 2011)
**Erik Demaine & Srini Devadas.** MIT OpenCourseWare. [ocw.mit.edu/6-006F11](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-fall-2011/)

**Role — best video companion for Modules 4 and 5.** Lectures 7–8 (hashing) pair with Days 39–40. Lectures 13–14 (sorting) pair with Days 26–31. The lecture notes are downloadable and dense but well-structured.

---

### V3. Algorithms Specialization (Stanford / Coursera)
**Tim Roughgarden.** Parts 1–4, 2016–present. Search: *"Roughgarden Algorithms Coursera"* or *"Stanford Algorithms YouTube."*

**Role — best video for divide-and-conquer and quicksort analysis (Days 28–30).** Part 1, Weeks 2–3 (merge sort recurrences, quicksort pivot analysis) are among the clearest anywhere. Roughgarden's master theorem treatment (Part 1 Week 3) gives a useful second angle on Day 28's recurrence.

---

## Optional Deeper Dives

- **Steven S. Skiena.** *The Algorithm Design Manual*, 3rd ed. Springer, 2020. — Practical war-stories grounding abstract algorithms in real problems. Useful *after* this course as a "what do I reach for?" reference. The "War Stories" chapters alone are worth the price.
- **Udi Manber.** *Introduction to Algorithms: A Creative Approach.* Addison-Wesley, 1989. — Unusually strong on the *invention* of algorithms rather than their presentation. Good capstone companion.
- **3Blue1Brown / Grant Sanderson.** Search YouTube for "3Blue1Brown sorting" or "3Blue1Brown algorithm." While not TAOCP-specific, Sanderson's visual approach to mathematical reasoning is the closest video equivalent to this course's intuition-first pedagogy.
