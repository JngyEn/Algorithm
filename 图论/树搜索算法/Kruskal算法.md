>Kruskal算法是一种用于找到**加权连通图**的最小生成树（MST）的贪心算法。

## 概念梳理
---
**连通图**：在无向图中，如果任意两个顶点之间都有路径相连，那么这个图就是连通图。对于Kruskal算法，图通常是连通图，因为我们需要找到一棵连接所有顶点的生成树。

## 算法原理
---
- **贪心策略**：Kruskal算法遵循贪心策略，每次选择当前权重最小的边，并确保不会形成环。这种策略保证了每一步选择都是局部最优的，最终可以得到全局最优解（即最小生成树）。
    
- **避免环的形成**：为了避免在添加边时形成环，Kruskal算法使用并查集（Union-Find）数据结构。并查集能够高效地管理和合并集合，并检测两个节点是否在同一个集合中，从而帮助我们判断是否会形成环。

## 实现步骤
---
- **边排序**：
    - 将图中的所有边按照权重从小到大排序。排序后的边列表保证了每次选择的边都是当前未处理的边中权重最小的。
- **初始化并查集**：
    - 每个节点开始时都是一个独立的集合。并查集用来管理这些集合，并支持两个关键操作：查找（Find）和合并（Union）。
    - 查找操作用于确定某个节点属于哪个集合。
    - 合并操作用于将两个集合合并成一个集合。
- **选择边**：
    - 按照排序后的边列表，依次处理每条边 `(u, v)`：
        - 使用并查集的查找操作检查节点 `u` 和节点 `v` 是否在同一个集合中：
            - 如果在同一个集合中，说明加入这条边会形成环，跳过这条边。
            - 如果不在同一个集合中，说明加入这条边不会形成环，将这条边添加到生成树中，并使用合并操作将这两个集合合并。
    - 重复上述过程，直到生成树中包含 `n-1` 条边（其中 `n` 是节点数）。

## 特殊结构
---
### 图的边排序

通过操作符重载来使用 `sort` 进行排序

```
struct Edge {
    int u, v, weight;
    bool operator<(const Edge& other) const {
        return weight < other.weight;
    }
};
```
### 并查集结构

```
// 并查集结构
int find(vector<int>& parent, int u) {
	// 父节点不是自己（已经被mst计入了）
    if (parent[u] != u) {
		//寻找到自己所属的父节点
        parent[u] = find(parent, parent[u]);
    }
    return parent[u];
}

void unionSets(vector<int>& parent, vector<int>& rank, int u, int v) {
    int rootU = find(parent, u);
    int rootV = find(parent, v);
    if (rootU != rootV) {
        if (rank[rootU] > rank[rootV]) {
            parent[rootV] = rootU;
        } else if (rank[rootU] < rank[rootV]) {
            parent[rootU] = rootV;
        } else {
            parent[rootV] = rootU;
            rank[rootU]++;
        }
    }
}

// 算法中并查集的初始化和使用
    vector<int> parent(n);
    vector<int> rank(n, 0);
    // 初始化父节点，parent[i]的值为i节点的父集合，初始父节点为自己
    for (int i = 0; i < n; ++i) {
        parent[i] = i;
    }

    vector<Edge> mst;
    for (const Edge& edge : edges) {
	    //边的两端不属于同一个集合，说明不会成环
        if (find(parent, edge.u) != find(parent, edge.v)) {
            mst.push_back(edge);
            unionSets(parent, rank, edge.u, edge.v);
        }
        if (mst.size() == n - 1) break;
    }

```

- `vector<int>& parent`：并查集的父节点数组，`parent[i]`存储元素`i`的父节点。
- `vector<int>& rank`：并查集的秩数组，`rank[i]`存储以`i`为根的树的秩（近似表示树的高度）
	- “按秩合并”是一种优化技术，目的是尽量保持树的高度较低，从而提高查找操作的效率。在并查集中，树的高度直接影响`find`操作的时间复杂度。通过将秩较小的树合并到秩较大的树中，可以避免树的高度迅速增加，从而保持较低的时间复杂度。