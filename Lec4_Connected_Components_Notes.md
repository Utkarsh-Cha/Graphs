# Graph Series — Lecture 4: Connected Components

---

## 1. Where this lecture fits

Up to this point in the series we have seen graphs that look "whole" — one single piece where you can walk from any node to any other node. Two examples used at the start of the lecture:

1. **A square graph attached to a 5th node** — four nodes forming a closed square, with one extra node hanging off it.
2. **A binary tree** — a root with two children, and the left child having two children of its own.

The important reminder here (also made in Lecture 1): **a binary tree is also a graph.** It has nodes, it has edges, and there is no cycle — it is a valid connected graph. Tree structures are just a special, restricted family of graphs.

Both of these are **connected graphs**: every node is reachable from every other node.

Now the real question of this lecture: **what happens when the picture is not one single piece?**

---

## 2. The motivating question

Suppose you are shown a drawing containing four separate, unconnected clusters:

| Cluster | Shape | Node count |
|---|---|---|
| Section 1 | 4 nodes forming a square | 4 |
| Section 2 | 3 nodes forming a triangle | 3 |
| Section 3 | 2 nodes joined by one edge | 2 |
| Section 4 | 1 isolated node, no edges | 1 |

If you are asked *"Is this a graph?"*, the natural first answer is:

> "These are **four** graphs, not one. This cluster is a graph, that cluster is a graph, that single node is also a graph (a single node counts as a graph). They are not connected to each other, so they can't be one graph."

That answer is **not wrong** — but it is **incomplete**. This is exactly the gap that the definition of *connected components* fills.

---

## 3. The key example (N = 10, M = 8)

Number the nodes in the drawing from 1 to 10:

- **Component 1:** nodes `1`, `2`, `3`, `4` (the square)
- **Component 2:** nodes `5`, `6`, `7` (the triangle)
- **Component 3:** nodes `8`, `9` (the single edge)
- **Component 4:** node `10` (isolated, no edges at all)

Now imagine someone hands you a standard graph problem statement:

> *Given an undirected graph with 10 nodes (1, 2, 3, 4, 5, 6, 7, 8, 9, 10) and 8 edges.*

**Input as written on screen:**

```text
N = 10    M = 8

1 2
1 3
2 4
3 4
5 6
6 7
5 7
8 9
```

Count the edges: `1-2`, `1-3`, `2-4`, `3-4` (4 edges for the square), `5-6`, `6-7`, `5-7` (3 edges for the triangle), `8-9` (1 edge). Total = **8 edges = M**. Node `10` appears in the node count `N = 10` but **never appears in the edge list** — that is precisely how an isolated node shows up in an input format.

### The crucial realisation

The problem statement says **"a graph"** — singular. It gives **one** value of N and **one** edge list. So by the definition of the input, all ten nodes belong to **one single graph**.

But when you draw it, that one graph physically sits in **four pieces**.

> **Definition:** Instead of calling them "pieces", we call them **components**. This graph has been broken into **4 different components** — i.e. it has **4 connected components**.

On screen, four circles are drawn around the four disconnected parts and labelled `1`, `2`, `3`, `4`, with the text **"4 Components"** written below.

### The warning to carry forward

> **Never say "those are not graphs" just because you see two disconnected portions.**

They *could* have been described as four separate graphs — but if the question and the input describe them as one graph with N nodes and M edges, then they are **four components of a single graph**. What defines "one graph" here is the input, not the visual appearance.

---

## 4. Why this matters: it breaks naive traversal

This is the real, practical reason the concept is being taught this early in the series.

Going forward you will learn many **traversal algorithms** (the lecture deliberately does not name them yet, since they haven't been taught — these will be BFS and DFS). On screen this is written simply as:

```text
Applying -> Traversal
```

### The property of every traversal algorithm

> **A traversal algorithm is designed to visit the entire connected portion of the graph reachable from its starting node — and nothing beyond that.**

That is both its strength and its limitation.

### The failure case

Suppose you start a traversal at node `1`. The journey would go something like:

```
1 → 2 → 4 → 3
```

It visits every node of Component 1. And then it **stops** — because there is no edge leading out of `{1, 2, 3, 4}`.

So nodes `5, 6, 7, 8, 9, 10` are **never reached**. They "would have not been touched."

> **The wrong assumption to avoid:**
> ```
> ❌ traversal(1)   // "this will surely visit everyone"
> ```
> On screen this is literally written and then **crossed out with an X**.
>
> Calling the traversal once from node 1 and assuming it covers the whole graph is **only valid if the graph has exactly one component**. If the graph has multiple components, it is simply wrong.

Since a problem will rarely promise you that the graph is connected, you must always write code that handles multiple components.

---

## 5. The solution: a `visited` array + an outer loop

### 5.1 The visited array

> **Remember the concept of the visited array. Any traversal you ever do will use one.**

**Sizing it:**

- Here we have 10 nodes, numbered **1 to 10** (1-based numbering).
- Create an array with indices from `0` up to `10`.
- Therefore the **size is 11**, i.e. **`N + 1`**.

```text
vis[]  →  index:  0  1  2  3  4  5  6  7  8  9  10
                  ─────────────────────────────────
         size = 11
```

**Why `N + 1` and not `N`?** Because the nodes are numbered starting at 1, and the largest node number is `N = 10`. An array of size 10 would only give you valid indices `0..9`, and `vis[10]` would be out of bounds. Taking size `N + 1` lets you index the array directly by the node number (`vis[node]`) without ever doing `node - 1` arithmetic. Index `0` simply sits there unused. This avoids a whole class of off-by-one bugs.

> **Note:** If a problem instead numbers its nodes from `0` to `N-1`, an array of size `N` is enough. Always size the array by the node-numbering scheme given in the question.

**Initialisation:** every node starts as **unvisited**, i.e. `false` / `0`.

```text
vis:  0  0  0  0  0  0  0  0  0  0  0
i  :  0  1  2  3  4  5  6  7  8  9  10
```

### 5.2 The universal loop pattern

This is the pattern written on screen and later circled in red for emphasis:

```cpp
for(i = 1 -> 10) {
    if(!vis[i]) {
        traversal(i);
    }
}
```

In proper C++ form, generalised:

```cpp
vector<int> vis(N + 1, 0);          // all nodes initially unvisited

for (int i = 1; i <= N; i++) {      // node numbering is 1..N
    if (!vis[i]) {                  // if this node has never been reached
        traversal(i);               // start a fresh traversal from it
    }
}
```

**Line-by-line logic:**

| Line | What it does | Why it is needed |
|---|---|---|
| `vector<int> vis(N+1, 0)` | Creates the visited array of size N+1, all false | Lets us index by node number directly; records what's already been covered |
| `for (i = 1; i <= N; i++)` | Touches **every** node number at least once | Guarantees no component is missed, no matter where it sits |
| `if (!vis[i])` | Only enter if node `i` has **not** been reached by any earlier traversal | Prevents re-traversing a component we have already fully covered |
| `traversal(i)` | Launches the traversal from `i`; it marks everything reachable from `i` as visited | Covers one entire component in one call |

> **This is the pattern. Memorise it.** Loop over all nodes, and if a node is unvisited, start the traversal from it. The traversal will then mark everyone it can reach. Then move on to the next node, and repeat.

---

## 6. Full dry run on the example

Graph: N = 10, edges `1-2, 1-3, 2-4, 3-4, 5-6, 6-7, 5-7, 8-9`. Node 10 isolated.

**Initial state — everyone unvisited:**

```text
index:  0  1  2  3  4  5  6  7  8  9  10
vis  :  0  0  0  0  0  0  0  0  0  0   0
```

---

**`i = 1`** → `vis[1] == 0`, so the `if` passes → call **`traversal(1)`**.

The traversal walks the whole connected portion: `1 → 2 → 4 → 3`. Since it *went to* all of them, it *marks* all of them.

```text
index:  0  1  2  3  4  5  6  7  8  9  10
vis  :  0  1  1  1  1  0  0  0  0  0   0
                 ↑ component 1 fully covered
```

---

**`i = 2`** → `vis[2] == 1`, already visited in the traversal that started at 1 → **skip**.
**`i = 3`** → `vis[3] == 1` → **skip**.
**`i = 4`** → `vis[4] == 1` → **skip**.

*(Notice: nodes 1, 2, 3, 4 were all covered by a single call. The loop reaching them again costs O(1) each and does nothing.)*

---

**`i = 5`** → `vis[5] == 0`, not visited → call **`traversal(5)`**.

It traverses `5 → 6 → 7` and stops (nothing connects the triangle to anything else).

```text
index:  0  1  2  3  4  5  6  7  8  9  10
vis  :  0  1  1  1  1  1  1  1  0  0   0
```

---

**`i = 6`** → visited → **skip**.
**`i = 7`** → visited → **skip**.

---

**`i = 8`** → `vis[8] == 0` → "I have not been visited" → call **`traversal(8)`**.

It covers `8` and `9`, marking both true.

```text
index:  0  1  2  3  4  5  6  7  8  9  10
vis  :  0  1  1  1  1  1  1  1  1  1   0
```

---

**`i = 9`** → visited → **skip**.

---

**`i = 10`** → `vis[10] == 0`, not traversed yet → call **`traversal(10)`**.

Node 10 has no edges, so the traversal visits only itself and immediately finishes. It still gets marked.

```text
index:  0  1  2  3  4  5  6  7  8  9  10
vis  :  0  1  1  1  1  1  1  1  1  1   1
```

---

**Loop ends.** Every node has been visited exactly once, and **every component was entered exactly once**.

### What the dry run proves

- The traversal function was called from the outer loop exactly **4 times** — once per component (from nodes `1`, `5`, `8`, `10`).
- An isolated node (node 10) is a perfectly valid component of size 1 and is handled correctly with no special-casing.
- Without the outer loop, only the first component would ever have been visited.

---

## 7. Counting the number of connected components

This falls straight out of the dry run and is the single most common interview use of this pattern: **the number of times the outer loop actually fires `traversal(i)` is exactly the number of connected components.**

```cpp
int count = 0;
vector<int> vis(N + 1, 0);

for (int i = 1; i <= N; i++) {
    if (!vis[i]) {
        count++;          // a brand-new component has been discovered
        traversal(i);      // consume that entire component
    }
}
// count == number of connected components
```

For the example: the counter increments at `i = 1`, `i = 5`, `i = 8`, `i = 10` → **count = 4**, matching the "**4 Components**" written on screen.

**Why this is correct (the reasoning):** reaching the `if` body with `vis[i] == false` means node `i` was not reachable from *any* previously visited node — so it must belong to a component we have never touched before. And after `traversal(i)` runs, that entire component is marked, so no other node of it can ever trigger the counter again. One increment ⟺ one component.

---

## 8. Complexity of the pattern

- The **outer loop** runs exactly `N` times, doing O(1) work per node when the node is already visited.
- Across all calls, the **traversal** visits each node once and each edge a constant number of times, because the `visited` array stops it from re-entering anything.
- So the total cost of the whole pattern is **O(N + M)** using an adjacency list (`N` for the loop and node visits, `M` for the edges) — *not* O(N × traversal cost). The visited array is what keeps the work linear instead of exponential/repeated.
- **Space:** O(N) for the `visited` array, plus whatever the traversal itself needs (recursion stack or queue) and O(N + M) for the adjacency list.

---

## 9. Key takeaways / revision checklist

1. **A graph need not be one connected piece.** It can be split into several disconnected pieces; each piece is a **connected component**.
2. **A single node with no edges is a valid component** (and a valid graph on its own).
3. **A binary tree is also a graph** — connected, and a special case of a graph.
4. If a problem gives one `N` and one edge list, all of it is **one graph** — possibly with multiple components. Don't call the pieces "separate graphs".
5. In the input format, an **isolated node is invisible in the edge list** — it only shows up through the node count `N`. Always trust `N`, not the edge list, for how many nodes exist.
6. **Every traversal algorithm only covers the component it starts in.** That is by design.
7. Therefore **never** write `traversal(1)` alone and assume the whole graph is covered. ❌
8. **Always use a `visited` array**, sized `N + 1` for 1-based node numbering (size `N` for 0-based), initialised to false.
9. **Always wrap the traversal in the standard loop:**
   ```cpp
   for (i = 1; i <= N; i++)
       if (!vis[i])
           traversal(i);
   ```
10. **The number of times that traversal is fired from the loop = the number of connected components.**
11. Total complexity of this pattern: **O(N + M)** time, **O(N)** extra space for `visited`.

> **Bottom line:** this loop-with-visited-array pattern is not specific to any one algorithm. It is the outer skeleton you will wrap around **every** graph traversal you write from here on, so that multi-component graphs are handled automatically.
