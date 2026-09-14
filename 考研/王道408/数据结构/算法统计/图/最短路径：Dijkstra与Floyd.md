---
tags:
  - 考研408
  - 考研408/数据结构/图
  - 考研408/优先级/S级
  - 考研408/算法统计
Date: Sun Sep 13 2026
---
# 最短路径：Dijkstra 与 Floyd

> 单源最短路 **Dijkstra（贪心）** 与多源最短路 **Floyd（动态规划）**，是 408 图论大题的必背模板。核心区别：Dijkstra 不能处理负权边，Floyd 可处理负权边但不能有负权回路。

## 算法思想

### Dijkstra（单源，贪心）

维护 `dist[]`（源点到各点的当前最短距离）与已确定顶点集合 $S$：

1. 初始：`dist[源点] = 0`，其余 `dist = ∞`
2. 每趟在**未确定**顶点中选 `dist` **最小**者 $u$ 加入 $S$
3. 用 $u$ **松弛**其邻接点的 `dist`

> 贪心前提：选择了当前最小 `dist` 的顶点后，它的 `dist` 就**不可能再被更新**——所以负权边会破坏该前提。

### Floyd（多源，动态规划）

以 $0..k$ 作为中间顶点逐步扩展：

$$A^{(k)}[i][j] = \min\left(A^{(k-1)}[i][j],\ A^{(k-1)}[i][k] + A^{(k-1)}[k][j]\right)$$

> $A^{(k)}[i][j]$ 表示"只允许经过编号 $\le k$ 的中间顶点"时 $i \to j$ 的最短路径长度。三重循环迭代 $n$ 轮得到所有点对最短路。

## 代码实现

### Dijkstra（邻接矩阵）

```c
void Dijkstra(int G[MAXV][MAXV], int n, int v0) {
    bool S[MAXV] = {false};
    int dist[MAXV], path[MAXV];     // path[i] = i 的前驱
    // 初始化
    for (int i = 0; i < n; i++) {
        dist[i] = G[v0][i];
        path[i] = (dist[i] < INF) ? v0 : -1;
    }
    S[v0] = true; dist[v0] = 0;
    for (int k = 0; k < n - 1; k++) {
        // 选未确定中 dist 最小的 u
        int u = -1, min = INF;
        for (int i = 0; i < n; i++)
            if (!S[i] && dist[i] < min) { min = dist[i]; u = i; }
        if (u == -1) break;
        S[u] = true;
        // 松弛
        for (int v = 0; v < n; v++)
            if (!S[v] && G[u][v] + dist[u] < dist[v]) {
                dist[v] = G[u][v] + dist[u];
                path[v] = u;
            }
    }
}
```

### Floyd（三重循环）

```c
void Floyd(int A[MAXV][MAXV], int n) {   // A 初始 = 邻接矩阵
    for (int k = 0; k < n; k++)          // k 作为中间顶点，必须在外层！
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                if (A[i][k] + A[k][j] < A[i][j])
                    A[i][j] = A[i][k] + A[k][j];
    // 判负环：结束后检查 A[i][i] < 0 则存在负权回路
}
```

## 时间复杂度

| 算法 | 时间 | 空间 | 特点 |
|---|---|---|---|
| Dijkstra（邻接矩阵） | $O(V^2)$ | $O(V)$ | 单源，**不能负权边** |
| Dijkstra（堆优化） | $O(E\log V)$ | $O(V)$ | 稀疏图优化 |
| Floyd | $O(V^3)$ | $O(V^2)$ | 多源，可负权边，**不可负权回路** |

## 易错点

> [!warning] 易错点
> - Dijkstra **不能处理负权边**——负权边可能使已确定顶点的 `dist` 再变小
> - Floyd 的**中间顶点 $k$ 必须在外层循环**，否则结果错误
> - 负权回路：`A[i][i] < 0` 说明存在负权回路，Floyd 结果无意义
> - **无权图**最短路径直接用 **BFS**，比 Dijkstra 简单
> - Dijkstra 手算时每趟要**同时更新** `dist` 和路径前驱

## 相关知识（考研408）

> [!note] 408 常考结论
> - Dijkstra 手算：逐趟更新 `dist` 表与路径过程（大题高频）
> - Floyd 手算：$A^{(0)} \to A^{(1)} \to \cdots \to A^{(n-1)}$ 矩阵逐步变化（大题）
> - 选择题常问：负权边存在时哪个算法失效？（Dijkstra）
> - 单源最短路也可用 BFS（无权图）、拓扑排序（DAG，最长路径）

## 相关算法

- [[图的遍历：BFS与DFS]]：BFS 是无权图最短路径的特例
- [[最小生成树：Prim与Kruskal]]：Prim 与 Dijkstra 贪心框架相似
- [[拓扑排序与关键路径]]：DAG 中求最长路径（关键路径）用拓扑序

## 例题

参见相关笔记中的具体例题。