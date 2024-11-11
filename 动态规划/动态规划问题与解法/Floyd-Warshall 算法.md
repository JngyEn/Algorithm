# 问题描述
---
Floyd-Warshall算法，用于解决加权图中任意两点之间的最短路径问题。这个算法不仅能处理正权边，还能处理负权边，但要求图中不能存在负权回路（即总权重为负数的环路）

# 五步走

## 1.确定 dp 数组及下标的含义

- 定义 `dp[k][i][j]` 表示通过前 `k` 个顶点（从 0 到 k-1）作为中间顶点时，顶点 `i` 到顶点 `j` 的最短路径长度。

## 2. 确定递推公式

- 如果不通过第 `k` 个顶点，则 `dp[k][i][j] = dp[k-1][i][j]`。
- 如果通过第 `k` 个顶点，则 `dp[k][i][j] = min(dp[k-1][i][j], dp[k-1][i][k] + dp[k-1][k][j])`。

## 3. DP 数组如何初始化

- 初始化 `dp` 数组，`dp[0][i][j]` 等于初始图的权重 `graph[i][j]`，如果 `i == j`，则 `dp[0][i][j] = 0`。

## 4. 确定遍历顺序

- 依次计算 `k` 从 `1` 到 `n`（顶点数量），`i` 从 `0` 到 `n-1`，`j` 从 `0` 到 `n-1`。

```
  for (int k = 0; k < n; ++k) {
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                if (dist[i][k] < INF && dist[k][j] < INF && dist[i][k] + dist[k][j] < dist[i][j]) {
                    dist[i][j] = dist[i][k] + dist[k][j];
                    next[i][j] = next[i][k];
                }
            }
        }
    }
```

# 复杂度分析

- **时间复杂度**：
    
    - 三重循环的时间复杂度是 `O(n^3)`，其中 `n` 是顶点数量。
    - 总的时间复杂度是 `O(n^3)`。
- **空间复杂度**：
    
    - `dist` 和 `next` 数组的空间复杂度是 `O(n^2)`。
    - 总的空间复杂度是 `O(n^2)`。
# 代码
---
```
[[include]] <iostream>
[[include]] <vector>
[[include]] <algorithm>

using namespace std;

const int INF = 1e9;  // 定义一个很大的数表示无穷大

// Function to implement Floyd-Warshall algorithm and track the path
pair<vector<vector<int>>, vector<vector<int>>> floydWarshall(vector<vector<int>>& graph) {
    int n = graph.size();
    vector<vector<int>> dist = graph;
    vector<vector<int>> next(n, vector<int>(n, -1));

    // Initialize the next array for path reconstruction
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            if (graph[i][j] != INF && i != j) {
                next[i][j] = j;
            }
        }
    }

    // Apply Floyd-Warshall algorithm
    for (int k = 0; k < n; ++k) {
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                if (dist[i][k] < INF && dist[k][j] < INF && dist[i][k] + dist[k][j] < dist[i][j]) {
                    dist[i][j] = dist[i][k] + dist[k][j];
                    next[i][j] = next[i][k];
                }
            }
        }
    }

    return {dist, next};
}

// Function to reconstruct the path from i to j using the next array
vector<int> reconstructPath(int i, int j, vector<vector<int>>& next) {
    if (next[i][j] == -1) return {};  // No path
    vector<int> path = {i};
    while (i != j) {
        i = next[i][j];
        path.push_back(i);
    }
    return path;
}

int main() {
    // Example graph
    vector<vector<int>> graph = {
        {0, 3, INF, INF},
        {2, 0, INF, INF},
        {INF, 7, 0, 1},
        {6, INF, INF, 0}
    };

    // Apply Floyd-Warshall algorithm
    pair<vector<vector<int>>, vector<vector<int>>> result = floydWarshall(graph);
    vector<vector<int>> dist = result.first;
    vector<vector<int>> next = result.second;

    // Output the shortest distances
    cout << "Shortest distances between every pair of vertices:" << endl;
    for (int i = 0; i < dist.size(); ++i) {
        for (int j = 0; j < dist[i].size(); ++j) {
            if (dist[i][j] == INF) {
                cout << "INF ";
            } else {
                cout << dist[i][j] << " ";
            }
        }
        cout << endl;
    }

    // Output the path from vertex 0 to vertex 3
    cout << "Path from vertex 0 to vertex 3:" << endl;
    vector<int> path = reconstructPath(0, 3, next);
    for (int v : path) {
        cout << v << " ";
    }
    cout << endl;

    return 0;
}

```