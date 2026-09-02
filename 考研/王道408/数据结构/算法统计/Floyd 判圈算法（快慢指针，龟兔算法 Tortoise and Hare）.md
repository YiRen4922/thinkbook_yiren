# Floyd判圈算法（快慢指针，龟兔算法 Tortoise and Hare）

> 经典用途：①判断单链表是否存在环；②求环的入口结点。 两个指针：慢指针slow一次走1步，快指针fast一次走2步。

## 1）判断链表是否有环

### 设计思想

1. slow、fast 都从链表头出发；slow每次走1结点，fast每次走2结点。
2. 如果链表无环：fast会率先走到`NULL`，结束，无环。
3. 如果链表存在环：fast进入环之后会在环内循环跑圈，**fast一定会追上slow，两者相遇**。
    
    > ✔有环一定相遇；相遇≠相遇点就是环入口！这点是高频易错。
    

结点定义

```
typedef struct LNode{
    int data;
    struct LNode *next;
}LNode,*LinkList;
```

```
// 判断链表是否有环，有环返回1，无环返回0
int hasCycle(LNode *head)
{
    LNode *slow = head,*fast = head;
    while(fast != NULL && fast->next != NULL)
    {
        slow = slow->next;
        fast = fast->next->next; // fast走两步
        if(slow == fast){
            return 1; // 相遇，存在环
        }
    }
    return 0; // fast走到末尾，无环
}
```

循环条件：`fast != NULL && fast->next != NULL`

> fast一次跳两步，必须保证`fast`和`fast->next`不为空，否则`fast->next->next`空指针访问崩溃。

## 2）进阶：求环的入口结点⭐408考点

### 数学结论（背诵）

设：

- L：链表头到环入口的距离（结点数）
- C：环的长度
- x：**相遇点距离环入口的距离**

快慢指针相遇之后：

> 将其中一个指针放回链表头部，然后 slow、fast **两者都每次走1步**，再次相遇的位置，就是**环的入口结点**。

推演公式简要： 相遇时：快指针路程 = 2 × 慢指针路程 可以推导出：**从起点到入口 的距离 = 相遇点继续走到入口的距离**。

### 代码：返回环入口结点，无环返回NULL

```
LNode *detectCycle(LNode *head) {
    LNode *slow = head, *fast = head;
    //第一阶段：快慢指针找相遇点
    while(fast != NULL && fast->next != NULL){
        slow = slow->next;
        fast = fast->next->next;
        if(slow == fast){
            //相遇，开始第二阶段
            slow = head;   //slow放回头部
            while(slow != fast){
                slow = slow->next;
                fast = fast->next; //全部一次走1步
            }
            return slow; //再次相遇，环入口
        }
    }
    return NULL; //无环
}
```

## 复杂度

- 时间复杂度：$(O(n))$，最多遍历链表常数遍
- 空间复杂度：$(O(1))$，只两个指针，原地算法。

## ⚠考试高频坑点

1. **相遇点 ≠ 环入口！！很多人搞错**。相遇只是证明有环，不能直接拿相遇点当入口。
2. fast一次走两步，循环条件必须写 `fast && fast->next`； ❌错误：`while(fast->next && fast->next->next)`，容易记混。
3. 第二阶段，**两个指针都只走一步**，不是fast继续两步。
4. 初始 slow、fast 都等于 head，不是head->next。

## 记忆口诀

> 慢一快二跑链表；fast为空无环见； 一旦相遇知有环；一个拨回表头站； 同速前进再相遇，此点即是环入口。

## 拓展场景

除链表判环，也可以用于：判断数组是否存在环（LeetCode287寻找重复数）。

### 和之前算法横向对比

- Boyer‑Moore摩尔投票：计数抵消找众数
- Floyd判圈：快慢指针，链表判环、找环入口
- 快慢压缩法：原地数组过滤有效元素

如果你需要，我给你画文字版推演例子。