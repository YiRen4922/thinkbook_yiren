---
tags:
  - 考研408
  - 考研408/数据结构/图
  - 考研408/优先级/S级
  - 考研408/算法统计
Date: Sun Sep 13 2026
---
# 图的遍历：BFS 与 DFS

> 图的两种基本遍历：**BFS（广度优先，队列）** 按层扩展，**DFS（深度优先，递归/栈）** 一路深入再回溯。408 高频考点：遍历序列不唯一、调用次数 = 连通分量数、与存储结构的关系。

## 算法思想

- **BFS**：类似二叉树**层序遍历**。访问一个顶点 → 将其**所有未被访问的邻接点入队**，队头出队继续
- **DFS**：类似树的**先序遍历**。访问顶点 → 递归访问其第一个未访问邻接点，走到底再回溯

> BFS 需要**辅助队列**，DFS 需要**递归栈或显式栈**。两者都需要 `visited[]` 标记已访问顶点。

## 代码实现

### DFS（邻接表存储）

```c
bool visited[MAXV];
void DFS(Graph G, int v) {
    visited[v] = true; visit(v);
    for (ArcNode *p = G.adjlist[v].first; p; p = p->next)
        if (!visited[p->adjvex])
            DFS(G, p->adjvex);
}
// 调用：for (int i = 0; i < G.vexnum; i++)
//           if (!visited[i]) DFS(G, i);  // 非连通图需要多次调用
```

### BFS（邻接表存储）

```c
void BFS(Graph G, int v) {
    Queue Q; InitQueue(&Q);
    visited[v] = true; visit(v); EnQueue(&Q, v);
    while (!QueueEmpty(&Q)) {
        DeQueue(&Q, &v);
        for (ArcNode *p = G.adjlist[v].first; p; p = p->next) {
            if (!visited[p->adjvex]) {
                visited[p->adjvex] = true;
                visit(p->adjvex);
                EnQueue(&Q, p->adjvex);
            }
        }
    }
}
```

## 时间复杂度

| 存储结构 | BFS | DFS |
|---|---|---|
| 邻接矩阵 | $O(V^2)$ | $O(V^2)$ |
| 邻接表 | $O(V+E)$ | $O(V+E)$ |

- 空间复杂度均为 $O(V)$（`visited[]` + 队列/递归栈）

## 易错点

> [!warning] 易错点
> - 遍历序列**不唯一**：由存储结构（邻接矩阵/邻接表）和起始顶点决定
> - **调用 BFS/DFS 的次数 = 连通分量数**（无向图）/ 强连通分量数（有向图）
> - BFS 从顶点 $v$ 出发，第一次访问某顶点的**路径一定是最短路径**（无权图）
> - DFS 回溯时 `visited[]` 不重置——一旦标记，整个遍历过程不再重访
> - 邻接矩阵下 BFS/DFS 复杂度都是 $O(V^2)$，与边数无关

## 相关知识（考研408）

> [!note] 408 常考结论
> - BFS 可用于：无权图最短路径、判断图的连通性、层序遍历
> - DFS 可用于：有向图判环、拓扑排序、求强连通分量、判断二分图
> - BFS/DFS 序列的手写是选择题高频
> - 遍历序列中**没有体现边权信息**，只体现访问顺序

## 相关算法

- [[最短路径：Dijkstra与Floyd]]：BFS 是无权图最短路径的特例
- [[拓扑排序与关键路径]]：基于 DFS/BFS 的图的应用
- [[最小生成树：Prim与Kruskal]]：图的另一类经典应用
- [[链表高频操作]]：快慢指针在链表中的应用（非图）

## 例题

参见相关笔记中的具体例题。