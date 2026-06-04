# Day 8 — Numbers and Information — How Data Is Represented

> **Today's one idea:** Every data structure Knuth builds rests on a concrete foundation: a word of memory with named sub-fields. Understanding bytes, words, and fields is understanding the atoms of all information structures.
> **Reading time:** ~32 min · **Prereqs:** Day 4
> **Primary source:** Knuth, *TAOCP* Vol. 1, §2.1 "Introduction to Information Structures" (pp. 232–246, 3rd ed.)

---

## The hook

In your COBOL career, you declared data items with `PIC` clauses — `PIC 9(5)`, `PIC X(20)`. You specified exactly how many bytes each field occupies and what type of data it holds. This was not just syntax; it was a direct description of how the data sits in memory.

Knuth has the same instinct, taken to a logical extreme. Before describing any data structure — lists, trees, tables — he first asks: *how does a single node of that structure sit in memory?* What bits does it occupy? What sub-fields does it contain? How much space does each pointer take?

Today you learn Knuth's way of describing these low-level building blocks. It is the bridge from the abstract mathematics of Days 1–7 to the concrete structures of Days 9–16.

---

## Building the intuition

### The word as a container

In MIX (from Day 4), a **word** is 5 bytes plus a sign — 31 bits of usable storage. Knuth uses a word as the basic unit for a *node*: one record in a data structure.

A word can be divided into **fields** — named sub-sections, each holding one piece of information. Think of a COBOL record:

```cobol
01 NODE-RECORD.
   05 INFO     PIC 9(4).
   05 LINK     PIC 9(6).
```

Knuth writes the same idea as a field specification: `(L:R)` means bytes L through R of the word. A node in a linked list might have:

```
Byte positions:  0  1  2  3  4  5
                +--+--+--+--+--+--+
                |  INFO   |  LINK  |
                +--+--+--+--+--+--+
                  (1:3)     (4:5)
```

- Field `INFO` = bytes 1–3: the data payload (could be an integer, a character code, etc.)
- Field `LINK` = bytes 4–5: a memory address pointing to the next node

This is the physical reality of every linked list node you will encounter in Module 2.

---

### Characters and character codes

Before Unicode, characters were numbers. In MIX's era, a byte held one character from a 64-character alphabet — letters, digits, and punctuation. A word could hold exactly 5 characters.

The key point: *there is no fundamental distinction between a "number" and a "character" in memory*. Both are bit patterns. What makes an integer an integer and a character a character is how you *interpret* those bits — what operation you apply to them.

This is the root of every type system in computing. Knuth makes it explicit; most modern languages hide it behind abstractions.

---

### Addresses and pointers — a first look

A **pointer** (Knuth uses the word *link*) is just a word that contains a *memory address* — the location of another word. An address is a non-negative integer.

```
Memory layout:

Address  Contents
  100    [ INFO=42 | LINK=200 ]  ← node A
  ...
  200    [ INFO=17 | LINK=350 ]  ← node B
  ...
  350    [ INFO=99 | LINK=000 ]  ← node C (LINK=0 means "no next node")
```

The three nodes form a linked list: A → B → C. Following the chain means reading the LINK field of the current word to find the address of the next word, then reading *that* word's LINK, and so on.

Notice: the nodes are *not* adjacent in memory. They are at addresses 100, 200, 350 — wherever memory happened to be available. This is the fundamental freedom that pointers buy, and it is exactly what distinguishes a linked list from an array.

---

### The special value zero (Λ)

Knuth uses **Λ** (lambda, but really meaning "null") for a pointer that points to nothing — the end of a chain, or an absent value. It corresponds to memory address 0, which Knuth reserves as a sentinel (no actual node lives there).

In Python this is `None`. In C it is `NULL`. In COBOL it might be `LOW-VALUE` in a pointer field. The concept is universal: every linked structure needs a way to say "there is no next node here."

---

## The formal picture

The vocabulary you need for Module 2:

| Term | Symbol | Meaning |
|------|--------|---------|
| Word | W | The basic storage unit; one node fits in one word |
| Field | `(L:R)` | Bytes L through R of a word |
| Pointer / Link | `LINK(X)` | The address stored in the LINK field of node X |
| Info | `INFO(X)` | The data payload stored in node X |
| Null pointer | Λ | "No node here"; address 0 |
| Node at address p | `NODE(p)` | The word stored at memory address p |

**Knuth's notation in practice:** When he writes $\text{INFO}(P)$ he means "the INFO field of the node whose address is stored in variable P." When he writes $\text{LINK}(P) \leftarrow Q$ he means "store the address Q into the LINK field of the node at address P." This is pointer assignment.

---

## Where it breaks / what it is not

**Misconception: Pointers are dangerous and low-level — modern languages don't use them.**  
Every object reference in Python, Java, and C# is a pointer. The language runtime manages the memory for you, but the underlying mechanism is identical: a variable holds an address, and following a reference means jumping to that address. Knuth's explicit notation makes the hidden mechanism visible.

**Misconception: The LINK field and the INFO field must be the same size.**  
They need not be. A LINK field needs to hold a memory address (so it must be large enough for the address space). An INFO field holds whatever data the structure stores. Knuth chooses field widths based on the problem.

**Misconception: Λ (null) is a special magic value.**  
It is just the convention that address 0 means "no node." The MIX machine never stores a real node at address 0, so any LINK field containing 0 is unambiguously "end of chain." Modern languages enforce this in the type system; Knuth enforces it by convention.

**COBOL parallel:** The `SET ADDRESS OF pointer TO NULL` statement in COBOL is exactly Λ. A `POINTER` data item in COBOL is exactly Knuth's LINK field. The concepts are identical; the notation differs.

---

## Try it yourself

**Exercise 1 — Check understanding:** Draw the memory layout (as an ASCII table like the one above) for a linked list containing the values 7, 3, 15 in that order. Choose arbitrary starting addresses (e.g., 500, 600, 700). Show the INFO and LINK fields of each node. What is the LINK value of the last node?

<details>
<summary>Solution</summary>

```
Address  INFO  LINK
  500      7    600    ← head of list
  600      3    700
  700     15      0    ← LINK=0 (Λ) = end of list
```

The LINK of the last node is 0 (Λ). A variable `HEAD` pointing to 500 gives access to the whole list.
</details>

---

**Exercise 2 — Apply:** You are given the following memory state. Starting from address 300, trace the linked list and list its INFO values in order.

```
Address  INFO  LINK
  100     42    000
  200      8    100
  300     55    200
  400     17    300  (this node is not reachable from 300)
```

<details>
<summary>Solution</summary>

Start at 300: INFO=55, LINK=200  
→ 200: INFO=8, LINK=100  
→ 100: INFO=42, LINK=000 (Λ, stop)

**Values in order: 55, 8, 42.**  
Node at address 400 is orphaned — not reachable from 300.
</details>

---

**Exercise 3 — Stretch:** In Python, a linked list node can be represented as a simple object:

```python
class Node:
    def __init__(self, info, link=None):
        self.info = info
        self.link = link   # None = Λ
```

Write a function `list_to_chain(values)` that takes a Python list and returns the HEAD node of a linked chain. Then write `chain_to_list(head)` that recovers the original list. Verify: `chain_to_list(list_to_chain([7, 3, 15])) == [7, 3, 15]`.

<details>
<summary>Solution</summary>

```python
class Node:
    def __init__(self, info, link=None):
        self.info = info
        self.link = link

def list_to_chain(values: list) -> Node | None:
    head = None
    for v in reversed(values):   # build right-to-left so head is first
        head = Node(v, head)
    return head

def chain_to_list(head: Node | None) -> list:
    result = []
    p = head
    while p is not None:         # while p ≠ Λ
        result.append(p.info)
        p = p.link
    return result

assert chain_to_list(list_to_chain([7, 3, 15])) == [7, 3, 15]
```

`chain_to_list` is your first linked-list traversal. The invariant: at the start of each iteration, `result` contains the INFO values of all nodes from HEAD to the node before p.
</details>

---

## Connect it back

Days 1–7 gave you analysis tools. Today gave you the physical substrate that all structures live in: words made of fields, fields holding either data or pointers, and the null pointer Λ marking the edge of every structure.

Starting tomorrow, every data structure you encounter is built from exactly these pieces. A stack (Day 9) is an array of words. A linked list (Day 10) is a chain of word-nodes connected by LINK fields. A tree (Day 12) is a node with two LINK fields — left and right. The complexity explodes, but the atoms do not change.

**Tomorrow:** Stacks and queues — the two simplest dynamic structures, and the foundation for understanding why linked allocation exists at all.

**One sharp question you can answer now:**  
*What is the difference between a variable that holds an integer and a variable that holds a pointer, at the level of memory?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:**  
Knuth, *TAOCP* Vol. 1, §2.1 "Introduction to Information Structures," pp. 232–246. Pay attention to Figure 1 (pp. 233–234) — Knuth's own diagram of a word divided into fields. It maps directly to this page.

**If you want the deep version:**
- Knuth, *TAOCP* Vol. 1, §1.3.2 "The MIX Assembly Language" (pp. 153–168) — if you want to see how field specifications `(L:R)` work in actual MIX instructions. Optional for L1 but satisfying if Day 4 left you wanting more concreteness.

---

## Navigation

← **Previous:** [Day 7 — Rest & Synthesize I](day-07-rest-synthesize-1.md)  
→ **Next:** [Day 9 — Stacks and Queues](../../02-information-structures/days/day-09-stacks-and-queues.md)
