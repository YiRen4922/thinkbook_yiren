# 考研数学常用公式 & LaTeX 用法

> 在 Obsidian 中使用 `$...$` 行内公式，`$$...$$` 独立公式块。下文公式均按此规范书写。

---

## 一、LaTeX 基础语法速查

### 希腊字母

| 名称 | LaTeX | 显示 |
| --- | --- | --- |
| α | `$\alpha$` | $\alpha$ |
| β | `$\beta$` | $\beta$ |
| γ | `$\gamma$` | $\gamma$ |
| θ | `$\theta$` | $\theta$ |
| λ | `$\lambda$` | $\lambda$ |
| μ | `$\mu$` | $\mu$ |
| π | `$\pi$` | $\pi$ |
| σ | `$\sigma$` | $\sigma$ |
| φ | `$\phi$` / `$\varphi$` | $\phi$ / $\varphi$ |
| ω | `$\omega$` | $\omega$ |
| Γ | `$\Gamma$` | $\Gamma$ |
| Σ | `$\Sigma$` | $\Sigma$ |
| Ω | `$\Omega$` | $\Omega$ |

### 上标 / 下标 / 分数 / 根号

| 用途 | LaTeX | 显示 |
| --- | --- | --- |
| 上标 | `$x^2$` | $x^2$ |
| 下标 | `$x_i$` | $x_i$ |
| 分数 | `$\frac{a}{b}$` | $\frac{a}{b}$ |
| 根号 | `$\sqrt{x}$` | $\sqrt{x}$ |
| n 次根号 | `$\sqrt[n]{x}$` | $\sqrt[n]{x}$ |

### 求和 / 积分 / 极限

| 用途 | LaTeX | 显示 |
| --- | --- | --- |
| 求和 | `$\sum_{i=1}^{n} x_i$` | $\sum_{i=1}^{n} x_i$ |
| 积分 | `$\int_{a}^{b} f(x)\,\mathrm{d}x$` | $\int_{a}^{b} f(x)\,\mathrm{d}x$ |
| 二重积分 | `$\iint_{D} f(x,y)\,\mathrm{d}\sigma$` | $\iint_{D} f(x,y)\,\mathrm{d}\sigma$ |
| 三重积分 | `$\iiint_{\Omega} f(x,y,z)\,\mathrm{d}v$` | $\iiint_{\Omega} f(x,y,z)\,\mathrm{d}v$ |
| 极限 | `$\lim_{x \to a} f(x)$` | $\lim_{x \to a} f(x)$ |

### 常用数学符号

| 用途 | LaTeX | 显示 |
| --- | --- | --- |
| 无穷 | `$\infty$` | $\infty$ |
| 偏导 | `$\frac{\partial}{\partial x}$` | $\frac{\partial}{\partial x}$ |
| 梯度 | `$\nabla f$` | $\nabla f$ |
| 不等于 | `$\neq$` | $\neq$ |
| 约等于 | `$\approx$` | $\approx$ |
| 大于等于 | `$\geq$` | $\geq$ |
| 属于 | `$\in$` | $\in$ |
| 包含于 | `$\subset$` | $\subset$ |
| 并集 | `$\cup$` | $\cup$ |
| 交集 | `$\cap$` | $\cap$ |
| 空集 | `$\varnothing$` | $\varnothing$ |
| 对任意 | `$\forall$` | $\forall$ |
| 存在 | `$\exists$` | $\exists$ |
| 向量 | `$\vec{a}$` / `$\boldsymbol{a}$` | $\vec{a}$ / $\boldsymbol{a}$ |
| 矩阵 | `$A = (a_{ij})_{m \times n}$` | $A = (a_{ij})_{m \times n}$ |

### 对齐与多行公式

```latex
$$
\begin{aligned}
f(x) &= x^2 + 2x + 1 \\
     &= (x+1)^2
\end{aligned}
$$

$$
\begin{cases}
x + y = 1 \\
x - y = 0
\end{cases}
$$
```

---

## 二、高等数学

### 1. 极限

**等价无穷小（$x \to 0$）**

$$
\begin{aligned}
\sin x &\sim x, \quad \tan x \sim x, \quad \arcsin x \sim x, \quad \arctan x \sim x \\
1 - \cos x &\sim \frac{1}{2}x^2, \quad \ln(1+x) \sim x \\
e^x - 1 &\sim x, \quad (1+x)^a - 1 \sim ax, \quad a^x - 1 \sim x \ln a
\end{aligned}
$$

**洛必达法则**

$$
\lim_{x \to a} \frac{f(x)}{g(x)} = \lim_{x \to a} \frac{f'(x)}{g'(x)} \quad \left(\frac{0}{0} \text{ 或 } \frac{\infty}{\infty}\right)
$$

**泰勒展开**

$$
\begin{aligned}
e^x &= \sum_{n=0}^{\infty} \frac{x^n}{n!} = 1 + x + \frac{x^2}{2!} + \cdots \\
\sin x &= \sum_{n=0}^{\infty} \frac{(-1)^n x^{2n+1}}{(2n+1)!} = x - \frac{x^3}{3!} + \frac{x^5}{5!} - \cdots \\
\cos x &= \sum_{n=0}^{\infty} \frac{(-1)^n x^{2n}}{(2n)!} = 1 - \frac{x^2}{2!} + \frac{x^4}{4!} - \cdots \\
\ln(1+x) &= \sum_{n=1}^{\infty} \frac{(-1)^{n-1} x^n}{n} = x - \frac{x^2}{2} + \frac{x^3}{3} - \cdots \\
\frac{1}{1-x} &= \sum_{n=0}^{\infty} x^n = 1 + x + x^2 + \cdots
\end{aligned}
$$

### 2. 导数

**基本导数公式**

$$
\begin{aligned}
(x^n)' &= nx^{n-1}, \quad (a^x)' = a^x \ln a, \quad (e^x)' = e^x \\
(\log_a x)' &= \frac{1}{x \ln a}, \quad (\ln x)' = \frac{1}{x} \\
(\sin x)' &= \cos x, \quad (\cos x)' = -\sin x \\
(\tan x)' &= \sec^2 x, \quad (\cot x)' = -\csc^2 x \\
(\sec x)' &= \sec x \tan x, \quad (\csc x)' = -\csc x \cot x
\end{aligned}
$$

**求导法则**

$$
\begin{aligned}
(f \pm g)' &= f' \pm g' \\
(fg)' &= f'g + fg' \\
\left(\frac{f}{g}\right)' &= \frac{f'g - fg'}{g^2} \\
[f(g(x))]' &= f'(g(x)) \cdot g'(x) \quad \text{（链式法则）}
\end{aligned}
$$

**反函数与隐函数求导**

$$
\frac{\mathrm{d}y}{\mathrm{d}x} = \frac{1}{\frac{\mathrm{d}x}{\mathrm{d}y}}, \qquad \frac{\mathrm{d}^2 y}{\mathrm{d}x^2} = -\frac{\frac{\mathrm{d}^2 x}{\mathrm{d}y^2}}{\left(\frac{\mathrm{d}x}{\mathrm{d}y}\right)^3}
$$

### 3. 不定积分

**基本积分公式**

$$
\begin{aligned}
\int x^n \,\mathrm{d}x &= \frac{x^{n+1}}{n+1} + C \quad (n \neq -1) \\
\int \frac{1}{x} \,\mathrm{d}x &= \ln|x| + C \\
\int e^x \,\mathrm{d}x &= e^x + C \\
\int \sin x \,\mathrm{d}x &= -\cos x + C \\
\int \cos x \,\mathrm{d}x &= \sin x + C \\
\int \sec^2 x \,\mathrm{d}x &= \tan x + C \\
\int \frac{1}{\sqrt{a^2 - x^2}} \,\mathrm{d}x &= \arcsin\frac{x}{a} + C \\
\int \frac{1}{a^2 + x^2} \,\mathrm{d}x &= \frac{1}{a}\arctan\frac{x}{a} + C \\
\int \frac{1}{\sqrt{x^2 \pm a^2}} \,\mathrm{d}x &= \ln\left|x + \sqrt{x^2 \pm a^2}\right| + C
\end{aligned}
$$

**换元法与分部积分**

$$
\int f(g(x))g'(x)\,\mathrm{d}x = \int f(u)\,\mathrm{d}u, \qquad \int u\,\mathrm{d}v = uv - \int v\,\mathrm{d}u
$$

### 4. 定积分

**牛顿–莱布尼茨公式**

$$
\int_{a}^{b} f(x)\,\mathrm{d}x = F(b) - F(a)
$$

**常用性质**

$$
\begin{aligned}
\int_{a}^{b} f(x)\,\mathrm{d}x &= -\int_{b}^{a} f(x)\,\mathrm{d}x \\
\int_{a}^{a} f(x)\,\mathrm{d}x &= 0 \\
\int_{0}^{a} f(x)\,\mathrm{d}x &= \int_{0}^{a} f(a-x)\,\mathrm{d}x \quad \text{（区间再现）}
\end{aligned}
$$

**Wallis 公式**

$$
\int_{0}^{\frac{\pi}{2}} \sin^n x \,\mathrm{d}x = \int_{0}^{\frac{\pi}{2}} \cos^n x \,\mathrm{d}x = \begin{cases} \frac{(n-1)!!}{n!!} \cdot \frac{\pi}{2}, & n \text{ 为偶数} \\ \frac{(n-1)!!}{n!!}, & n \text{ 为奇数} \end{cases}
$$

### 5. 级数

**收敛判别**

$$
\rho = \lim_{n \to \infty} \left|\frac{a_{n+1}}{a_n}\right| \quad \text{（比值判别法）}
$$

**常见展开**

$$
\begin{aligned}
e^x &= \sum_{n=0}^{\infty} \frac{x^n}{n!}, & x \in (-\infty, +\infty) \\
\sin x &= \sum_{n=0}^{\infty} \frac{(-1)^n x^{2n+1}}{(2n+1)!}, & x \in (-\infty, +\infty) \\
\frac{1}{1-x} &= \sum_{n=0}^{\infty} x^n, & x \in (-1, 1) \\
\ln(1+x) &= \sum_{n=1}^{\infty} \frac{(-1)^{n-1} x^n}{n}, & x \in (-1, 1]
\end{aligned}
$$

### 6. 多元函数

**方向导数与梯度**

$$
\frac{\partial f}{\partial l} = \nabla f \cdot \vec{l} = \frac{\partial f}{\partial x}\cos\alpha + \frac{\partial f}{\partial y}\cos\beta
$$

**全微分**

$$
\mathrm{d}z = \frac{\partial z}{\partial x}\mathrm{d}x + \frac{\partial z}{\partial y}\mathrm{d}y
$$

**隐函数求导（二元）**

$$
\frac{\partial z}{\partial x} = -\frac{F_x}{F_z}, \qquad \frac{\partial z}{\partial y} = -\frac{F_y}{F_z}
$$

**多元泰勒展开（二元，到二阶）**

$$
f(x,y) \approx f(x_0,y_0) + f_x'(x_0,y_0)\Delta x + f_y'(x_0,y_0)\Delta y + \frac{1}{2}\left[f_{xx}''\Delta x^2 + 2f_{xy}''\Delta x\Delta y + f_{yy}''\Delta y^2\right]
$$

### 7. 重积分

**直角坐标 ↔ 极坐标**

$$
\iint_{D} f(x,y)\,\mathrm{d}x\mathrm{d}y = \iint_{D'} f(r\cos\theta, r\sin\theta)\,r\,\mathrm{d}r\mathrm{d}\theta
$$

**直角坐标 ↔ 柱坐标**

$$
\iiint_{\Omega} f(x,y,z)\,\mathrm{d}v = \iiint_{\Omega'} f(r\cos\theta, r\sin\theta, z)\,r\,\mathrm{d}r\mathrm{d}\theta\mathrm{d}z
$$

**球坐标**

$$
\iiint_{\Omega} f(x,y,z)\,\mathrm{d}v = \iiint_{\Omega'} f(\rho\sin\varphi\cos\theta, \rho\sin\varphi\sin\theta, \rho\cos\varphi)\,\rho^2 \sin\varphi\,\mathrm{d}\rho\mathrm{d}\varphi\mathrm{d}\theta
$$

### 8. 曲线积分与曲面积分

**第一型曲线积分（弧长）**

$$
\int_{L} f(x,y)\,\mathrm{d}s = \int_{\alpha}^{\beta} f(x(t), y(t))\sqrt{x'(t)^2 + y'(t)^2}\,\mathrm{d}t
$$

**第二型曲线积分（格林公式）**

$$
\oint_{L} P\,\mathrm{d}x + Q\,\mathrm{d}y = \iint_{D}\left(\frac{\partial Q}{\partial x} - \frac{\partial P}{\partial y}\right)\mathrm{d}x\mathrm{d}y
$$

**斯托克斯公式**

$$
\oint_{\Gamma} P\mathrm{d}x + Q\mathrm{d}y + R\mathrm{d}z = \iint_{\Sigma} \begin{vmatrix} \mathrm{d}y\mathrm{d}z & \mathrm{d}z\mathrm{d}x & \mathrm{d}x\mathrm{d}y \\ \frac{\partial}{\partial x} & \frac{\partial}{\partial y} & \frac{\partial}{\partial z} \\ P & Q & R \end{vmatrix}
$$

**高斯公式（散度定理）**

$$
\oiint_{\Sigma} P\,\mathrm{d}y\mathrm{d}z + Q\,\mathrm{d}z\mathrm{d}x + R\,\mathrm{d}x\mathrm{d}y = \iiint_{\Omega}\left(\frac{\partial P}{\partial x} + \frac{\partial Q}{\partial y} + \frac{\partial R}{\partial z}\right)\mathrm{d}v
$$

### 9. 常微分方程

**一阶线性**

$$
y' + P(x)y = Q(x) \implies y = e^{-\int P\,\mathrm{d}x}\left[\int Q \cdot e^{\int P\,\mathrm{d}x}\,\mathrm{d}x + C\right]
$$

**二阶常系数齐次**

$$
y'' + py' + qy = 0 \quad \Rightarrow \quad r^2 + pr + q = 0
$$

$$
\begin{cases}
\Delta > 0: & y = C_1 e^{r_1 x} + C_2 e^{r_2 x} \\
\Delta = 0: & y = (C_1 + C_2 x)e^{rx} \\
\Delta < 0: & y = e^{\alpha x}(C_1 \cos\beta x + C_2 \sin\beta x)
\end{cases}
$$

**二阶常系数非齐次（特解形式）**

$$
f(x) = P_m(x)e^{\lambda x} \implies y^* = x^k Q_m(x)e^{\lambda x}
$$

其中 $k$ 为 $\lambda$ 作为特征根的重数（$0, 1, 2$）。

---

## 三、线性代数

### 1. 行列式

**二阶与三阶**

$$
\begin{vmatrix} a & b \\ c & d \end{vmatrix} = ad - bc
$$

**n 阶性质**

$$
\det(A^T) = \det(A), \quad \det(AB) = \det(A)\det(B), \quad \det(kA) = k^n \det(A)
$$

**特殊行列式**

$$
\begin{vmatrix}
1 & 1 & \cdots & 1 \\
a_1 & a_2 & \cdots & a_n \\
a_1^2 & a_2^2 & \cdots & a_n^2 \\
\vdots & \vdots & & \vdots \\
a_1^{n-1} & a_2^{n-1} & \cdots & a_n^{n-1}
\end{vmatrix} = \prod_{1 \leq i < j \leq n} (a_j - a_i) \quad \text{（范德蒙德行列式）}
$$

### 2. 矩阵

**逆矩阵**

$$
A^{-1} = \frac{1}{\det(A)} A^*, \qquad A^* = \begin{pmatrix} A_{11} & A_{21} & \cdots \\ A_{12} & A_{22} & \cdots \\ \vdots & & \ddots \end{pmatrix}
$$

**秩**

$$
r(A) = r(A^T) = r(kA) \quad (k \neq 0), \qquad r(AB) \leq \min\{r(A), r(B)\}
$$

**分块矩阵**

$$
\begin{pmatrix} A & 0 \\ 0 & B \end{pmatrix}^{-1} = \begin{pmatrix} A^{-1} & 0 \\ 0 & B^{-1} \end{pmatrix}, \qquad \begin{pmatrix} 0 & A \\ B & 0 \end{pmatrix}^{-1} = \begin{pmatrix} 0 & B^{-1} \\ A^{-1} & 0 \end{pmatrix}
$$

### 3. 向量

**线性相关性**

$$
k_1\boldsymbol{\alpha}_1 + k_2\boldsymbol{\alpha}_2 + \cdots + k_s\boldsymbol{\alpha}_s = \boldsymbol{0} \Rightarrow k_i \text{ 不全为零} \Leftrightarrow \text{线性相关}
$$

**施密特正交化**

$$
\begin{aligned}
\boldsymbol{\beta}_1 &= \boldsymbol{\alpha}_1 \\
\boldsymbol{\beta}_2 &= \boldsymbol{\alpha}_2 - \frac{(\boldsymbol{\alpha}_2, \boldsymbol{\beta}_1)}{(\boldsymbol{\beta}_1, \boldsymbol{\beta}_1)}\boldsymbol{\beta}_1 \\
\boldsymbol{\beta}_3 &= \boldsymbol{\alpha}_3 - \frac{(\boldsymbol{\alpha}_3, \boldsymbol{\beta}_1)}{(\boldsymbol{\beta}_1, \boldsymbol{\beta}_1)}\boldsymbol{\beta}_1 - \frac{(\boldsymbol{\alpha}_3, \boldsymbol{\beta}_2)}{(\boldsymbol{\beta}_2, \boldsymbol{\beta}_2)}\boldsymbol{\beta}_2
\end{aligned}
$$

### 4. 线性方程组

**齐次** $A\boldsymbol{x} = \boldsymbol{0}$：有非零解 $\Leftrightarrow r(A) < n$

**非齐次** $A\boldsymbol{x} = \boldsymbol{b}$：有解 $\Leftrightarrow r(A) = r(A \mid \boldsymbol{b})$

$$
\boldsymbol{x} = \boldsymbol{x}_0 + \boldsymbol{\eta} \quad (\boldsymbol{x}_0 \text{ 为特解}, \ \boldsymbol{\eta} \text{ 为齐次通解})
$$

### 5. 特征值与特征向量

$$
A\boldsymbol{\alpha} = \lambda\boldsymbol{\alpha}, \quad \boldsymbol{\alpha} \neq \boldsymbol{0}
$$

$$
\det(A - \lambda I) = 0 \quad \text{（特征方程）}
$$

**性质**

$$
\sum \lambda_i = \operatorname{tr}(A), \quad \prod \lambda_i = \det(A)
$$

### 6. 二次型

$$
f(x_1, \ldots, x_n) = \boldsymbol{x}^T A \boldsymbol{x} \quad (A = A^T)
$$

**正定判定**

$$
A \text{ 正定} \Leftrightarrow \text{所有特征值} \lambda_i > 0 \Leftrightarrow \text{所有顺序主子式} > 0
$$

**合同变换（配方法 / 正交变换）**

$$
\boldsymbol{x} = C\boldsymbol{y} \implies f = \lambda_1 y_1^2 + \lambda_2 y_2^2 + \cdots + \lambda_n y_n^2 \quad \text{（标准形）}
$$

---

## 四、概率论与数理统计

### 1. 概率基础

**加法公式**

$$
P(A \cup B) = P(A) + P(B) - P(AB)
$$

**条件概率与乘法公式**

$$
P(A|B) = \frac{P(AB)}{P(B)}, \qquad P(AB) = P(A)P(B|A)
$$

**全概率与贝叶斯**

$$
P(A) = \sum_{i=1}^{n} P(B_i)P(A|B_i), \qquad P(B_k|A) = \frac{P(B_k)P(A|B_k)}{\sum_{i=1}^{n} P(B_i)P(A|B_i)}
$$

**独立性**

$$
P(AB) = P(A)P(B) \Leftrightarrow A, B \text{ 相互独立}
$$

### 2. 随机变量与分布

**常见分布**

$$
\begin{aligned}
&\text{二项分布:} \quad P(X=k) = \binom{n}{k}p^k(1-p)^{n-k}, & X \sim B(n,p) \\
&\text{泊松分布:} \quad P(X=k) = \frac{\lambda^k e^{-\lambda}}{k!}, & X \sim P(\lambda) \\
&\text{正态分布:} \quad f(x) = \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(x-\mu)^2}{2\sigma^2}}, & X \sim N(\mu, \sigma^2) \\
&\text{指数分布:} \quad f(x) = \lambda e^{-\lambda x}, & X \sim E(\lambda)
\end{aligned}
$$

**标准化**

$$
Z = \frac{X - \mu}{\sigma} \sim N(0, 1), \qquad P(a < X < b) = \Phi\left(\frac{b-\mu}{\sigma}\right) - \Phi\left(\frac{a-\mu}{\sigma}\right)
$$

### 3. 数字特征

**期望**

$$
E(X) = \sum_{i} x_i p_i \text{ (离散)}, \quad E(X) = \int_{-\infty}^{+\infty} x f(x)\,\mathrm{d}x \text{ (连续)}
$$

**方差**

$$
D(X) = E(X^2) - [E(X)]^2, \qquad D(aX+b) = a^2 D(X)
$$

**协方差与相关系数**

$$
\operatorname{Cov}(X,Y) = E(XY) - E(X)E(Y), \qquad \rho_{XY} = \frac{\operatorname{Cov}(X,Y)}{\sqrt{D(X)}\sqrt{D(Y)}}
$$

**期望与方差的运算性质**

$$
\begin{aligned}
E(X+Y) &= E(X) + E(Y) \\
D(X \pm Y) &= D(X) + D(Y) \pm 2\operatorname{Cov}(X,Y) \\
E(XY) &= E(X)E(Y) + \operatorname{Cov}(X,Y)
\end{aligned}
$$

### 4. 大数定律与中心极限定理

**切比雪夫不等式**

$$
P(|X - E(X)| \geq \varepsilon) \leq \frac{D(X)}{\varepsilon^2}
$$

**中心极限定理**

$$
\frac{\sum_{i=1}^{n} X_i - n\mu}{\sqrt{n}\sigma} \xrightarrow{d} N(0, 1)
$$

### 5. 数理统计

**样本均值与方差**

$$
\bar{X} = \frac{1}{n}\sum_{i=1}^{n}X_i, \qquad S^2 = \frac{1}{n-1}\sum_{i=1}^{n}(X_i - \bar{X})^2
$$

**三大分布**

$$
\begin{aligned}
&\chi^2 \text{ 分布:} \quad \chi^2 = X_1^2 + X_2^2 + \cdots + X_n^2, \quad X_i \sim N(0,1) \\
&t \text{ 分布:} \quad t = \frac{X}{\sqrt{\chi^2/n}}, \quad X \sim N(0,1), \ \chi^2 \sim \chi^2(n) \\
&F \text{ 分布:} \quad F = \frac{\chi_1^2/n_1}{\chi_2^2/n_2}
\end{aligned}
$$

**矩估计与最大似然估计**

$$
\hat{\mu} = \bar{X}, \qquad \hat{\sigma}^2 = \frac{1}{n}\sum_{i=1}^{n}(X_i - \bar{X})^2
$$

**矩估计法**: 令 $E(X^k) = \frac{1}{n}\sum_{i=1}^{n}X_i^k$ 解方程求参数。

**最大似然估计法**: 对 $L(\theta) = \prod_{i=1}^{n}f(x_i; \theta)$ 取对数后令 $\frac{\mathrm{d}\ln L}{\mathrm{d}\theta} = 0$。

**置信区间（正态总体均值）**

$$
\bar{X} \pm t_{\alpha/2}(n-1)\frac{S}{\sqrt{n}} \quad \text{（方差未知）}
$$

---

## 五、LaTeX 编写小技巧（Obsidian 适用）

| 技巧 | 语法 | 示例 |
| --- | --- | --- |
| 行内公式 | `$...$` | `$\int_0^1 x\,\mathrm{d}x$` |
| 独立公式块 | `$$...$$` | 用于长公式或需要居中的公式 |
| 粗体向量 | `$\boldsymbol{x}$` | $\boldsymbol{x}$ |
| 竖线分隔 | `$\left\|...\right\|$` | 范数 |
| 大括号分段函数 | `\begin{cases}...\end{cases}` | 分段函数、特解形式 |
| 对齐多行 | `\begin{aligned}...\end{aligned}` | 推导过程 |
| 矩阵 | `\begin{pmatrix}...\end{pmatrix}` | 圆括号矩阵 |
| 行列式 | `\begin{vmatrix}...\end{vmatrix}` | 行列式 |
| 显示微分符号 | `\mathrm{d}x` | 避免 d 被斜体 |
| 上方标注 | `\overset{...}{=}` | $\overset{\text{注释}}{=}$ |
