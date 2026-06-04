# Day 11 — Doubly Linked and Circular Lists

> **Today's one idea:** Adding a backwards pointer to every node costs one field per node and buys O(1) deletion at any known position — without needing the predecessor.
> **Reading time:** ~33 min · **Prereqs:** Day 10
> **Primary source:** Knuth, *TAOCP* Vol. 1, §2.2.5 "Doubly Linked Lists" (pp. 280–287, 3rd ed.)

---

## The hook

Yesterday you discovered linked lists' dirty secret: deleting a node at position p requires knowing p's *predecessor* — the node whose LINK points to p. To find that predecessor in a singly-linked list, you must scan from HEAD: O(n).

This is not a theoretical inconvenience. It becomes a real problem in:
- Memory allocators that need to merge adjacent free blocks (you need to find both neighbours)
- Undo/redo systems that need to traverse history in both directions
- Any structure where arbitrary deletion at a known position must be O(1)

The fix is immediate: give every node a second pointer, `PREV`, pointing backwards to its predecessor. Two pointers, O(1) deletion anywhere. The price is one extra field per node.

---

## Building the intuition

### The doubly linked node

Each node now has three fields:

```
+--------+----------+--------+
|  PREV  |   INFO   |  NEXT  |
+--------+----------+--------+
```

`PREV(p)` = address of the node before p.  
`NEXT(p)` = address of the node after p (what was LINK in a singly-linked list).

For the first node, `PREV = Λ`. For the last node, `NEXT = Λ`.

---

### Deletion at a known position

Given only the address p of the node to delete (no predecessor needed):

```
NEXT(PREV(p)) ← NEXT(p)    ← predecessor's forward pointer skips p
PREV(NEXT(p)) ← PREV(p)    ← successor's backward pointer skips p
Free p
```

Two pointer changes. O(1). No search from HEAD.

```
Before:
  ... ← [A: PREV=?, NEXT=p] ↔ [p: PREV=A, NEXT=B] ↔ [B: PREV=p, NEXT=?] → ...

After deleting p:
  ... ← [A: PREV=?, NEXT=B] ↔ [B: PREV=A, NEXT=?] → ...
```

This is the operation that made doubly-linked lists ubiquitous in operating system kernels — Linux's `list_head` structure is exactly this.

---

### Insertion (doubly linked)

Insert a new node q *before* node p:

```
PREV(q) ← PREV(p)
NEXT(q) ← p
NEXT(PREV(p)) ← q
PREV(p) ← q
```

Four pointer assignments — still O(1). Order matters: set q's pointers before modifying p's predecessor's NEXT, so you do not lose the reference.

---

### Circular lists — closing the ring

A **circular list** has the last node's NEXT point back to the first node (and in a doubly-linked circular list, the first node's PREV points to the last). There is no Λ terminator.

```
Singly-linked circular list (3 nodes):

    HEAD
     ↓
  [INFO=7, NEXT=→] → [INFO=14, NEXT=→] → [INFO=3, NEXT=HEAD]
                                                         ↑
                                            loops back here
```

**Why circular?** Two reasons:

1. **Traversal from any starting point:** in a circular list you can start anywhere and visit all nodes by stopping when you return to the start. No need to track HEAD separately.
2. **Efficient deque operations:** with a doubly-linked circular list and a single LIST pointer, you get O(1) insertion and deletion at both ends. This is exactly how Python's `collections.deque` is implemented internally.

---

### The circular doubly-linked list with a header

Knuth's standard configuration: a **header node** whose NEXT points to the first real node and whose PREV points to the last real node. An empty list has the header's NEXT and PREV both pointing to the header itself.

```
Empty list:    HEADER.NEXT = HEADER,  HEADER.PREV = HEADER

One node (x):  HEADER ↔ [x] ↔ HEADER  (it is circular)

Three nodes:   HEADER ↔ [a] ↔ [b] ↔ [c] ↔ HEADER
```

The beauty: the deletion algorithm `NEXT(PREV(p)) ← NEXT(p); PREV(NEXT(p)) ← PREV(p)` works on *any* node in the list, including the first and last, because the header's pointers are always valid neighbours. No special cases. No "is this the head?" checks.

---

## The formal picture

Singly-linked vs. doubly-linked vs. circular — the trade-off map:

| Property | Singly-linked | Doubly-linked | Circular (doubly) |
|----------|--------------|---------------|--------------------|
| Fields per node | 1 pointer | 2 pointers | 2 pointers |
| Traverse forward | O(n) | O(n) | O(n) |
| Traverse backward | ✗ impossible | O(n) | O(n) |
| Delete node at p | O(n) (find predecessor) | O(1) | O(1) |
| Insert before p | O(n) (find predecessor) | O(1) | O(1) |
| O(1) access to last node | ✗ | ✗ | ✓ (via PREV(header)) |
| Empty-list special case | Yes (check HEAD=Λ) | Yes | No (header handles it) |

---

## Where it breaks / what it is not

**Misconception: Doubly-linked lists are always better than singly-linked.**  
They cost twice the pointer storage per node. For long lists of small nodes, this doubles memory. If you never need backwards traversal or O(1) delete-at-position, the extra field is waste.

**Misconception: Circular lists always loop forever.**  
Only if the traversal condition is wrong. The standard termination condition is `p ≠ HEAD` (or `p ≠ HEADER`): stop when you return to the starting point. This is precisely analogous to modular arithmetic — the values wrap around, but the count is finite.

**Misconception: The pointer-update order in deletion does not matter.**  
It does. In `NEXT(PREV(p)) ← NEXT(p)`, you use `PREV(p)` *before* overwriting it. If you change `PREV(p)` first, you lose the address of the predecessor and cannot fix its NEXT pointer.

---

## Try it yourself

**Exercise 1 — Check understanding:** Draw a doubly-linked circular list with header containing values [10, 20, 30]. Show every PREV and NEXT field. Then trace the steps to delete the node containing 20. Show the state after deletion.

<details>
<summary>Solution</summary>

```
Before:
  H.NEXT=A, H.PREV=C
  A.PREV=H, A.INFO=10, A.NEXT=B
  B.PREV=A, B.INFO=20, B.NEXT=C
  C.PREV=B, C.INFO=30, C.NEXT=H

Delete B (p=B):
  NEXT(PREV(B)) ← NEXT(B)  →  NEXT(A) ← C  (A now points forward to C)
  PREV(NEXT(B)) ← PREV(B)  →  PREV(C) ← A  (C now points back to A)

After:
  H.NEXT=A, H.PREV=C
  A.PREV=H, A.INFO=10, A.NEXT=C
  C.PREV=A, C.INFO=30, C.NEXT=H
```
</details>

---

**Exercise 2 — Apply:** Implement a doubly-linked circular list with a header in Python. Provide `insert_before(p, x)`, `delete(p)`, and `to_list()`. Verify: insert 10, 20, 30 (at rear each time), delete 20, result is [10, 30].

<details>
<summary>Solution</summary>

```python
class DNode:
    def __init__(self, info=None):
        self.info = info
        self.prev: 'DNode | None' = None
        self.next: 'DNode | None' = None

class DoublyCircularList:
    def __init__(self):
        self.header = DNode()
        self.header.next = self.header   # empty: points to itself
        self.header.prev = self.header

    def insert_before(self, p: DNode, x) -> DNode:
        """Insert new node with value x immediately before node p."""
        q = DNode(x)
        q.prev = p.prev
        q.next = p
        p.prev.next = q
        p.prev = q
        return q

    def append(self, x) -> DNode:
        """Insert at rear = insert before header."""
        return self.insert_before(self.header, x)

    def delete(self, p: DNode):
        """Delete node p (not the header)."""
        p.prev.next = p.next
        p.next.prev = p.prev

    def to_list(self) -> list:
        result, p = [], self.header.next
        while p is not self.header:
            result.append(p.info)
            p = p.next
        return result

dl = DoublyCircularList()
n10 = dl.append(10)
n20 = dl.append(20)
n30 = dl.append(30)
print(dl.to_list())   # [10, 20, 30]
dl.delete(n20)
print(dl.to_list())   # [10, 30]
```
</details>

---

**Exercise 3 — Stretch:** Implement an LRU (Least Recently Used) cache of capacity k using a doubly-linked circular list + a Python dict. `get(key)` returns the value (or -1 if missing) and moves the accessed node to the front. `put(key, value)` inserts at front; if capacity is exceeded, evict from the rear. All operations O(1). *(This is a classic interview problem whose optimal solution is exactly Knuth's doubly-linked list with a hash map for O(1) node lookup.)*

<details>
<summary>Hint</summary>
Use the dict to map key → node (O(1) lookup). Use the doubly-linked list for O(1) move-to-front and O(1) evict-from-rear.
</details>

<details>
<summary>Solution</summary>

```python
class LRUCache:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.map: dict = {}           # key → DNode
        self.cache = DoublyCircularList()   # reuse from Exercise 2

    def _move_to_front(self, node: DNode):
        self.cache.delete(node)
        self.cache.insert_before(self.cache.header.next, node.info)
        # re-register new node (simpler: store key+val in node)

    def get(self, key: int) -> int:
        # simplified version: rebuild with key stored in node
        ...  # see full implementation below

# Full implementation with (key, val) stored in node:
class KVNode:
    def __init__(self, key=None, val=None):
        self.key, self.val = key, val
        self.prev = self.next = None

class LRUCache2:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.map: dict[int, KVNode] = {}
        self.head, self.tail = KVNode(), KVNode()   # sentinels
        self.head.next, self.tail.prev = self.tail, self.head

    def _remove(self, n: KVNode):
        n.prev.next, n.next.prev = n.next, n.prev

    def _add_front(self, n: KVNode):
        n.next, n.prev = self.head.next, self.head
        self.head.next.prev, self.head.next = n, n

    def get(self, key: int) -> int:
        if key not in self.map: return -1
        n = self.map[key]; self._remove(n); self._add_front(n)
        return n.val

    def put(self, key: int, val: int):
        if key in self.map: self._remove(self.map[key])
        n = KVNode(key, val); self.map[key] = n; self._add_front(n)
        if len(self.map) > self.cap:
            lru = self.tail.prev
            self._remove(lru); del self.map[lru.key]
```

Every `get` and `put` is O(1). The doubly-linked list provides O(1) structural modification; the dict provides O(1) lookup.
</details>

---

## Connect it back

The singly-linked list (Day 10) gave you O(1) insertion after a known node. The doubly-linked list gives you O(1) insertion *and* deletion at any known node. The circular variant with a header eliminates all boundary special-cases. Together they form the complete linked-structure toolkit.

Every tree node you will meet from Day 12 onward is a variation on this node: instead of PREV and NEXT, it will have LEFT and RIGHT child pointers. The manipulation patterns — follow a pointer, update two pointers to insert/delete — are identical.

**Tomorrow:** Trees — the linked list that branches. The most important data structure in all of TAOCP.

**One sharp question you can answer now:**  
*Why does the circular doubly-linked list with a header never require an "is the list empty?" special case in its insert and delete operations?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:**  
Knuth, *TAOCP* Vol. 1, §2.2.5 pp. 280–287. Read Algorithm A (doubly-linked list insertion) and Algorithm B (deletion). The four-line deletion algorithm on p. 281 is one of the most satisfying things in the book.

**If you want the deep version:**
- CLRS, 4th ed., §10.2 "Linked lists" pp. 255–260 — the sentinel/circular treatment. Contrast with Knuth's explicit header to see two equivalent formulations.

---

## Navigation

← **Previous:** [Day 10 — Linked Lists](day-10-linked-lists.md)  
→ **Next:** [Day 12 — Trees — Definitions and Traversal](day-12-trees.md)
