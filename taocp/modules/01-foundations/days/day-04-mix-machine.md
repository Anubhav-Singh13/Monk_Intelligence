# Day 4 — How Knuth Describes Machines

> **Today's one idea:** Knuth's MIX/MMIX is a fictional but precise computer — a yardstick that lets him say "this step costs exactly 2 units" without depending on any real hardware. You need just enough to read the cost labels, not to write MIX programs.
> **Reading time:** ~30 min · **Prereqs:** Day 3
> **Primary source:** Knuth, *TAOCP* Vol. 1, §1.3 "MIX" (pp. 120–152, 3rd ed.) — read §1.3.1 pp. 120–132 only today.

---

## The hook

Here is a question Knuth needs to answer: "How many operations does this sorting algorithm perform?"

If the answer is "it depends on the computer," the whole comparison falls apart. An algorithm that looks twice as fast on one machine might look slower on another.

His solution: invent a computer. A completely specified, fictional computer whose every instruction has an exact cost. Analyse algorithms on *this* machine, and all comparisons become machine-independent. Anyone who wants to translate to real hardware can apply a constant factor.

MIX is that fictional computer. MMIX is its 64-bit RISC update (defined in a 2005 supplement). You will meet them every time Knuth writes "this instruction requires 2u" or draws a register diagram.

The goal today is not to become a MIX programmer. It is to decode Knuth's cost labels when you see them.

---

## Building the intuition

### The anatomy of MIX

MIX is a byte-addressable machine from a mid-1960s design aesthetic — because that is when Knuth designed it. Think of a mainframe with:

```
Registers:
  rA  — the Accumulator (5 bytes + sign = 31 bits)
  rX  — the Extension register (same size; rA and rX work together for multiplication)
  rI1–rI6  — six Index registers (2 bytes + sign; used as array offsets)
  rJ  — the Jump register (stores return address)

Memory:
  4,000 words, each 5 bytes + sign

Instructions:
  Each instruction = 1 word = opcode (1 byte) + address (2 bytes) + index (1 byte) + field (1 byte)
```

Each **word** in MIX is 5 bytes plus a sign bit. Knuth describes a byte as an unspecified number of bits (at least 6), to keep the analysis byte-count-independent.

---

### The unit of cost: "u"

Knuth measures time in **u** — one "unit" of time, corresponding roughly to the cost of a simple memory access. Different instructions have different costs:

| Instruction type | Cost (u) | Example |
|-----------------|---------|---------|
| Load / Store | 2 | `LDA` (load into rA) |
| Add / Subtract | 2 | `ADD` |
| Multiply | 10 | `MUL` |
| Divide | 12 | `DIV` |
| Jump (taken) | 1 | `JMP` |
| No-op | 1 | `NOP` |

These numbers are not precise predictions for any real hardware — they are a *consistent scale*. When Knuth says Algorithm X takes $12n + O(\log n)$ units, the 12 and the $O(\log n)$ are computed on MIX. The shape ($O(n)$) is what matters for comparison; the 12 is context.

---

### Reading a MIX instruction trace

Here is how Knuth might present the inner loop of a simple algorithm:

```
Step  Instruction  Cost   Meaning
1     LDA  X,i      2u    Load A[i] into register rA
2     CMPA Y,j      2u    Compare rA with Y[j]
3     JL   done     1u    Jump if less-than
4     STA  Y,j      2u    Store rA back into Y[j]
      Total per iteration: 7u (if no jump taken)
```

When the total cost of a loop that runs n times is "$7n$ u," Knuth says the algorithm takes $7n$ u in the worst case. He then applies Day 3's asymptotic notation: $7n = O(n)$.

---

### MMIX — the modern replacement

Knuth realised in the 1990s that MIX's 1960s byte-and-word design was showing its age. He designed **MMIX** — a 64-bit RISC architecture in the spirit of modern CPUs (closer to MIPS or SPARC). MMIX has:
- 256 general-purpose 64-bit registers
- A cleaner instruction set
- Knuth has been retrofitting later volumes with MMIX; Vol. 1's supplement (freely available from Knuth's website) contains MMIX replacements for many programs.

For this course you do not need to know MMIX in detail. When you see "MMIX" in later volumes, read it as "the modern version of the same yardstick."

---

## The formal picture

The only MIX facts you need to retain:

```
MIX word structure:
  +/- | byte₁ | byte₂ | byte₃ | byte₄ | byte₅

Field specification (F):
  Selects a sub-range of bytes, written (L:R)
  e.g., (0:5) = full word, (1:5) = bytes 1-5 (no sign), (4:5) = last 2 bytes

Instruction format:
  OP  ADDRESS,INDEX(FIELD)
  e.g.,  LDA  2000,3(1:3)  = load bytes 1-3 of M[2000 + rI3] into rA
```

When reading Knuth's algorithm listings, you can treat each labelled step as pseudocode and the cost annotation (e.g., `2u`) as a step-count annotation. The analysis then adds up costs multiplied by how many times each step executes.

---

## Where it breaks / what it is not

**Misconception: You need to learn MIX assembly to follow TAOCP.**  
No. Knuth provides English descriptions alongside every MIX program. For L1 readers, the English description is the algorithm; the MIX code is the proof that it is precise. Read the description; glance at the code to check your understanding; trust the cost labels.

**Misconception: MIX costs predict real performance.**  
They do not. Modern CPUs have caches, pipelines, branch predictors, and out-of-order execution. A $O(n^2)$ algorithm is still $O(n^2)$ on MIX and on a modern CPU — the *shape* is preserved — but the hidden constants can differ dramatically.

**Misconception: MMIX replaced MIX everywhere.**  
Not yet. Volumes 1–3 use MIX; only selected reprints and Vol. 4 use MMIX. The supplement at Knuth's website (cs.stanford.edu/~knuth/mmix.html) provides translations.

---

## Try it yourself

**Exercise 1 — Check understanding:** A MIX loop executes the following instructions per iteration:
- 1 `LDA` (2u)
- 1 `ADD` (2u)
- 1 `STA` (2u)
- 1 `JMP` (1u)
- 1 comparison `CMPA` (2u)
- 1 conditional jump `JNE` (1u, not taken)

If the loop runs n times and the final conditional jump (taken) costs 1u, what is the total cost of the loop in MIX units? Express as a function of n.

<details>
<summary>Solution</summary>

Per iteration (not counting the last taken jump): 2+2+2+1+2+1 = 10u  
n iterations: 10n u  
Final taken jump: 1u  
**Total: 10n + 1 u = O(n)**
</details>

---

**Exercise 2 — Apply:** Look at Knuth's Algorithm E from Day 1. Knuth's full analysis in §1.1 shows the average number of times step E1 executes is $O(\log n)$. If step E1 costs 2u and the other steps together cost 5u per iteration, what is the average-case total cost? What is the asymptotic class?

<details>
<summary>Solution</summary>

Per iteration: E1 (2u) + rest (5u) = 7u  
Average iterations: O(log n)  
**Total average cost: 7 · O(log n) = O(log n)**  
Asymptotic class: O(log n) — logarithmic.
</details>

---

## Connect it back

On Day 3, "O(log n)" was an abstract shape. Today it became concrete: Algorithm E runs an average of $c \log n$ MIX instructions, for a specific computable constant c. You can now read any line in TAOCP that says "this takes 2u" and understand that it is a precise cost on a defined scale, not an approximation.

**Tomorrow:** Proof by induction and loop invariants — the technique that lets Knuth (and you) *prove* that an algorithm does what it claims to do, without testing every input.

**One sharp question you can answer now:**  
*Why does Knuth define his own fictional computer rather than analysing algorithms on a real one?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:**  
Knuth, *TAOCP* Vol. 1, §1.3.1 pp. 120–132. Skim the instruction table (Table 1) — do not memorise it, just notice the cost column. Read the prose descriptions of rA, rX, and the index registers.

**If you want the deep version:**
- Knuth's MMIX website: cs.stanford.edu/~knuth/mmix.html — the MMIX supplement PDF, which contains the full MMIX instruction set and "MMIX: A RISC Computer for the New Millennium" paper. Read the introduction (pp. 1–5) for Knuth's own justification for designing a new machine.

---

## Navigation

← **Previous:** [Day 3 — Asymptotic Analysis](day-03-asymptotic-analysis.md)  
→ **Next:** [Day 5 — Proof by Induction and Loop Invariants](day-05-induction-and-invariants.md)
