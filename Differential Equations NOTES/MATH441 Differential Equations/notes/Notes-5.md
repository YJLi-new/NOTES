# MATH441 Notes 5 — Existence & Uniqueness (cont.), Uniform Convergence, Picard Iteration

> Transcribed from the handwritten notes [Notes-5.pdf](Notes-5.pdf). `(?)` marks unclear handwriting.

## 1

**Solve** $\begin{cases}\frac{dy}{dx} = \frac{3x^2 + 4x + 2}{2(y - 1)} = f(x, y)\\ y(0) = -1\end{cases}$. Assumptions: $f(x, y)$ is cont. on $\mathbb{R}$ (where defined); $f_y(x, y)$ likewise.

$\int 2(y - 1)\,dy = \int(3x^2 + 4x + 2)\,dx$ ⇒ $y^2 - 2y = x^3 + 2x^2 + 2x + C$; $(-1)^2 - 2(-1) = C$ ⇒ $C = 3$.
$y^2 - 2y - (x^3 + 2x^2 + 2x + 3) = 0$ ⇒ $y(x) = \frac{2 \pm\sqrt{4 + 4(x^3 + 2x^2 + 2x + 3)}}{2} = 1 \pm\sqrt{x^3 + 2x^2 + 2x + 4}$
⇒ Unique sol. is $y(x) = 1 - \sqrt{x^3 + 2x^2 + 2x + 4}$, needing $x^3 + 2x^2 + 2x + 4 > 0$ ⇒ $(x + 2)(x^2 + 2) > 0$ ⇒ $x > -2$.

Take $y(0) = 1$ ⇒ $C = 1^2 - 2\times 1 = -1$: $y^2 - 2y - (x^3 + 2x^2 + 2x - 1) = 0$ ⇒ $y(x) = 1 \pm\sqrt{x^3 + 2x^2 + 2x}$ (non-uniqueness: $f$ is not defined at $y = 1$), needing $x(x^2 + 2x + 2) > 0$ ⇒ $x > 0$.

## Sec. 2.8

$a_n = \frac1n$, $f(n) = \frac1n$.

1. Seq. of functions: $f_n(x) = \frac xn$ ($f_1(x) = x$, $f_2(x) = \frac x2$, $f_3(x) = \cdots$), $f_n: A \to \mathbb{R}$, $x \in A$: $\lim_{n\to\infty}f_n(x) = 0$. $f_n$ is a seq. of func.'s.
2. $f_n(x) = x^n$, $x \in [0, 1]$, $n = 1, 2, \dots$ ($f_1(x) = x$, $f_2(x) = x^2$, …): $\forall n$, $f_n(0) = 0$, $f_n(1) = 1$ →
   $$\lim_{n\to\infty}f_n(x) = \begin{cases}1, & x = 1\\ 0, & 0 \le x < 1\end{cases}$$
3. $\int_0^1 f_n(x)\,dx = a_n$. If $\lim_{n\to\infty}a_n = L$ ⇒ then $\lim_{n\to\infty}\int_0^1 f_n = L$. 是否 $\int_0^1\lim_{n\to\infty}f_n(x)\,dx = \lim_{n\to\infty}\int_0^1 f_n(x)\,dx = L$? → 有条件.

### Uniform vs. pointwise convergence

Let $f_n: A \to \mathbb{R}$ be a seq. of func.'s.

- **Def.** $f_n(x)$ conv. to $f(x)$ **pointwise** $\iff \forall\varepsilon > 0$ and $x \in A$ (fixed on $x$!), $\exists N \in \mathbb{N}$ s.t. $\forall n > N$, $|f_n(x) - f(x)| < \varepsilon$.
- **Def.** $f_n(x)$ conv. to $f(x)$ **uniformly** (较强) $\iff \forall\varepsilon > 0$ (不定 $x$), $\exists N \in \mathbb{N}$ s.t. $\forall n > N$ and $x \in A$, $|f_n(x) - f(x)| < \varepsilon$.

**E.g.** $f_n(x) = \frac xn$ (on $[0, 1]$), $f(x) = 0$. Take $N = [\frac1\varepsilon] + 1$ → $N(\varepsilon)$ only depends on $\varepsilon$ (⇔ uni. conv.):
$|f_n(x) - f(x)| = \left|\frac xn - 0\right| = \frac xn \le \frac1n < \frac1N = \frac{1}{1 + [\frac1\varepsilon]} < \varepsilon$.
(pw. conv.: $N(\varepsilon, x)$ → depends on $\varepsilon$ and $x$.)

## 2

**Q:** If $\lim_{n\to\infty}f_n(x) = f(x)$, then 是否可能有 $\lim_{n\to\infty}\int_a^b f_n(x)\,dx = \int_a^b f(x)\,dx$? **A:** ✗ (not in general).

For $f: [a, b] \to \mathbb{R}$, uni. conv. 的另一种 def.: $f_n(x) \to f(x)$ uniformly $\iff \lim_{n\to\infty}\max_{x\in[a,b]}|f_n(x) - f(x)| = 0$.

**Prop.** Let $f_n: A \to \mathbb{R}$; $f_n$ is a **cont.** seq. of func.'s and $f_n(x) \to f(x)$ **uni.** in $A$ ⇒ $f(x)$ is cont.

*Proof.* $\forall$ point $x_0 \in A$ (WTS: $f(x)$ is cont. at $\forall x_0 \in A$). Let $\varepsilon > 0$ be given. Take $\delta > 0$ s.t. $|x - x_0| < \delta$ …
- Fact: $f_n \to f$ uni. ⇒ we can fix $N$ s.t. $\forall n > N$, $|f_n(x) - f(x)| < \frac\varepsilon3$ (for all $x$).
- Fact: $\forall n$, $f_n$ is cont. at $x_0$ ⇒ $f_{N+1}$ is cont. at $x_0$ ⇒ $(|x - x_0| < \delta \Rightarrow |f_{N+1}(x) - f_{N+1}(x_0)| < \frac\varepsilon3)$.

$$|f(x) - f(x_0)| = |f(x) - f_n(x) + f_n(x) - f_n(x_0) + f_n(x_0) - f(x_0)| \le |f(x) - f_n(x)| + |f_n(x) - f_n(x_0)| + |f_n(x_0) - f(x_0)|$$

(代其上 $n$ 全为 $N + 1$) $< \frac\varepsilon3 + \frac\varepsilon3 + \frac\varepsilon3 = \varepsilon$.

**E.g.** ① $f_n(x) = \frac xn$, $x \in [0, 1]$ → $f_n \to 0$ uni. ② $f_n(x) = x^n$, $x \in [0, 1]$: $f_n(x) \to \begin{cases}1, & x = 1\\ 0, & x \in [0, 1)\end{cases} = f(x)$ (not cont. ⇒ not uni.). (Recall $\forall a$ with $|a| < 1$, $\lim_{n\to\infty}a^n = 0$.)

**Prop.** Let $f_n: A \to \mathbb{R}$; $f_n$ is a cont. seq. of func.'s, $f_n(x) \to f(x)$ uni. in $A$ ⇒

$$\lim_{n\to\infty}\underbrace{\int_a^b f_n(x)\,dx}_{=:a_n} = \underbrace{\int_a^b f(x)\,dx}_{=:L}$$

*Proof.* (WTS: $\lim_{n\to\infty}a_n = L$.) $\forall\varepsilon > 0$:
$|a_n - L| = \left|\int_a^b(f_n(x) - f(x))\,dx\right| \le \int_a^b|f_n(x) - f(x)|\,dx \le \max_{x\in[a,b]}|f_n(x) - f(x)|\cdot\underbrace{\int_a^b dx}_{=b-a} < \frac{\varepsilon}{b - a}(b - a) = \varepsilon$,
using $\lim_{n\to\infty}\left(\max_{x\in[a,b]}|f_n(x) - f(x)|\right) = 0$ ⇒ $\exists N \in \mathbb{N}$ s.t. $\forall n > N$, $\max_{x\in[a,b]}|f_n(x) - f(x)| < \frac{\varepsilon}{b - a}$.

## Picard iteration

**Solve** $\begin{cases}y' = 2t(1 + y)\\ y(0) = 0\end{cases}$.
($y(t) - \underbrace{y(0)}_{=0} = \int_0^t y'(s)\,ds = \int_0^t 2s(1 + y(s))\,ds$) ⇒ $y(t) = \int_0^t 2s(1 + y(s))\,ds$.

- $y_0(t) = y(0) = 0$
- $y_1(t) = \int_0^t 2s(1 + y_0(s))\,ds = \int_0^t 2s\,ds = t^2$
- $y_2(t) = \int_0^t 2s(1 + y_1(s))\,ds = \int_0^t 2s(1 + s^2)\,ds = t^2 + \frac{t^4}{2}$
- $y_n(t) = \int_0^t 2s(1 + y_{n-1}(s))\,ds = t^2 + \cdots$ (见 …; converges to $e^{t^2} - 1$)
