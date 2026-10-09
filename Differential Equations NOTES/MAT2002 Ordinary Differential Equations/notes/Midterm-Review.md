# MAT2002 Ordinary Differential Equations — Review Notes

> Transcribed from the handwritten review notes [Midterm-Review.pdf](Midterm-Review.pdf) (Ch1–4, 6, 7, 9; Boyce & DiPrima numbering). `(?)` marks unclear handwriting. The last page contains an unrelated brainstorming sketch (a story/game map) and is omitted.

## 1.3

- **Systems of DE**: 一组 DE.
- **Order**: eq. 中最高阶导.
- **Linear eq.** ($n$-th order): $a_0(t)y^{(n)} + \cdots + a_n(t)y = 0$ (or $= g(t)$).
- **Nonlinear eq.**, e.g. $\theta'' + \frac gL\sin\theta = 0$.
- **Linearization**: approximating a nonlinear eq. by a linear eq.
- **Solution**: for a DE eq. ①, a sol. of it is a func. $\phi$ whose $\phi^{(0)}, \phi^{(1)}, \dots, \phi^{(n)}$ satisfy eq. ①.

## 2.1 First-order linear eq. (2.1 linear, 2.2 separable, 2.6 exact)

Form: $y' + p(t)y = g(t)$ (or $P(t)y' + Q(t)y = G(t)$).

**Integrating factor** — to determine $\mu(t)$ (an undetermined func.):
$\mu(t)y' + \mu(t)p(t)y = \mu(t)g(t)$; require the LHS $= \frac{d}{dt}(\mu(t)y(t))$
⇒ $\mu'(t) = \mu(t)p(t)$ ⇒ $\frac{\mu'(t)}{\mu(t)} = p(t)$ ⇒ $\ln\mu(t) = \int p(t)\,dt + C$ ⇒ $\mu(t) = e^{\int p(t)dt}$
⇒ $\mu(t)y = \int\mu(t)g(t)\,dt + C$ ⇒ $y = \dfrac{\int_{t_0}^t\mu(s)g(s)\,ds + C}{\mu(t)}$ (★).

## 2.2 Separable equations

(In 1.2: $y' + ay = b$ — solve by direct integration.)
一阶 ODE: $\frac{dy}{dx} = f(x, y)$ ↓ 重写 $M(x, y) + N(x, y)\frac{dy}{dx} = 0$.

Separable: $M(x) + N(y)\frac{dy}{dx} = 0$ ⇒ $\int M(x)\,dx + \int N(y)\,y'\,dx = 0$ ⇒ $\int M(x)\,dx + \int N(y)\,dy = 0$.

Sol.: let $m(x), n(y)$ be 2 func.'s with $\frac{d}{dx}m(x) = M(x)$, $\frac{d}{dy}n(y) = N(y)$:
$M(x) + N(y)\frac{dy}{dx} = 0$ ⇒ $\frac{d}{dx}[m(x) + n(y(x))] = 0$ ⇒ $m(x) + n(y(x)) = C$.
Initial condition $y(x_0) = y_0$ ⇒ $m(x_0) + n(y_0) = C$ ⇒ $m(x) - m(x_0) + n(y) - n(y_0) = 0$, i.e. ★ $\int_{x_0}^x M(s)\,ds + \int_{y_0}^y N(t)\,dt = 0$.

(solve explicitly 显式求解; solve implicitly 隐式求解)

## 2.3

(?)

## 2.4 Existence & uniqueness of sol.'s

**Thm 2.4.1** (对线性的 ODE). Func. $p(t)$ & $g(t)$ are cont. on an open interval $I = (\alpha, \beta)$, $t_0 \in I$
⇒ ∃ $y = \phi(t)$ s.t. $\forall t \in I$, $y' + p(t)y = g(t)$ and $y(t_0) = y_0$ (the initial cond.).
(此处, 解 $y(t)$ 可以是不 cont. 的) $\mu(t) = e^{\int_{t_0}^t p(s)ds}$, $y = \frac{1}{\mu(t)}\left[\int_{t_0}^t\mu(s)g(s)\,ds + y_0\right]$.

**Thm 2.4.2** (对非线性的 ODE). Func. $f(t, y)$ and $\frac{\partial}{\partial y}f(t, y)$ are cont. in a rectangle $\alpha < t < \beta$, $\gamma < y < \delta$, with $t_0 \in (\alpha, \beta)$, $y_0 \in (\gamma, \delta)$
⇒ ∃ $h$ s.t. $(t_0 - h, t_0 + h) \subset (\alpha, \beta)$ (局部的) and ∃ $y = \phi(t)$ defined on $(t_0 - h, t_0 + h)$ s.t. $y$ is a sol. of the IVP $y' = f(t, y)$, $y(t_0) = y_0$.

**General sol.** 线性方程和非线性方程的另一个不同之处涉及 general solution 的概念。对于一阶线性方程，可以获得包含一个任意常数的解，通过指定该常数的值，可以得出所有可能的解。对于非线性方程，情况可能并非如此；即使可以找到包含任意常数的解，也可能存在无法通过给该常数赋值而获得的其它解。因此, general sol. 仅对线性 eq. 用.

**Implicit sol.** 找到一个 func. $F(t, y) = 0$, 且其解 $y$ 满足 DE 的解 $y = \phi(t)$.

## 2.5 Autonomous equations

Def.: eq.'s of the form $y' = f(y)$ (自变量 $t$ 未出现), $y' = \frac{dy}{dt}$.

- **Exponential growth**: $\frac{dy}{dt} = ry$ ($r$ is a constant: the rate of growth or decline), $y(0) = y_0$ (initial condition) ⇒ $y = y_0e^{rt}$.
- **Logistic growth**: $r$ replaced by a func. $h(y) = r - ay$. Verhulst eq. / logistic eq.: $\frac{dy}{dt} = (r - ay)y \iff \frac{dy}{dt} = r\left(1 - \frac yK\right)y$ (Eq. ⑦, $r$: intrinsic growth rate).
  The constant sol.'s $\phi_1(t) = 0$, $\phi_2(t) = K$ = equilibrium sol.'s (of Eq. ⑦).
- The **critical points** of eq. $y' = f(y)$ = the roots of eq. $f(y) = 0$.

## 2.6 Exact DE

$M(x, y) + N(x, y)y' = 0$ (Eq. ⑥). Sol.'s of Eq. ⑥ are given implicitly by $\psi(x, y) = C$ ($\forall$ constant).

**Thm 2.6.1.** Region $R$: $\alpha < x < \beta$, $\gamma < y < \delta$. Eq. $M(x, y) + N(x, y)y' = 0$ is an exact DE
⇔ $M_y(x, y) = N_x(x, y)$, $\forall(x, y) \in R$
⇔ ∃ func. $\psi(x, y)$ s.t. $\psi_x(x, y) = M(x, y)$, $\psi_y(x, y) = N(x, y)$.

**Integrating factors.** Cond.: $\mu'(x) = \frac{M_y - N_x}{N}\mu(x)$; $\mu'(y) = \frac{N_x - M_y}{M}\mu(y)$.
用法: $\mu M + \mu Ny' = 0$ with $(\mu M)_y = (\mu N)_x$.

## Ch3 Second-order linear eq.'s

### 3.1

A 2nd-order ODE in the form $y'' = f(t, y, y')$ is **linear** ⇐ $f(t, y, y') = g(t) - p(t)y' - q(t)y$; otherwise nonlinear. If $g(t) = 0$ ⇒ the eq. is homogeneous; otherwise nonhomogeneous.

**Characteristic eq.** For $ay'' + by' + cy = 0$, try sol.'s of the form $y = e^{rt}$ ($r$: 待定 coeff.). Then $ar^2e^{rt} + bre^{rt} + ce^{rt} = 0$ ⇒ $ar^2 + br + c = 0$ — the chara. eq. of the eq.

If the roots of the chara. eq. are real & different, $r_1$ and $r_2$: $y_1(t) = e^{r_1t}$ & $y_2(t) = e^{r_2t}$ are 2 sol.'s of $ay'' + by' + cy = 0$.
Now solve the IVP $y(t_0) = y_0$, $y'(t_0) = y_0'$:

$$\begin{cases}y_0 = c_1e^{r_1t_0} + c_2e^{r_2t_0}\\ y_0' = c_1r_1e^{r_1t_0} + c_2r_2e^{r_2t_0}\end{cases}\Rightarrow\ c_1 = \frac{y_0' - y_0r_2}{r_1 - r_2}e^{-r_1t_0},\quad c_2 = \frac{y_0r_1 - y_0'}{r_1 - r_2}e^{-r_2t_0}$$

General sol. of $ay'' + by' + cy = 0$: $y = c_1e^{r_1t} + c_2e^{r_2t}$.

### 3.2

$\forall t \in (\alpha, \beta) = I$ (an open interval; 可 $\alpha = -\infty$ 及 $\beta = \infty$), $p(t), q(t)$ are cont. on $I$.
The differential operator $L$: $L[\phi] = \phi'' + p(t)\phi' + q(t)\phi$, i.e. $L = D^2 + pD + q$ ($D$: the derivative operator). (用 $y$ 表示 $\phi(t)$.)
(WT know: the IVP $L[y] = y'' + p(t)y' + q(t)y = 0$, $y(t_0) = y_0$, $y'(t_0) = y_0'$ always has a sol.)

**Thm 3.2.1 (Existence & uniqueness)** (homo. & nonhomo. eq. 都可用). IVP $y'' + p(t)y' + q(t)y = g(t)$, $y(t_0) = y_0$, $y'(t_0) = y_0'$; $p, q, g$ cont. on an open $I$; $t_0 \in I$
⇒ ∃ exactly only 1 sol. $y = \phi(t)$ of the IVP (★ sol. exists; unique; defined on $I$; at least twice diff. on $I$).
E.g. 1: find the longest interval ↔ 找 $p(t), q(t)$ 的 cont. 区间 (↔ 找 discont. 的区间).

**Thm 3.2.2 (Principle of superposition).** $L[y] = y'' + p(t)y' + q(t)y = 0$, $y_1, y_2$ 2 sol.'s ⇒ $\forall$ constants $c_1, c_2$, $c_1y_1 + c_2y_2$ is a sol.

**Wronskian** (determinant) of the solutions $y_1$ & $y_2$: $W(y_1, y_2)(t_0)$.

**Thm 3.2.3.** $y_1, y_2$ sol.'s of $y'' + p(t)y' + q(t)y = 0$ (Eq. ①), initial condition $y(t_0) = y_0$, $y'(t_0) = y_0'$, $W = y_1y_2' - y_1'y_2 \ne 0$ at $t_0$ ⇒ ∃ constants $c_1, c_2$ s.t. $y = c_1y_1(t) + c_2y_2(t)$ (satisfies the IVP).

**Thm 3.2.4.** $y = c_1y_1(t) + c_2y_2(t)$ (任意 $c_1, c_2$; 此 $y$ 包含 Eq. ① 的所有解) is the general sol. of Eq. ①; $y_1, y_2$: a fundamental set of sol.'s ($W(y_1, y_2)(t_0) \ne 0$).

**Thm 3.2.5.** Eq. ①, $p, q$ cont. on open $I$, $t_0 \in I$, $y_1(t_0) = 1$, $y_1'(t_0) = 0$, $y_2(t_0) = 0$, $y_2'(t_0) = 1$ ⇒ $y_1, y_2$ form a fundamental set of sol.'s ($W(y_1, y_2)(t_0) = 1$).

**Thm 3.2.6.** Eq. ①, $p, q$ cont. on open $I$, $y = u(t) + iv(t)$ is a complex sol. ⇒ $u$ and $v$ are sol.'s.

**Thm 3.2.7 (Abel's theorem).** $y'' + p(t)y' + q(t)y = 0$, $y_1, y_2$ sol.'s, $p, q$ cont. on open $I$ ⇒ $W(y_1, y_2)(t) = c\,e^{-\int p(t)dt}$ ($c(y_1, y_2)$ is a constant depending on $y_1$ & $y_2$); $\forall t \in I$: $c = 0$ ⇒ $W(y_1, y_2)(t) = 0$; $c \ne 0$ ⇒ $W(y_1, y_2)(t) \ne 0$.

### 3.3 Complex roots of the chara. eq.

$ay'' + by' + cy = 0$ (Eq. ①), $ar^2 + br + c = 0$ (Eq. ②). If $b^2 - 4ac < 0$ ⇒ roots $r_{1,2} = \lambda \pm i\mu$ ($\lambda, \mu \in \mathbb{R}$) ⇒ $y_1(t) = e^{(\lambda + i\mu)t}$, $y_2(t) = e^{(\lambda - i\mu)t}$.
Euler's formula: $e^{it} = \cos t + i\sin t$; $e^{(\lambda + i\mu)t} = e^{\lambda t}e^{i\mu t} = e^{\lambda t}(\cos\mu t + i\sin\mu t)$
⇒ real part $u(t) = e^{\lambda t}\cos\mu t$, im. part $v(t) = e^{\lambda t}\sin\mu t$; Wronskian $W(u, v)(t) = \mu e^{2\lambda t} \ne 0$
⇒ General sol.: $y = c_1e^{\lambda t}\cos\mu t + c_2e^{\lambda t}\sin\mu t$.

### 3.4 Repeated roots; reduction of order

If $b^2 - 4ac = 0$ ⇒ only root $r_1 = r_2 = -\frac{b}{2a}$ ⇒ 1st sol. $y_1(t) = e^{-\frac{b}{2a}t}$. Find the 2nd sol.: $v''(t) = 0$ ⇒ $v(t) = c_1 + c_2t$
⇒ $y = v(t)e^{-\frac{b}{2a}t} = c_1e^{-\frac{b}{2a}t} + c_2te^{-\frac{b}{2a}t}$ (general sol.); 2nd sol. $y_2(t) = te^{-\frac{b}{2a}t}$; $W(y_1, y_2)(t) = e^{-\frac bat} \ne 0$.

**Reduction of order** (参照前页方法, 普遍化以找 2nd sol.). Eq. $y'' + p(t)y' + q(t)y = 0$, 1st sol. $y_1(t)$.
① Let $y = v(t)y_1(t)$ ⇒ $y' = v'y_1 + vy_1'$ ⇒ $y'' = v''y_1 + 2v'y_1' + vy_1''$.
② 代入: $y_1v'' + (2y_1' + py_1)v' + (y_1'' + py_1' + qy_1)v = 0$; $y_1$ 是 eq. 的一个 sol. ($y_1'' + py_1' + qy_1 = 0$) ⇒ $y_1v'' + (2y_1' + py_1)v' = 0$ (first-order in $v'$).

### 3.5 Nonhomogeneous eq.

Form: $L[y] = y'' + p(t)y' + q(t)y = g(t)$ (Eq. ①), $p, q, g$ cont. on an open $I$; homo. Eq. ② ($g(t) = 0$): $L[y] = 0$.

**Thm 3.5.1.** $Y_1, Y_2$ are 2 sol.'s of nonhomo. Eq. ① ⇒ $Y_1 - Y_2$ is a sol. of homo. Eq. ②; with $y_1, y_2$ a fund. set of Eq. ② ⇒ ∃ constants $c_1, c_2$ s.t. $Y_1 - Y_2 = c_1y_1 + c_2y_2$.

**Thm 3.5.2.** The general sol. of nonhomo. Eq. ① can be written as $y = \phi(t) = c_1y_1(t) + c_2y_2(t) + Y(t)$ ($y_1, y_2$: 2 sol.'s of Eq. ②; $Y$: 1 sol. of Eq. ①).

寻找 $Y(t)$ (方法 × 2): **Method of undetermined coefficients** (先假设 $Y(t)$ 的形式). Nonhomo. eq. $ay'' + by' + cy = g(t)$: ① 求 homo. eq. 的 general sol.; ② $g(t)$ 应为三角函数 / 多项式 / 指数 func.; ③ $g(t) = \sum_{i=1}^n g_i(t)$; …

### 3.6 Variation of parameters (这里看 PPT)

Nonhomo. eq. $y'' + p(t)y' + q(t)y = r(t)$; homo. eq. $\cdots = 0$.
**Thm (7.3).** $I$ is open, $p, q, r$ cont. on $I$, $(y_1, y_2)$ a set of sol.'s of the homo. eq. ⇒ a sol. of the nonhomo. eq. is

$$Y(t) = -y_1\int\frac{y_2\,r}{W(y_1, y_2)}(t)\,dt + y_2\int\frac{y_1\,r}{W(y_1, y_2)}(t)\,dt$$

General sol.: $y(t) = c_1y_1(t) + c_2y_2(t) + Y(t)$.

## Ch4 Higher-order linear eq.'s

### 4.1 General theory of $n$-th order eq.'s

$L[y] = y^{(n)} + p_1(t)y^{(n-1)} + \cdots + p_{n-1}(t)y' + p_n(t)y = g(t)$ (Eq. ②); IVP $y(t_0) = y_0, \dots, y^{(n-1)}(t_0) = y_0^{(n-1)}$ (③).

- **Thm 4.1.1.** $p_1, \dots, p_n, g$ cont. on open $I$, $t_0 \in I$ ⇒ Eq. ② with IVP ③ has only 1 sol. $y = \phi(t)$. Homo. eq.: $g(t) = 0$ (④).
- **Thm 4.1.2.** $p_1, \dots, p_n$ cont. on open $I$, $y_1, \dots, y_n$ sol.'s of homo. Eq. ④, ∃ $t_k \in I$ s.t. $W(y_1, \dots, y_n)(t_k) \ne 0$ ⇒ $y = c_1y_1 + \cdots + c_ny_n$ is the general sol. of homo. Eq. ④ (fundamental set of sol.'s).
  ∃ constants $k_1, \dots, k_n$ (not all 0) s.t. $k_1f_1(t) + \cdots + k_nf_n(t) = 0$ ⇔ the func.'s are linearly dependent.
- **Thm 4.1.3.** $(y_1, \dots, y_n)$ is a fund. set of sol.'s of homo. Eq. ④ on $I$ ⇔ $y_1, \dots, y_n$ are l. indep. on $I$. (这里有证: 见课本 P247.)
- Nonhomo. eq. (Eq. ②, $g(t) \ne 0$): $Y_1, Y_2$ any 2 sol.'s ⇒ $L[Y_1 - Y_2](t) = L[Y_1](t) - L[Y_2](t) = g(t) - g(t) = 0$ ⇒ $Y_1 - Y_2$ is a sol. of the corresponding homo. eq. ④ ⇒ general sol. of nonhomo. eq. ②: $y = c_1y_1(t) + \cdots + c_ny_n(t) + Y(t)$ ($Y$: a particular sol.).

### 4.2

Homo. eq.: $L[y] = a_0y^{(n)} + a_1y^{(n-1)} + \cdots + a_{n-1}y' + a_ny = 0$; chara. poly. $Z(r) = a_0r^n + \cdots + a_{n-1}r + a_n$; chara. eq. $Z(r) = 0$.

- **Real & distinct roots**: $n$ sol.'s $e^{r_1t}, \dots, e^{r_nt}$ (all real, no 2 equal); general sol. $y = c_1e^{r_1t} + \cdots + c_ne^{r_nt}$.
- **Complex roots**: (blank)
- **Repeated roots**: for an order-$n$ eq., $Z(r_1) = 0$ with multiplicity (root appearing times) $s$ ($s \le n$): real root $r_1$: $e^{r_1t}, te^{r_1t}, \dots, t^{s-1}e^{r_1t}$; complex roots $\lambda \pm i\mu$: $e^{\lambda t}\cos\mu t, \dots, t^{s-1}e^{\lambda t}\cos\mu t$, $e^{\lambda t}\sin\mu t, \dots, t^{s-1}e^{\lambda t}\sin\mu t$.

### 4.3 Method of undetermined coefficients

$Y$: a particular sol. of the nonhomo. eq. $L[y] = a_0y^{(n)} + \cdots + a_ny = g(t)$.

### 4.4 Method of variation of parameters

$Y(t) = u_1(t)y_1(t) + \cdots + u_n(t)y_n(t)$; determine $n$ func.'s $(u_1, \dots, u_n)$ ← $n$ conditions.

## Ch7 Systems of first-order linear eq.'s

**7.1 Thm 7.1.1.** $F_1, \dots, F_n$ and $\frac{\partial}{\partial x_n}F_1, \dots, \frac{\partial}{\partial x_n}F_n$ are cont. on a region $R$ ($\alpha < t < \beta$, $\alpha_1 < x_1 < \beta_1$, …, $\alpha_n < x_n < \beta_n$) of $t x_1\cdots x_n$ space, $(t_0, x_1^0, \dots, x_n^0) \in R$ ⇒ ∃ interval $|t - t_0| < h$ s.t. ∃ (a unique sol. …).

**7.7** For $\mathbf{x}' = P(t)\mathbf{x}$: 基本矩阵 (fundamental matrix) $\Psi(t) = [\mathbf{x}_1(t) \mid \cdots \mid \mathbf{x}_n(t)]$ (columns: 系统的每个解).
Diagonalizable: $A$ is diag.-able ⇔ ∃ $T$ s.t. $T^{-1}AT = D$.

## Ch6 Laplace transform

### 6.1

Improper integral $\int_a^\infty f(t)\,dt = \lim_{A\to\infty}\int_a^A f(t)\,dt$: limit exists → conv.; does not exist → diverge.

**Piecewise cont.** A func. $f(t)$ is p.w. cont. on $[\alpha, \beta)$ ⇐ ∃ $n < \infty$ s.t. $\alpha = t_0 < \cdots < t_n = \beta$, $f$ is cont. on every $(t_{i-1}, t_i) \subset [\alpha, \beta]$, and $\forall i \in [1, n]$, $\lim_{t\to t_{i-1}^+}f(t) = L < \infty$ and $\lim_{t\to t_i^-}f(t) = M < \infty$ exist.

**Thm 6.1.1.** $f: [a, \infty) \to \mathbb{R}$, $\forall t \ge a$, $f$ is p.w. cont.; ∃ $g: [M, \infty) \to [0, \infty)$, $M \ge a$, s.t. $\forall t \ge M$, $|f(t)| \le g(t)$ and $\int_M^\infty g(t)\,dt$ conv. ⇒ $\int_M^\infty f(t)\,dt$ conv.
If $\forall t \ge M$, $f(t) \ge g(t)$ and $\int_M^\infty g(t)\,dt$ diverges ⇒ $\int_M^\infty f(t)\,dt$ diverges.

**Laplace transform** (of a func. $f$): $\mathcal{L}(f)(s) = F(s) = \int_0^\infty e^{-ts}f(t)\,dt$ (kernel $e^{-ts}$).
Linearity: $\mathcal{L}\{c_1f_1 + c_2f_2\} = c_1\mathcal{L}\{f_1\} + c_2\mathcal{L}\{f_2\}$.

**Thm 6.1.2.** $f: [0, \infty) \to \mathbb{R}$, $\forall A > 0$, $f$ is p.w. cont. on $[0, A]$; ∃ $K > 0$, $M \ge 0$, $a \in \mathbb{R}$ s.t. $\forall t \ge M$, $|f(t)| \le Ke^{at}$
⇒ $\forall s \in \mathbb{C}$ with $\mathrm{Re}(s) > a$, $\mathcal{L}(f)(s) = F(s) = \int_0^\infty e^{-st}f(t)\,dt$ abs. conv.
(Split into 2 parts: $\int_0^\infty e^{-st}f(t)\,dt = \int_0^M\cdots + \int_M^\infty\cdots$ — finite + depends on the growth cond. of $f(t)$: $\forall t \ge M$, $|e^{-st}f(t)| \le Ke^{-st}e^{at} = Ke^{(a-s)t}$; $s > a$ ⇒ conv.)

### 6.2

**Thm 6.2.1.** $f$ is cont.; $\forall A > 0$, $f'$ is p.w. cont. on $[0, A]$; ∃ constants $K, M, a$ s.t. $\forall t \ge M$, $|f(t)| \le Ke^{at}$
⇒ $\forall s > a$, $\mathcal{L}(f')$ exists and $\mathcal{L}(f') = s\mathcal{L}(f) - f(0)$ (递归使用以找到 $\mathcal{L}(f^{(n)})$).

**Corollary 6.2.2.** $f, f', \dots, f^{(n-1)}$ are cont.; $\forall A > 0$, $f^{(n)}$ is p.w. cont. on $[0, A]$; ∃ $K, M, a$ s.t. $\forall i \in [0, n]$, $\forall t \ge M$, $|f^{(i)}(t)| \le Ke^{at}$
⇒ $\forall s > a$, $\mathcal{L}(f^{(n)})$ exists and $\mathcal{L}(f^{(n)}) = s^n\mathcal{L}(f) - s^{n-1}f(0) - \cdots - sf^{(n-2)}(0) - f^{(n-1)}(0)$ (★ order ↓ as power of $s$ ↓).

**L.T. 用于二阶线性 ODE**: for $ay'' + by' + cy = f(t)$ (将 LT 应用于每一个 $y^{(k)}$ 以及 $f(t)$; 将 $\mathcal{L}(y)$ 表示为 $Y(s)$):
$a[s^2Y(s) - sy(0) - y'(0)] + b[sY(s) - y(0)] + c\,Y(s) = F(s)$
⇒ $Y(s) = \dfrac{(as + b)y(0) + ay'(0) + F(s)}{as^2 + bs + c}$ ($y$ 转为 $Y(s)$, 后可再将 $Y(s)$ 逆转换为 $y(t)$).

### 6.3 Unit / step func. / Heaviside func.

$u_c(t) = \begin{cases}0, & t < c\\ 1, & t \ge c\end{cases}$; $\mathcal{L}\{u_c(t)\} = \int_0^\infty e^{-st}u_c(t)\,dt = \int_c^\infty e^{-st}dt = \frac{e^{-cs}}{s}$, $s > 0$.

**Thm 6.3.1.** For func. $f(t)$, $\forall s > a$, $\mathcal{L}\{f(t)\}$ exists; $c > 0$ ⇒ $\mathcal{L}\{u_c(t)f(t - c)\}(s) = e^{-cs}\mathcal{L}\{f(t)\}(s) = e^{-cs}F(s)$, $\forall s > a$ ($f(t)$ 向 $t$ 轴 + 平移 $c$; proof: 替换, 设 $u = t - c$). Equivalently $u_c(t)f(t - c) = \mathcal{L}^{-1}\{e^{-cs}F(s)\}$.

**Thm 6.3.2.** For func. $f(t)$, $\forall s > a \ge 0$, $\mathcal{L}\{f(t)\}$ exists; $c \in \mathbb{R}$ ⇒ $\forall s > a + c$, $\mathcal{L}\{e^{ct}f(t)\} = \mathcal{L}\{f(t)\}(s - c) = F(s - c)$.
(?) For $c \in \mathbb{R}$, $e^{ct}f(t)$ is the inverse Laplace transform of $F(s - c)$ ⇒ $\forall s > a + c$, $e^{ct}f(t) = \mathcal{L}^{-1}\{F(s - c)\}$.

### 6.4

求解含有 discont. forcing func.'s 的 DE: 用 Lap. trans.
E.g. 1: $2y'' + y' + 2y = g(t)$, $g(t) = u_5(t) - u_{20}(t) = \begin{cases}1, & t \in [5, 20)\\ 0, & t \in [0, 5)\cup[20, \infty)\end{cases}$.

### 6.5 Impulse function

$ay'' + by' + cy = g(t)$; $d_\tau(t) = g(t)$: ∃ $\tau$, 使 $g(t)$ 在 $(t_0 - \tau, t_0 + \tau)$ 上很大, 0 otherwise.
The total impulse of the force $g(t)$ over $(t_0 - \tau, t_0 + \tau)$: $I(\tau) = \int_{t_0-\tau}^{t_0+\tau}g(t)\,dt = \int_{-\infty}^\infty g(t)\,dt$.
$\forall t \ne 0$, $\lim_{\tau\to 0^+}d_\tau(t) = 0$; $\forall\tau \ne 0$, $I(\tau) = 1$ ⇒ $\lim_{\tau\to 0^+}I(\tau) = 1$.

**Unit impulse function / Dirac delta function**: $\forall t \ne 0$, $\delta(t) = 0$; $\int_{-\infty}^\infty\delta(t)\,dt = 1$.
$\forall t_0$, a unit imp. func. at $t_0$: $\forall t \ne t_0$, $\delta(t - t_0) = 0$; $\int_{-\infty}^\infty\delta(t - t_0)\,dt = 1$.
Lap. trans.: $\mathcal{L}\{\delta(t - t_0)\} = \lim_{\tau\to 0^+}\mathcal{L}\{d_\tau(t - t_0)\} = e^{-st_0}$; $\mathcal{L}\{\delta(t)\} = \lim_{t_0\to 0}e^{-st_0} = 1$.
$\forall$ cont. func. $f(t)$: $\int_{-\infty}^\infty\delta(t - t_0)f(t)\,dt = f(t_0)$.

### 6.6 Convolution integral

The convolution of func.'s $f$ and $g$: $h(t) = (f*g)(t) = \int_0^t f(\tau)g(t - \tau)\,d\tau = \int_0^t f(t - \tau)g(\tau)\,d\tau$.
**Thm 6.6.1**: $\mathcal{L}\{h(t)\} = F(s)G(s) = \mathcal{L}\{f(t)\}\mathcal{L}\{g(t)\}$.
Prop.: $f*g = g*f$; $f*(g + h) = f*g + f*h$; $(f*g)*h = f*(g*h)$; $f*0_{\mathbb{R}\to\mathbb{R}} = 0_{\mathbb{R}\to\mathbb{R}}$.

**Transfer function** $H(s) = \frac{1}{as^2 + bs + c}$; **impulse response** = the inverse Lap. trans. of $H$:
- 不同实根 $(r_1, r_2)$: $h(t) = \frac{e^{r_1t} - e^{r_2t}}{a(r_1 - r_2)}u(t)$
- 重复实根 $(r)$: $h(t) = \frac1a te^{rt}u(t)$
- 共轭复根 $(\alpha \pm \beta i)$: $h(t) = \frac{1}{a\beta}e^{\alpha t}\sin(\beta t)\,u(t)$ (as written: $\frac1a e^{\alpha t}\sin(\beta t)u(t)$)

解 $ay'' + by' + cy = g(t)$, $y(0) = y_0$, $y'(0) = y_0'$ — use Lap. trans.:
$(as^2 + bs + c)Y(s) - (as + b)y_0 - ay_0' = G(s)$ ($Y(s) = \int_0^\infty e^{-st}y(t)\,dt$, $G(s) = \int_0^\infty e^{-st}g(t)\,dt$)
⇒ Sol.: $y(t) = \phi(t) + \psi(t)$, $\phi$: homo. eq. sol.; $\psi(t) = \int_0^t h(t - \tau)g(\tau)\,d\tau$.

## Ch9 Nonlinear systems & stability

### 9.1 (~ 见 2.5)

- **Equilibrium sol.**: the sol.'s $y$ s.t. $y' = 0$.
- **Critical pt.**: 对系统 $\frac{d\mathbf{x}}{dt} = A\mathbf{x}$, critical pt.'s $\mathbf{x}_0$ s.t. $\frac{d\mathbf{x}}{dt} = \mathbf{0}$.
- **Trajectory**: 当 $t$↑ 时, sol. 的轨迹 (phase plane 中).
- **Phase plane**: 一个 sys. 的 2D 图形表示. **Phase portrait**: 一个 phase plane 中所有 trajectory 的集合.

解 sys. $\frac{d\mathbf{x}}{dt} = A\mathbf{x}$ ($A$: $2\times2$, $\mathbf{x}$: $2\times1$). 对于 $A$ 的特征值 $\det(A - \lambda I) = 0$ — general sol.:

1. $\lambda_i$ 均实、不等、同号 (corresponding eigenvectors $\xi^{(i)}$): $\mathbf{x} = c_1\xi^{(1)}e^{\lambda_1t} + c_2\xi^{(2)}e^{\lambda_2t} = e^{\lambda_2t}(c_1\xi^{(1)}e^{(\lambda_1-\lambda_2)t} + c_2\xi^{(2)})$. Node: $\lambda_1$ 与 $\lambda_2$ 同号; nodal sink $\lambda_1, \lambda_2 < 0$; nodal source $\lambda_1, \lambda_2 > 0$.
2. $\lambda_i$ 均实、不等、异号: $\mathbf{x} = c_1\xi^{(1)}e^{\lambda_1t} + c_2\xi^{(2)}e^{\lambda_2t}$.
3. $\lambda_i$ 相等: eigenvectors indep. ⇒ $\mathbf{x} = (c_1\xi^{(1)} + c_2\xi^{(2)})e^{\lambda t}$; eigenvectors dep. ⇒ $\mathbf{x} = [c_1\xi + c_2(\xi t + \eta)]e^{rt}$ with $(A - \lambda I)\eta = \xi$.
4. $\lambda_i$ 均复, $\mathrm{Re}(\lambda_i) \ne 0$, $\lambda_{1,2} = a \pm ib$: for the system $\mathbf{x}' = \begin{pmatrix}a & b\\ -b & a\end{pmatrix}\mathbf{x}$ (or $x_1' = ax_1 + bx_2$, $x_2' = -bx_1 + ax_2$), let $r^2 = x_1^2 + x_2^2$, $\tan\theta = \frac{x_2}{x_1}$
   ⇒ $r'(t) = ar(t)$, $\theta'(t) = -b$ ⇒ $r(t) = Ce^{at}$, $\theta(t) = -bt + \theta_0$ ($\theta_0 = \theta(0)$) ⇒ $x(t) = r\cos\theta = Ce^{at}\cos(-bt + \theta_0)$, $y(t) = r\sin\theta = Ce^{at}\sin(-bt + \theta_0)$.
5. $\lambda_i$ 均复, $\mathrm{Re}(\lambda_i) = 0$: for $\mathbf{x}' = \begin{pmatrix}0 & b\\ -b & 0\end{pmatrix}\mathbf{x}$ (or $x_1' = bx_2$, $x_2' = -bx_1$) ⇒ $r'(t) = 0$, $\theta'(t) = -b$ ⇒ $r(t) = C$, $\theta(t) = -bt + \theta_0$.

### 9.2 Autonomous system

$\frac{dx}{dt} = F(x, y)$, $\frac{dy}{dt} = G(x, y)$ (自变量与 $t$ 无关), $x(t_0) = x_0$, $y(t_0) = y_0$; linear case $\mathbf{x}' = A\mathbf{x}$.

**Critical points**: for sys. $\mathbf{x}' = \mathbf{f}(\mathbf{x})$, the $\mathbf{x}_0$ s.t. $\mathbf{x}_0' = \mathbf{f}(\mathbf{x}_0) = \mathbf{0}$.
- Critical pt. $\mathbf{x}_0$ is **stable** ⇐ $\forall\varepsilon > 0$, ∃ $\delta > 0$ s.t. $\forall$ sol. $\mathbf{x}$ with $\|\mathbf{x}(0) - \mathbf{x}_0\| < \delta$, $\forall t > 0$, $\|\mathbf{x}(t) - \mathbf{x}_0\| < \varepsilon$; **unstable** ⇐ otherwise.
- **Asymptotically stable** ⇐ stable, and ∃ $\delta_0 > 0$ s.t. $\forall\mathbf{x}$ with $\|\mathbf{x}(0) - \mathbf{x}_0\| < \delta_0$, $\lim_{t\to\infty}\mathbf{x}(t) = \mathbf{x}_0$.
- Suppose pt. $P(x_p, y_p)$: basin of attraction; separatrix.

### 9.3

**Thm 9.3.1.** $A \in \mathbb{R}^{2\times2}$ with eigenvalues $\lambda_1, \lambda_2$; system $\mathbf{x}' = A\mathbf{x}$; $\mathbf{x}_0 = \mathbf{0}$ is (the critical pt.) ⇒ $\mathbf{x}_0$ is:
- asy. stable ⇔ $\lambda_1, \lambda_2$ 都 (实/复) $\mathrm{Re}(\lambda_i) < 0$;
- stable (not asy.) ⇔ $\lambda_1, \lambda_2$ 都 (复) $\mathrm{Re}(\lambda_i) = 0$;
- unstable ⇔ etc. ($\lambda_1, \lambda_2$ 都 (实/复) $\mathrm{Re}(\lambda_i) > 0$ …).
