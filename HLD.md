
### Heavy-Light Decomposition
Heavy-Light Decomposition (HLD) is a powerful technique used in tree algorithms to efficiently answer queries and perform updates on paths or subtrees of a tree. It is particularly useful in problems involving Lowest Common Ancestor (LCA), path queries, and subtree queries.
> special character : perform updates on paths or subtrees

---

### **Overview of Heavy-Light Decomposition (HLD)**

HLD decomposes a tree into "heavy" and "light" edges:
1. **Heavy Edge**: An edge `(u, v)` is heavy if the subtree size of `v` is the largest among all children of `u`.
2. **Light Edge**: All other edges are light.

The goal of HLD is to break the tree into a small number of paths such that each path can be processed efficiently. This decomposition allows us to reduce the complexity of path queries or updates from `O(n)` to `O(log²(n))` or `O(log(n))` (depending on the implementation).

---

### **Steps in HLD**

#### **Step 1: Subtree Size Calculation**
The first step is to calculate the size of the subtree for each node. This is done using a Depth-First Search (DFS). The subtree size is used to determine heavy edges.

#### **Proof**:
- The subtree size of a node `u` is the total number of nodes in the subtree rooted at `u`, including `u` itself.
- By definition, the heavy edge is the one leading to the child with the largest subtree size. This ensures that the heavy path contains as many nodes as possible, minimizing the number of light edges.

---

#### **Step 2: Decomposing the Tree**
Using the subtree sizes, the tree is decomposed into heavy and light edges:
- Start from the root and assign it to a "chain."
- Follow the heavy edge to extend the current chain.
- When a light edge is encountered, start a new chain.

#### **Proof**:
- Each node belongs to exactly one chain.
- The number of chains is proportional to the number of light edges, which is at most `O(log(n))` for a tree of size `n`. This is because every time we move along a light edge, the size of the subtree decreases by at least half.

---

#### **Step 3: Path Queries**
To answer queries on paths (e.g., sum, max, min), we process the path by jumping between chains. Within a chain, queries can be answered efficiently using a Segment Tree or Binary Indexed Tree (BIT).

#### **Proof**:
- The number of chains a path crosses is at most `O(log(n))` because the number of light edges on the path is bounded by `O(log(n))`.
- Within each chain, queries are processed in `O(log(n))` time using a Segment Tree or BIT.

---

#### **Step 4: Subtree Queries**
For subtree queries, we use the Euler Tour Technique to flatten the tree into an array. This allows subtree queries to be answered using range queries on the array.

#### **Proof**:
- The Euler Tour ensures that all nodes in the subtree of a node `u` are contiguous in the flattened array.
- Range queries on the array can be processed in `O(log(n))` time using a Segment Tree or BIT.

---

### **Implementation Details**

Below is a basic implementation of HLD:

```cpp
#include <bits/stdc++.h>
using namespace std;

const int MAXN = 1e5 + 5;
vector<int> tree[MAXN];
int parent[MAXN], depth[MAXN], subtree_size[MAXN];
int chain_head[MAXN], chain_index[MAXN], pos_in_base[MAXN];
int base_array[MAXN], seg_tree[4 * MAXN];
int n, curr_pos = 0;

// Step 1: Calculate subtree sizes and depths
void dfs(int node, int par) {
    parent[node] = par;
    subtree_size[node] = 1;
    for (int &child : tree[node]) {
        if (child == par) continue;
        depth[child] = depth[node] + 1;
        dfs(child, node);
        subtree_size[node] += subtree_size[child];
        // Heavy child first
        if (subtree_size[child] > subtree_size[tree[node][0]]) {
            swap(child, tree[node][0]);
        }
    }
}

// Step 2: Decompose the tree into chains
void hld(int node, int par) {
    if (chain_head[curr_pos] == -1) chain_head[curr_pos] = node;
    chain_index[node] = curr_pos;
    pos_in_base[node] = curr_pos++;
    base_array[curr_pos] = node; // Store node values in base array

    for (int child : tree[node]) {
        if (child == par) continue;
        if (child == tree[node][0]) {
            hld(child, node); // Heavy edge
        } else {
            curr_pos++;
            hld(child, node); // Light edge
        }
    }
}

// Step 3: Build Segment Tree (for path queries)
void build_seg_tree(int node, int start, int end) {
    if (start == end) {
        seg_tree[node] = base_array[start];
        return;
    }
    int mid = (start + end) / 2;
    build_seg_tree(2 * node, start, mid);
    build_seg_tree(2 * node + 1, mid + 1, end);
    seg_tree[node] = seg_tree[2 * node] + seg_tree[2 * node + 1];
}

// Query and update functions for Segment Tree would go here...

int main() {
    cin >> n;
    for (int i = 1; i < n; i++) {
        int u, v;
        cin >> u >> v;
        tree[u].push_back(v);
        tree[v].push_back(u);
    }

    memset(chain_head, -1, sizeof(chain_head));
    dfs(1, -1);
    hld(1, -1);
    build_seg_tree(1, 0, n - 1);

    // Process queries...
    return 0;
}
```

---

### **Key Observations**
1. **Heavy Path Length**: The heavy path minimizes the number of chains, ensuring efficient queries.
2. **Light Edges**: The number of light edges is logarithmic in the size of the tree.
3. **Segment Tree**: Efficiently handles range queries within a chain.

---
</assistant>
