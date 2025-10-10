
 ## Euler Tour Technique
  It is a powerful algorithmic tool used in tree algorithms to preprocess a tree for answering various queries efficiently. It involves performing a Depth First Search (DFS) traversal of the tree and recording the entry and exit times of each node. This technique is particularly useful for solving problems related to subtree queries, Lowest Common Ancestor (LCA), and path queries.

---

### **Steps of the Euler Tour Technique**

1. **Tree Representation**:
   - The tree is represented as an adjacency list. Each node has a list of its children.

2. **DFS Traversal**:
   - Perform a DFS traversal starting from the root node.
   - Record the time when a node is first visited (entry time).
   - Record the time when the DFS finishes exploring all descendants of the node (exit time).

3. **Flattening the Tree**:
   - During the DFS, the nodes are visited in a specific order. This order can be stored in an array, effectively "flattening" the tree into a linear structure.
   - This flattened structure allows subtree queries to be reduced to range queries on the array.

4. **Subtree Queries**:
   - Using the entry and exit times, the subtree of a node can be represented as a contiguous range in the flattened array.
   - This allows subtree queries to be answered efficiently using data structures like Fenwick Trees or Segment Trees.

---

### **Proof of Correctness**

#### **1. Entry and Exit Times**:
- When a node `u` is visited during DFS, all its descendants are visited before the DFS backtracks to its parent. This ensures that:
  - The entry time of `u` is before the entry time of any of its descendants.
  - The exit time of `u` is after the exit time of all its descendants.

#### **2. Contiguous Subtree Representation**:
- The DFS traversal ensures that all nodes in the subtree of `u` are visited consecutively. This means the subtree of `u` corresponds to a contiguous range in the flattened array.

#### **3. Range Queries**:
- Since the subtree of a node is a contiguous range, any operation on the subtree (e.g., sum, maximum, etc.) can be reduced to a range query on the flattened array.

---

### **Implementation**

Below is an implementation of the Euler Tour Technique:

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;

vector<int> euler, entry, exit_time;

void dfs(int u, int p, vector<vector<int>> &adj) {
    entry[u] = euler.size();
    euler.push_back(u); // Add node to Euler tour
    for (int v : adj[u]) {
        if (v != p) {
            dfs(v, u, adj);
        }
    }
    exit_time[u] = euler.size();
    euler.push_back(u); // Add node again for exit time
}

signed main() {
    int n, q;
    cin >> n >> q;

    vector<vector<int>> adj(n + 1);
    for (int i = 2; i <= n; i++) {
        int u;
        cin >> u;
        adj[u].push_back(i);
    }

    euler.reserve(2 * n);
    entry.resize(n + 1);
    exit_time.resize(n + 1);

    dfs(1, -1, adj);

    // Example: Print the Euler tour
    cout << "Euler Tour: ";
    for (int x : euler) {
        cout << x << " ";
    }
    cout << endl;

    // Example: Print entry and exit times
    cout << "Entry and Exit Times:\n";
    for (int i = 1; i <= n; i++) {
        cout << "Node " << i << ": Entry = " << entry[i] << ", Exit = " << exit_time[i] << endl;
    }

    return 0;
}
```

---

### **Explanation of the Code**

1. **DFS Function**:
   - The `dfs` function performs a DFS traversal of the tree.
   - It records the entry time of a node when it is first visited.
   - It records the exit time of a node after all its descendants have been visited.

2. **Euler Tour**:
   - The `euler` array stores the nodes in the order they are visited during the DFS.
   - Each node appears twice in the Euler tour: once at its entry time and once at its exit time.

3. **Entry and Exit Times**:
   - The `entry` array stores the index of the first occurrence of each node in the Euler tour.
   - The `exit_time` array stores the index of the last occurrence of each node in the Euler tour.

---

### **Applications**

1. **Subtree Queries**:
   - To query the nodes in the subtree of a node `u`, use the range `[entry[u], exit_time[u]]` in the Euler tour.

2. **LCA Queries**:
   - Using the Euler tour and a Sparse Table, the LCA of two nodes can be computed efficiently.

3. **Path Queries**:
   - Path queries can be reduced to range queries on the Euler tour.

---

### **Complexity Analysis**

1. **Preprocessing**:
   - The DFS traversal takes **O(n)** time.

2. **Query Processing**:
   - Using a Segment Tree or Fenwick Tree, subtree queries can be answered in **O(log n)** time.

---

This technique is widely used in competitive programming and tree-based problems due to its simplicity and efficiency.
