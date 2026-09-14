---
tags:
  - flashcards
  - 计算机/串
  - 408/数据结构/串
Date: Fri Sep 11 2026
---
# KMP算法的进一步优化（nextval） · Flashcards

### 一、题目

给定模式串 `aaaab` 和 `ababaa`，分别求它们的 nextval 数组，并说明 nextval 相比 next 的改进。
?
- `aaaab`：next = `[0,1,2,3,4]` → **nextval = `[0, 0, 0, 0, 4]`**
- `ababaa`：next = `[0,1,1,2,3,4]` → **nextval = `[0, 1, 0, 1, 0, 4]`**
- 改进：当 `T[j] = T[next[j]]` 时，nextval 跳过该无效跳转，避免与主串同一字符再次失配。
<!--SR:!2026-09-15,4,270-->

### 二、算法思想

为什么要引入 nextval 数组？
?
标准 KMP 存在**无效跳转**：当 `T[j] = T[next[j]]` 时，`j` 跳到 `next[j]` 后与主串同一字符比较**必然再次失配**（如模式串 `aaaaab` 失配时连续 4 次无效回退）。nextval 修正这类跳转。
<!--SR:!2026-09-15,4,270-->

nextval 的计算规则是什么？
?
`nextval[1] = 0`；对 `j ≥ 2`：
- 若 `T[j] = T[next[j]]`：`nextval[j] = nextval[next[j]]`（沿链继续跳）
- 若 `T[j] ≠ T[next[j]]`：`nextval[j] = next[j]`
<!--SR:!2026-09-16,2,230-->

口诀：“**字符相同沿链走，字符不同用 next**”。
<!--SR:!2026-09-15,4,270-->

手算 nextval 时最易混淆的一点是什么？
?
判定 `T[j]` 与谁比较用 next（`T[next[j]]`）；但赋值时若相等，取的是 `nextval[next[j]]`（已修正值），不是 next。
<!--SR:!2026-09-17,4,210-->

### 三、代码实现

请写出求 nextval 数组的代码。
?
```c
void GetNextval(SString T, int nextval[]) {
    int i = 1, j = 0;
    nextval[1] = 0;
    while (i < T.length) {
        if (j == 0 || T.ch[i] == T.ch[j]) {
            ++i; ++j;
            if (T.ch[i] != T.ch[j])
                nextval[i] = j;
            else
                nextval[i] = nextval[j];
        } else {
            j = nextval[j];
        }
    }
}
```
<!--SR:!2026-09-17,4,210-->

`get_nextval` 与 `get_next` 代码的差异在哪三处？
?
① 成功分支中多 `if (T.ch[i] != T.ch[j])` 判断；
② 字符相同时取 `nextval[j]`；
③ 失配回退用 `j = nextval[j]`。
<!--SR:!2026-09-16,2,190-->

### 四、时间复杂度

nextval 优化后的时间复杂度是多少？
?
与标准 KMP 相同：求 nextval 数组 $O(m)$ + 匹配阶段 $O(n)$ = **$O(n + m)$**。优化的是常数因子（减少无效比较），不改变数量级。
<!--SR:!2026-09-15,4,270-->

### 五、易错点和重点

next 与 nextval 的主要区别是什么？
?
next 只考虑“最长相等前后缀 + 1”；nextval 在其基础上**跳过与自身相同字符的无效跳转**。匹配代码完全相同，仅替换数组，总复杂度仍是 $O(n+m)$。
<!--SR:!2026-09-16,2,190-->

nextval 中常见错误有哪些？
?
1. 比较时误用 `nextval` 而非 `next`；
2. 相等时误取 `next[j]` 而非 `nextval[next[j]]`；
3. 忘记 `nextval[1] = 0` 的边界；
4. 将 `nextval` 与 `next` 混用。
<!--SR:!2026-09-15,4,270-->

### 六、相关知识（考研408）

nextval 在 408 中的常考点有哪些？
?
- 给定模式串手算 nextval 数组（选择题高频）；
- next 与 nextval 的区别与联系；
- nextval 优化对匹配过程的影响；
- 求 nextval 的代码填空。
<!--SR:!2026-09-17,4,210-->

### 七、算法（手算）

手工求 nextval 的规则是什么？（三步）
?
1. `nextval[1] = 0`（边界）；
2. 先求 next 数组；
3. 对 `j ≥ 2`：若 `T[j] = T[next[j]]`，则沿链取 `nextval[next[j]]`；否则取 `next[j]`。
<!--SR:!2026-09-15,4,270-->

求模式串 `aaaab` 的 nextval 数组。
?
next = `[0,1,2,3,4]` → **nextval = `[0, 0, 0, 0, 4]`**
（`j=2,3,4` 时 `T[j]` 与 `T[next[j]]` 都是 a → 沿链归 0；`j=5` 时 b≠a → 取 `next[5]=4`）
<!--SR:!2026-09-15,4,270-->

求模式串 `ababaa` 的 nextval 数组。
?
next = `[0,1,1,2,3,4]` → **nextval = `[0, 1, 0, 1, 0, 4]`**
（`j=3`：a=a → `nextval[1]=0`；`j=4`：b=b → `nextval[2]=1`；`j=6`：a≠b → 取 4）
<!--SR:!2026-09-15,4,270-->

### 八、挖空卡片

nextval 解决的问题是 ==消除 `T[j]` 与 `T[next[j]]` 相同字符导致的必然重复失配==。
<!--SR:!2026-09-17,4,210-->

`aaaab` 的 nextval 为 ==`[0, 0, 0, 0, 4]`==。
<!--SR:!2026-09-15,3,250-->

`ababaa` 的 nextval 为 ==`[0, 1, 0, 1, 0, 4]`==。
<!--SR:!2026-09-16,2,190-->

求 nextval 的口诀是：字符相同 ==沿链走==，字符不同 ==用 next==。
<!--SR:!2026-09-16,2,190!2000-01-01,1,250-->