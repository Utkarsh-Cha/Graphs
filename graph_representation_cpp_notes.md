# Graph Representation in C++ — Complete Study Notes
### (Striver / take U forward — Graph Series, Lecture 2)

---

## 0. Where this lecture fits

In the previous lecture you learned **what a graph is** and **its types** (directed / undirected, cyclic / acyclic, weighted / unweighted, etc.).

The natural next question is: *"Which data structure do we actually use to keep a graph inside our program?"* A graph looks like circles and lines on paper, but a computer needs something concrete — an array, a matrix, a vector.

This lecture answers that in two parts:

1. **Input** — how the graph is *given* to you in a problem.
2. **Storage** — how you *store* it in a data structure so that algorithms (BFS, DFS, shortest path…) can be run on it later.

> **Order matters:** First understand the input format, then decide the storage. Storage is chosen after you know what you are receiving.

---

## 1. The graph used throughout the lecture

The running example is an **undirected, unweighted** graph:

- **Nodes:** `1, 2, 3, 4, 5`
- **Edges:** `(1–2)`, `(1–3)`, `(2–4)`, `(3–4)`, `(2–5)`, `(4–5)`

```
        1
       / \
      2---3
      |\  |
      | \ |
      5---4
```

Counting them:
- Number of nodes = **5**
- Number of edges = **6** (count them one by one: 1-2, 1-3, 2-4, 3-4, 2-5, 4-5)

---

## 2. Input format of a graph

### 2.1 The first line: `n` and `m`

Almost every graph problem starts with a sentence like:

> *"Given an **undirected** graph which contains `n` nodes and `m` edges…"*

So:

| Symbol | Meaning |
|---|---|
| `n` | number of **nodes** (vertices) |
| `m` | number of **edges** |

Two important things are hidden in that sentence:

1. **The problem statement itself tells you whether the graph is directed or undirected.** You never have to guess — it is stated at the beginning of the question. This single word decides how you store the edges (explained later).
2. The **first line of input is always `n` and `m`** — the first number is the node count, the second is the edge count. You must assume this convention.

For our example graph, the first input line is:

```
5 6
```

### 2.2 The next `m` lines: the edges

Knowing that there are 6 edges is useless unless you know **between which nodes** those edges lie. So after the first line, the input gives you exactly **`m` lines, and each line represents one edge**.

Each line contains two integers `u` and `v`, meaning *"there is an edge between u and v"*.

Full input for our graph:

```
5 6      <- n = 5 nodes, m = 6 edges
2 1      <- edge between 2 and 1
1 3      <- edge between 1 and 3
2 4      <- edge between 2 and 4
3 4      <- edge between 3 and 4
2 5      <- edge between 2 and 5
4 5      <- edge between 4 and 5
```

### 2.3 Three crucial observations about the edge lines

**(a) In an undirected graph, `u v` and `v u` mean the same thing.**
If the input says `1 2`, it means there is an edge from 1 to 2 **and also** from 2 to 1. Likewise `2 1` means the same edge. That is exactly why the first edge above is printed as `2 1` and not `1 2` — for an undirected graph it makes no difference. Both directions are implied automatically.

**(b) The edges can come in any order.**
There is **no specific ordering** guaranteed. The edges may be listed in whatever sequence the setter chose. Your code must not depend on the order.

**(c) There is no fixed relationship between `m` and `n`.**
Students often ask: "Is the number of edges bounded by the number of nodes?" **No.**
- `n` is fixed once the node count is given.
- But `m` can be anything — you can keep adding one more edge, then another, then another between the same 5 nodes, and `m` becomes 7, 8, 9, and so on.

So never assume `m ≈ n`. This is why edge-count-based complexities (`O(E)`, `O(2E)`) are written separately from node-count-based ones (`O(N)`, `O(N²)`).

---

## 3. How do we store the graph? Two techniques

Once the edges are read, they must be **stored somewhere** so that future algorithms can query them. Two standard ways:

```
Store ??
  -> (1) Matrix   (adjacency matrix)
  -> (2) List     (adjacency list)
```

We study the matrix first, then the list (which is cheaper).

---

# 4. Technique 1 — Adjacency Matrix

## 4.1 The idea

We store the graph in a 2-D matrix where the **row index is one node** and the **column index is another node**. The cell at their intersection answers a single question:

> *"Is there an edge between node `i` and node `j`?"* → `1` = yes, `0` = no.

## 4.2 First decide: 0-based or 1-based nodes?

Look at the graph: the nodes are `1, 2, 3, 4, 5`. There is **no node 0**, and the last node is `n = 5`. So this graph uses **1-based indexing** of nodes.

Because the highest node number we must index is `n` itself, the matrix must be declared of size:

```
adj[n+1][n+1]
```

i.e. a **6 × 6** matrix with indices `0,1,2,3,4,5` on both sides. Row/column `0` simply stays unused (it is wasted, but it keeps the code simple and lets us write `adj[1]`, `adj[5]` directly without subtracting 1 everywhere).

> If the graph were **0-based** (nodes `0 … n-1`), you would declare `adj[n][n]` instead. This is the only change needed.

## 4.3 Filling the matrix

For every input edge `u v` (undirected), mark **both** intersections:

- Go to row `u`, column `v` → mark `1`
- Go to row `v`, column `u` → mark `1`

Why both? Because in an undirected graph, an edge between 1 and 2 also *is* an edge between 2 and 1. If you mark only one side, then later when an algorithm asks "is 2 connected to 1?" it would wrongly get `0`.

Walking through the edges:

| Edge | Cells marked |
|---|---|
| 1 – 2 | `adj[1][2] = 1`, `adj[2][1] = 1` |
| 1 – 3 | `adj[1][3] = 1`, `adj[3][1] = 1` |
| 2 – 4 | `adj[2][4] = 1`, `adj[4][2] = 1` |
| 3 – 4 | `adj[3][4] = 1`, `adj[4][3] = 1` |
| 2 – 5 | `adj[2][5] = 1`, `adj[5][2] = 1` |
| 4 – 5 | `adj[4][5] = 1`, `adj[5][4] = 1` |

**Everything else is filled with `0`** (meaning "no edge"). You either explicitly fill zeros or make sure the matrix is zero-initialised — see the warning in §4.6.

## 4.4 The final matrix

```
      0   1   2   3   4   5
  0   0   0   0   0   0   0
  1   0   0   1   1   0   0
  2   0   1   0   0   1   1
  3   0   1   0   0   1   0
  4   0   0   1   1   0   1
  5   0   0   1   0   1   0
```

Read it like this:
- Row `1` has 1s at columns 2 and 3 → node 1 is connected to 2 and 3. ✔
- Row `2` has 1s at columns 1, 4, 5 → node 2 is connected to 1, 4, 5. ✔
- Row `4` has 1s at columns 2, 3, 5. ✔

**Queries the matrix answers instantly:**
- Is there an edge between 3 and 4? → `adj[3][4] = 1` → **Yes**.
- Is there an edge between 4 and 2? → `adj[4][2] = 1` → **Yes**.
- Is there an edge between 5 and 2? → `adj[5][2] = 1` → Yes. (The lecture verbally asks "Is there an edge between 5 and 2? No" while pointing at the board, but from the given edge list `(2,5)` **does** exist, so the correct answer is *yes*. Trust the edge list.)

> ### ⚠️ Note on a slip in the video
> While filling the matrix on screen, the instructor says *"…like 3 and 5, 5 and 3"* and marks `adj[3][5]` and `adj[5][3]`. That is a slip of the tongue — the actual edge in the edge list is **2 – 5**, not 3 – 5. The correct matrix is the one printed above. You can verify against the adjacency list he builds later in the same video, where `3 -> {1, 4}` and `5 -> {2, 4}` — confirming there is **no** 3–5 edge. Always cross-check a drawn matrix against the original edge list.

**Symmetry property (interview-relevant):** For an *undirected* graph the adjacency matrix is always **symmetric** about the main diagonal (`adj[i][j] == adj[j][i]`). For a *directed* graph it generally is not.

## 4.5 Space complexity

The matrix is `(N+1) × (N+1)`, which asymptotically is:

```
Space = O(N × N) = O(N²)
```

**This is costly.** Consider a graph with `N = 10⁵` nodes: a 10⁵ × 10⁵ integer matrix is 10¹⁰ cells — impossible to allocate. And most of those cells would be `0` (unused), because real graphs are usually sparse (far fewer edges than `N²`).

That waste is exactly the motivation for the adjacency list.

## 4.6 Code — adjacency matrix (undirected)

```cpp
#include <iostream>
using namespace std;

int main() {
    int n, m;
    cin >> n >> m;              // first line: nodes and edges

    int adj[n+1][n+1];          // 1-based graph -> (n+1) x (n+1)

    for (int i = 0; i < m; i++) {
        int u, v;
        cin >> u >> v;          // one edge per line
        adj[u][v] = 1;          // mark the intersection
        adj[v][u] = 1;          // undirected -> mark the reverse too
    }
    // graph is now stored here

    return 0;
}
```

**Line-by-line reasoning:**

| Line | Why |
|---|---|
| `cin >> n >> m;` | The first input line always holds node count then edge count. |
| `int adj[n+1][n+1];` | 1-based nodes go up to `n`, so we need index `n` to be valid → size `n+1`. For a **0-based** graph use `int adj[n][n];`. |
| `for (int i = 0; i < m; i++)` | Exactly `m` lines follow, one per edge — so loop `m` times, not `n` times. |
| `cin >> u >> v;` | Each line gives the two endpoints of one edge. |
| `adj[u][v] = 1;` | Records "u is connected to v". |
| `adj[v][u] = 1;` | Records "v is connected to u". **Required only because the graph is undirected.** |

**Time complexity of storing the graph:** `O(m)` — we do constant work per edge and there are `m` edges. (Plus `O(N²)` if you explicitly initialise the whole matrix with zeros.)

> ⚠️ **Practical warning (not stated verbally, but important):** a local array like `int adj[n+1][n+1];` is **not automatically zero-initialised** — it contains garbage. In real submissions either use `int adj[n+1][n+1] = {0};`, a global array, `memset`, or `vector<vector<int>> adj(n+1, vector<int>(n+1, 0));`. Also note that `int adj[n+1][n+1]` with a runtime `n` is a **VLA**, which is a GCC extension and not standard C++ — the `vector` form is the portable choice.

---

# 5. Technique 2 — Adjacency List

## 5.1 The motivation

The matrix wastes `N²` space, most of it on zeros. The adjacency list stores **only what matters** — nothing else. No zeros, no empty cells for non-existent edges.

## 5.2 The structure

We use an **array**, but each element of the array is itself a **list**. In C++, "list" here simply means `vector<int>`.

Since the graph is 1-based with `n = 5`, we create an array of size `n+1 = 6`:

```cpp
vector<int> adj[n+1];   // indices 0, 1, 2, 3, 4, 5
```

What does this mean?
- We now have 6 slots: `0, 1, 2, 3, 4, 5`.
- **Every slot currently contains an empty vector.**
- If you had declared `int adj[n+1]`, each slot would hold an integer; if `double`, a double. The moment you declare the element type as `vector`, each index starts life holding an **empty list**.

> For a **0-based** graph, declare `vector<int> adj[n];`.

## 5.3 The motive of the list

Look at node `4` in the graph. Its neighbours are `2`, `5`, `3`.

**The goal of the adjacency list is:** at index `4`, store `{2, 5, 3}` (in any order). Then if someone asks *"who are the neighbours of 4?"*, you just read `adj[4]` and answer: 4 is connected to 2, 4 is connected to 5, 4 is connected to 3.

That is the entire purpose — **index = node, list at that index = all its neighbours.**

## 5.4 Building it, edge by edge

For each undirected edge `u – v`, do two pushes:
- Go to index `u` and say *"list, please store `v`"* — because if u and v are connected, `v` is definitely a neighbour of `u`.
- Go to index `v` and say *"list, please store `u`"* — because `u` is equally a neighbour of `v`.

Dry run on our 6 edges:

| Edge | Action | Resulting change |
|---|---|---|
| 1 – 2 | `adj[1].push_back(2)`, `adj[2].push_back(1)` | `1 -> {2}`, `2 -> {1}` |
| 1 – 3 | `adj[1].push_back(3)`, `adj[3].push_back(1)` | `1 -> {2,3}`, `3 -> {1}` |
| 3 – 4 | `adj[3].push_back(4)`, `adj[4].push_back(3)` | `3 -> {1,4}`, `4 -> {3}` |
| 2 – 4 | `adj[2].push_back(4)`, `adj[4].push_back(2)` | `2 -> {1,4}`, `4 -> {3,2}` |
| 2 – 5 | `adj[2].push_back(5)`, `adj[5].push_back(2)` | `2 -> {1,4,5}`, `5 -> {2}` |
| 4 – 5 | `adj[4].push_back(5)`, `adj[5].push_back(4)` | `4 -> {3,2,5}`, `5 -> {2,4}` |

## 5.5 The final adjacency list

```
0 -> { }            (unused, because nodes are 1-based)
1 -> { 2, 3 }
2 -> { 1, 4, 5 }
3 -> { 1, 4 }
4 -> { 3, 2, 5 }
5 -> { 2, 4 }
```

Verify against the picture:
- 1's neighbours are 2 and 3 ✔
- 5's neighbours are 2 and 4 ✔
- 3's neighbours are 1 and 4 ✔

Note that the order inside a list depends on the order the edges appeared in the input (that is why `4 -> {3, 2, 5}` and not `{2, 3, 5}`). **Order within a neighbour list does not matter.**

## 5.6 Space complexity — `O(2E)`

Count the total numbers actually stored: `2+3+2+3+2 = 12` entries, for `6` edges.

```
12 = 2 × 6  →  Space = O(2E)
```

**Why exactly twice the number of edges?** Because **every edge has two endpoints**, and for each edge we store the information twice — once in `u`'s list and once in `v`'s list. Hence `2E`.

**Why this beats the matrix:**
- Matrix → `O(N²)`, and *most cells are unused zeros*.
- List → `O(2E)`, storing **only the nodes that actually matter**, nobody else.

For sparse graphs (the common case) `2E ≪ N²`, so the list is dramatically cheaper.

## 5.7 Code — adjacency list (undirected)

```cpp
#include <iostream>
using namespace std;

int main() {
    int n, m;
    cin >> n >> m;              // input of n and m is the same as before

    vector<int> adj[n+1];       // 1-based -> n+1 ; 0-based -> n

    for (int i = 0; i < m; i++) {
        int u, v;
        cin >> u >> v;
        adj[u].push_back(v);    // v is a neighbour of u
        adj[v].push_back(u);    // u is a neighbour of v
    }

    return 0;
}
```

**Meaning of the two push lines, in plain words:**
- `adj[u].push_back(v)` → *"On the u-th index, please store v, because v is a neighbour."*
- `adj[v].push_back(u)` → *"On the v-th index, please store u, because u is a neighbour."*

**Time complexity of storing:** `O(m)` — two constant-time `push_back` calls per edge.

> In practice you will often see `vector<vector<int>> adj(n+1);` instead of the array-of-vectors form. Functionally identical; the array-of-vectors version is what is written on screen.

---

# 6. What changes for a DIRECTED graph?

Recall from lecture 1: in a directed graph, writing an edge `u → v` means the edge goes **from u to v only**.

### 6.1 Adjacency list

Ask yourself: *if the edge goes only from `u` to `v`, is `u` a neighbour of `v`?* **No.** Only `v` is a neighbour of `u`. Therefore the second push is simply **removed**:

```cpp
// u ---> v
cin >> u >> v;
adj[u].push_back(v);
// adj[v].push_back(u);   <-- NOT required for a directed graph
```

### 6.2 Space complexity of a directed graph

```
Space = O(E)
```

**Why?** Because we are no longer consuming two slots per edge — we consume just one. One entry per edge → total entries = number of edges = `E`.

### 6.3 Adjacency matrix (same logic applies)

By the same reasoning, for a directed graph you mark only one cell:

```cpp
adj[u][v] = 1;
// adj[v][u] = 1;   <-- dropped
```

The matrix then loses its symmetry, but the space is still `O(N²)` — the matrix cost does not improve with direction.

### 6.4 Which representation will we use going forward?

> **Adjacency list, in every problem from here on — not the adjacency matrix — because of the space-complexity issue (`O(N²)` is simply too expensive).**

---

# 7. Weighted graphs

## 7.1 What is a weighted graph?

So far every edge was just "present or absent". Now suppose each edge carries a **number (weight / cost)**. A graph whose edges carry weights is a **weighted graph**.

Example used in the lecture (same shape, with weights added):

| Edge | Weight |
|---|---|
| 1 – 2 | 2 |
| 1 – 3 | 3 |
| 3 – 4 | 4 |
| 2 – 4 | 1 |
| 2 – 5 | 6 |
| 4 – 5 | 3 |

The input in such problems has **three values per line**: `u v wt`.

## 7.2 Weighted graph in an adjacency matrix

Previously, for edge `1 – 2`, you went to `adj[1][2]` and wrote `1` (meaning "edge exists"). Now, instead of writing `1`, **write the edge weight**. That is the only change.

```cpp
adj[u][v] = wt;
adj[v][u] = wt;   // undirected only
```

So `adj[1][2] = 2`, `adj[2][4] = 1`, `adj[2][5] = 6`, and so on — the weight is written at **both** intersections for an undirected graph.

## 7.3 Weighted graph in an adjacency list

Earlier, index `4` stored just the neighbour numbers:

```
4 -> { 2, 3, 5 }
```

But now each neighbour also needs its edge weight attached. A plain `int` cannot carry two pieces of information, so we **tweak the data structure to store pairs** instead of single integers:

```
4 -> { (2, 1), (3, 4), (5, 3) }
```

Reading this:
- `(2, 1)` → neighbour **2**, edge weight **1**
- `(3, 4)` → neighbour **3**, edge weight **4**
- `(5, 3)` → neighbour **5**, edge weight **3**

**Convention:** in each pair, the **first** element is *"go to this node"* and the **second** element is *"the weight of that edge"*.

### Declaration change

```cpp
// unweighted
vector<int> adj[n+1];

// weighted:  int  ->  pair<int,int>
vector<pair<int,int>> adj[n+1];
```

And the insertion becomes:

```cpp
int u, v, wt;
cin >> u >> v >> wt;
adj[u].push_back({v, wt});
adj[v].push_back({u, wt});   // undirected only
```

> The instructor notes that the **full implementation of weighted graphs will be shown in a later video where an actual weighted-graph problem is solved**. For now, just keep the idea in mind: *same structure, only the element type changes from `int` to `pair<int,int>`.*

---

# 8. Consolidated comparison

| | Adjacency Matrix | Adjacency List |
|---|---|---|
| Declaration (1-based) | `int adj[n+1][n+1];` | `vector<int> adj[n+1];` |
| Declaration (0-based) | `int adj[n][n];` | `vector<int> adj[n];` |
| Undirected insert | `adj[u][v]=1; adj[v][u]=1;` | `adj[u].push_back(v); adj[v].push_back(u);` |
| Directed insert | `adj[u][v]=1;` | `adj[u].push_back(v);` |
| Weighted insert | `adj[u][v]=wt;` | `adj[u].push_back({v,wt});` with `vector<pair<int,int>>` |
| Space (undirected) | `O(N²)` — **costly** | `O(2E)` |
| Space (directed) | `O(N²)` | `O(E)` |
| Time to build | `O(m)` | `O(m)` |
| Wasted space | Large — most cells are `0` | None — only real neighbours stored |
| Used going forward? | ✘ | ✔ **Yes, in every problem** |

---

# 9. Quick revision checklist

1. **Input format:** line 1 = `n m`; next `m` lines = one edge each (`u v`, or `u v wt` if weighted).
2. The **problem statement tells you** whether the graph is directed or undirected — read the first line of the question carefully.
3. For an undirected graph, `u v` and `v u` are the **same edge**; edges may arrive in **any order**; `m` has **no bound** tied to `n`.
4. **1-based nodes → size `n+1`; 0-based nodes → size `n`.** Index `0` is simply left unused in the 1-based case.
5. **Adjacency matrix:** `adj[i][j] = 1` means an edge exists between `i` and `j`. Symmetric for undirected graphs. Space `O(N²)` → too costly for large `N`.
6. **Adjacency list:** index = node, list = all its neighbours. Space `O(2E)` undirected, `O(E)` directed — because every undirected edge is stored twice (once for each endpoint).
7. **Undirected = two insertions per edge. Directed = one insertion per edge.** That single removed line is the entire difference.
8. **Weighted graph:** matrix → store `wt` instead of `1`; list → store `pair<int,int>` = `{neighbour, weight}` instead of plain `int`.
9. Building the graph always costs **`O(m)` time**, since work per edge is constant.
10. From this point on in the series, **everything uses the adjacency list.**
