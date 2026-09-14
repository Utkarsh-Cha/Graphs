# DFS Traversal in a Graph — Complete Study Notes
### (Striver Graph Series — Lecture 6: Depth First Search)

---

## 1. Where DFS Fits In

A graph can be traversed in two standard ways. This lecture covers the **second** of them.

| Traversal | Full Form | Core Idea | Data Structure Used |
|---|---|---|---|
| BFS | Breadth First Search | Explores **level by level** — finish all nodes at distance 1, then all at distance 2, etc. | Queue |
| DFS | **Depth First Search** | Explores **depth first** — go as deep as possible along one path, then backtrack | Recursion (implicitly a Stack) |

The instructor deliberately stresses the word **"depth"** repeatedly at the start of the lecture. That repetition is the whole intuition of the algorithm: *once you step into a neighbour, you do not stop there — you keep diving deeper until you physically cannot go any further. Only then do you come back.*

**Inputs given to you (same as BFS):**
1. A graph
2. A **starting node**

---

## 2. The Example Graph Used Throughout

The graph used in the entire lecture is an **undirected, connected graph with 8 nodes (1 to 8)**.

**Edges:**
```
1 — 2
1 — 3
2 — 5
2 — 6
3 — 4
3 — 7
4 — 8
7 — 8
```

**Structure, described in words:**
- Node `1` is the top, branching into `2` (left side) and `3` (right side).
- The left branch: `2` has two children `5` and `6` (both are dead-ends).
- The right branch: `3` connects to `4` and `7`. And `4—8` and `7—8` exist, which means **`3, 4, 8, 7` forms a cycle** (3 → 4 → 8 → 7 → 3).

> **Note on a small slip in the lecture:** while pointing at the diagram the instructor once says node 1's neighbours are "2, 3, 4". The adjacency list he actually writes on screen — and the one the code follows — is `1 -> {2, 3}`. Node 4 is reached via node 3, **not** directly from 1. Use the adjacency list below as the source of truth.

**Adjacency List (as written on screen):**
```text
1 -> {2, 3}
2 -> {1, 5, 6}
3 -> {1, 4, 7}
4 -> {3, 8}
5 -> {2}
6 -> {2}
7 -> {3, 8}
8 -> {4, 7}
```

Notice it is undirected, so every edge appears **twice** (e.g. `4` appears in 3's list, and `3` appears in 4's list). Remember this — it is the basis of the time-complexity derivation later.

---

## 3. Walkthrough 1 — Starting Node = 1 (Hand Traversal)

This is the manual, intuition-building traversal done on the whiteboard before any code.

**Step by step:**

1. Start at **1**. Mark it visited and record it. → `1`
2. From 1 you may go to 2 **or** 3 — *your choice*. Say you go left, to **2**. → `1 2`
3. **Very important:** you do **not** stop at 2. DFS means keep going deeper. From 2 you can go to 5 or 6. Generically we take whoever is first in the adjacency list (i.e., left to right). So go to **5**. → `1 2 5`
4. Node **5 has no further depth** (its only neighbour 2 is already visited). So you **come back** to 2.
5. From 2, the remaining unvisited neighbour is **6**. Go to 6. → `1 2 5 6`
6. **6 has no depth either** → come back to 2. 2 has no further children → come back to 1.
7. The entire left portion's depth is now completed. Only now do we start the next portion: **3**. → `1 2 5 6 3`
8. From 3 you may go to 4 or 7 — again your choice. The instructor chooses **7** in this hand-traversal. → `1 2 5 6 3 7`
9. 7's depth is **8** → go to 8. → `... 3 7 8`
10. 8's depth is **4** → go to 4. → `... 3 7 8 4`
11. From 4, the only other neighbour is 3, which is **already done**. So you cannot go anywhere. Come back → nothing further → come back → nothing further → come back. The whole portion is finished.

**Resulting DFS traversal:**
```
1 2 5 6 3 7 8 4
```

**Extra point made on screen:** had there been extra nodes, say `9` and `10`, hanging off some node in that branch, then while backtracking you would have descended into `9`, `10` as well before finally returning. Backtracking is not "giving up" — it is returning to explore *remaining* unexplored depth.

### 3.1 DFS Is Not Unique
The instructor stresses: **it is not necessary that you go in that exact order.** You could equally have done:

```
1 → 3 → 4 → 8 → 7 → (back) → 2 → 6 → 5
```

Any order is a valid DFS **as long as you are always travelling in depth**. The specific sequence you get depends on the order in which neighbours appear in the adjacency list. Depth-first behaviour is what defines DFS, not one fixed output.

---

## 4. Walkthrough 2 — Starting Node = 3

To reinforce that the answer depends on the starting node, the traversal is redone with **start = 3**.

1. **3** is the first node printed, for sure. → `3`
2. From 3 you may go to 1, 4, or 7 — your choice. Choose **4**. → `3 4`
3. From 4 → **8**, from 8 → **7**. → `3 4 8 7`
4. From 7, both neighbours (3 and 8) are already visited. Cannot go further → come back → come back → come back. **This portion's depth is complete.**
5. Now go the other way from 3, which is **1**. → `3 4 8 7 1`
6. From 1 → **2**, and from 2 do the same thing: **5**, then **6** (or 6 then 5). → `3 4 8 7 1 2 5 6`
7. Come back up. **Key check:** when we are back at 3, will it now go to 7? **No — 7 has already been covered** during the first branch.

**Resulting DFS traversal:**
```
3 4 8 7 1 2 5 6
```

> **Takeaway:** There can be plenty of valid DFS traversals for the same graph, depending on (a) the starting node and (b) the ordering inside the adjacency list.

---

## 5. Why Recursion?

**Question posed:** which algorithm naturally "goes deep, solves it, and comes back"?

**Answer: Recursion.**

That go-deep-then-return pattern *is* exactly what a recursive call does — the call dives in, does its job at the bottom, and unwinds back up the call stack. So DFS is implemented with recursion, and the "coming back" in the walkthrough above is literally the recursion **returning**.

> **Instructor's strong warning:** If you have not studied recursion, go back and watch the **Recursion (Basics to Advanced) and Backtracking series, at least videos 1 through 7**, before continuing. *"The entire graph without recursion will be a nightmare."* You must understand how the recursive call stack works, otherwise graph DFS will be a big mess.

---

## 6. Setting Up Before the Algorithm Runs

### 6.1 Store the Graph in an Adjacency List
Whatever the picture looks like, the first step is to represent the graph as an **adjacency list** (shown in Section 2).

### 6.2 Create the Visited Array

Check the indexing carefully:
- This diagram uses nodes **1 to 8** → **1-based indexing** → create an array of **size 9** (indices `0` to `8`), so that index 8 is valid.
- If the problem gives **0-based** nodes (`0` to `V-1`), create an array of size `V`.

> This "check the indexing" habit is exactly the same as in BFS. Getting it wrong causes off-by-one / out-of-bounds bugs.

Initially, mark only the **starting node** as visited; everything else stays `0`:

```text
Index: 0  1  2  3  4  5  6  7  8
vis:   0  1  0  0  0  0  0  0  0
        ^ start node = 1 marked visited
```

### 6.3 Call the Recursive DFS Function
Then simply call `dfs(startNode)`. Everything else is handled by recursion.

---

## 7. The DFS Function — Line-by-Line Logic

**Pseudocode written on screen:**

```cpp
dfs(node)
{
    vis[node] = 1;          // 1. the moment I enter, this node is DONE
    hist.add(node);         // 2. record it — this is the actual "traversal" output
    for(auto it : adj[node])// 3. look at every neighbour of this node
    {
        if(!vis[it])        // 4. only dive into a neighbour that is NOT yet visited
            dfs(it);        // 5. go into its depth (recursive call)
    }
}
```

### What each line means and *why* it is there

**Line 1 — `vis[node] = 1`**
The moment control enters the function, the node announces: *"Hey, listen — this guy is done, and this is a part of my DFS."* Marking immediately on entry is what stops the same node from being entered twice.

**Line 2 — `hist.add(node)`**
This is the line that actually performs the "traversal" — since you are *visiting* the node right now, you store it. The order in which nodes get pushed into this list **is** the DFS traversal order.

**Line 3 — `for(auto it : adj[node])`**
You must now go into the depth of each neighbour. Where are the neighbours stored? In the adjacency list. So for any node, you pick up `adj[node]` — that vector/list *is* the list of neighbours — and run a for-each loop over it.

**Line 4 — `if(!vis[it])`**
This is the crucial guard. Consider node 2: its neighbours are `{1, 5, 6}`. From 2 you must **not** go back to 1, because 1 is where you came from and it's already visited. Rule: *a node that has already been visited in this traversal is never visited again.* Or stated as the condition itself: **"this node has to be unvisited for me to go into the depth of it."** Without this check on an undirected graph you would bounce `1 → 2 → 1 → 2 → ...` forever.

**Line 5 — `dfs(it)`**
The recursive dive.

### How the loop and recursion interleave (important execution detail)

For node 1 with `adj[1] = {2, 3}`:

- The first iteration picks `it = 2`, so **`dfs(2)` is called**.
- `dfs(2)` **runs to completion** — it goes depth, depth, depth, and comes all the way back — *before* the loop moves on.
- Only **after** `dfs(2)` fully returns does the loop advance to `it = 3` and call `dfs(3)`.

This is the entire difference from BFS: the neighbour is fully explored in depth before the next neighbour is even touched.

---

## 8. Complete Dry Run (Start = 1, Following the Adjacency List Order)

Now we follow the adjacency list **strictly in order** (not the free-hand choice of Section 3), which is what the code will actually do.

| Step | Call | `vis` marked | Neighbours checked | Action |
|---|---|---|---|---|
| 1 | `dfs(1)` | 1 | `{2, 3}` | 2 unvisited → call `dfs(2)` |
| 2 | `dfs(2)` | 2 | `{1, 5, 6}` | 1 visited → skip; 5 unvisited → call `dfs(5)` |
| 3 | `dfs(5)` | 5 | `{2}` | 2 visited → **no further calls** → return |
| 4 | back in `dfs(2)` | — | next neighbour `6` | 6 unvisited → call `dfs(6)` |
| 5 | `dfs(6)` | 6 | `{2}` | 2 visited → no further calls → return |
| 6 | back in `dfs(2)` | — | no neighbours left | return to `dfs(1)` |
| 7 | back in `dfs(1)` | — | next neighbour `3` | 3 unvisited → call `dfs(3)` |
| 8 | `dfs(3)` | 3 | `{1, 4, 7}` | 1 visited → skip; 4 unvisited → call `dfs(4)` |
| 9 | `dfs(4)` | 4 | `{3, 8}` | 3 visited → skip; 8 unvisited → call `dfs(8)` |
| 10 | `dfs(8)` | 8 | `{4, 7}` | 4 visited → skip; 7 unvisited → call `dfs(7)` |
| 11 | `dfs(7)` | 7 | `{3, 8}` | **both already visited** → return |
| 12 | unwind | — | — | 8 done → return; 4 done → return |
| 13 | back in `dfs(3)` | — | next neighbour `7` | **7 is now visited → call is SKIPPED** |
| 14 | unwind | — | — | 3 done → return; 1 done → return. **Finished.** |

**Output (order of insertion into the list):**
```
1 2 5 6 3 4 8 7
```

### The Recursion Call Tree

```text
                 dfs(1)
                /      \
          dfs(2)        dfs(3)
          /    \         /    \
     dfs(5)  dfs(6)  dfs(4)   dfs(7)  ← this branch is NEVER taken (7 already visited)
                        |
                     dfs(8)
                        |
                     dfs(7)
```

### The Single Most Important Observation From This Dry Run

Node 3 had two unvisited-looking neighbours: `4` and `7`. It descended into **4 first**. But that descent (`4 → 8 → 7`) **reached 7 by another path**. So when the loop in `dfs(3)` finally comes around to `7`, the `if(!vis[it])` check fails and **the call is never made**. There is no need to visit 7 again — it was already covered by that path.

This is exactly why the `vis` check lives *inside the loop* and is re-evaluated each iteration, not decided once up front.

> **Note:** the on-screen highlighted final sequence reads `1 2 5 6 4 8 7`; node `3` is clearly just omitted by mistake while writing, since `dfs(3)` is drawn in the call tree and must record itself before descending to 4. The correct result is `1 2 5 6 3 4 8 7`.

### Also note
Whether you go `3 → 4 → 8 → 7` or `3 → 7 → 8 → 4` depends purely on **where 4 and 7 lie in 3's adjacency list**. If the list were stored as `3 -> {1, 7, 4}`, you would have gone the other way. *"So depends on the adjacency list, which traversal you will traverse."*

---

## 9. The Code

**Problem context (GeeksforGeeks practice):** given a **connected undirected graph**, return its DFS traversal starting from the **0th vertex**. Note this problem is **0-based indexed**, so `vis` is of size `V` and `start = 0`. The graph itself is already given to you — **you do not need to build it**.

### 9.1 C++ Solution

```cpp
class Solution {
    private:
        void dfs(int node, vector<int> adj[], int vis[], vector<int> &ls) {
            vis[node] = 1;
            ls.push_back(node);
            // traverse all its neighbours
            for(auto it : adj[node]) {
                if(!vis[it]) {
                    dfs(it, adj, vis, ls);
                }
            }
        }
    public:
        // Function to return a list containing the DFS traversal of the graph.
        vector<int> dfsOfGraph(int V, vector<int> adj[]) {
            int vis[V] = {0};
            int start = 0;
            vector<int> ls;
            dfs(start, adj, vis, ls);
            return ls;
        }
};
```

### 9.2 Java Solution (identical logic, near-identical syntax)

```java
class Solution {
    public static void dfs(int node, boolean vis[],
                           ArrayList<ArrayList<Integer>> adj,
                           ArrayList<Integer> ls) {
        vis[node] = true;
        ls.add(node);

        for(Integer it: adj.get(node)) {
            if(vis[it] == false) {
                dfs(it, vis, adj, ls);
            }
        }
    }

    // Function to return a list containing the DFS traversal of the graph.
    public ArrayList<Integer> dfsOfGraph(int V, ArrayList<ArrayList<Integer>> adj) {
        boolean vis[] = new boolean[V];
        vis[0] = true;
        ArrayList<Integer> ls = new ArrayList<>();
        dfs(0, vis, adj, ls);
        return ls;
    }
}
```

### 9.3 Notes on the Code

**Which parameters the helper needs, and why:**
- `node` — the node currently being processed.
- `adj` — you must carry the adjacency list, since you need the neighbours at every level of recursion.
- `vis` — you must carry the visited array, so the "don't re-enter" rule is shared globally across all calls.
- `ls` — you must carry the output list so every call can append to the *same* list.

**Pass `ls` by reference (`vector<int> &ls`) in C++.** If you pass it by value, each recursive call gets its own copy and your traversal will be lost. In Java, `ArrayList` is a reference type so this is automatic.

**Why `dfs` is `private` and `dfsOfGraph` is `public`:** `dfs` is just the internal recursive helper; the judge only calls `dfsOfGraph`.

**The driver function does only 4 things:**
1. Create `vis` (all zeros).
2. Set `start = 0`.
3. Create the output list `ls`.
4. Call `dfs(start, adj, vis, ls)` and, once the entire DFS completes, `return ls`.

**Small redundancy in the Java version:** `vis[0] = true;` is set in the driver *and* `vis[node] = true;` is set on entry to `dfs`. This is harmless duplication — the line inside `dfs` already handles it, which is why the C++ version omits the pre-marking.

**Compile-time mistake the instructor actually hit (16:46):** he first wrote the visited array size using `N` instead of `V` — *"as usual, our common mistake."* Always use the variable that the function signature actually gives you (`V` here).

**Submission result:**
- `Problem Solved Successfully`
- `Test Cases Passed: 13 / 13`
- `Total Time Taken: 0.01`

---

## 10. Space Complexity

```text
SC → O(N) + O(N) + O(N) ≈ O(N)
```

| Component | Cost | Reason |
|---|---|---|
| Visited array | `O(N)` | one slot per node |
| Traversal list | `O(N)` | at most N nodes get stored in the DFS output |
| Recursion stack space | `O(N)` | worst case depth of recursion |

**Why the recursion stack can reach O(N):** recall from recursion that every pending call occupies stack space. In the **worst case — a skewed graph** like

```
1 → 2 → 3 → 4 → ... → N
```

there is one single long chain, so all N calls are alive on the stack at the same time before any of them returns. Hence `O(N)`.

**Final space complexity: `O(N)`**, *not counting the adjacency list itself* (that is the input, and it costs `O(2E)` for an undirected graph).

---

## 11. Time Complexity

The shape of the algorithm is:

```text
f()
{
    for( neighbors )
    {
        f();
    }
}
```

**Reasoning, built up step by step:**

1. **The function is called once per node.** Because of the `vis` check, you traverse each node exactly once — `dfs(1)` happens once, `dfs(2)` happens once, and so on. That contributes **`O(N)`**.

2. **Inside each call, you iterate over that node's neighbours.** For node 3, the neighbours are `{1, 7, 4}` — and the count of neighbours of a node *is its **degree***.

3. **Summing across all nodes:** the total loop work is
   `degree(1) + degree(2) + ... + degree(N)` = **summation of all degrees**.

4. **From Lecture 1:** for an undirected graph, the **sum of degrees = 2 × E** (each edge contributes 1 to the degree at each of its two endpoints).

**Time complexity (undirected graph):**
```text
TC → O(N) + O(2 × E)
```

**For a directed graph:** the factor of 2 disappears (each edge appears in exactly one adjacency list), so it boils down to
```text
TC → O(N) + O(E)
```

---

## 12. Quick Revision Sheet

| Point | Detail |
|---|---|
| Idea | Go as deep as possible, then backtrack |
| Implementation | Recursion (implicit stack) |
| Order of operations inside `dfs` | mark visited → store node → loop neighbours → recurse on unvisited ones |
| Guard condition | `if(!vis[it])` — prevents infinite loops on undirected edges and re-visits |
| Traversal unique? | **No.** Depends on starting node **and** adjacency-list ordering |
| Example output (start = 1, adj-list order) | `1 2 5 6 3 4 8 7` |
| Example output (start = 3) | `3 4 8 7 1 2 5 6` |
| Time Complexity (undirected) | `O(N) + O(2E)` |
| Time Complexity (directed) | `O(N) + O(E)` |
| Space Complexity | `O(N)` — visited + list + recursion stack |
| Common bug 1 | Forgetting 1-based vs 0-based indexing when sizing `vis` |
| Common bug 2 | Passing the output list by value instead of by reference in C++ |
| Common bug 3 | Sizing the array with the wrong variable (`N` vs `V`) |
| Prerequisite | Recursion series videos 1–7 |
