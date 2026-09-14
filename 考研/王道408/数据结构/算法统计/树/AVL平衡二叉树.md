---
tags:
  - 考研408
  - 考研408/数据结构/树与二叉树
  - 考研408/优先级/S级
  - 考研408/算法统计
Date: Sun Sep 13 2026
---
# AVL 平衡二叉树

> 任意结点左右子树高度差 $|BF| \le 1$ 的二叉排序树，保证查找/插入/删除均为 $O(\log n)$。插入失衡后**旋转一次或两次即可恢复平衡**；删除失衡可能需要**多次调整**回溯到根。

## 算法思想

AVL 的核心是在二叉排序树的基础上维护**平衡因子** $BF = H_{\text{左}} - H_{\text{右}} \in \{-1,0,1\}$：

1. **插入**：沿查找路径找到插入位置，插入新结点后**向上回溯**更新 $BF$，遇到第一个 $|BF|=2$ 的结点时做旋转
2. **删除**：与普通 BST 删除相同（找前驱/后继替换），删除后**向上回溯**可能需要多次旋转

### 四种旋转

| 类型 | 失衡原因 | 调整方法 | 旋转次数 |
|---|---|---|---|
| LL | 插到左孩子的**左**子树 | 对失衡结点**右旋**（单旋） | 1 次 |
| RR | 插到右孩子的**右**子树 | 对失衡结点**左旋**（单旋） | 1 次 |
| LR | 插到左孩子的**右**子树 | 先对左孩子左旋，再对失衡结点右旋 | 2 次 |
| RL | 插到右孩子的**左**子树 | 先对右孩子右旋，再对失衡结点左旋 | 2 次 |

> 插入后旋转**一次或两次**即可恢复平衡，旋转后子树高度恢复到插入前 → 无需向上继续调整。
> **删除**后旋转**可能需要多次**，一路调整回根。

## 代码实现

### 结点定义

```c
typedef struct AVLNode {
    int data;
    int height;                         // 高度（平衡因子 = 左高 - 右高）
    struct AVLNode *lchild, *rchild;
} AVLNode;
```

### 辅助函数

```c
int height(AVLNode *T) { return T ? T->height : 0; }

int getBF(AVLNode *T) { return T ? height(T->lchild) - height(T->rchild) : 0; }

void updateHeight(AVLNode *T) {
    T->height = (height(T->lchild) > height(T->rchild) ? height(T->lchild) : height(T->rchild)) + 1;
}
```

### 右旋（LL 型）

```c
AVLNode *rotateRight(AVLNode *T) {
    AVLNode *p = T->lchild;
    T->lchild = p->rchild;
    p->rchild = T;
    updateHeight(T); updateHeight(p);
    return p;                           // p 成为新的子树根
}
```

### 左旋（RR 型）

```c
AVLNode *rotateLeft(AVLNode *T) {
    AVLNode *p = T->rchild;
    T->rchild = p->lchild;
    p->lchild = T;
    updateHeight(T); updateHeight(p);
    return p;
}
```

### 插入（含平衡调整）

```c
AVLNode *insert(AVLNode *T, int key) {
    if (!T) { T = (AVLNode*)malloc(sizeof(AVLNode)); T->data = key; T->lchild = T->rchild = NULL; T->height = 1; return T; }
    if (key < T->data) T->lchild = insert(T->lchild, key);
    else if (key > T->data) T->rchild = insert(T->rchild, key);
    else return T;                      // 相等则不插入
    updateHeight(T);
    int bf = getBF(T);
    // LL
    if (bf > 1 && key < T->lchild->data) return rotateRight(T);
    // RR
    if (bf < -1 && key > T->rchild->data) return rotateLeft(T);
    // LR
    if (bf > 1 && key > T->lchild->data) { T->lchild = rotateLeft(T->lchild); return rotateRight(T); }
    // RL
    if (bf < -1 && key < T->rchild->data) { T->rchild = rotateRight(T->rchild); return rotateLeft(T); }
    return T;
}
```

## 最少结点数

$$N(h) = N(h-1) + N(h-2) + 1, \quad N(0)=0,\ N(1)=1$$

$N(h)$ 近似斐波那契数列 → $n$ 个结点的 AVL 高度 $H \approx O(\log n)$，且 $H < 1.44\log_2(n+2)$。

## 时间复杂度

- 查找：$O(\log n)$
- 插入：$O(\log n)$（查找 + 旋转常数次）
- 删除：$O(\log n)$（查找 + 可能多次旋转回溯）
- **适合读多写少**的场景；插入删除常数比普通 BST 大

## 易错点

> [!warning] 易错点
> - **插入**后旋转**一次或两次**即可恢复，**删除**后可能**多次旋转**回溯到根
> - 判断旋转类型：看失衡结点 $BF = 2$ 时是插在**左孩子**（LL/LR）还是**右孩子**（RR/RL）；再看是左孩子的**左**（LL）还是**右**（LR）
> - 旋转后**必须更新**相关结点的高度（先更新子结点再更新父结点）
> - 判断是否为平衡二叉树：不能只看根结点 $BF$，要递归检查**所有结点**

## 相关知识（考研408）

> [!note] 408 常考结论
> - 手算：给出插入序列，画出 AVL 树并标注每次旋转类型
> - 删除操作后可能连续失衡——这是 408 高频易错点
> - AVL 树是**严格平衡**的 BST；红黑树是**近似平衡**的 BST（408 不考红黑树）
> - 与 [[二分查找]] 的判定树对比：判定树是平衡树但不一定是 AVL

## 相关算法

- [[二分查找]]：二分判定树是平衡 BST 的特例
- [[快速排序]]：快速排序的递归划分过程与 BST 类似
- [[堆排序]]：另一种保证 $O(n\log n)$ 的排序方式，基于完全二叉树
- [[卡特兰数]]：$n$ 个结点的不同二叉树形态数 = $C_n$

## 例题

- [[二叉树的性质]]：二叉树的基本性质与计算