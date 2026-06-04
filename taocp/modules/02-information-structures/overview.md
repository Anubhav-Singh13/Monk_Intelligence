# Module 2 — Information Structures

## What this module earns you

By the end of Day 17, you will be able to:

- Draw the memory layout of any of Knuth's linked structures (list, doubly-linked list, circular list, tree) from a verbal description
- Trace a traversal algorithm by hand, showing the state of all pointer variables at each step
- Identify the invariant of any structure operation (insert, delete, traverse)
- Understand why different structures exist: what each one costs, what it buys, and in what situation you would choose it

These skills are the foundation for every algorithm in Modules 4 and 5. Trees (Days 12–14) reappear as heaps (Day 29) and BSTs (Day 37). Linked allocation (Day 10) underpins every dynamic data structure in the series.

## What to watch for

- **Days 9–11:** The transition from arrays to pointers is the conceptual leap of this module. If you are used to thinking of memory as a flat numbered sequence, the linked model requires a shift — nodes live anywhere, and "next" is a pointer, not "current address + 1."
- **Day 12:** Knuth's definition of a tree is recursive. Read it slowly; the recursion is not circular, it is definitional.
- **Day 14:** Traversal order (preorder vs. inorder vs. postorder) seems like a detail but is a major design choice. Inorder traversal of a BST yields sorted order — that insight will be waiting for you in Day 37.
- **Day 15:** Memory allocation is unglamorous but important. COBOL programmers who managed WORKING-STORAGE manually will find this familiar.

## Navigation

→ **First page:** [Day 9 — Stacks and Queues](days/day-09-stacks-and-queues.md)  
← **Previous module:** [Module 1 — Foundations](../../01-foundations/overview.md)  
→ **Next module:** [Module 3 — Seminumerical Algorithms](../../03-seminumerical-algorithms/overview.md)
