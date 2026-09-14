---
tags:
  - 考研408
  - 考研408/数据结构/树与二叉树
  - 考研408/优先级/S级
  - 考研408/算法统计
Date: Mon Sep 14 2026
---
# 二叉排序树（BST）

> 左子树所有结点值 < 根 < 右子树所有结点值的二叉树。**中序遍历得到递增有序序列**。查找/插入/删除均 $O(\log n)$ 平均、$O(n)$ 最坏。408 S 级考点：插入、删除三情况、ASL 计算。

## 算法思想

**核心性质**：对任意结点，左 < 根 < 右 → **中序序列递增有序**。

- **查找**：从根出发，`key < 当前` 走左、`key > 当前` 走右、相等即命中；走到空则失败——每次排除一棵子树
- **插入**：先按查找过程定位，失败位置就是插入点，**新结点总是叶子**
- **删除**：
  1. 叶子 → 直接删
  2. 只有一棵子树 → 子树顶替
  3. 有两棵子树 → 用**中序前驱/中序后继**（值上最接近的结点）替换后，转化为情况 1/2

## 代码实现

```c
typedef struct BSTNode { int data; struct BSTNode *lchild, *rchild; } BSTNode, *BSTree;

// 查找（递归），失败返回 NULL
BSTNode *BSTSearch(BSTree T, int key) {
    if (!T || T->data == key) return T;
    return key < T->data ? BSTSearch(T->lchild, key) : BSTSearch(T->rchild, key);
}

// 插入（递归，返回树根）
BSNode *BSTInsert(BSNode *T, int key) {
    if (!T) { T = (BSNode*)malloc(sizeof(BSNode)); T->data = key; T->lchild = T->rchild = NULL; return T; }
    if (key < T->data)      T->lchild = BSTInsert(T->lchild, key);
    else if (key > T->data) T->rchild = BSTInsert(T->rchild, key);
    return T;                                 // 相等则不插入（BST 不允许重复）
}

// 删除（找中序后继替换版）
BSNode *BSTDelete(BSNode *T, int key) {
    if (!T) return NULL;
    if (key < T->data)      T->lchild = BSTDelete(T->lchild, key);
    else if (key > T->data) T->rchild = BSTDelete(T->rchild, key);
    else {                                    // 找到待删结点
        if (!T->lchild) return T->rchild;     // 无左孩子（含叶子）：右子树顶替
        if (!T->rchild) return T->lchild;     // 无右孩子：左子树顶替
        // 有两棵子树：找中序后继（右子树最左下）替换
        BSTNode *s = T->rchild;
        while (s->lchild) s = s->lchild;      // 中序后继
        T->data = s->data;                    // 换成后继的值
        T->rchild = BSTDelete(T->rchild, s->data); // 删除后继结点（它至多一个右孩子）
    }
    return T;
}
```

## 时间复杂度

- 查找/插入/删除：$O(h)$，$h$ 为树高
- 平均（结点随机插入）：$O(\log n)$；**最坏 $O(n)$**：按有序序列插入退化为单链
- 空间：递归栈 $O(h)$

## 易错点

> [!warning] 易错点
> - **按有序序列依次插入 → BST 退化为单链**，ASL $O(n)$——这就是引入 AVL 的原因
> - 删除有两棵子树的结点：用**中序前驱或中序后继**替换，两者皆可
> - BST 中序遍历**必递增**（可用来判定一棵树是否为 BST）
> - 插入位置一定是**叶子**（叶子或空位置）
> - 等值元素处理：通常不允许重复或约定插到一侧

## 相关知识（考研408）

> [!note] 408 常考结论
> - ASL 成功/失败计算：与[[二分查找]]的判定树类似（BST 是动态查找，判定树是静态）
> - 408 常考"给定插入序列画 BST""删除后形态""中序序列"
> - BST 的查找过程等价于在判定树上走一条路径，比较次数 = 路径结点数
> - 与 [[AHU树]] 无关；但 BST 是 [[AVL平衡二叉树]]、[[红黑树]] 的基础

## 相关算法

- [[AVL平衡二叉树]]：保证树高 $O(\log n)$ 的严格平衡 BST
- [[红黑树]]：近似平衡的 BST（2022 新增考点）
- [[二分查找]]：BST 查找与折半查找的思想同源
- [[二叉树的遍历算法]]：中序遍历即有序序列

## 例题

参见相关笔记中的具体例题。