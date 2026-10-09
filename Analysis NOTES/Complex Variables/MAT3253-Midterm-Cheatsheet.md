# MAT3253 Complex Variables — Midterm Cheat Sheet

> Transcribed from the handwritten cheat sheet [MAT3253-Midterm-Cheatsheet.pdf](MAT3253-Midterm-Cheatsheet.pdf) (front & back). `(?)` marks unclear handwriting.

## Front

### L1
- unit: $(1,0)$ / $\begin{bmatrix}1&0\\0&1\end{bmatrix}$; zero: $(0,0)$ / $\begin{bmatrix}0&0\\0&0\end{bmatrix}$; im. unit: $(0,1)$ / $\begin{bmatrix}0&-1\\1&0\end{bmatrix}$
- $(a,b) \leftrightarrow \begin{bmatrix}a&-b\\b&a\end{bmatrix}$
- $(a,b)\cdot(c,d) = (ac - bd,\ ad + bc)$

### L2
- modulus / abs. value / radius: $\sqrt{x^2 + y^2}$
- $z\cdot\bar z = |z|^2 = x^2 + y^2$; $|z_1z_2| = |z_1|\cdot|z_2|$
- $z_1z_2 = r_1r_2(\cos(\theta_1 + \theta_2) + i\sin(\theta_1 + \theta_2))$
- $z^n = r^n(\cos(n\theta) + i\sin(n\theta)) = (\cos\theta + i\sin\theta)^n$
- find $\sqrt[n]{z}$: 化为 polar form, 且 $\mathrm{Re}(1) = 1(\cos(\pi + 2k\pi) + i\sin(\pi + 2k\pi))$ (?)

### L3
- 复数无法比较大小
- 3 pts $\frac{d - b}{c - a} = \frac{f - d}{e - c}$ ⇒ $(a,b), (c,d), (e,f)$ are collinear
- 3 complex pts $z_1, z_2, z_3$ are collinear $\iff \arg\left(\frac{z_1 - z_2}{z_3 - z_2}\right) = 0$ or $\pi$ $\iff \operatorname{Im}(\cdot) = 0$
- Principal argument $\operatorname{Arg}(z)$: $[0, 2\pi)$ or $(-\pi, \pi]$
- $\arg\left(\frac{z_1}{z_2}\right) = \arg(z_1) - \arg(z_2) \pmod{2\pi}$, i.e. $\exists k \in \mathbb{Z}$ s.t. $\arg(z_1/z_2) - (\cdots) = 2k\pi$
- LFT (linear fractional transformations): $f(z) = \frac{az + b}{cz + d}$
  - if $c = 0$: $\left(\frac ad\right)z + \left(\frac bd\right)$
  - if $c \ne 0$: $\frac{az + b}{cz + d} = \frac ac - \frac{ad - bc}{c}\cdot\frac{1}{cz + d}$

### L4
- Extended complex plane: $\mathbb{C}\cup\{\infty\} = \hat{\mathbb{C}}$; function $f(z): \hat{\mathbb{C}} \to \hat{\mathbb{C}}$
- LFT: $f(z) = \begin{cases}\frac{az + b}{cz + d} & \text{if } \cdots\\ \infty & \text{if } z = -\frac dc\\ \frac ac & \text{if } z = \infty\end{cases}$; $\frac c0 = \infty$, $\frac c\infty = 0$
- LFT is a bijection on $\hat{\mathbb{C}}$
- Thm: inverse LFT $f(z) = \frac1z$ is a LFT; preserves circles or 直线 (maps 圆/直线 to 圆/直线)
- Thm: ∃ unique LFT s.t. $f(z_1) = w_1$, $f(z_2) = w_2$, $f(z_3) = w_3$

### L5
- $\exp(z) = e^z = \sum_{k=0}^\infty\frac{z^k}{k!}$; $e^z = e^x(\cos y + i\sin y)$
- $|z - w| = \sqrt{(x - x')^2 + (y - y')^2}$; $|z_1 + z_2| \le |z_1| + |z_2|$
- Open disc 以 $z_0$ 为中心: $D(z_0, r) = \{z \in \mathbb{C} : |z - z_0| < r\}$; 以 $\infty$ 为中心: $\{z \in \mathbb{C} : |z| > R\}$
- 开放 open 集 $S$: $\forall z_0 \in S$, $\exists r > 0$ s.t. $D(z_0, r) \subseteq S$
- A seq. conv. → $\infty$: $\forall R > 0$, $\exists N \in \mathbb{N}$ s.t. $\forall k \ge N$, $|z_k| > R$
- A seq. conv. → $L \in \mathbb{C}$ $\iff$ $\mathrm{Re}(\text{seq.})$ conv. to $\mathrm{Re}(L)$ and $\mathrm{Im}(\text{seq.})$ conv. to $\mathrm{Im}(L)$
- A seq. is Cauchy $\iff \forall\varepsilon > 0$, $\exists N \in \mathbb{N}$ s.t. $\forall m, n \ge N$, $|z_m - z_n| \le \varepsilon$ $\iff$ seq. is conv.
- Series: $\sum_{k=0}^\infty z_k = \lim_{n\to\infty}\left(\sum_{k=0}^n z_k\right)$ exists ⇒ $\{z_k\}$ conv.; $\{z_k\}$ conv. ⇒ $\lim_{n\to\infty}z_n = 0$; abs. conv.
- ratio test: $\lim_{k\to\infty}\left|\frac{a_{k+1}}{a_k}\right| = L$: $L > 1$ ⇒ div.; $L < 1$ ⇒ conv.; $L = 1$ ⇒ nothing
- $\sum_{l=0}^k\frac{z_1^l}{l!}\cdot\frac{z_2^{k-l}}{(k-l)!} = \frac{(z_1 + z_2)^k}{k!}$; $e^{i\theta} = \cos\theta + i\sin\theta$

### L6
- $\sin(z) = z - \frac{z^3}{3!} + \frac{z^5}{5!} - \cdots$; $\cos(z) = 1 - \frac{z^2}{2!} + \frac{z^4}{4!} - \cdots$
- $\sinh(z) = z + \frac{z^3}{3!} + \frac{z^5}{5!} + \cdots = \frac{e^z - e^{-z}}{2}$; $\cosh(z) = 1 + \frac{z^2}{2!} + \frac{z^4}{4!} + \cdots = \frac{e^z + e^{-z}}{2}$; $\cosh' = \sinh$, $\sinh' = \cosh$
- $e^{iz} = 1 + iz + \frac{i^2z^2}{2!} + \cdots$; $i = \cos\frac\pi2 + i\sin\frac\pi2 = e^{i\pi/2}$
- $\cos^2z + \sin^2z = 1$
- ★ $e^z = w \iff z = \log(w)$; $\log(w) = \ln|w| + i(\operatorname{Arg}(w) + 2\pi k)$ (若为 principal log 则去掉 $2\pi k$)

## Back

- 计算角度常用 $\arctan(\frac{\text{虚}}{\text{实}})$
- 计算 $a^z = e^{\ln|a|\cdot(x + iy)}$ … $= \cos(\ln|a|\cdot y) + i\sin(\ln|a|\cdot y)$ (times $e^{x\ln|a|}$)
- $\frac{\partial}{\partial z} = \frac12\left(\frac{\partial}{\partial x} - i\frac{\partial}{\partial y}\right)$, $\frac{\partial}{\partial\bar z} = \frac12\left(\frac{\partial}{\partial x} + i\frac{\partial}{\partial y}\right)$
- $\cos(n\theta) = \frac{e^{in\theta} + e^{-in\theta}}{2}$

### L12
- Winding number of a closed path: $\frac{\text{total change in 角度}}{2\pi} \in \mathbb{Z}$ (含方向!!)
- change in 角度 (angle) / change in argument: $[f_1(z_1) - f_1(z_0)] + [f_2(z_2) - f_2(z_1)]$
- Inverse func. $g(z)$: if $g'(x_0, y_0)$ is invertible ⇒ ∃ open set $U, V$ s.t. $g: U \to V$ is bij. and inverse of $g$ is diff.
- $g$ is holomorphic ⇒ $g'(z_0)$ is rep. by $\begin{bmatrix}a & -b\\ b & a\end{bmatrix}$ ⇒ $\begin{bmatrix}a & -b\\ b & a\end{bmatrix}^{-1} = \frac{1}{a^2 + b^2}\begin{bmatrix}a & b\\ -b & a\end{bmatrix}$

### power
- $z^w = e^{w\cdot\log(z)}$; $e^{i\cdot\log(i)} = i^i = e^{-\pi/2}$

### L11
- $df = (x + y)\,dz + (x - y)\,d\bar z$ for $f(x, y) = \ldots$ (e.g. $\frac{\partial f}{\partial z} = x + y$, $\frac{\partial f}{\partial\bar z} = x - y$)
- path/curve: cont. $\gamma(t): [a, b] \to \mathbb{C}$
- Summation formula: $z^a + z^{a+1} + \cdots + z^b = \frac{z^a(1 - z^{b - a + 1})}{1 - z}$

### L7
- $f(z) = u(x, y) + i\cdot v(x, y)$ is cont. $\iff u$ 和 $v$ 都 cont.
- $f(z)$ is diff. $\iff \lim_{\Delta z\to 0}\frac{f(z_0 + \Delta z) - f(z_0)}{\Delta z}$ 存在 ⇒ $u_x(x_0, y_0) = v_y(\cdots)$, $u_y(\cdots) = -v_x(\cdots)$

### L8
- Holomorphic: $f(z)$ is ~ $\iff$ ∃ neighbourhood $N$ s.t. $\forall z \in N$, $f$ is c. diff. at $z$
- $f(z)$ is complex diff. at $z_0 = x_0 + iy_0$ ⇐ $(u(x, y), v(x, y))$ is diff. at $(x_0, y_0)$, $u_x(x_0, y_0) = v_y(\cdots)$, $u_y(\cdots) = -v_x(\cdots)$ ($u_x, u_y, v_x, v_y$ are well-def. at $\cdots$ and cont. in a neighbourhood of $\cdots$)
- $f(z)$ is entire $\iff f$ 在 $\mathbb{C}$ 上所有 $z$ 处 c. diff.

### L9
- $f(z)$ is conformal at $z_0 \in \mathbb{C}$ ⇐ $f$ is diff. at $z_0$ & $f'(z_0) \ne 0$ ⇒ $\arg\left(\frac{\frac{d}{dt}f(\gamma_1(t_0))}{\frac{d}{dt}f(\gamma_2(t_0))}\right) = \arg\left(\frac{\gamma_1'(t_0)}{\gamma_2'(t_0)}\right) = \theta$ (角的大小、方向均保!)
- Regions $R_1$ 和 $R_2$ are conformally equiv. $\iff$ ∃ bij. holomorphic map $f(z): R_1 \to R_2$ (and 偏导均 cont.)
- Harmonic func.: $f(z)$ is harmonic $\iff$ ($f(z)$ is 2 次 diff. &) Laplace equation $u_{xx} + u_{yy} = 0$, $v_{xx} + v_{yy} = 0$
- Real-v. $u(x, y)$ is har. $\iff u_{xx} + u_{yy} = 0$
- $u(x, y) + i\cdot v(x, y)$ is holomorphic ⇒ $v(x, y)$ is a harmonic conjugate of $u(x, y)$

### L10
- $v_p = (p, v)$ ($v$ that emanates from $p$)
- Tangent space $T_p = \{v_p : v \in \mathbb{R}^2\}$; $(p, v_1) + (p, v_2) = (p, v_1 + v_2)$; $a(p, v) = (p, av)$; $p \ne q$ ⇒ $T_p \ne T_q$
- Vector field: a func. that selects $v_p$ for every $p$
- $T_p^*$: the dual space of $T_p$ (func: $T_p \to \mathbb{R}$); element $\in T_p^*$ is a linear functional / covector
- Directional deri.: $(re_1 + se_2)_p$ 的 $(\nabla f)_p\cdot(re_1 + se_2)$; $\Phi_f(v_p) = (\nabla f)_p\cdot v_p = (df)_p$ is a l. functional at $p$
- differential form: a func. that selects a l. func. $(df)_p$ for $\forall$ point $(x, y) \in \mathbb{C}$: $\alpha\,dx + \beta\,dy$
- complex-valued diff. form: $df = du + i\,dv$; $(dz)_p = (dx)_p + i(dy)_p$, $(d\bar z)_p = (dx)_p - i(dy)_p$; $(dx)_p = \frac{dz + d\bar z}{2}$, $(dy)_p = \frac{dz - d\bar z}{2i}$
- $df = \frac{\partial f}{\partial z}dz + \frac{\partial f}{\partial\bar z}d\bar z$
- $\frac12\left(\frac{\partial f}{\partial x} + i\frac{\partial f}{\partial y}\right) = 0 \iff u_x = v_y,\ u_y = -v_x$
