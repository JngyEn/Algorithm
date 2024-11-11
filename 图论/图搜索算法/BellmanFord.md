>检测有无负权环，通常用于有向图找root到每个节点的最短路径
>参考博客：https://programmercarl.com/kamacoder/0094.%E5%9F%8E%E5%B8%82%E9%97%B4%E8%B4%A7%E7%89%A9%E8%BF%90%E8%BE%93I.html#%E4%BB%80%E4%B9%88%E5%8F%AB%E5%81%9A%E6%9D%BE%E5%BC%9B

# 核心思想
---
从 对整个图进行 V-1 次松弛操作后，再进行一次，如果还有边减小，那么说明存在负权环。

1. **什么是松弛：**
	如果 通过 A 到 B 这条边可以获得更短的到达B节点的路径，即如果 `minDist[B] > minDist[A] + value`，那么我们就更新 `minDist[B] = minDist[A] + value` ，**这个过程就叫做 “松弛**” 。
2. **松弛顺序：**
	edgeList不需要进行排序。Bellman-Ford算法的核心在于通过V-1次遍历所有边来执行松弛操作，以确保找到从源点到每个顶点的最短路径。这些操作与边的顺序无关，只要能遍历所有边就可以进行正确的松弛操作。
	- 保证松弛到该边的时候 `distance[起点]` 不为 INT_MAX 就行，可以按照BFS或者DFS
3. **为什么是V-1次：**
	对所有边松弛**一次**，相当于计算 **起点** 到达与 起点**一条边**相连 的节点 的最短距离
	对所有边松弛**两次**，相当于计算 **起点** 到达与 起点**两条边**相连 的节点 的最短距离

	那么最多 V-1 次，就能获得所有边的（大部分时候不需要 V-1 次就行，提前剪枝）
	- 因为所有边都在 edgeList 中，所以 “起点**一条边**相连” 意思就是除了起点直接连接到这条边以外，没有其他路径可以抵达这个顶点，所以一次松弛就能找到最短路径
	- 而有多个路径可以抵达的，鉴于中间节点的最小距离可能改变，所以最坏情况下需要V-1 次松弛才能找到最短路径
4. **如何检测负权环：**
	如果 V-1 次之后，再进行一次松弛，还会有边的距离减小，那就说明有负权环

# 复杂度
---
- 时间复杂度： O(N * E) , N为节点数量，E为图中边的数量
- 空间复杂度： O(N) ，即 minDist 数组所开辟的空间
# 代码
---
```
[[include]] <iostream>
[[include]] <vector>
[[include]] <list>
[[include]] <climits>
using namespace std;

int main() {
    int n, m, p1, p2, val;
    cin >> n >> m;

    vector<vector<int>> grid;

    // 将所有边保存起来
    for(int i = 0; i < m; i++){
        cin >> p1 >> p2 >> val;
        // p1 指向 p2，权值为 val
        grid.push_back({p1, p2, val});

    }
    int start = 1;  // 起点
    int end = n;    // 终点

    vector<int> minDist(n + 1 , INT_MAX);
    minDist[start] = 0;
    for (int i = 1; i < n; i++) { // 对所有边 松弛 n-1 次
        for (vector<int> &side : grid) { // 每一次松弛，都是对所有边进行松弛
            int from = side[0]; // 边的出发点
            int to = side[1]; // 边的到达点
            int price = side[2]; // 边的权值
            // 松弛操作 
            // minDist[from] != INT_MAX 防止从未计算过的节点出发
            if (minDist[from] != INT_MAX && minDist[to] > minDist[from] + price) { 
                minDist[to] = minDist[from] + price;  
            }
        }
    }
    if (minDist[end] == INT_MAX) cout << "unconnected" << endl; // 不能到达终点
    else cout << minDist[end] << endl; // 到达终点最短路径

}
```

