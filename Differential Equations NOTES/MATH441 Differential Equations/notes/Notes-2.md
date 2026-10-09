# MATH441 Notes 2 — Intro, Well-posedness, First-order Linear ODEs

> Transcribed from the handwritten notes [Notes-2.pdf](Notes-2.pdf). `(?)` marks unclear handwriting.

## 1 Ch1 Sec. 1.1–1.3

Falling object with drag:

$$\begin{cases}\dfrac{dv(t)}{dt} = g - \dfrac{\gamma}{m}v(t)\\ v(0) = v_0\end{cases}\qquad \frac{1}{g - \frac\gamma m v(t)}\frac{dv(t)}{dt} = 1$$

Sol.: $v(t) = \dfrac{mg}{\gamma} + \left[v_0 - \dfrac{mg}{\gamma}\right]e^{-\frac\gamma m t}$ (all solution curves approach the equilibrium $\frac{mg}{\gamma}$).

### Well-posed d.e.'s

1. existence
2. uniqueness
3. continuity w.r.t. initial data

### Ill-posed d.e.'s — examples

1. $\begin{cases}\frac{dy}{dt} = y^{1/3}\\ y(0) = 0\end{cases}$: $y_1(t) = 0$ is a sol. Also $\frac{1}{y^{1/3}}\frac{dy}{dt} = 1$ ⇒ $\frac32\frac{d}{dt}\left(y^{2/3}(t)\right) = 1$ ⇒ $y^{2/3} = \frac23t + C$, $0 = y(0)$ ⇒ $C = 0$ ⇒ $y(t) = \left(\frac23t\right)^{3/2}$ — non-uniqueness.
2. (Non-continuity w.r.t. data / blow-up)
   ① $\begin{cases}\frac{dy}{dt} = y^2\\ y(0) = 1\end{cases}$: $\frac{1}{y^2}\frac{dy}{dt} = 1$ ⇒ $\frac{d}{dt}\left(-\frac{1}{y(t)}\right) = 1$ ⇒ $-\frac{1}{y(t)} = t + C$; $-\frac{1}{y(0)} = 0 + C$ ⇒ $C = -1$ ⇒ $y(t) = \frac{1}{1 - t}$ on $[0, 1)$, $\lim_{t\to 1}y(t) = \infty$.
   ② $\begin{cases}\frac{dy}{dt} = y^2\\ y(2) = 1\end{cases}$: $-\frac{1}{y(2)} = 2 + C$ ⇒ $C = -3$ ⇒ $y = \frac{1}{3 - t}$.

### The pendulum problem

设无 friction, 机械能守恒. Formula: $W = F\cdot d$.
- 势能 $E_p = F\cdot d = mgl(1 - \cos\theta)$ ($d = l - l\cos\theta$)
- 动能 $E_k = \frac12mv^2(t)$, $v = \omega l = \frac{d\theta(t)}{dt}l$ (角速度) ⇒ $E_k = \frac12m\left(\frac{d\theta(t)}{dt}\right)^2l^2$
- $E = E_k + E_p$, $\frac{dE(t)}{dt} = 0$ (机械能守恒):

$$\frac{dE}{dt} = \frac12ml^2\cdot 2\frac{d\theta}{dt}\frac{d^2\theta}{dt^2} + mgl\sin\theta\frac{d\theta}{dt} = 0\ \Rightarrow\ \frac{d\theta}{dt}\Big(l\frac{d^2\theta}{dt^2} + g\sin\theta\Big) = 0$$

Eq. of motion: $\dfrac{d^2\theta(t)}{dt^2} + \dfrac gl\sin\theta(t) = 0$.

Small oscillations ($|\theta(t)| \ll 1$): $\sin\theta = \theta - \frac{\theta^3}{3!} + \frac{\theta^5}{5!} - \cdots$ (对于小 $\theta$, $\sin\theta \approx \theta$) ⇒ $\frac{d^2\theta}{dt^2} + \omega^2\theta = 0$ where $\omega^2 = \frac gl$.

Try $\theta(t) = \sin(\omega t)$: $\theta''(t) = -\omega^2\sin(\omega t) = -\omega^2\theta$ ⇒ $\theta'' + \omega^2\theta = 0$. $\theta(t) = \cos(\omega t)$ is another solution; $\theta(t) = A\cos(\omega t) + B\sin(\omega t)$.

## 2 Ch2 Sec. 2.1

$$\frac{dy(t)}{dt} + p(t)y(t) = g(t)$$

(first-order, linear diff. eq. with non-constant but cont. func.'s). When $p(t) = 0$: $y(t) = \int g(t)\,dt$.

### Method of integrating factor

$\mu(t)\frac{dy}{dt} + \mu(t)p(t)y(t) = \mu(t)g(t)$, 这里的 $\mu(t)$ 待定. Since $\frac{d}{dt}(\mu(t)y(t)) = \frac{d\mu(t)}{dt}y(t) + \mu(t)\frac{dy(t)}{dt}$, demand $\frac{d\mu(t)}{dt} = \mu(t)p(t)$
⇒ $\frac1\mu\frac{d\mu}{dt} = p$ ⇒ $\frac{d}{dt}\ln|\mu(t)| = p(t)$ ⇒ $\ln|\mu(t)| = \int p(t)\,dt$ ⇒ $|\mu(t)| = e^{\int p(t)dt}$ ⇒ $\mu(t) = \pm e^{\int p(t)dt}$. Pick $\mu(t) = e^{\int p(t)\,dt}$.

$\frac{dy}{dt} + py = g$ becomes $\frac{d}{dt}(\mu y) = \mu g$. Thus $\mu y = \int\mu(t)g(t)\,dt + C$:

$$y(t) = \frac{1}{\mu(t)}\left[\int\mu(t)g(t)\,dt + C\right]\quad(\text{solution})$$

**E.g.** $\frac{dy}{dt} + ay = b$, where $a, b \in \mathbb{R}$. Step 1: $\mu(t) = e^{\int a\,dt} = e^{at}$; $e^{at}\frac{dy}{dt} + ae^{at}y = be^{at}$ ⇒ $\frac{d}{dt}(e^{at}y(t)) = be^{at}$ ⇒ $e^{at}y(t) = \int be^{at}dt = \frac bae^{at} + C$ ⇒ $y(t) = Ce^{-at} + \frac ba$.

### Initial value problem

$\frac{dy}{dt} + ay = b$, $y(0) = 1$: $y(t) = Ce^{-at} + \frac ba$, $1 = y(0) = C + \frac ba$ ⇒ $C = 1 - \frac ba$ ⇒ $y(t) = \left(1 - \frac ba\right)e^{-at} + \frac ba = e^{-at} + \frac ba(1 - e^{-at})$ → unique solution.

$\begin{cases}\frac{dy}{dt} + ay = g(t)\\ y(t_0) = y_0\end{cases}$, $\mu(t) = e^{at}$: $y(t) = \frac{1}{\mu(t)}\left[\int_{t_0}^t\mu(s)g(s)\,ds + C\right]$; $y_0 = y(t_0) = e^{-at_0}[0 + C]$ ⇒ $C = y_0e^{at_0}$. Unique:

$$y(t) = e^{-at}\left[\int_{t_0}^t e^{as}g(s)\,ds + e^{at_0}y_0\right] = y_0e^{-a(t - t_0)} + \int_{t_0}^t e^{-a(t-s)}g(s)\,ds$$

## 3

$\frac{dy}{dt} + ay = g(t)$, $y(t_0) = y_0$, $\mu(t) = e^{\int p\,dt} = e^{at}$: $e^{at}y' + aye^{at} = g(t)e^{at}$ ⇒ $\frac{d}{dt}(e^{at}y) = g(t)e^{at}$ ⇒ $e^{at}y(t) = \int_{t_0}^t g(s)e^{as}ds + C$; $e^{at_0}y_0 = \int_{t_0}^{t_0}\cdots + C = C$ ⇒

$$y(t) = e^{-a(t - t_0)}y_0 + \int_{t_0}^t e^{-a(t-s)}g(s)\,ds = y_h(t) + y_{nh}(t)$$

- $y_h(t) = e^{-a(t-t_0)}y_0$ is a sol. of $\begin{cases}y' + ay = 0\\ y(t_0) = y_0\end{cases}$;
- $y_{nh}(t) = \int_{t_0}^t e^{-a(t-s)}g(s)\,ds$ is a sol. of $\begin{cases}y' + ay = g(t)\\ y(t_0) = 0\end{cases}$.

⇒ **Duhamel's Principle**: $y(t) = y_h(t) + y_{nh}(t)$.

### The method of variation of parameters

$y' + p(t)y = g(t)$.

- Step 1: solve $y' + p(t)y = 0$: $\frac{dy}{dt} = -p(t)y$ ⇒ $\frac1y\frac{dy}{dt} = -p$ ⇒ $\frac{d}{dt}\ln|y(t)| = -p(t)$ ⇒ $\ln|y(t)| = -\int p(t)\,dt + C$ ⇒ $y(t) = \pm e^{C}e^{-\int p\,dt}$, i.e. $y(t) = Ce^{-\int p(t)\,dt}$.
- Step 2: try $y(t) = C(t)e^{-\int p(t)dt}$ in the equation: $\frac{d}{dt}y(t) = C'(t)e^{-\int p\,dt} + C(t)e^{-\int p\,dt}(-p(t))$, where the second term $= -p(t)y(t)$; so $\frac{d}{dt}y + py = C'(t)e^{-\int p\,dt} = g(t)$ ⇒ $C'(t) = g(t)e^{\int p\,dt} = g(t)\mu(t)$ ⇒ $C(t) = \int g(t)\mu(t)\,dt + C$.

Thus $y(t) = C(t)e^{-\int p\,dt} = \frac{C(t)}{\mu(t)} = \frac{1}{\mu(t)}\left[\int g(t)\mu(t)\,dt + C\right]$.

Two methods: integrating factor; variation of parameters.

**Solve** $\begin{cases}ty' + 2y = 4t^2\\ y(1) = 2\end{cases}$.
- Step 1: $ty' + 2y = 0$ ⇒ $\frac1y\frac{dy}{dt} = -\frac2t$ ($= \frac{d}{dt}\ln|y|$) ⇒ $\ln|y(t)| = -2\int\frac{dt}{t} = -2\ln t + C = \ln\frac{1}{t^2} + C$, i.e. $\ln|y| + 2\ln t = C$ or $\ln|yt^2| = C$ ⇒ $y(t) = \frac{C}{t^2}$.
- Step 2: try $y(t) = \frac{C(t)}{t^2}$ in $y' + \frac2ty = 4t$: $y' = C'(t)\frac{1}{t^2} + C(t)(-2t^{-3}) = \frac{C'}{t^2} - \frac2t\frac{C(t)}{t^2}$; $y' + \frac2ty = \frac{C'(t)}{t^2} = 4t$ ⇒ $C'(t) = 4t^3$ ⇒ $C(t) = t^4 + C$.

$y(t) = \frac{C(t)}{t^2} = \frac{t^4 + C}{t^2} = \frac{C}{t^2} + t^2$; $2 = y(1) = \frac{C}{1^2} + 1^2 = C + 1$ ⇒ $C = 1$ ⇒ $y(t) = \frac{1}{t^2} + t^2$, $t > 0$.
