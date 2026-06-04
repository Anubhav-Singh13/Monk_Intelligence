# Day 10 — Linked Lists — The Pointer Idea

> **Today's one idea:** A pointer breaks the tyranny of contiguous memory — linked nodes can live anywhere, and "next" is a value you follow, not an address you calculate.
> **Reading time:** ~38 min · **Prereqs:** Day 8, Day 9
> **Primary source:** Knuth, *TAOCP* Vol. 1, §2.2.3 "Linked Allocation" (pp. 254–279, 3rd ed.)

---

## The hook

Imagine a filing cabinet. The sequential approach says: keep all related documents in adjacent drawers — 1, 2, 3, 4. Finding the nth document is instant: go to drawer n. But inserting a document between drawers 2 and 3 means physically shifting everything from drawer 3 onward one position. For a large cabinet, that is expensive.

Now imagine a different system: each document has a sticky note saying "the next document is in drawer X." The documents live wherever there was a free drawer when they arrived. Finding the nth document means following n sticky notes — slower to reach. But inserting between two documents means changing exactly two sticky notes. No shifting.

That is the linked list. The sticky notes are pointers (LINK fields). The drawers are memory addresses. The entire trade-off — random access vs. cheap insertion — flows from this one structural choice.

---

## Building the intuition

### The node

From Day 8, a linked-list node is a word with two fields:

```
+----------+----------+
|   INFO   |   LINK   |
+----------+----------+
```

`INFO` holds the payload (a number, a character, anything). `LINK` holds the address of the next node, or Λ (null) if this is the last node.

A linked list is a chain of such nodes, accessed through a single pointer `HEAD` that holds the address of the first node.

```
HEAD = 100

Addr  INFO  LINK
 100    7    300   ← first node
 300   14    450
 450    3      0   ← LINK = Λ, last node
```

The list contains: 7 → 14 → 3.

Note what is *not* here: nodes 100, 300, 450 are not adjacent. They happen to be wherever memory was free. The structure is defined entirely by the LINK fields.

---

### Traversal

To visit every node in order:

```python
p = HEAD
while p != Λ:
    visit(INFO(p))
    p = LINK(p)
```

**Invariant:** at the start of each iteration, p is the address of the next unvisited node. When p = Λ, all nodes have been visited.

Cost: O(n) — you touch each node exactly once. No shortcuts; to reach the kth node you must follow k−1 links.

---

### Insertion

**Insert a new node with value x after the node at address p:**

```
1. Allocate a new node at some free address q
2. INFO(q) ← x
3. LINK(q) ← LINK(p)    ← new node points to p's former successor
4. LINK(p) ← q           ← p now points to new node
```

Steps 3 and 4 must happen in this order. Reversing them loses the reference to p's original successor.

```
Before:    ... → [p: INFO=5, LINK=200] → [200: INFO=8, LINK=...] → ...

After inserting x=99 after p:
           ... → [p: INFO=5, LINK=q] → [q: INFO=99, LINK=200] → [200: INFO=8, LINK=...] → ...
```

**Cost: O(1).** Two pointer assignments, regardless of list size. Compare with arrays: inserting in the middle of an n-element array costs O(n) because everything after the insertion point must shift.

---

### Deletion

**Delete the node immediately after the node at address p:**

```
1. t ← LINK(p)           ← t is the node to delete
2. LINK(p) ← LINK(t)     ← skip over t
3. Free the memory at t
```

**Cost: O(1).** One pointer change.

But — and this is critical — **you cannot delete the node at p itself in O(1) given only p**. To change the LINK of p's predecessor, you need a pointer to that predecessor. There is no way to traverse backwards in a singly-linked list. This limitation motivates Day 11's doubly-linked list.

---

### The list header — Knuth's standard trick

Knuth almost always adds a **header node** (also called a sentinel or list head) — a dummy node at the front of the list whose INFO field holds no meaningful data, only a LINK to the first real node.

```
HEAD → [HEADER: INFO=*, LINK=100] → [100: INFO=7, LINK=300] → ...
```

Why? It eliminates special cases. Without a header, inserting at the very front requires special treatment (updating HEAD itself, not a LINK field). With a header, the "insert after node p" algorithm works identically whether p is the header or any other node. The code simplifies; the invariant strengthens.

---

## The formal picture

The three fundamental list operations and their costs:

| Operation | Array (sequential) | Linked list |
|-----------|-------------------|-------------|
| Access element k | O(1) — compute address | O(k) — follow k links |
| Insert after position p | O(n) — shift elements | O(1) — two pointer changes |
| Delete at position p | O(n) — shift elements | O(1) (given predecessor) |
| Search for value x | O(n) | O(n) |
| Memory overhead | None | One LINK field per node |

The trade-off is stark: linked lists sacrifice random access to gain O(1) structural modification.

---

## Where it breaks / what it is not

**Misconception: Linked lists are faster than arrays.**  
For sequential access of all n elements, arrays are often *faster* in practice because modern CPUs are optimised for cache-friendly sequential memory reads. Linked nodes scattered across memory cause cache misses. The theoretical O(1) insertion advantage is real; the cache penalty is also real. Knuth is writing in an era before CPU caches; the trade-off looked different then.

**Misconception: You can delete a node in O(1) given only its address.**  
Only if you have a pointer to its predecessor. A standard singly-linked list requires either (a) a pointer to the predecessor or (b) a linear search from HEAD to find the predecessor. The doubly-linked list (Day 11) solves this.

**Misconception: The header node wastes space.**  
It costs one node. The saving in code clarity — eliminating all "is this the first node?" special cases — is almost always worth it. Knuth uses headers universally.

**COBOL connection:** In COBOL, you sometimes maintained linked structures manually using address pointers (`SET ADDRESS OF ptr TO ...`). The header trick is the same discipline: keep a fixed anchor so that all insertions use the same code path.

---

## Try it yourself

**Exercise 1 — Check understanding:** Given the following linked list (HEAD = 500):

```
Addr  INFO  LINK
 500    1    200
 200    4    350
 350    9      0
```

Trace the steps to insert a new node with INFO=6 *after* the node at address 200. Show the state of the memory table after insertion (assign address 700 to the new node).

<details>
<summary>Solution</summary>

```
Step 1: Allocate node at 700
Step 2: INFO(700) ← 6
Step 3: LINK(700) ← LINK(200) = 350
Step 4: LINK(200) ← 700

Result:
Addr  INFO  LINK
 500    1    200
 200    4    700   ← changed
 700    6    350   ← new
 350    9      0
```

List is now: 1 → 4 → 6 → 9.
</details>

---

**Exercise 2 — Apply:** Implement a singly-linked list in Python with a header node. Provide:
- `insert_after(p, x)` — insert value x after node p
- `delete_after(p)` — delete the node after p
- `to_list()` — return all INFO values as a Python list

Verify: start with an empty list, insert 7, insert 14 after the header, insert 3 after the node containing 14. Result should be [14, 7, 3] — wait, think about order. Then delete the node after the header.

<details>
<summary>Solution</summary>

```python
class Node:
    def __init__(self, info=None, link=None):
        self.info = info
        self.link = link   # Λ = None

class LinkedList:
    def __init__(self):
        self.header = Node()   # header.info is unused

    def insert_after(self, p: Node, x) -> Node:
        """Insert value x after node p. Returns the new node."""
        new_node = Node(x, p.link)
        p.link = new_node
        return new_node

    def delete_after(self, p: Node):
        """Delete the node after p. Returns its INFO value."""
        if p.link is None:
            raise IndexError("No node to delete")
        t = p.link
        p.link = t.link
        return t.info

    def to_list(self) -> list:
        result, p = [], self.header.link
        while p is not None:
            result.append(p.info)
            p = p.link
        return result

ll = LinkedList()
ll.insert_after(ll.header, 7)        # list: [7]
ll.insert_after(ll.header, 14)       # insert after header → [14, 7]
node_14 = ll.header.link             # pointer to the node with 14
ll.insert_after(node_14, 3)          # insert after 14 → [14, 3, 7]
print(ll.to_list())                  # [14, 3, 7]
ll.delete_after(ll.header)           # delete first real node (14) → [3, 7]
print(ll.to_list())                  # [3, 7]
```
</details>

---

**Exercise 3 — Stretch:** Write a function `reverse_linked_list(head)` that reverses a linked list in place in O(n) time and O(1) extra space (no new nodes, no Python list). Use the three-pointer technique (prev, curr, next). State the loop invariant before writing the code.

<details>
<summary>Invariant and solution</summary>

**Invariant:** at the start of each iteration, all nodes from `head` to `prev` have been reversed (their LINK now points toward `head`), and `curr` is the next node to process.

```python
def reverse_linked_list(head: Node | None) -> Node | None:
    """Reverse in place; return new head. Does not use a header node."""
    prev = None
    curr = head
    while curr is not None:
        nxt = curr.link    # save next before overwriting
        curr.link = prev   # reverse the pointer
        prev = curr        # advance prev
        curr = nxt         # advance curr
    return prev            # prev is now the new head
```

At termination: curr = None (end of original list), prev = last original node = new head, and all links point in reverse order.
</details>

---

## Connect it back

Yesterday's stack and queue used arrays — contiguous memory, O(1) access, O(n) insertion. Today's linked list breaks contiguity: O(k) access, O(1) insertion. Neither is universally better; the right choice depends on the operation mix of the algorithm that uses the structure.

You now understand the single most important primitive in TAOCP's information structures chapter. Every structure Knuth builds from Day 11 onward is either an extension of the linked list (doubly-linked, circular, with headers) or a tree (which is a linked list that branches). The atoms — INFO and LINK fields, pointer following, null termination — do not change.

**Tomorrow:** Doubly-linked and circular lists — extending the LINK idea in two directions that buy O(1) deletion at any known position, and the ability to traverse the list both ways.

**One sharp question you can answer now:**  
*Why must you set `LINK(q) ← LINK(p)` before `LINK(p) ← q` when inserting after node p? What happens if you reverse the order?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:**  
Knuth, *TAOCP* Vol. 1, §2.2.3 pp. 254–264. Read the insertion and deletion algorithms (Algorithms A and B) and Knuth's commentary on the header node. Figure 4 (p. 257) shows the pointer-update diagrams you traced in Exercise 1.

**If you want the deep version:**
- CLRS, 4th ed., §10.2 "Linked lists" (pp. 253–260) — a clean modern treatment with sentinel nodes that maps directly to Knuth's header. The sentinel approach in CLRS §10.2 (pp. 257–259) is the same as Knuth's header.

---

## Navigation

← **Previous:** [Day 9 — Stacks and Queues](day-09-stacks-and-queues.md)  
→ **Next:** [Day 11 — Doubly Linked and Circular Lists](day-11-doubly-linked-circular.md)
