---
tags:
  - flashcards
  - 计算机/串
  - 408/数据结构/串
Date: Fri Sep 11 2026
---
# KMP算法的进一步优化（nextval） · Flashcards

### 一、优化动机与规则

为什么要引入 nextval 数组？
?
标准 KMP 存在**无效跳转**：当 $T[j] = T[\text{next}[j]]$ 时，$j$ 跳到 $\text{next}[j]$ 后与主串同一字符比较**必然再次失配**（如模式串 `aaaaab` 失配时连续 4 次无效回退）。nextval 修正这类跳转。
<!--SR:!2026-09-15,4,270-->

nextval 的计算规则是什么？
?
$\text{nextval}[1] = 0$；对 $j \ge 2$：
- 若 $T[j] = T[\text{next}[j]]$：$\text{nextval}[j] = \text{nextval}[\text{next}[j]]$（沿链继续跳）
- 若 $T[j] \ne T[\text{next}[j]]$：$\text{nextval}[j] = \text{next}[j]$

口诀："**字符相同沿链走，字符不同用 next**"。
<!--SR:!2026-09-15,4,270-->

手算 nextval 时最易混淆的一点是什么？
?
判定 $T[j]$ 与谁比较用 **next**（$T[\text{next}[j]]$）；但赋值时若相等，取的是 **nextval[next[j]]**（已修正值），不是 next。
<!--SR:!2026-09-13,2,210-->

### 二、例题与对比

求模式串 `aaaab` 的 nextval 数组。
?
next = [0,1,2,3,4] → **nextval = [0, 0, 0, 0, 4]**
（j=2,3,4 时 $T[j]$ 与 $T[\text{next}[j]]$ 都是 a → 沿链归 0；j=5 时 b≠a → 取 next[5]=4）
<!--SR:!2026-09-15,4,270-->

求模式串 `ababaa` 的 nextval 数组。
?
next = [0,1,1,2,3,4] → **nextval = [0, 1, 0, 1, 0, 4]**
（j=3：a=a → nextval[1]=0；j=4：b=b → nextval[2]=1；j=6：a≠b → 取 4）
<!--SR:!2026-09-15,4,270-->

next 与 nextval 的主要区别是什么？
?
next 只考虑"最长相等前后缀 + 1"；nextval 在其基础上**跳过与自身相同字符的无效跳转**。匹配代码完全相同，仅替换数组，总复杂度仍是 $O(n+m)$。
<!--SR:!2026-09-13,2,210-->

get_nextval 与 get_next 代码的差异在哪三处？
?
① 成功分支中多 `if (T.ch[i] != T.ch[j])` 判断；② 字符相同时取 `nextval[j]`；③ 失配回退用 `j = nextval[j]`。
<!--SR:!2026-09-13,2,210-->

### 三、挖空卡片

nextval 解决的问题是 ==消除 T[j] 与 T[next[j]] 相同字符导致的必然重复失配==。
<!--SR:!2026-09-13,2,210-->

`aaaab` 的 nextval 为 ==[0, 0, 0, 0, 4]==。
<!--SR:!2026-09-12,1,230-->

`ababaa` 的 nextval 为 ==[0, 1, 0, 1, 0, 4]==。
<!--SR:!2026-09-13,2,210-->

求 nextval 的口诀是：字符相同 ==沿链走==，字符不同 ==用 next==。
<!--SR:!2026-09-12,1,230-->