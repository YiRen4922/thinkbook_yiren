---
tags:
  - 考研408
  - 考研408/数据结构/图
  - 考研408/优先级/S级
  - 考研408/算法统计
Date: Sun Sep 13 2026
---
# 最小生成树：Prim 与 Kruskal

> **连通无向带权图**的最小生成树（MST）：$n$ 个顶点、$n-1$ 条边、边权之和最小。408 高频考点：两种算法的加边/加点顺序手算、MST 不唯一性的判断。

## 算法思想

MST 的性质：**贪心**。两种经典算法分别从"点"和"边"两个角度贪心：

- **Prim（加点法）**：从任一顶点出发，每步在「已选集合 → 未选集合」的边中挑**权值最小**者，把对应顶点加入集合，直到覆盖所有顶点
- **Kruskal（加边法）**：所有边按权从小到大排序，依次选边；用**并查集**判断两端点是否已连通，**不成环才加入**，选满 $n-1$ 条停止

## 代码实现

### Kruskal（并查集判环，408 常考）

```c
typedef struct { int u, v, w; } Edge;  // 边：起点、终点、权值

int find(int parent[], int x) {
    while (parent[x] != x) x = parent[x];
    return x;
}
void unionSet(int parent[], int ru, int rv) { parent[ru] = rv; }

int Kruskal(Edge edges[], int n, int m) {   // n 顶点，m 边
    // 1. 边按权值从小到大排序（可用 qsort）
    qsort(edges, m, sizeof(Edge), cmp);
    int parent[n];  for (int i = 0; i < n; i++) parent[i] = i;
    int count = 0, sum = 0;
    for (int i = 0; i < m && count < n - 1; i++) {
        int ru = find(parent, edges[i].u);
        int rv = find(parent, edges[i].v);
        if (ru != rv) {                 // 不成环才加入
            unionSet(parent, ru, rv);
            sum += edges[i].w;
            count++;
        }
    }
    return (count == n - 1) ? sum : -1; // -1 表示不存在 MST
}
```

## 时间复杂度

| 算法 | 时间 | 空间 | 适用场景 |
|---|---|---|---|
| Prim（邻接矩阵） | $O(V^2)$ | $O(V)$ | **稠密图**（边多） |
| Prim（堆优化） | $O(E\log V)$ | — | 稀疏图优化 |
| Kruskal | $O(E\log E)$ | $O(E)$ | **稀疏图**（边少），排序主导 |

## 易错点

> [!warning] 易错点
> - MST **不唯一**（权值相等的边可互换），但**权值之和唯一**
> - 加的边/点顺序可能不唯一，但结果都正确
> - **回路中最大权边一定不在 MST 中**；最小权边不一定在（多选一时可不在）
> - Kruskal 判环必须用**并查集**，不能用"visited"标记（会漏判环）
> - 有向图不存在"最小生成树"（生成树针对**无向图**）

## 相关知识（考研408）

> [!note] 408 常考结论
> - 手算：按 Prim/Kruskal 规则写出加点/加边顺序
> - 判断某条边是否**一定在** MST 中（割的性质）
> - 负权边**不影响** MST 算法正确性（与 [[最短路径：Dijkstra与Floyd]] 不同）
> - MST 的应用：通信网络布线、电路板连线

## 相关算法

- [[最短路径：Dijkstra与Floyd]]：Prim 与 Dijkstra 的贪心框架相似（Prime 选边大师）
- [[拓扑排序与关键路径]]：同为图论中的优化问题
- [[图的遍历：BFS与DFS]]：Prim 的加点过程类似 BFS 层扩展

## 例题

参见相关笔记中的具体例题。