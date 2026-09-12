## 串 · 考研408核心笔记（六）KMP算法的进一步优化（nextval）

> 标准 KMP 已做到 $O(n+m)$，但仍存在"**无效跳转**"：当 $T[j] = T[\text{next}[j]]$ 时，$j$ 跳到 $\text{next}[j]$ 后，与主串同一字符比较**必然再次失配**，白比一次。**nextval 数组**在求 next 的同时修掉这类跳转，是 408 的高频手算考点。


### 1. 问题引入

模式串 $T = 'aaaaab'$（$\text{next} = [0,1,2,3,4,5]$）：

```
主串:  a a a a a c …
模式:  a a a a a b
                 ↑ 失配于 j=6
next[6]=5 → j=5, 但 T[5]='a' 仍 ≠ 'c'，再次失配
next[5]=4 → j=4, T[4]='a' 仍 ≠ 'c'，又失配……
```

连续 4 次跳转全是无效比较——因为 $T[2..6]$ 全是 `a`，跳到哪都是 `a`，而主串失配字符是 `c`，**必然再失配**。优化思路：让 next 直接"跳过"与自己**相同字符**的位置。


### 2. nextval 的定义（核心规则）

对 $j \ge 2$（$\text{nextval}[1] = 0$ 固定）：

$$
\text{nextval}[j] = \begin{cases}
\text{nextval}[\text{next}[j]] & T[j] = T[\text{next}[j]] \\[4pt]
\text{next}[j] & T[j] \ne T[\text{next}[j]]
\end{cases}
$$

> [!important] 一句话规则（背熟）
> **"字符相同沿链走，字符不同用 next"**
> - 若 $T[j] == T[\text{next}[j]]$：$\text{nextval}[j] = \text{nextval}[\text{next}[j]]$（跳过相等字符，继续回退）
> - 若 $T[j] \ne T[\text{next}[j]]$：$\text{nextval}[j] = \text{next}[j]$（跳到 next 位置是安全的，不再重复失配）


### 3. 手算 nextval 步骤

1. 先按[[考研/王道408/数据结构/4、串/求next数组|三步法]]求出完整 next 数组
2. $\text{nextval}[1] = 0$
3. 从 $j=2$ 起逐个套用规则，**注意要使用已算出的 nextval 值递归**（不是 next）

> [!warning] 易错点
> 判定 $T[j]$ 与 $T[\text{next}[j]]$ 用 **next**；赋值时若相等用 **nextval[next[j]]**（已修正的跳转值）。二者别混！


### 4. 手算例题

**例 1：$T = 'aaaab'$，next = [0,1,2,3,4]**

| $j$ | $T[j]$ | $\text{next}[j]$ | $T[\text{next}[j]]$ | 判定 | $\text{nextval}[j]$ |
| --- | --- | --- | --- | --- | --- |
| 1 | — | — | — | — | **0** |
| 2 | a | 1 | a | 相等 | nextval[1]=0 |
| 3 | a | 2 | a | 相等 | nextval[2]=0 |
| 4 | a | 3 | a | 相等 | nextval[3]=0 |
| 5 | b | 4 | a | 不等 | next[5]=4 |

$$
\text{nextval} = [0,\ 0,\ 0,\ 0,\ 4]
$$

失配于 $j=5$ 时直接跳 4（b ≠ a 不需要跳过）；失配于 $j=2,3,4$ 时（全是 a）直接归 0，主串后移——消除全部无效比较 ✔

**例 2：$T = 'ababaa'$，next = [0,1,1,2,3,4]**

| $j$ | $T[j]$ | $\text{next}[j]$ | $T[\text{next}[j]]$ | 判定 | $\text{nextval}[j]$ |
| --- | --- | --- | --- | --- | --- |
| 1 | — | — | — | — | **0** |
| 2 | b | 1 | a | 不等 | next[2]=1 |
| 3 | a | 1 | a | 相等 | nextval[1]=0 |
| 4 | b | 2 | b | 相等 | nextval[2]=1 |
| 5 | a | 3 | a | 相等 | nextval[3]=0 |
| 6 | a | 4 | b | 不等 | next[6]=4 |

$$
\text{nextval} = [0,\ 1,\ 0,\ 1,\ 0,\ 4]
$$


### 5. 代码实现（`get_nextval`，王道风格）

```cpp
// 求模式串 T 的 nextval 数组（下标从 1 开始）
void get_nextval(SString T, int nextval[]) {
    int i = 1, j = 0;
    nextval[1] = 0;
    while (i < T.length) {
        if (j == 0 || T.ch[i] == T.ch[j]) {
            ++i;
            ++j;
            if (T.ch[i] != T.ch[j])       // 与 next 的区别：这里多一步判断
                nextval[i] = j;           // 字符不同 → 用 j（同 next）
            else
                nextval[i] = nextval[j];  // 字符相同 → 沿 nextval 链继续跳
        } else {
            j = nextval[j];               // 回退也用 nextval（而非 next）
        }
    }
}
```

> [!note] 与 `get_next` 的三处差异
> ① 成功分支中多一个 `if (ch[i] != ch[j])` 判断；② 相同字符时取 `nextval[j]`；③ 失配回退时用 `j = nextval[j]`。整体仍是 $O(m)$。


### 6. next vs nextval 对比

| 对比项 | next | nextval |
| --- | --- | --- |
| 含义 | 失配时 $j$ 的跳转位置（最长相等前后缀 + 1） | 修正后的跳转位置（跳过相同字符的无效跳转） |
| 边界 | next[1] = 0 | nextval[1] = 0 |
| 求法 | 三步骤 / 递推 | 在 next 基础上套用判定规则 |
| 复杂度 | $O(m)$ | $O(m)$ |
| 匹配效果 | 次优（存在重复比较） | 最优（无重复失配比较） |

**使用 nextval 的 KMP 匹配代码与使用 next 时完全相同**，只是把数组换掉即可：

```cpp
int Index_KMP(SString S, SString T, int nextval[]) {
    int i = 1, j = 1;
    while (i <= S.length && j <= T.length) {
        if (j == 0 || S.ch[i] == T.ch[j]) { ++i; ++j; }
        else j = nextval[j];
    }
    if (j > T.length) return i - T.length;
    else return 0;
}
```


### 7. 408 常考点

| 考点 | 说明 |
| --- | --- |
| nextval 引入原因 | 消除 $T[j] = T[\text{next}[j]]$ 时必然再次失配的无效比较 |
| 判定用 next、赋值用 nextval | 手算时最容易混的一点 |
| 例 `'aaaab'` | nextval = [0,0,0,0,4]——经典考题 |
| 复杂度 | 求 nextval 仍为 $O(m)$，KMP 整体 $O(n+m)$ |
| 与 next 共用匹配代码 | 仅替换数组，主流程不变 |


### Obsidian 双向链接建议

- [[考研/王道408/数据结构/4、串/求next数组]]：nextval 的基础，必须先会求 next
- [[考研/王道408/数据结构/4、串/KMP算法]]：nextval 只是换掉 next 数组，匹配流程不变
- [[考研/王道408/数据结构/4、串/朴素模式匹配算法]]：对比整个优化链条的起点
- [[考研/王道408/数据结构/4、串/串的存储结构]]：下标从 1 的约定贯穿始终


### Tags

#408/数据结构 #408/串 #408/必背


### Related Notes

- [[考研/王道408/数据结构/4、串/KMP算法]]：nextval 替换 next 的匹配算法
- [[考研/王道408/数据结构/4、串/求next数组]]：先求 next，再据规则推 nextval
- [[考研/王道408/数据结构/4、串/朴素模式匹配算法]]：$O(nm) \to O(n+m)$ 的优化链条终点
- [[考研/王道408/数据结构/4、串/串的存储结构]]：存储约定与下标规则