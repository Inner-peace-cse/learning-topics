### Tree Re-rooting: A Detailed Explanation

Tree re-rooting is a powerful technique used to solve problems where you need to calculate some property for every node in a tree, and the property depends on the structure of the tree. Instead of recalculating the property from scratch for each node, re-rooting allows you to reuse previously computed values efficiently.

---

### Key Concepts of Tree Re-rooting

1. **Rooting the Tree**:
   - Choose an arbitrary node as the root (commonly node 1).
   - Perform a Depth-First Search (DFS) to calculate the desired property for the root node.

2. **Re-rooting Transition**:
   - When moving the root from a node \(a\) to one of its children \(b\), adjust the property for \(b\) based on the property of \(a\).
   - This adjustment is typically derived from the relationship between \(a\) and \(b\) (e.g., subtree sizes, distances, etc.).

3. **Efficiency**:
   - The first DFS calculates the property for the initial root in \(O(n)\).
   - The second DFS propagates the property to all other nodes in \(O(n)\), making the overall complexity \(O(n)\).

---

### Example Problem: Sum of Distances in a Tree

#### Problem Statement
Given a tree with \(n\) nodes, calculate the sum of distances from each node to all other nodes.

---

### Step-by-Step Solution

#### Step 1: Calculate the Sum of Distances for the Root Node
- Use a post-order DFS to calculate:
  - `subtree[node]`: The size of the subtree rooted at `node`.
  - `subSum[node]`: The sum of distances from `node` to all nodes in its subtree.

#### Step 2: Re-root the Tree
- Use a pre-order DFS to calculate the sum of distances for all other nodes using the formula:<br>
  `ans[b] = ans[a] - subtree[b] + (n - subtree[b])`
  - Nodes in b's subtree get closer by 1.
  - Nodes outside b's subtree get farther by 1.


---

### Clean Code Implementation

```cpp
#include <iostream>
#include <vector>
using namespace std;
using ll = long long;

const int N = 200001; // Maximum number of nodes

int n; // Number of nodes
vector<int> g[N]; // Adjacency list for the tree
int subtree[N]; // Size of the subtree rooted at each node
ll ans[N]; // Answer for each node (sum of distances to all other nodes)

// First DFS: Calculate subtree sizes and sum of distances for the root
ll dfs(int node, int parent) {
    subtree[node] = 1; // Each node is part of its own subtree
    ll distance_sum = 0; // Sum of distances for the current node
    for (int child : g[node]) {
        if (child == parent) continue; // Skip the parent
        distance_sum += dfs(child, node); // Recursively calculate for the child
        subtree[node] += subtree[child]; // Add the child's subtree size
    }
    distance_sum += subtree[node] - 1; // Add distances to all nodes in the subtree
    return distance_sum;
}

// Second DFS: Re-root the tree and calculate answers for all nodes
void calc(int node, int parent) {
    for (int child : g[node]) {
        if (child == parent) continue; // Skip the parent
        // Re-root the tree at the child
        ans[child] = ans[node] - subtree[child] + (n - subtree[child]);
        calc(child, node); // Recurse for the child
    }
}

int main() {
    cin >> n; // Input number of nodes

    // Input the tree edges
    for (int i = 0; i < n - 1; ++i) {
        int a, b;
        cin >> a >> b;
        g[a].push_back(b);
        g[b].push_back(a);
    }

    // Calculate the sum of distances for the root node
    ans[1] = dfs(1, -1);

    // Re-root the tree and calculate answers for all nodes
    calc(1, -1);

    // Output the answers
    for (int i = 1; i <= n; ++i) {
        cout << ans[i] << ' ';
    }
    cout << '\n';

    return 0;
}
```

---

### Explanation of the Code

1. **DFS Function**:
   - Calculates the size of each subtree (`subtree[node]`).
   - Calculates the sum of distances from the root to all nodes in its subtree (`subSum[node]`).

2. **Re-rooting Transition**:
   - When moving the root from \(a\) to \(b\):
     - Subtract the contribution of \(b\)'s subtree from \(a\)'s answer.
     - Add the contribution of the rest of the tree to \(b\)'s answer.

3. **Efficiency**:
   - The first DFS runs in \(O(n)\) to calculate the initial values.
   - The second DFS runs in \(O(n)\) to propagate the values to all nodes.

---

### Example Walkthrough

#### Input:
```
5
1 2
1 3
3 4
3 5
```

#### Tree Structure:
```
    1
   / \
  2   3
     / \
    4   5
```

#### Output:
```
6 9 5 8 8
```

#### Explanation:
- For node 1: Sum of distances = \(6\).
- For node 2: Re-root at 2, adjust distances → \(9\).
- For node 3: Re-root at 3, adjust distances → \(5\).
- For nodes 4 and 5: Re-root at 4 and 5, adjust distances → \(8\).

---

### Key Takeaways

1. **Re-rooting Formula**:
   - The transition formula is the heart of re-rooting problems. Derive it carefully based on the problem's requirements.

2. **Efficiency**:
   - Re-rooting reduces the complexity from \(O(n^2)\) to \(O(n)\) by reusing previously computed values.

3. **Applications**:
   - Re-rooting is widely used in problems involving distances, subtree properties, and dynamic tree algorithms.
