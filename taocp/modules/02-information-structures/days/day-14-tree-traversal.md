# Day 14 — Tree Traversal Algorithms

> **Today's one idea:** Preorder, inorder, and postorder are not just abstract orderings — each is a precise algorithm with a loop invariant, and inorder traversal of a binary search tree is the same operation as sorting.
> **Reading time:** ~38 min · **Prereqs:** Days 12–13
> **Primary source:** Knuth, *TAOCP* Vol. 1, §2.3.1 "Traversing Binary Trees" (pp. 318–331, 3rd ed.)

---

## The hook

You know the three traversal orders from Day 12. Today you move from definition to algorithm — from "what order" to "how, precisely, does the code produce that order?"

The recursive implementations from Day 12 are clean but hide a secret: they use the call stack. Every recursive call to `inorder(v.left)` pushes a frame onto the system's call stack. For a balanced tree of one million nodes, that stack is about 20 frames deep (log₂ 10⁶ ≈ 20). For a degenerate tree (a chain), it is one million frames deep — a stack overflow.

Understanding traversal at the algorithm level — not just as "write three recursive lines" — prepares you for the BST operations in Day 37 and the heap operations in Day 29, where traversal is embedded inside other algorithms and cannot always rely on recursion.

---

## Building the intuition

### The call stack made visible

The recursive inorder traversal:

```python
def inorder(v):
    if v is None: return
    inorder(v.left)    # ← this pushes a frame; execution "goes down left"
    visit(v)
    inorder(v.right)
```

What is the call stack doing? At every point it is holding a list of nodes that have been *reached but not yet visited*. When `inorder(v.left)` is called, v stays on the call stack waiting. When all of v's left subtree is done, execution returns to v, visits it, then descends right.

The call stack's contents at the moment you visit a node v are exactly the **ancestors of v on the path from the root**, waiting to be visited in turn.

If you make this explicit — use your own stack instead of the implicit call stack — you get the iterative inorder algorithm.

---

### Iterative inorder traversal

```python
def inorder_iterative(root):
    result = []
    stack = []
    curr = root

    while curr is not None or stack:
        # Invariant: curr is the next node to descend into;
        # stack holds ancestors waiting to be visited
        while curr is not None:
            stack.append(curr)   # push current node, go left
            curr = curr.left
        curr = stack.pop()       # leftmost unvisited node
        result.append(curr.val)  # VISIT
        curr = curr.right        # now process right subtree

    return result
```

**Tracing on a small tree:**

```
Tree:     2
         / \
        1   3
```

| curr | stack | action |
|------|-------|--------|
| 2 | [] | push 2, go left |
| 1 | [2] | push 1, go left |
| Λ | [2,1] | pop 1, VISIT 1, go right |
| Λ | [2] | pop 2, VISIT 2, go right |
| 3 | [] | push 3, go left |
| Λ | [3] | pop 3, VISIT 3, go right |
| Λ | [] | done |

Output: 1, 2, 3 — sorted order. This is not a coincidence. It is *the* property of inorder traversal on a binary search tree, which Day 37 will exploit fully.

---

### Knuth's threaded trees — traversal without a stack

Knuth goes further. He observes that in any binary tree with n nodes, there are n+1 null pointer fields (proven in Day 13 Exercise 3). These are *wasted space*. What if you used them?

A **threaded binary tree** replaces null LEFT pointers with "threads" pointing to the node's inorder predecessor, and null RIGHT pointers with threads pointing to its inorder successor. A one-bit flag per pointer distinguishes "real child pointer" from "thread."

With threads, you can do inorder traversal with **no stack at all** — just follow threads when a child pointer is null. Cost: O(n) time, O(1) extra space (beyond the tree itself). This is Knuth's Algorithm T (§2.3.1).

For this course we do not implement the threaded version, but knowing it exists tells you something important: the extra space consumed by an explicit stack during traversal is not fundamental — it is a choice, and there is a way to eliminate it.

---

### Level-order traversal (BFS on a tree)

There is a fourth traversal not in the preorder/inorder/postorder family:

**Level-order:** visit all nodes at depth 0, then depth 1, then depth 2, and so on — left to right within each level.

```
Tree:     1
         / \
        2   3
       / \
      4   5

Level-order: 1, 2, 3, 4, 5
```

Implementation: use a **queue** (Day 9), not a stack. Enqueue the root; repeatedly dequeue a node, visit it, and enqueue its children. This is breadth-first search (BFS) on the tree.

Level-order traversal is the right order for: printing a tree level by level, building a heap from an array (Day 29), and certain shortest-path problems.

---

## The formal picture

All four traversals and their data structures:

| Traversal | Visit order | Auxiliary structure | Typical use |
|-----------|-------------|--------------------|----|
| Preorder | root → left → right | Stack (implicit or explicit) | Copy tree, prefix expressions |
| Inorder | left → root → right | Stack (implicit or explicit) | BST sorted order (Day 37) |
| Postorder | left → right → root | Stack (implicit or explicit) | Delete tree, postfix evaluation |
| Level-order | by depth, left to right | Queue | Heap building (Day 29), BFS |

The deep pattern: **DFS traversals use a stack; BFS (level-order) uses a queue.** This is not specific to trees — it is a general principle of graph search.

---

## Where it breaks / what it is not

**Misconception: Recursive traversal is always correct.**  
It is correct for any finite tree. But Python's default recursion limit is 1,000 frames. For a degenerate tree (a sorted list inserted into a BST, producing a chain), the recursive version crashes on n > 1,000. The iterative version or the threaded version handles any depth.

**Misconception: Level-order is just "inorder at each level."**  
No. Level-order visits nodes top-to-bottom by depth, not in left-to-right inorder across the whole tree. For the tree in the example above, inorder gives 4, 2, 5, 1, 3 while level-order gives 1, 2, 3, 4, 5.

**The invariant connection:** Every traversal algorithm has an invariant:
- Inorder iterative: *the stack holds the ancestors of the current position, in root-to-curr order, all awaiting their visit.*
- Level-order: *the queue holds all nodes at the current depth (or the next depth) that have been reached but not yet visited.*

Stating the invariant is how you verify the algorithm is correct — and how you know when to stop.

---

## Try it yourself

**Exercise 1 — Check understanding:** Trace the iterative inorder algorithm on the following tree, showing the stack and `curr` at each step:

```
      4
     / \
    2   6
   / \ / \
  1  3 5  7
```

<details>
<summary>Solution</summary>

Following the algorithm, the visited order is: 1, 2, 3, 4, 5, 6, 7 — sorted ascending, confirming this is a valid BST and inorder gives sorted order.

Trace (abbreviated):
- Push 4→2→1; pop 1 (VISIT), go right (Λ)
- Pop 2 (VISIT), go right to 3
- Push 3; pop 3 (VISIT), go right (Λ)
- Pop 4 (VISIT), go right to 6
- Push 6→5; pop 5 (VISIT), go right (Λ)
- Pop 6 (VISIT), go right to 7
- Push 7; pop 7 (VISIT), done.
</details>

---

**Exercise 2 — Apply:** Implement level-order (BFS) traversal using Python's `collections.deque`. Return the values grouped by level (a list of lists).

<details>
<summary>Solution</summary>

```python
from collections import deque

def level_order(root) -> list[list]:
    if root is None: return []
    result, q = [], deque([root])
    while q:
        level_vals, next_level = [], []
        for _ in range(len(q)):     # process exactly this level's nodes
            node = q.popleft()
            level_vals.append(node.val)
            if node.left:  q.append(node.left)
            if node.right: q.append(node.right)
        result.append(level_vals)
    return result

# For the tree with root 4 above:
# level_order(root) == [[4], [2, 6], [1, 3, 5, 7]]
```

**Invariant:** at the start of each outer-loop iteration, `q` contains exactly the nodes at the *next* level that have been reached but not yet visited.
</details>

---

**Exercise 3 — Stretch:** Given the *inorder* and *preorder* traversals of a binary tree, reconstruct the tree. (This shows that two traversals together uniquely identify a binary tree, even though neither alone does.)

*Inorder:* [4, 2, 5, 1, 3, 6]  
*Preorder:* [1, 2, 4, 5, 3, 6]

<details>
<summary>Hint</summary>
The first element of preorder is always the root. Find it in inorder — everything to its left is the left subtree, everything to its right is the right subtree. Recurse.
</details>

<details>
<summary>Solution</summary>

```python
def build_tree(preorder: list, inorder: list):
    if not preorder: return None
    root_val = preorder[0]
    root = TNode(root_val)
    idx = inorder.index(root_val)        # split point in inorder
    root.left  = build_tree(preorder[1:1+idx],  inorder[:idx])
    root.right = build_tree(preorder[1+idx:],   inorder[idx+1:])
    return root

tree = build_tree([1,2,4,5,3,6], [4,2,5,1,3,6])
print(inorder(tree))    # [4, 2, 5, 1, 3, 6]  ✓
print(preorder(tree))   # [1, 2, 4, 5, 3, 6]  ✓
```

Root = 1. Inorder split: left = [4,2,5], right = [3,6]. Recurse on each half with the corresponding preorder slice.
</details>

---

## Connect it back

You now have the full traversal toolkit: three DFS orders (using a stack, implicit or explicit) and one BFS order (using a queue). The critical insight to carry forward: **inorder traversal of a binary tree with the BST property gives nodes in sorted ascending order.** This is the thread that connects today's traversal algorithms to Day 37's binary search tree and Day 38's AVL tree — the sorted order is not incidental, it is the point.

**Tomorrow:** Memory allocation — how does a program get new nodes to build these structures, and what happens when it is done with them?

**One sharp question you can answer now:**  
*Why does level-order traversal use a queue while DFS traversals use a stack? What would happen if you used a queue for inorder traversal?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:**  
Knuth, *TAOCP* Vol. 1, §2.3.1 pp. 318–331. Read Algorithm T (threaded traversal) on pp. 325–327. You do not need to implement it — read it to appreciate that O(1)-space traversal is possible.

**If you want the deep version:**
- Knuth, *TAOCP* Vol. 1, §2.3.1 "Traversal of right-threaded trees," pp. 322–329 — the full treatment of threading, with Knuth's own cost analysis. The analysis uses the step-counting method from Day 4.

---

## Navigation

← **Previous:** [Day 13 — Binary Trees and Representation](day-13-binary-trees.md)  
→ **Next:** [Day 15 — Memory Allocation](day-15-memory-allocation.md)
