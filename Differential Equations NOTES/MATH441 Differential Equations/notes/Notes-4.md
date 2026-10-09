# MATH441 Notes 4 — Modeling with D.E.'s, Population Models, Existence & Uniqueness

> Transcribed from the handwritten notes [Notes-4.pdf](Notes-4.pdf). `(?)` marks unclear handwriting.

## 1 Sec. 2.3 & 2.5 — Modeling with D.E.'s

### ① Compound interest

Sum of money deposited in a bank with rate $r$; $S(0) = S_0$ initial investment. Interest compounds once per year:
- 1st: $S_0 + rS_0 = S_0(1 + r)$; 2nd: $S_0(1 + r) + rS_0(1 + r) = S_0(1 + r)^2$; … $S(t) = S_0(1 + r)^t$.
- If the interest is compounded $m$ times per year, after $t$ years: $S(t) = S_0\left(1 + \frac rm\right)^{mt}$.
- If the interest is compounded continuously, then $m \to \infty$ (recall $\lim_{n\to\infty}(1 + \frac1n)^n = e$; let $n = \frac mr$):
  $S(t) = S_0\lim_{m\to\infty}\left(1 + \frac{1}{m/r}\right)^{\frac mr rt} = S_0\left[\lim_{n\to\infty}\left(1 + \frac1n\right)^n\right]^{rt} = S_0e^{rt}$.

$S(t) = S_0e^{rt}$ is the sol. of $\begin{cases}\frac{dS}{dt} = rS\\ S(0) = S_0\end{cases}$.
($\Delta t$ small: $S(t + \Delta t) = S(t) + S(t)r\Delta t$ ⇒ $\frac{S(t + \Delta t) - S(t)}{\Delta t} = rS(t)$ → as $\Delta t \to 0$, $S'(t) = rS$.)

Assume deposits at a constant rate $k$ (= money/time): $\frac{dS}{dt}$ = rate in − rate out $= rS + k$ ⇒ $S(t) = S_0e^{rt} + \frac kr(e^{rt} - 1)$, $S(0) = S_0$.

### ② Escape velocity

$F = ma = m\frac{dv}{dt}$ ($v$: velocity). Gravitation $F = \frac{kmM}{d^2}$.
$m\frac{dv}{dt} = \frac{-C}{(R + x)^2} = F$, where $C = mgR^2$ ($x$: displacement), $v(0) = v_0$; $F_{x=0} = -mg = -\frac{C}{R^2}$ ⇒ $m\frac{dv}{dt} = \frac{-mgR^2}{(R + x)^2}$.
$\frac{dx}{dt} = v(t)$, $\frac{d^2x}{dt^2} = \frac{dv}{dt} = a$. Chain rule: $\frac{dv(x(t))}{dt} = \frac{dv}{dx}\frac{dx}{dt} = \frac{dv}{dx}v = \frac{-gR^2}{(R + x)^2}$; $v_0 = v(x = 0) = v(0)$.

$\int v\,dv = \int\frac{-gR^2}{(R + x)^2}dx$ ⇒ $\frac{v^2}{2} = \frac{gR^2}{R + x} + C$. $x = 0$: $\frac{v_0^2}{2} = \frac{gR^2}{R + 0} + C$ ⇒ $C = \frac{v_0^2}{2} - gR$.
($\frac{v^2}{2} = \frac{gR^2}{R + x} + \frac{v_0^2}{2} - gR$) ⇒ $v(x) = \sqrt{v_0^2 - 2gR + \frac{2gR^2}{R + x}}$.
When $v = 0$, $x$ is $x_{\max}$: $v_0^2 - 2gR + \frac{2gR^2}{R + x} = 0$ ⇒ $x_{\max} = \frac{v_0^2R}{2gR - v_0^2}$.
Escape velocity: when $x_{\max} \to \infty$, i.e. when $2gR = v_0^2$ ⇒ $v_0 = \sqrt{2gR}$.

### ③ Population

$\frac{dy}{dt} = ry$ (population ~ time), $y(0) = y_0$ ⇒ $y(t) = y_0e^{rt}$. Next: $\frac{dy}{dt} = y(r - ay)$.

## 2 Population Growth Models — Sec. 2.5

$\frac{dy}{dt} = ry$, $y(0) = y_0$, $y(t) \ge 0$, $y(t) = y_0e^{rt}$. General: $\frac{dy}{dt} = yf(r, y)$, $y(0) = y_0$, with $f(r, y) = r - ay$:

$$\frac{dy}{dt} = y(r - ay) = ry\left(1 - \frac ary\right) = ry\left(1 - \frac yk\right),\quad\text{where } k = \frac ra$$

$\frac{dy}{dt} = f(y)$: the sign of $f(y)$ 决定 the monotonicity of $y(t)$.
$f(y) = ry - \frac rky^2$, $f'(y) = r - \frac{2r}{k}y$, $f''(y) = -\frac{2r}{k} < 0$; $y = \frac k2 \iff f'(\frac k2) = 0$; $f(\frac k2) = \cdots = \frac{rk}{4} > 0$; $y < \frac k2$: $f' > 0$, $f$↑; $y > \frac k2$: $f' < 0$, $f$↓. ($f(y)$: a downward parabola with roots $0$ and $k$, positive in between.)

$y = 0$ & $y = k$ are called **equilibrium solutions**.

$\frac{d^2y}{dt^2} = \frac{d}{dt}\left(\frac{dy}{dt}\right) = \frac{d}{dt}f(y) = f'(y)\frac{dy}{dt} = f'(y)f(y)$; $f'(y) = r - \frac{2r}{k}y$ is $+$ for $y < \frac k2$, $-$ for $y > \frac k2$.

**接下来: solve** $\frac{dy}{dt} = ry\left(1 - \frac yk\right)$. Use $y = y_1 + \frac1u = k + \frac1u$, $\frac{dy}{dt} = -\frac{1}{u^2}\frac{du}{dt}$:
$-\frac{1}{u^2}\frac{du}{dt} = r\left(k + \frac1u\right)\left(1 - 1 - \frac{1}{ku}\right) = -r\left(k + \frac1u\right)\frac{1}{ku}$
⇒ $\frac{du}{dt} = \frac{ru}{k}\left(k + \frac1u\right) = ru + \frac rk$
⇒ $e^{-rt}\left(\frac{du}{dt} - ru\right) = \frac rke^{-rt}$ ⇒ $\frac{d}{dt}(e^{-rt}u) = \frac rke^{-rt}$ ⇒ $e^{-rt}u = \int\frac rke^{-rt}dt = -\frac1ke^{-rt} + C$
⇒ $u(t) = Ce^{rt} - \frac1k = \frac{kCe^{rt} - 1}{k}$ ⇒ $\frac1u = \frac{k}{kCe^{rt} - 1}$ ⇒ $y(t) = k + \frac1u = k + \cdots$
⇒ $y_0 = y(0) = k + \frac{k}{kC - 1}$ ⇒ $C = \frac1k + \frac{1}{y_0 - k}$ ⇒

$$y(t) = \frac{y_0k}{y_0 + (k - y_0)e^{-rt}}$$

(Alternatively: $\frac{dy}{dt} = f(y)$ ⇒ $\int\frac{dy}{f(y)} = t + C$.)

### Threshold model

$\begin{cases}\frac{dy}{dt} = -ry\left(1 - \frac yT\right)\\ y(0) = y_0\end{cases}$: $f(y) = -ry + \frac{ry^2}{T}$ ⇒ $f'(y) = -r + \frac{2ry}{T}$; $\frac{dy}{dt} = f(y)$ ⇒ $\frac{d^2y}{dt^2} = f(y)f'(y)$.
($f(y)$: upward parabola with roots $0$ and $T$, minimum $f(\frac T2) = \cdots = -\frac{rT}{4}$; $f'(y)$ is $-$ for $y < \frac T2$ and $+$ beyond; solution curves: $y = T$ is an unstable threshold, curves below it decay toward 0, curves above blow up.)
Sol.: $y(t) = \frac{y_0T}{y_0 + (T - y_0)e^{rt}}$ … (见 …)

## 3

$\frac{dy}{dt} = -r\left(1 - \frac yT\right)y$, $y(0) = y_0$: if $y_0 > T$, $\lim_{t\to t^*}y(t) = \infty$ with
$t^* = \frac1r\ln\frac{y_0}{y_0 - T}$ ($y_0 > T$), from $y(t) = \frac{y_0T}{y_0 + (T - y_0)e^{rt}}$ and $y_0 + (T - y_0)e^{rt^*} = 0$.

### Logistic growth with a threshold

$$\frac{dy}{dt} = -ry\left(1 - \frac yT\right)\left(1 - \frac yk\right),\quad r > 0,\ 0 < T < k,\quad y(0) = y_0$$

$f(y) = -ry\left(1 - \frac yT\right)\left(1 - \frac yk\right)$, $f'(y) = -r + 2yr\left(\frac1T + \frac1k\right) - \frac{3y^2r}{Tk}$.

Inflection points: $f'(y) = 0 \iff 3y^2 - 2(k + T)y + Tk = 0$.
$k^2 + T^2 - Tk = \left(k - \frac T2\right)^2 + \frac34T^2 > 0$; $\Delta = 4(k + T)^2 - 12Tk = 4(k^2 + T^2 - kT)$;
$y_{1,2} = \frac{2(k + T) \pm 2\sqrt{k^2 + T^2 - kT}}{2\cdot 3}$; $y_+ > 0$, $y_- > 0$, $0 < y_- < y_+ < k$;
$T < \frac{k + T + \sqrt{k^2 + T^2 - kT}}{3} < k$ (since $(2k - T)^2 > k^2 + T^2 - kT$).

## Sec. 2.4 and 2.8 — Existence & uniqueness

**Thm.** Let $f(y, t)$ and $\frac{\partial f}{\partial y}(y, t) = f_y(y, t)$ be cont. in some rectangle $\alpha < t < \beta$, $\gamma < y < \delta$ containing (含点) $(y_0, t_0)$. Then ∃ interval $(t_0 - h, t_0 + h)$ around $t_0$, $h > 0$, s.t. ∃ unique sol. to

$$\begin{cases}\frac{dy}{dt} = f(y, t)\\ y(t_0) = y_0\end{cases}$$

(局部!) E.g. the linear case $\begin{cases}y' + p(t)y = g(t)\\ y(t_0) = y_0\end{cases}$: $f(y, t) = g(t) - p(t)y$; $y(t) = \frac{1}{\mu(t)}\left[\int\mu(t)g(t)\,dt + C\right]$, where $\mu(t) = e^{\int p(t)dt}$.
