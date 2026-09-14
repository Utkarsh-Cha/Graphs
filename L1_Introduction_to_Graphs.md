# Graph Series — Lecture 1: Introduction to Graphs
### Types of Graphs & Conventions Used

> **Purpose of this lecture:** This is the very first video of the Graph series. Before solving any graph problem, you must know *what kinds of graphs exist* and *what vocabulary/conventions* will be used in every subsequent lecture. Every term introduced here (node, edge, cycle, path, degree, weight) will keep reappearing in later problems, so this is foundational vocabulary, not optional theory.

---

## 1. What is a Graph?

A **graph** is a structure made of two things, and *only* these two things are required:

1. **Nodes** (also called **vertices**)
2. **Edges** (lines connecting those nodes)

That's it. This is the **minimal condition** for something to be called a graph.

### Important clarification (a very common misconception)

Beginners often assume a graph must be a **closed / enclosed / circular** structure — some loop that comes back on itself. **This is wrong.**

- A graph does **not** have to be an enclosed structure.
- A graph can be an **open** structure too.

**Proof by example given in the lecture:** A **binary tree** is also a graph!

```
            o
           / \
          o   o
         / \ / \
        o  o o  o
```

Why is a binary tree a graph? Because it satisfies all the rules:
- It has nodes ✔
- It has edges ✔

Nothing else is required. So a binary tree is a perfectly valid graph. (On screen, the instructor literally wrote the word `enclosed` and **crossed it out in red** to kill this misconception.)

So remember:

| Claim | True/False |
|---|---|
| A graph must form a circle/loop | ❌ False |
| A graph needs nodes and edges | ✅ True |
| A tree can be called a graph | ✅ True |
| A graph can be an open structure | ✅ True |

---

## 2. Nodes / Vertices

- The **circular things** you draw in a graph are called **nodes** or **vertices**. Both words mean the same thing and are used interchangeably.
- Nodes are **represented by numbers** — e.g. 1, 2, 3, 4, 5.

### ⚠️ Key convention: numbering has NO fixed order

> **There is no specific order in which you number the nodes. The numbering can be in any order.**

You can label the top-left node as `1` and the bottom node as `3`, or label them in any arbitrary way. The numbering is just an *identity/label*, not a position or a ranking. Don't assume node 1 is "first" in any spatial or hierarchical sense.

### Notation for the number of nodes

If a graph has 5 circular nodes, we write:

```
N = 5        (number of nodes)
V = 5        (number of vertices)
```

- **N** → number of **n**odes
- **V** → number of **v**ertices

Both notations are used in problem statements, so be comfortable with either.

---

## 3. Edges

An **edge** is the line segment that **connects two nodes**.

In the first example graph, nodes `1` and `4` are connected by a horizontal line — that horizontal line is the **edge**.

Edges come in two flavours, and this is exactly what splits graphs into the two major types.

---

## 4. Type 1 — Undirected Graph

### Diagram (as drawn on screen)

```
        1 ─────────── 4
        │             │
        │             │
        2 ─────────── 5
         \           /
          \         /
           \       /
              3
```

*(5 nodes: 1 top-left, 4 top-right, 2 middle-left, 5 middle-right, 3 bottom-centre. Edges: 1–4, 1–2, 4–5, 2–5, 2–3, 5–3 — plain line segments with **no arrows**.)*

### Definition

A graph where the edges have **no direction** (no arrowheads) is an **undirected graph**.

The edge connecting two nodes is called an **undirected edge**.

### Why is it called "undirected"? (the reasoning)

Consider the edge between node `1` and node `4`:
- You can travel from **1 → 4**, and
- You can also travel from **4 → 1**

The edge works in **both directions**. It is effectively a **bidirectional edge**.

### 🔑 The critical convention you must remember

> Whenever you see an **undirected graph**, and the problem says *"there is an edge between u and v"*, it means **both** of the following are true:
>
> ```
> u → v    (you can go from u to v)
> v → u    (you can go from v to u)
> ```

This single line is written on screen as `u - v`, `u → v`, `v → u`. This convention is *hugely* important later — when you build an adjacency list for an undirected graph, **one input edge `u v` must be stored twice** (once in u's list and once in v's list), precisely because of this rule.

### Terminology collected so far for this graph

- node / vertex
- undirected edge
- undirected graph

---

## 5. Type 2 — Directed Graph

### Diagram (as drawn on screen)

```
        1 ──────────▶ 4
        │             ▲
        ▼             │
        2 ──────────▶ 5
         \           /
          ▼         ▼
           \       /
              3
```

*(Same 5-node shape, but every connector now has an **arrowhead**. Edges drawn: 1 → 4, 1 → 2, 5 → 4, 2 → 5, 2 → 3, 5 → 3.)*

### Definition

> A **directed graph** is a graph where **all the edges are directed**.

Mind that word **"all"** — the instructor emphasised it. It is not "some edges"; in a directed graph, every single edge carries a direction.

The connector itself is called a **directed edge**.

### What changes compared to undirected?

**Only the edge type changes. Everything else stays exactly the same** (nodes are still nodes, vertices still vertices, etc.).

### The behavioural difference (the reasoning)

If there is a directed edge `1 → 4`:
- If you are standing at **1**, you **can** go to **4**.
- If you are standing at **4**, you **cannot** come back to **1**.

That one-way restriction is the entire point of a directed graph.

### Can a directed graph still have two-way movement?

**Yes.** You are allowed to draw *two separate directed edges* between the same pair:

```
        1 ──────▶ 4
        1 ◀────── 4
```

i.e. an edge `1 → 4` **and** an edge `4 → 1`. This gives you bidirectional movement, but note the important conceptual difference:

| | Undirected graph | Directed graph |
|---|---|---|
| One edge `u–v` | Automatically means both `u→v` and `v→u` | N/A |
| Two-way movement | Free, implied by a single edge | Must be **explicitly** drawn as two separate directed edges |

---

## 6. Summary of Graph Types (so far)

| Type | Edge look | Meaning of an edge between u and v |
|---|---|---|
| **Undirected Graph** | plain line, no arrow | movement allowed both ways (u→v and v→u) |
| **Directed Graph** | arrow-headed line | movement allowed only in the arrow's direction |

---

## 7. Cycles in a Graph

### Definition of a Cycle

> **A cycle means: you start from a node, and via some path you reach back to that same node.**
>
> On screen: `start from a node & end at that node`

Both the start point and the end point must be the **same node**.

### Example — cycle in an undirected graph

Using the undirected graph from earlier:

```
        1 ─────────── 4
        │             │
        │             │
        2 ─────────── 5
         \           /
           \       /
              3
```

Is there a cycle? **Yes.**

**Dry run:** Start at `1` → go to `2` → go to `5` → go to `4` → come back to `1`. You started at 1 and ended at 1 ⇒ **cycle exists**.

### There can be **multiple** cycles

The lecture explicitly points out that this graph contains **more than one** cycle. Other cycles present:
- `2 → 5 → 3 → 2` (the lower triangle)
- `1 → 2 → 3 → 5 → 4 → 1` (the bigger loop around)

So a graph can contain many cycles simultaneously.

### Naming rule

> If there is **even a single cycle** in an undirected graph, we call it an **Undirected Cyclic Graph**.

**Important nuance spelled out in the lecture:**
The plain word *"graph"* does **not** assume any enclosed structure. But **the moment you attach the word "cyclic"** to it, you are now asserting that a closed loop exists inside it. So:

- "graph" → says nothing about loops
- "cyclic graph" → guarantees at least one loop
- "acyclic graph" → guarantees **no** loop at all

---

## 8. Cycles in Directed Graphs — DAG

Now take the **directed** version of the same structure:

```
        1 ──────────▶ 4
        │             ▲
        ▼             │
        2 ──────────▶ 5
         \           /
          ▼         ▼
           \       /
              3
```

### Is there a cycle here? — **No.**

**Reasoning (dry run over each candidate loop):**
- Start at `1`: 1 → 2 → 5 → 4 … and then you are stuck. There is no edge from 4 back to 1 (the arrow is `1 → 4`, not `4 → 1`). So you **start at 1 but never end at 1**.
- Start at `2`: 2 → 5 → 3, and then stop. 3 has no outgoing edge back to 2.
- Any other starting node: same story — you start somewhere but never return there.

Because you never start at a node and return to that node **via some path**, this graph has **no cycle**.

### Naming

> **Directed Acyclic Graph (DAG)**
> *Acyclic* = "no cycle".

This is abbreviated **DAG**, and the instructor notes you will hear this term constantly in later lectures (topological sort, shortest path in DAG, etc.). Memorise the acronym.

### Turning a DAG into a cyclic directed graph

If you **add just one more edge** — for example an edge going from `4` back to `1`:

```
        1 ──────────▶ 4
        ▲             │
        │ ◀───────────┘      (new edge 4 → 1)
```

…then `1 → 2 → 5 → 4 → 1` becomes a genuine cycle, and the graph is now called a **Directed Cyclic Graph**.

**So:**

| Condition | Name |
|---|---|
| Directed + at least one cycle | Directed Cyclic Graph |
| Directed + no cycle at all | **Directed Acyclic Graph (DAG)** |
| Undirected + at least one cycle | Undirected Cyclic Graph |

---

## 9. Path

### Definition

> **A path contains a lot of nodes (vertices), and each of them is reachable.**
>
> On screen: `Path → Contain a lot of nodes and each of them are reachable`

The word **reachable** is the critical part. Two rules come out of this definition.

### Rule 1 — A node cannot appear twice in a path

> **A node cannot appear twice in a path. It can appear only once.**

### Rule 2 — Adjacent nodes in the path must have an edge between them

You cannot "jump" between two nodes that aren't directly connected. Every consecutive pair written in the path must be joined by an actual edge, otherwise you physically cannot travel along it.

### Worked example (exact example from the lecture)

Graph used:

```
        1 ─── 2 ─── 3 ─── 4
                    │
                    5
```

*Nodes:* 1, 2, 3, 4, 5
*Edges:* `1–2`, `2–3`, `3–4`, `3–5`

(Note: this is an **open structure** with no loop — and it is still a valid graph, since it has nodes and edges.)

#### ✅ Example A: `1 2 3 5` — **Valid path**

Check the consecutive pairs:
- 1 → 2: edge `1–2` exists ✔
- 2 → 3: edge `2–3` exists ✔
- 3 → 5: edge `3–5` exists ✔
- No node repeats ✔

So you can genuinely walk 1 → 2 → 3 → 5. **This is a path.**

#### ❌ Example B: `1 2 3 2 1` — **NOT a path**

Reason: you write 1, 2, 3, and then **2 again** (and then 1 again). Node `2` appears **twice**.

On screen this was crossed out with an **X** and annotated `node x 2` (meaning "node appears 2 times").

> Even though you *could* physically walk back along those edges in an undirected graph, by **definition** a path forbids repeating a node. This is a definitional restriction, so don't argue with the traversal — just remember the rule.

#### ❌ Example C: `1 3 5` — **NOT a path**

Reason: how do you get from `1` to `3`? There is **no edge between 1 and 3** — there is no direct connectivity. So the jump `1 → 3` is impossible.

On screen this was also crossed out with an **X**.

### Checklist for validating a path (interview-useful)

| Check | Question to ask |
|---|---|
| 1 | Does every consecutive pair in my sequence have a real edge between them? |
| 2 | Does any node appear more than once? (If yes → not a path) |

---

## 10. Degree of a Node — Undirected Graph

### Definition

> **The degree of a node is the number of edges attached to it** — counting edges going "inside" it or "outside" it, i.e. the **total** number of edges incident on that node.

Notation used on screen: `D(node)`.

### Example graph (as used in the lecture)

```
        1 ─────────── 4
        │             │
        │             │
        2 ─────────── 5
         \           /
           \       /
              3
```

### Degree computation for every node

| Node | Edges attached to it | Degree |
|---|---|---|
| 1 | 2 edges | **2** |
| 2 | 2 edges | **2** |
| 3 | 3 edges | **3** |
| 4 | 2 edges | **2** |
| 5 | 3 edges | **3** |

Written on screen:
```
D(3) → 3
D(4) → 2
```

Node `3` has three edges touching it, so its degree is 3. Node `4` has two edges touching it, so its degree is 2.

---

## 11. ⭐ Property: Total Degree = 2 × Number of Edges

This is an important, frequently-asked property — the instructor explicitly says *"please take care of this property."*

### Statement

```
Total Degree of a Graph = 2 × E
```

where `E` = number of edges in the graph.

### Intuition / WHY it works (this is the part to actually understand)

> **Every edge is associated with (connected to) exactly two nodes.**

So when you compute degrees node-by-node and sum them up, **each edge gets counted twice** — once at the node on one end, and once at the node on the other end. Therefore the total of all degrees must be exactly double the number of edges.

This is not a coincidence or a formula to memorise blindly — it falls directly out of the fact that an edge has two endpoints.

### Verification / dry run (done live in the lecture)

**Step 1 — Sum all degrees:**

```
2 + 2 + 3 + 2 + 3
= 4  (2+2)
= 7  (+3)
= 9  (+2)
= 12 (+3)
```

Total degree = **12**

**Step 2 — Count the edges:**

Edges are: `1–4`, `1–2`, `4–5`, `2–5`, `2–3`, `5–3` → counting: 1, 2, 3, 4, 5, 6

Number of edges **E = 6**

**Step 3 — Check the property:**

```
12 = 2 × 6   ✔
```

Written on screen exactly as:
```
property
Total Degree of a Graph = 2 x E

12 = 2 x 6
```

The property is satisfied. ✅

### Useful corollary (worth noting for problems)

Since `Total Degree = 2E`, the total degree of any undirected graph is **always an even number**.

---

## 12. Degree in a Directed Graph — Indegree & Outdegree

In a **directed graph**, the single notion of "degree" splits into **two different things**, because edges now have direction:

1. **Indegree(node)** — the number of **incoming** edges (edges whose arrow points *into* the node)
2. **Outdegree(node)** — the number of **outgoing** edges (edges whose arrow points *away from* the node)

On screen:
```
Directed Graph → InDegree (node)
                 OutDegree (node)
```

### Worked example (as drawn on screen)

A 5-node directed graph arranged in 3 layers. Focus on node number **3** (middle-right):

- Two edges come **into** node 3 (one from the top-right node, one from the middle-left node).
- One edge goes **out of** node 3 (down to the bottom node).

```
        ●             ●
         \           /        ← two arrows pointing INTO node 3
          ▼         ▼
             ( 3 )
               │
               ▼              ← one arrow pointing OUT of node 3
              ●
```

**Therefore:**

```
InDegree (3)  = 2      (number of incoming edges)
OutDegree (3) = 1      (number of outgoing edges)
```

### Summary table

| Graph type | Degree concept |
|---|---|
| Undirected | One value: **degree** = total edges attached |
| Directed | Two values: **indegree** (edges coming in) and **outdegree** (edges going out) |

---

## 13. Edge Weights

### What is an edge weight?

Every **edge** (not node — the instructor corrects himself mid-sentence: *"every node, every **edge** rather"*) can carry a **weight** — a number assigned to it.

In problems, the statement might say things like: *"this edge has weight 3, this edge has weight 2, this one 1, this one 5…"* and so on.

### On-screen example

An 8-node connected graph grid was drawn, with weights written on the edges:

```
Weights shown on edges: 3, 2, 1, 5, 6, 2, 1, 5, 7
```

**Any weight can be assigned to any edge.** There is no restriction or pattern — the problem setter decides.

Conceptually, a weight usually represents something like a **cost, distance, or time** to travel along that edge (this becomes central in shortest-path algorithms later).

### ⚠️ Very important convention: what if no weights are given?

> **If the weights are not assigned / not mentioned in the problem, we always assume unit weights, i.e. weight = 1 for every edge.**

On screen:
```
Unit Weight → 1
```

This is a standing convention for the whole series. So an "unweighted graph" is really just a graph where every edge silently has weight `1`. This is why, for example, in an unweighted graph the shortest path is simply the one with the fewest edges.

---

## 14. Complete Terminology Recap

| Term | Meaning |
|---|---|
| **Node / Vertex** | The circular element of a graph; labelled with a number, in any arbitrary order |
| **N / V** | Number of nodes / vertices in the graph |
| **Edge** | The line connecting two nodes |
| **E** | Number of edges |
| **Undirected edge** | Edge usable in both directions (`u→v` and `v→u`) |
| **Directed edge** | Edge usable in one direction only (arrowhead shows which) |
| **Undirected Graph** | Graph whose edges are all undirected |
| **Directed Graph** | Graph where **all** edges are directed |
| **Cycle** | Start from a node and end back at that same node via some path |
| **Undirected Cyclic Graph** | Undirected graph containing at least one cycle |
| **Directed Cyclic Graph** | Directed graph containing at least one cycle |
| **Acyclic Graph** | Graph with **no** cycle |
| **DAG** | **D**irected **A**cyclic **G**raph — directed graph with no cycle |
| **Path** | Sequence of nodes where consecutive nodes share an edge and **no node repeats** |
| **Degree (undirected)** | Number of edges attached to a node |
| **Indegree (directed)** | Number of incoming edges of a node |
| **Outdegree (directed)** | Number of outgoing edges of a node |
| **Total Degree property** | `Total Degree = 2 × E` (because every edge touches exactly 2 nodes) |
| **Edge weight** | Number assigned to an edge |
| **Unit weight** | Default weight of `1` assumed when no weights are given |

---

## 15. Points Most Likely to Be Tested / Trip You Up

1. **A graph need not be enclosed.** Trees, open chains, and even disconnected shapes are graphs as long as they have nodes and edges.
2. **Node numbering is arbitrary** — never assume ordering carries meaning.
3. **In an undirected graph, one stated edge `u–v` implies two directions.** This directly affects how you store the graph (you insert the edge twice in an adjacency list).
4. **In a directed graph, bidirectional movement must be written as two separate edges.**
5. **"All edges are directed"** is what makes a graph directed — mind the word *all*.
6. **A path cannot repeat a node** — this is the trap in `1 2 3 2 1`.
7. **A path cannot skip over missing edges** — this is the trap in `1 3 5`.
8. **`Total Degree = 2 × E`**, and know the one-line reason: every edge has two endpoints. A direct consequence: total degree is always even.
9. **Directed graphs have two degrees**, indegree and outdegree — not one.
10. **No weights mentioned ⇒ assume weight 1.**
11. **DAG** is the acronym to remember; it will be used heavily in later lectures.

---

*End of Lecture 1 — Introduction to Graphs.*
