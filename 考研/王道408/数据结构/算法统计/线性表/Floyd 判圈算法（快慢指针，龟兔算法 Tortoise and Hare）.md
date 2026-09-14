---
tags:
  - 考研408
  - 考研408/数据结构/线性表
  - 考研408/优先级/S级
  - 考研408/算法统计
Date: Sun Sep 13 2026
---
# Floyd 判圈算法（快慢指针，龟兔算法 Tortoise and Hare）

> 两个指针：`slow` 一次走 1 步，`fast` 一次走 2 步。用于：① 判断链表是否有环；② 求环的入口结点。考研 408 链表算法题的标志性技巧，掌握后可覆盖所有链表环相关问题。

## 算法思想

**核心观察**：在环内，`fast` 相对 `slow` 每轮追近 1 个结点。若存在环，`fast` 终将追上 `slow`；若不存在环，`fast` 先到达 `NULL`。

**两阶段**：
1. **阶段一**：快慢指针同时从头出发，寻找相遇点——相遇即证明有环
2. **阶段二**（找环入口）：一个指针拨回头部，两个指针都走 1 步，再次相遇即为**环入口**

### 数学推导

设：
- $L$：头结点到环入口的距离
- $C$：环的长度
- $x$：相遇点到环入口的距离（沿前进方向）

相遇时：`fast` 走的路程 = $L + C + x$（假设 `fast` 已绕环一圈追上 `slow`），`slow` 走的路程 = $L + x$，且 `fast` 路程 = 2 × `slow` 路程：

$$L + C + x = 2(L + x) \implies L = C - x$$

即：**头结点到环入口的距离 = 相遇点继续走到环入口的距离**。两指针分别从头和相遇点出发，每次走 1 步，再次相遇处必为环入口。

## 代码实现

### 结点定义

```c
typedef struct LNode {
    int data;
    struct LNode *next;
} LNode, *LinkList;
```

### 判断链表是否有环

```c
int hasCycle(LinkList L) {
    LNode *slow = L, *fast = L;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) return 1;     // 相遇，存在环
    }
    return 0;                           // fast 到 NULL，无环
}
```

### 求环入口结点（408 高频）

```c
LNode *detectCycle(LinkList L) {
    LNode *slow = L, *fast = L;
    // 阶段一：找相遇点
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) {
            // 阶段二：慢拨回头，同步走 1 步
            slow = L;
            while (slow != fast) {
                slow = slow->next;
                fast = fast->next;
            }
            return slow;                // 再次相遇，环入口
        }
    }
    return NULL;                        // 无环
}
```

## 时间复杂度

- 时间：$O(n)$，阶段一遍历不超过 $L+C$ 步，阶段二不超过 $L$ 步
- 空间：$O(1)$，只用两个指针，原地算法

## 易错点

> [!warning] 易错点
> - **相遇点 ≠ 环入口**！相遇只证明有环，必须走完阶段二才能得到入口
> - 阶段二**两个指针都只走 1 步**，不是 fast 继续走 2 步
> - 循环条件 `while (fast && fast->next)` 必须同时检查两者；错误写法 `while (fast->next && fast->next->next)` 可能在特殊边界情况出问题
> - 初始 `slow = fast = head`（不是 `head->next`）
> - 无环时 `fast` 最终到达 `NULL`，循环条件保证不会访问空指针

## 相关知识（考研408）

> [!note] 408 常考结论
> - 链表判环 / 环入口 → 直接套 Floyd 模板，408 算法设计题常考
> - 推导公式 $L = C - x$ 可作为简答题/证明题素材
> - 若题目要求"找到环中某个结点"，阶段一的相遇点即可；若要求"找到环入口"，需要阶段二
> - 408 高频坑点：混淆"找环入口"和"判断是否有环"两种题型

## 相关算法

- [[链表高频操作]]：快慢指针在链表上的其他应用（找中点、删除倒数第 k 个）
- [[双指针，数组压缩法]]：快慢指针在数组上的应用（同根不同形）
- [[合并有序链表]]：链表双指针操作（尾插法）

## 例题

- [[单链表判环]]：Floyd 判圈综合应用
- [[链表例题]]：链表综合题集（含判环变式）