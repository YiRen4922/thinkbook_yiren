---
tags:
  - flashcards
  - flashcards/数学
  - 数学/极限
  - 数学/等价无穷小
Date: Sat Sep 13 2026
---
# 等价无穷小 · Flashcards

### 一、基本等价无穷小（$x \to 0$ 时）

$x \to 0$ 时，$\sin x$ 等价于什么？
?
$\sin x \sim x$
这是由第一个重要极限 $\lim\limits_{x \to 0}\dfrac{\sin x}{x} = 1$ 直接推出的。
<!--SR:!2026-09-24,5,180-->

$x \to 0$ 时，$\tan x$ 等价于什么？
?
$\tan x \sim x$
因为 $\tan x = \dfrac{\sin x}{\cos x}$，当 $x \to 0$ 时 $\cos x \to 1$，所以 $\tan x \sim \sin x \sim x$。
<!--SR:!2026-09-24,5,180-->

$x \to 0$ 时，$\arcsin x$ 等价于什么？
?
$\arcsin x \sim x$
设 $y = \arcsin x$，则 $x = \sin y$，当 $x \to 0$ 时 $y \to 0$，所以 $x = \sin y \sim y$，即 $y \sim x$。
<!--SR:!2026-09-29,9,190-->

$x \to 0$ 时，$\arctan x$ 等价于什么？
?
$\arctan x \sim x$
同理，设 $y = \arctan x$，则 $x = \tan y$，当 $x \to 0$ 时 $y \to 0$，所以 $x = \tan y \sim y$，即 $y \sim x$。
<!--SR:!2026-09-29,9,190-->

$x \to 0$ 时，$e^x - 1$ 等价于什么？
?
$e^x - 1 \sim x$
令 $t = e^x - 1$，则 $x = \ln(1+t)$，当 $x \to 0$ 时 $t \to 0$，所以 $x = \ln(1+t) \sim t$，即 $e^x - 1 \sim x$。
<!--SR:!2026-10-01,12,230-->

$x \to 0$ 时，$\ln(1+x)$ 等价于什么？
?
$\ln(1+x) \sim x$
这是由第二个重要极限 $\lim\limits_{x \to 0}\dfrac{\ln(1+x)}{x} = 1$ 直接推出的。
<!--SR:!2026-10-01,12,230-->

$x \to 0$ 时，$1 - \cos x$ 等价于什么？
?
$1 - \cos x \sim \dfrac{1}{2}x^2$
因为 $1 - \cos x = 2\sin^2\dfrac{x}{2} \sim 2\left(\dfrac{x}{2}\right)^2 = \dfrac{x^2}{2}$。
<!--SR:!2026-09-29,9,190-->

$x \to 0$ 时，$(1+x)^a - 1$ 等价于什么？
?
$(1+x)^a - 1 \sim ax$
由幂指函数展开 $(1+x)^a = 1 + ax + \dfrac{a(a-1)}{2}x^2 + \cdots$，减去 1 后高阶项可以忽略。
<!--SR:!2026-10-04,13,240-->

$x \to 0$ 时，$a^x - 1$ 等价于什么？
?
$a^x - 1 \sim x\ln a$
因为 $a^x - 1 = e^{x\ln a} - 1 \sim x\ln a$（利用 $e^t - 1 \sim t$，令 $t = x\ln a$）。
<!--SR:!2026-10-10,18,250-->

### 二、等价无穷小的性质

什么是等价无穷小？
?
设 $\alpha$ 和 $\beta$ 是同一极限过程中的两个无穷小量，如果 $\lim\dfrac{\beta}{\alpha} = 1$，则称 $\beta$ 与 $\alpha$ **等价**，记作 $\beta \sim \alpha$。
<!--SR:!2026-09-26,5,170-->

等价无穷小具有什么性质？
?
等价无穷小具有**传递性**：若 $\alpha \sim \beta$，$\beta \sim \gamma$，则 $\alpha \sim \gamma$。
这是因为 $\lim\dfrac{\alpha}{\gamma} = \lim\dfrac{\alpha}{\beta} \cdot \dfrac{\beta}{\gamma} = 1 \cdot 1 = 1$。
<!--SR:!2026-09-24,5,180-->

等价无穷小代换的**核心定理**是什么？
?
若 $\alpha \sim \alpha'$，$\beta \sim \beta'$，且 $\lim\dfrac{\beta'}{\alpha'}$ 存在，则
$$\lim\dfrac{\beta}{\alpha} = \lim\dfrac{\beta'}{\alpha'}$$
即：在求极限时，分子和分母可以分别用等价无穷小代换。
<!--SR:!2026-09-29,9,190-->

### 三、使用条件与限制

等价无穷小代换**只能在**什么运算中使用？
?
只能在**乘除法**中使用，**不能在加减法**中直接替换。
<!--SR:!2026-10-05,14,240-->

为什么等价无穷小代换不能在加减法中使用？请举例说明。
?
因为加减法中的项可能相互抵消高阶项，导致错误。
**反例**：求 $\lim\limits_{x \to 0}\dfrac{\tan x - \sin x}{x^3}$
- 错误解法：$\tan x \sim x$，$\sin x \sim x$，所以 $\dfrac{x - x}{x^3} = 0$（错误！）
- 正确解法：$\tan x - \sin x = \tan x(1 - \cos x) \sim x \cdot \dfrac{x^2}{2} = \dfrac{x^3}{2}$，所以极限为 $\dfrac{1}{2}$。
<!--SR:!2026-10-09,17,250-->

等价无穷小代换在什么情况下可以用于加减法？
?
只有当替换后的**高阶无穷小不会被抵消**时才可以使用。通常需要先进行代数变形（如因式分解、提取公因子等），使加减法变为乘除法后再代换。
<!--SR:!2026-09-23,8,260-->

### 四、常见错误与易错点

求 $\lim\limits_{x \to 0}\dfrac{\sin x - \tan x}{x^3}$ 时，常见的错误是什么？
?
常见错误：将 $\sin x$ 和 $\tan x$ 都替换为 $x$，得到 $\dfrac{x - x}{x^3} = 0$。
正确做法：$\sin x - \tan x = \sin x\left(1 - \dfrac{1}{\cos x}\right) = \sin x \cdot \dfrac{\cos x - 1}{\cos x} \sim x \cdot \dfrac{-\frac{1}{2}x^2}{1} = -\dfrac{x^3}{2}$，所以极限为 $-\dfrac{1}{2}$。
<!--SR:!2026-09-23,8,260-->

求 $\lim\limits_{x \to 0}\dfrac{e^x - e^{\sin x}}{x - \sin x}$ 时，应该先化简再代换吗？
?
是的。先化简：$e^x - e^{\sin x} = e^{\sin x}(e^{x - \sin x} - 1)$，所以原式 $= \lim\limits_{x \to 0}e^{\sin x} \cdot \dfrac{e^{x - \sin x} - 1}{x - \sin x} = 1 \cdot 1 = 1$（利用 $e^t - 1 \sim t$，其中 $t = x - \sin x$）。
<!--SR:!2026-09-24,9,270-->

### 五、综合应用

考研常用等价无穷小：$(1+x)^{\frac{1}{x}} - e$ 在 $x \to 0^+$ 时等价于什么？
?
$(1+x)^{\frac{1}{x}} - e \sim -\dfrac{e}{2}x,\quad x \to 0^+$
**推导过程**：
1. 令 $f(x) = (1+x)^{\frac{1}{x}} = e^{\frac{1}{x}\ln(1+x)}$
2. 对指数部分泰勒展开：$\frac{1}{x}\ln(1+x) = \frac{1}{x}\left(x - \frac{x^2}{2} + o(x^2)\right) = 1 - \frac{x}{2} + o(x)$
3. 所以 $(1+x)^{\frac{1}{x}} = e^{1 - \frac{x}{2} + o(x)} = e \cdot e^{-\frac{x}{2} + o(x)}$
4. 再用 $e^u = 1 + u + o(u)$ 展开：$e \cdot e^{-\frac{x}{2} + o(x)} = e\left[1 + \left(-\frac{x}{2} + o(x)\right) + o(x)\right] = e - \frac{e}{2}x + o(x)$
5. 因此 $(1+x)^{\frac{1}{x}} - e = -\frac{e}{2}x + o(x) \sim -\frac{e}{2}x$
<!--SR:!2026-09-22,1,130-->

求 $\lim\limits_{x \to 0}\dfrac{\ln(1+x) - \sin x}{x^2}$ 时，应该用什么方法？
?
**不能直接代换**（因为是加减法）。正确做法：利用泰勒展开
$$\ln(1+x) = x - \dfrac{x^2}{2} + o(x^2), \quad \sin x = x - \dfrac{x^3}{6} + o(x^3)$$
所以 $\ln(1+x) - \sin x = \left(x - \dfrac{x^2}{2}\right) - x + o(x^2) = -\dfrac{x^2}{2} + o(x^2)$
原式 $= \lim\limits_{x \to 0}\dfrac{-\frac{1}{2}x^2}{x^2} = -\dfrac{1}{2}$。
<!--SR:!2026-09-25,10,280-->

求 $\lim\limits_{x \to 0}\dfrac{\sqrt{1+x} - \sqrt{1-x}}{x}$ 时，有哪些方法？
?
**方法一（有理化）**：分子有理化，乘以 $\dfrac{\sqrt{1+x} + \sqrt{1-x}}{\sqrt{1+x} + \sqrt{1-x}}$，得
$$\dfrac{(1+x) - (1-x)}{x(\sqrt{1+x} + \sqrt{1-x})} = \dfrac{2x}{x(\sqrt{1+x} + \sqrt{1-x})} = \dfrac{2}{\sqrt{1+x} + \sqrt{1-x}} \to 1$$
**方法二（等价无穷小）**：$\sqrt{1+x} - 1 \sim \dfrac{1}{2}x$，$\sqrt{1-x} - 1 \sim -\dfrac{1}{2}x$，所以分子 $\sim \dfrac{1}{2}x - (-\dfrac{1}{2}x) = x$，极限为 1。
<!--SR:!2026-09-26,11,290-->

考研真题：当 $n \to \infty$ 时，$\left(1+\dfrac{1}{n}\right)^n - e$ 与 $\dfrac{a}{n}$ 等价无穷小，求 $a$。
?
$a = -\dfrac{e}{2}$
**解题思路**：
1. **变量替换**：令 $x = \dfrac{1}{n}$，则 $n \to \infty$ 时 $x \to 0^+$，原式变为 $(1+x)^{\frac{1}{x}} - e$
2. **用等价无穷小公式**：$(1+x)^{\frac{1}{x}} - e \sim -\dfrac{e}{2}x$
3. **代回 $x = \dfrac{1}{n}$**：$\left(1+\dfrac{1}{n}\right)^n - e \sim -\dfrac{e}{2}\cdot\dfrac{1}{n}$
4. **对比系数**：$\dfrac{a}{n}$ 与 $-\dfrac{e}{2}\cdot\dfrac{1}{n}$ 等价 ⇒ $a = -\dfrac{e}{2}$
**核心考点**：离散数列极限 $n \to \infty$ 换成连续变量 $x = \dfrac{1}{n} \to 0^+$，是处理这类题的标准套路。
<!--SR:!2026-09-22,1,100-->

### 六、挖空卡片（Cloze）

$x \to 0$ 时，$\sin x \sim$ ==x==，$\tan x \sim$ ==x==。
<!--SR:!2026-09-24,5,180!2026-09-29,9,250-->

$x \to 0$ 时，$1 - \cos x \sim$ ==$\dfrac{1}{2}x^2$==。
<!--SR:!2026-09-29,9,190-->

$x \to 0$ 时，$e^x - 1 \sim$ ==x==，$\ln(1+x) \sim$ ==x==。
<!--SR:!2026-09-30,11,230!2026-09-29,9,250-->

$x \to 0$ 时，$(1+x)^a - 1 \sim$ ==ax==。
<!--SR:!2026-10-05,14,240-->

$x \to 0^+$ 时，$(1+x)^{\frac{1}{x}} - e \sim$ ==$-\dfrac{e}{2}x$==。
<!--SR:!2026-09-23,1,100-->

等价无穷小代换只能在 ==乘除法== 中使用，不能在 ==加减法== 中直接替换。
<!--SR:!2026-10-06,15,240!2026-09-29,9,250-->

等价无穷小具有 ==传递性==：若 $\alpha \sim \beta$，$\beta \sim \gamma$，则 $\alpha \sim \gamma$。
<!--SR:!2026-09-24,5,180-->

求 $\lim\limits_{x \to 0}\dfrac{\tan x - \sin x}{x^3} =$ ==$\dfrac{1}{2}$==（不能直接代换，需先变形）。
<!--SR:!2026-09-22,7,250-->

求 $\lim\limits_{x \to 0}\dfrac{\sin x - \tan x}{x^3} =$ ==$-\dfrac{1}{2}$==。
<!--SR:!2026-09-23,8,260-->

### 相关笔记

- [[极限]] · [[泰勒展开card]] · [[麦克劳林公式card]]