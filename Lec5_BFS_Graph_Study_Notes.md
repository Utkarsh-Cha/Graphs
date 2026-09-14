# Graph Series — Lecture 5: BFS of a Graph (Breadth First Search Traversal)

---

## 1. Why This Lecture Matters

To solve **any** graph problem, you must first know how to **traverse** a graph. Traversal is the foundation — almost every graph algorithm (cycle detection, connected components, shortest path in unweighted graphs, bipartite checking, topological sort, etc.) is built on top of a traversal technique.

There are two fundamental traversal techniques:

| Technique | Full Form | Covered In |
|---|---|---|
| **BFS** | **B**readth **F**irst **S**earch | This lecture |
| **DFS** | **D**epth **F**irst **S**earch | Next lecture |

This lecture is about **BFS**. This video **cannot be skipped** — everything ahead depends on it.

> If you have studied trees, you have already seen BFS there (level order traversal). The idea for graphs is the same, with one extra complication that trees do not have: **graphs can have cycles**, so we need a `visited` array to avoid going in circles.

---

## 2. Prerequisite Recap: 1-Based vs 0-Based Graphs

Graphs generally come in two flavours, and you must notice which one you have been given **before** you write code:

| Type | Node numbering | Example |
|---|---|---|
| **1-based indexing** | Nodes numbered `1, 2, 3, ..., N` | A graph with `N = 8` where nodes are `1 … 8` |
| **0-based indexing** | Nodes numbered `0, 1, 2, ..., N-1` | A graph with `N = 8` where nodes are `0 … 7` |

**Key point from the lecture:** The *logic of BFS does not change at all* between the two. Only the **array sizes and loop bounds** in your code change.

- For a **0-based** graph with `N` nodes → `visited` array of size `N`.
- For a **1-based** graph with `N` nodes → `visited` array of size `N + 1` (because index `0` stays unused, and you still need a valid index for node `N`).

This single detail causes a huge number of runtime errors in contests and interviews, so always check the indexing first.

---

## 3. What Exactly Is "Breadth First Search"?

The important word is **Breadth**.

- **Breadth** means *width* — you spread out **sideways**, in equal distance, before you go deeper.
- Because of this, BFS is also called **level-wise traversal** (or level order traversal).

### The core idea

You are always given (or you choose) a **starting node**.

1. The starting node is at **level 0**.
2. All nodes **directly connected** to the starting node (distance 1 edge away) are at **level 1**.
3. All nodes that are 2 edges away are at **level 2**.
4. … and so on.

**BFS prints all nodes of level 0, then all nodes of level 1, then all nodes of level 2, …** — it never jumps to a deeper level until the current level is completely finished.

### Crucial rule about only ONE node at level 0

Only **one node** can be at level 0 — the starting node. Everything else is measured *relative to that node*.

### Crucial rule about ordering *within* a level

Within a single level, **the order is your choice**. BFS does not mandate a particular ordering among nodes of the same level.

> If level 2 contains `{3, 4, 7, 8}`, then `3 4 7 8` is a valid BFS output, and so is `8 7 4 3`, and so is `4 3 8 7`.
>
> **The only hard requirement is:** all nodes of a level must be traversed *together*, before moving to the next level.

In practice, the order you actually get depends on the order in which neighbours are stored in your adjacency list — but conceptually any within-level ordering is a correct BFS.

---

## 4. Worked Example 1 — Graph with N = 8

### The graph

```
N = 8

            1
          /   \
         2     6
        / \   / \
       3   4 7   8
            \ /
             5
```

**Edges (undirected):**
`(1,2)`, `(1,6)`, `(2,3)`, `(2,4)`, `(6,7)`, `(6,8)`, `(4,5)`, `(7,5)`

Note: node `5` is reachable from **both** `4` and `7`.

---

### 4.1 Case A — Starting Node = 1

Assign levels by distance from node `1`:

| Level | Nodes | Reasoning |
|---|---|---|
| **0** | `1` | The starting node itself |
| **1** | `2`, `6` | Direct neighbours of `1` |
| **2** | `3`, `4`, `7`, `8` | `3`,`4` come from `2`; `7`,`8` come from `6` |
| **3** | `5` | Reachable from `4` (level 2) and from `7` (level 2), so it sits at level 3 |

**BFS Traversal (as written on screen):**

```
Traversal :  1   2   6   3   4   7   8   5
Level     :  0   1   1   2   2   2   2   3
```

**Explanation of how this is built:**

1. First print the starting node `1` (level 0).
2. Move to the next level (level 1). Both `2` and `6` sit there. Print them — in this case `2` then `6`. *(Printing `6` then `2` would have been equally valid.)*
3. Move to level 2. All of `3, 4, 7, 8` sit there. Print all four together before moving on. *(Any internal order is acceptable — `3 4 7 8`, `8 7 4 3`, etc.)*
4. Move to level 3. Only `5` sits there. Print it.

This entire traversal is valid **only because the starting node was 1**. That is extremely important — change the starting node and the entire answer changes.

---

### 4.2 Case B — Starting Node = 6 (Same Graph!)

Now the starting node is `6`. Node `6` becomes level 0 — and node `1` can **no longer** be level 0, because only one node is allowed at level 0.

Re-measure every distance from node `6`:

| Level | Nodes | Reasoning |
|---|---|---|
| **0** | `6` | The new starting node |
| **1** | `1`, `7`, `8` | All three are direct neighbours of `6` — **all at equivalent distance**, so all three are level 1 |
| **2** | `2`, `5` | `2` is reached from `1`; `5` is reached from `7` (and also from `8` — still distance 2) |
| **3** | `3`, `4` | Both reached from `2`, which is at level 2 |

**BFS Traversal (as written on screen):**

```
Traversal :  6   1   7   8   2   5   3   4
Level     :  0   1   1   1   2   2   3   3
```

Again, within level 1 you could have written `7 8 1` or `8 1 7` — all valid.

### The two big takeaways from comparing Case A and Case B

1. **Everything depends on the starting node.** The same graph gives a completely different BFS output for a different start. Levels are *relative* distances, not fixed properties of a node.
2. **You always travel breadth-wise.** You expand equally in all directions — like a ripple spreading out from where a stone hit water. All nodes at "equivalent distance" go together.

---

## 5. The Data Structures BFS Needs (Initial Configuration)

BFS has a fixed setup that you must memorise. There are **two** structures (three if you count the output list).

### 5.1 Queue — the engine of BFS

We use a **queue** data structure.

- A queue is **FIFO** — **F**irst **I**n, **F**irst **O**ut. *The guy who goes in first, gets out first.*
- This FIFO property is exactly **why BFS produces level-order output**: nodes of level `k` all enter the queue before any node of level `k+1`, so they must all come out before any level `k+1` node comes out. The queue enforces the level ordering automatically — you never have to compute levels explicitly.

**Initial state:** the queue contains only the **starting node**.

```
Queue  ->  FIFO
┌───┐
│ 1 │   <- starting node
└───┘
```

> If you are not comfortable with queues, go and revise the queue data structure before continuing.

### 5.2 Visited array — the safety net

We create a `visited` (commonly written `vis`) array.

- This array appears in **almost every graph question**, so get used to it.
- **Meaning of `vis[x] = 1`:** "node `x` has already been touched / traversed / put into the queue."
- **Meaning of `vis[x] = 0`:** "node `x` has never been seen yet."

**Sizing it correctly:**
In the dry-run graph of the next section, nodes are numbered up to `9` and the graph is **1-based**, so we create `vis` of **size 10** (indices `0` through `9`). Index `0` simply goes unused.

```
index :  0  1  2  3  4  5  6  7  8  9
vis   :  0  1  0  0  0  0  0  0  0  0
              ^
              starting node 1 marked as visited
```

**Why do we even need this?**
Graphs (unlike trees) can contain **cycles** and can have multiple paths to the same node. Without `vis`, node `2` would push node `1` back into the queue, `1` would push `2` again, and the program would loop forever. `vis` guarantees **every node is processed exactly once**.

### 5.3 Initial configuration — summary

| Structure | Initial content |
|---|---|
| `queue` | contains only the **starting node** |
| `vis[]` | all zeros, except `vis[starting node] = 1` |
| `bfs[]` (answer list) | empty |

---

## 6. The BFS Algorithm — Step by Step

The whole algorithm in words:

> **Keep taking elements out of the queue until the queue becomes empty.**
> For each element you take out:
> 1. Print it / push it into the answer list.
> 2. Ask it: **"Who are your neighbours?"**
> 3. For every neighbour that is **not yet visited**: mark it visited **and** push it into the queue.
> 4. Any neighbour that **is** already visited is simply **skipped** (omitted) — do not push it again.

The key mental sentence from the lecture is: **"Who are your neighbours?"** — and the answer to that question is stored in the **Adjacency List**.

### Why the "already visited → skip" rule is essential

When we pop `2` and look at its neighbours `{1, 3, 4}`, node `1` is a neighbour. But `1` has already been traversed — we know this because `vis[1] == 1`. If we pushed `1` again, it would be printed twice and we would bounce back and forth forever. So we **omit** it.

---

## 7. Full Dry Run — Worked Example 2 (9 Nodes)

### 7.1 The graph

```
        1
      /   \
     2     6
    / \   / \
   3   4 7   9
        \  \
         5  8
          \/
```

**Edges (undirected):**
`(1,2)`, `(1,6)`, `(2,3)`, `(2,4)`, `(6,7)`, `(6,9)`, `(4,5)`, `(7,8)`, `(5,8)`

**Starting node = 1**

### 7.2 The Adjacency List

The graph is stored in an **Adjacency List** (built exactly as taught in Lecture 2 — for an undirected edge `(u,v)`, you push `v` into `adj[u]` **and** `u` into `adj[v]`).

```text
0 -> { }            // unused (1-based graph)
1 -> {2, 6}
2 -> {1, 3, 4}
3 -> {2}
4 -> {2, 5}
5 -> {4, 8}
6 -> {1, 7, 9}
7 -> {6, 8}
8 -> {5, 7}
9 -> {6}
```

Read this as: index `1` stores a vector/list `{2, 6}` — these are node 1's neighbours. Index `2` stores `{1, 3, 4}` — node 2's neighbours. And so on.

### 7.3 Initial configuration

```
Queue : [ 1 ]

index :  0  1  2  3  4  5  6  7  8  9
vis   :  0  1  0  0  0  0  0  0  0  0

BFS   : (empty)
```

### 7.4 Iteration-by-iteration trace

| Step | Pop | Neighbours (`adj[node]`) | Action taken | Queue after step | BFS so far |
|---|---|---|---|---|---|
| 1 | `1` | `{2, 6}` | `2` unvisited → push + mark. `6` unvisited → push + mark. | `[2, 6]` | `1` |
| 2 | `2` | `{1, 3, 4}` | `1` **already visited → skip**. `3` push + mark. `4` push + mark. | `[6, 3, 4]` | `1 2` |
| 3 | `6` | `{1, 7, 9}` | `1` visited → skip. `7` push + mark. `9` push + mark. | `[3, 4, 7, 9]` | `1 2 6` |
| 4 | `3` | `{2}` | `2` visited → skip. Nothing to do. | `[4, 7, 9]` | `1 2 6 3` |
| 5 | `4` | `{2, 5}` | `2` visited → skip. `5` push + mark. | `[7, 9, 5]` | `1 2 6 3 4` |
| 6 | `7` | `{6, 8}` | `6` visited → skip. `8` push + mark. | `[9, 5, 8]` | `1 2 6 3 4 7` |
| 7 | `9` | `{6}` | `6` visited → skip. Nothing to do. | `[5, 8]` | `1 2 6 3 4 7 9` |
| 8 | `5` | `{4, 8}` | Both already visited → skip both. | `[8]` | `1 2 6 3 4 7 9 5` |
| 9 | `8` | `{5, 7}` | Both already visited → skip both. | `[]` → **empty, loop ends** | `1 2 6 3 4 7 9 5 8` |

### 7.5 Visited array evolution

```
After step 1 :  0  1  1  0  0  0  1  0  0  0     (2 and 6 marked)
After step 2 :  0  1  1  1  1  0  1  0  0  0     (3 and 4 marked)
After step 3 :  0  1  1  1  1  0  1  1  0  1     (7 and 9 marked)
After step 5 :  0  1  1  1  1  1  1  1  0  1     (5 marked)
After step 6 :  0  1  1  1  1  1  1  1  1  1     (8 marked — all visited)
```

### 7.6 Final answer

```
BFS   :  1   2   6   3   4   7   9   5   8
Level :  0   1   1   2   2   2   2   3   3
```

Notice how the output naturally came out grouped by level — level 0, then level 1, then level 2, then level 3 — **without us ever storing the level number anywhere**. That is the FIFO property of the queue doing the work for us.

---

## 8. The Code

### 8.1 Problem setup (as on GeeksforGeeks)

The typical function signature you are given:

- Return type: `vector<int>` — you store the BFS order and return it.
- `int V` — the number of nodes (vertices).
- `vector<int> adj[]` — the **adjacency list is given to you**. In almost every problem you do **not** need to build it yourself.

This particular GFG graph is **0-based** (node numbering starts from `0`), so the starting node is `0` and `vis` is of size `V` (not `V + 1`).

### 8.2 C++ Code

```cpp
class Solution {
  public:
    // Function to return Breadth First Traversal of given graph.
    vector<int> bfsOfGraph(int V, vector<int> adj[]) {
        int vis[V] = {0};        // visited array, all zeros
        vis[0] = 1;              // starting node 0 marked visited
        queue<int> q;
        q.push(0);               // starting node pushed into queue
        vector<int> bfs;         // answer list

        while(!q.empty()) {      // keep going till queue is empty
            int node = q.front();
            q.pop();
            bfs.push_back(node); // whichever node comes out, goes into BFS

            // traverse all neighbours of this node
            for(auto it : adj[node]) {
                if(!vis[it]) {   // neighbour not yet visited
                    vis[it] = 1; // mark visited
                    q.push(it);  // push so it gets processed later
                }
            }
        }
        return bfs;
    }
};
```

### 8.3 Line-by-line explanation

| Line | What it does and why |
|---|---|
| `int vis[V] = {0};` | Creates the visited array of size `V` and initialises **everything to 0**. Size is `V` because this graph is **0-based**. For a 1-based graph you would write `V + 1`. |
| `vis[0] = 1;` | The starting node is `0`. We mark it visited **at the moment we push it**, not when we pop it. This is the standard BFS convention and it prevents the same node from being pushed twice by two different parents. |
| `queue<int> q; q.push(0);` | The initial configuration — queue containing only the starting node. |
| `vector<int> bfs;` | The output list where the traversal order accumulates. |
| `while(!q.empty())` | "Keep taking out till the queue is not empty." This is the outer driver of the whole algorithm. |
| `int node = q.front(); q.pop();` | Take out the front element (FIFO). `front()` reads it, `pop()` removes it — in C++ these are two separate calls. |
| `bfs.push_back(node);` | The moment a node comes out of the queue, it is part of the BFS answer. |
| `for(auto it : adj[node])` | `adj[node]` means: go to index `node` in the adjacency list; that index stores a **vector** containing all the neighbours of `node`. We iterate over that vector, so `it` is one neighbour at a time. This is literally the code version of asking **"Who are your neighbours?"** |
| `if(!vis[it])` | Only act on neighbours that have **not** been visited. Visited neighbours are silently skipped — this is what breaks cycles. |
| `vis[it] = 1; q.push(it);` | Mark the neighbour as visited and push it into the queue so that in a later step it will be popped and added to the BFS. |
| `return bfs;` | Once the queue empties, every reachable node has been processed. Return the answer. |

### 8.4 Execution flow in one sentence

> Pop a node → record it → look up its neighbour list → push every unvisited neighbour (marking them) → repeat until the queue drains.

### 8.5 Java equivalent (same structure, line for line)

The lecture keeps the Java solution on the left pane while typing C++ on the right, deliberately following the **same structure** so you can map each line directly:

```java
class Solution {
    // Function to return Breadth First Traversal of given graph.
    public ArrayList<Integer> bfsOfGraph(int V, ArrayList<ArrayList<Integer>> adj) {
        boolean vis[] = new boolean[V];   // all false by default
        vis[0] = true;
        Queue<Integer> q = new LinkedList<>();
        q.add(0);
        ArrayList<Integer> bfs = new ArrayList<>();

        while(!q.isEmpty()) {
            Integer node = q.poll();      // poll() = front() + pop() combined
            bfs.add(node);

            for(Integer it : adj.get(node)) {
                if(vis[it] == false) {
                    vis[it] = true;
                    q.add(it);
                }
            }
        }
        return bfs;
    }
}
```

**Only real differences:**
- `q.poll()` in Java does the job of `q.front()` **and** `q.pop()` together.
- `adj.get(node)` replaces `adj[node]`.
- `boolean[]` is naturally initialised to `false`, so no explicit `{0}` needed.

The code was compiled and submitted successfully on the judge in the lecture.

---

## 9. Directed vs Undirected Graphs

An important point raised in the lecture:

- The GFG problem statement mentions a **directed** graph, but the code is written thinking of an **undirected** graph.
- **The very same code works for a directed graph too.**

**Why it works for both:** the BFS code never assumes anything about direction. It only ever does one thing — `for(auto it : adj[node])`, i.e. "give me everything reachable from this node in one step." The *direction information is already baked into how the adjacency list was built*:

- **Undirected edge `(u, v)`** → adjacency list stores `v` in `adj[u]` **and** `u` in `adj[v]`.
- **Directed edge `u → v`** → adjacency list stores **only** `v` in `adj[u]`.

So the traversal logic is identical; only the input structure differs. **This is a common interview follow-up question.**

---

## 10. Complexity Analysis

### 10.1 Space Complexity

Three structures scale with the number of nodes:

| Structure | Space |
|---|---|
| `queue` | up to `O(N)` (in the worst case all nodes can sit in the queue) |
| `vis[]` | `O(N)` |
| `bfs[]` (answer list) | `O(N)` |

```
SC = O(3N) ≈ O(N)
```

**Note on the adjacency list:** The adjacency list also takes `O(2E)` space, but since it is **given to you as input**, it is not counted as *extra* space used by your algorithm. You may omit it. (If you had to build it yourself, you would add `O(2E)`.)

### 10.2 Time Complexity

Build the reasoning up in the same order as the lecture:

**Observation 1 — every node enters the BFS exactly once.**
Because of the `vis` array, a node is marked the first time it is discovered and can never be pushed again. So the `while(!q.empty())` loop body runs exactly **N times** → `O(N)`.

**Observation 2 — for each popped node, the inner `for` loop runs over all its neighbours.**
If a node has 3 neighbours, the inner loop runs 3 times. The number of neighbours of a node is precisely its **degree**.

> **On-screen definition:** `Degrees = No. of neighbour nodes`

**Observation 3 — summing the inner loop over all nodes.**
The inner `for` loop, across the whole algorithm, runs:

```
degree(node_1) + degree(node_2) + ... + degree(node_N)  =  total degree of the graph
```

And from **Lecture 1**, we already proved:

> **Sum of degrees of all nodes in an undirected graph = 2 × E**
> (Because every single edge contributes +1 to the degree of each of its two endpoints.)

So the inner loop contributes `O(2E)` in total.

**Final result:**

```
TC = O(N) + O(2E)
```

Where `N` = number of nodes and `E` = number of edges.

### 10.3 Structure that produces this complexity

```
while(!q.empty())          ->  runs for all N nodes      ->  O(N)
    for(auto it : adj[node])  ->  runs for degree(node)   ->  Σ degrees = O(2E)
```

It is a common mistake to look at a nested loop and say `O(N × something)`. Here the inner loop does **not** run `N` times per node — it runs `degree(node)` times, and degrees **sum** to `2E` across the whole run. That is why it is `O(N) + O(2E)` and not `O(N × E)`.

**For a directed graph**, each edge appears in exactly one adjacency list, so the total degree sum is `E`, giving `O(N) + O(E)`.

---

## 11. Common Mistakes, Edge Cases & Interview Points

| Point | Detail |
|---|---|
| **Wrong array size** | 1-based graph with `N` nodes needs `vis` of size `N + 1`. In the dry-run graph, nodes go up to `9`, so `vis` is of **size 10**. Using size `N` on a 1-based graph → out-of-bounds. |
| **Mark visited when pushing, not when popping** | If you mark at pop time, two different parents can push the same node before it is ever popped, producing duplicates in the output. Always `vis[it] = 1` at the moment of `q.push(it)`. |
| **Forgetting `vis` entirely** | In a tree this "works", but in a graph with a cycle it produces an **infinite loop**, because neighbours keep pushing each other back and forth (e.g. `1 → 2 → 1 → 2 → …`). |
| **Order within a level** | Any ordering within a level is a correct BFS. The judge's expected output normally follows the adjacency-list order, so do not reorder the adjacency list. |
| **Traversal depends entirely on the starting node** | The same graph gives `1 2 6 3 4 7 8 5` from node `1` and `6 1 7 8 2 5 3 4` from node `6`. |
| **Only one node can be at level 0** | Levels are distances *relative to the start*, not fixed labels. |
| **`front()` + `pop()` in C++** | `q.pop()` in C++ returns `void`. You must read `q.front()` first, then pop. In Java, `q.poll()` does both. |
| **Disconnected graphs (important edge case)** | This code starts from a single node and therefore only visits the **connected component containing that node**. If the graph is disconnected, nodes in other components will never be printed. To cover all of them, you would wrap the BFS in an outer loop: `for(i = 0; i < V; i++) if(!vis[i]) bfs(i);` — this idea is used heavily in later problems (counting connected components, etc.). |
| **BFS is not just traversal** | Because BFS explores strictly level by level, the level of a node = its **shortest distance in edges** from the source. This makes BFS the standard algorithm for shortest path in an **unweighted** graph. |

---

## 12. Quick Revision Summary

```
BFS = Breadth First Search = Level-wise traversal

Needs:  queue (FIFO)  +  visited array  +  answer list

ALGORITHM
---------
1. vis[start] = 1 ; q.push(start)
2. while queue is not empty:
       node = q.front(); q.pop()
       add node to answer
       for every neighbour 'it' in adj[node]:
            if not visited:
                 vis[it] = 1
                 q.push(it)
3. return answer

WHY IT IS LEVEL-WISE : queue is FIFO, so all level-k nodes enter
                       (and hence exit) before any level-(k+1) node.

WHY vis IS NEEDED    : graphs have cycles / multiple paths → infinite loop otherwise.

TC = O(N) + O(2E)        [ N pops, and Σ degrees = 2E for undirected ]
SC = O(3N) ≈ O(N)        [ queue + vis + answer; adjacency list is input ]

Directed graph → same code, TC = O(N) + O(E)
```

---

**Next lecture:** DFS — Depth First Search traversal.
