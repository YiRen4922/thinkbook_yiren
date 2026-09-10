# 函数 $f(x)=\ln(x+\sqrt{x^2+1})$ 就是反双曲正弦函数 $\operatorname{arsinh} x$。它的麦克劳林展开为：

$$
\boxed{
\ln(x+\sqrt{x^2+1}) = x - \frac{x^3}{6} + \frac{3x^5}{40} - \frac{5x^7}{112} + \cdots
}
$$

通项公式：

$$
\ln(x+\sqrt{x^2+1}) = \sum_{n=0}^{\infty} \frac{(-1)^n (2n)!}{4^n (n!)^2 (2n+1)} x^{2n+1}
$$

或等价地：

$$
\ln(x+\sqrt{x^2+1}) = \sum_{n=0}^{\infty} \frac{(-1)^n (2n-1)!!}{(2n)!! (2n+1)} x^{2n+1}
$$

收敛域：$|x| \le 1$。

---

### 推导过程（求导法）

令 $f(x)=\ln(x+\sqrt{x^2+1})$。

#### 1. 求导

$$
f'(x) = \frac{1}{x+\sqrt{x^2+1}} \cdot \left(1 + \frac{x}{\sqrt{x^2+1}}\right)
$$

通分：

$$
1 + \frac{x}{\sqrt{x^2+1}} = \frac{\sqrt{x^2+1}+x}{\sqrt{x^2+1}}
$$

所以：

$$
f'(x) = \frac{1}{x+\sqrt{x^2+1}} \cdot \frac{x+\sqrt{x^2+1}}{\sqrt{x^2+1}} = \frac{1}{\sqrt{x^2+1}}
$$

即：

$$
\boxed{f'(x) = (1+x^2)^{-1/2}}
$$

#### 2. 广义二项式展开

利用公式：

$$
(1+u)^\alpha = \sum_{n=0}^{\infty} \binom{\alpha}{n} u^n
$$

取 $\alpha = -\frac12$，$u = x^2$：

$$
(1+x^2)^{-1/2} = \sum_{n=0}^{\infty} \binom{-1/2}{n} x^{2n}
$$

其中二项式系数：

$$
\binom{-1/2}{n} = \frac{(-1/2)(-3/2)\cdots(-(2n-1)/2)}{n!}
= (-1)^n \frac{(2n-1)!!}{(2n)!!}
$$

所以：

$$
f'(x) = 1 - \frac{1}{2}x^2 + \frac{3}{8}x^4 - \frac{5}{16}x^6 + \cdots
$$

#### 3. 逐项积分

由于 $f(0)=0$，对 $f'(x)$ 从 $0$ 到 $x$ 积分：

$$
f(x) = \int_0^x f'(t)\,\mathrm{d}t
= x - \frac{1}{6}x^3 + \frac{3}{40}x^5 - \frac{5}{112}x^7 + \cdots
$$

#### 4. 验证前几项

- $n=0$：$x$
- $n=1$：$-\frac{x^3}{6}$
- $n=2$：$+\frac{3x^5}{40}$
- $n=3$：$-\frac{5x^7}{112}$

与通项一致。

---

### 数形结合（Desmos 图像）

```desmos-graph
left=-1.5; right=1.5
bottom=-1.5; top=1.5
grid=true
---
y=\ln(x+\sqrt{x^2+1})|blue
y=x-x^3/6+3x^5/40-5x^7/112|red
(0.5,0.481)|label:y=\ln(x+√(x²+1))
(0.5,0.479)|label:7项近似
```

在 $|x| \le 1$ 范围内，多项式近似与真实函数几乎重合。

---

### 补充：另一种推导思路（不推荐）

也可以从导数出发，然后利用 $\ln(1+u)$ 的展开，但需要处理 $\sqrt{x^2+1}$，过程更繁琐。求导法是最简洁、最标准的做法。

---

如果需要，我可以继续给出 **“用这个展开求极限的例题”** 或 **“其他反双曲函数的麦克劳林展开”**。