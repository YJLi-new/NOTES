# MATH441 Notes 3 — Separable, Homogeneous, Bernoulli, Riccati, Exact Equations

> Transcribed from the handwritten notes [Notes-3.pdf](Notes-3.pdf). `(?)` marks unclear handwriting.

## 1 Sec. 2.2 Separable equations

$\frac{dy}{dx} = f(x, y)$ or $\frac{dy}{dx} - f(x, y) = 0$; write $M(x, y) + N(x, y)\frac{dy}{dx} = 0$.
The differential eq. is **separable** $\iff M(x, y) = M(x)$ and $N(x, y) = N(y)$: $M(x) + N(y)\frac{dy}{dx} = 0$.

Find $H_1(x)$ s.t. $H_1'(x) = M(x)$, find $H_2(y)$ s.t. $H_2'(y) = N(y)$:
$M(x) + N(y)\frac{dy}{dx} = 0 \Rightarrow H_1'(x) + H_2'(y)\frac{dy}{dx} = 0 \iff \frac{dH_1}{dx} + \underbrace{\frac{dH_2}{dy}\frac{dy}{dx}}_{=\frac{d}{dx}H_2(y(x))} = 0 \iff \frac{d}{dx}\big(H_1(x) + H_2(y(x))\big) = 0$.

Thus $H_1(x) + H_2(y(x)) = C$. This eq. defines the solution $y = y(x)$ **implicitly**.
(Equivalently: $M(x)\,dx + N(y)\,dy = 0$, $\int H_1'(x)\,dx = -\int H_2'(y)\,dy$, $H_1(x) = -H_2(y) + C$.)

**E.g.** $\frac{dy}{dx} = -\frac xy$ $\iff x + y\frac{dy}{dx} = 0$, or $\frac{d}{dx}\left(\frac{x^2}{2} + \frac{y^2(x)}{2}\right) = 0$ ⇒ $\frac{x^2}{2} + \frac{y^2}{2} = C$, or $x^2 + y^2 = 2C$. (Or $\int y\,dy = -\int x\,dx$ ⇒ $\frac{y^2}{2} = -\frac{x^2}{2} + C$.)

**IVP.** $M(x) + N(y)\frac{dy}{dx} = 0$, $y(x_0) = y_0$: $H_1(x) + H_2(y(x)) = C$ with $H_1(x_0) + H_2(y_0) = C$, i.e. $H_1(x) - H_1(x_0) + H_2(y) - H_2(y_0) = 0$, i.e.

$$\int_{x_0}^x M(s)\,ds + \int_{y_0}^y N(s)\,ds = 0$$

is the solution to the IVP.

**E.g.** Solve $\frac{dy}{dx} = \frac{4x - x^3}{4 + y^3}$: $\int(4 + y^3)\,dy = \int(4x - x^3)\,dx$ ⇒ $4y + \frac{y^4}{4} = 2x^2 - \frac{x^4}{4} + C$. 找过 $(0, 1)$ 点的 sol.: $4 + \frac14 = C$ ⇒ $4y + \frac{y^4}{4} = 2x^2 - \frac{x^4}{4} + \frac{17}{4}$ (画图). Where $4 + y^3 = 0$ ⇒ $y = (-4)^{1/3} \approx -1.6$, for $x_0 = \pm 3.35$. The sol. is given implicitly by $4y + \frac{y^4}{4} = 2x^2 - \frac{x^4}{4} + \frac{17}{4}$ on $(-3.35, 3.35)$ — interval of existence.

**E.g.** Solve $\frac{dy}{dx} = \frac{3x^2 + 4x + 2}{2(y - 1)}$ with $y(0) = -1$, and determine the interval in which the sol. exists.
$\int 2(y - 1)\,dy = \int(3x^2 + 4x + 2)\,dx$ ⇒ $y^2 - 2y = x^3 + 2x^2 + 2x + C$; $(-1)^2 - 2(-1) = 0 + C$ ⇒ $C = 3$.
$y = \frac{2 \pm \sqrt{4 + 4(x^3 + 2x^2 + 2x + 3)}}{2} = 1 \pm\sqrt{x^3 + 2x^2 + 2x + 4}$; $y(0) = 1 \pm\sqrt4 = 1 \pm 2$ ⇒ choose $-1$ ⇒ $y(x) = 1 - \sqrt{x^3 + 2x^2 + 2x + 4}$.
Need $x^3 + 2x^2 + 2x + 4 > 0$ (and $y \ne 1$) ⇒ $x(x^2 + 2) + 2(x^2 + 2) > 0$ ⇒ $(x^2 + 2)(x + 2) > 0$ ⇒ $x + 2 > 0$ ⇒ $x > -2$ (and $0 \in (-2, \infty)$).

### Homogeneous equations

$\frac{dy}{dx} = f\left(\frac yx\right)$, e.g. $\frac{dy}{dx} = \frac{y - 2x}{y + 5x}$.

## 2

Solve $\frac{dy(x)}{dx} = f\left(\frac{y(x)}{x}\right)$. (Last time: $M(x) + N(y)\frac{dy}{dx} = 0$.)
Set $u = \frac yx$, $y = ux = u(x)\cdot x$; $f\left(\frac yx\right) = f(u)$; $\frac{dy}{dx} = \frac{du}{dx}x + u$ ⇒ $\frac{du}{dx}x + u = f(u)$, or $x\frac{du}{dx} = f(u) - u$
⇒ $\int\frac{dx}{x} = \int\frac{du}{f(u) - u}$ ⇒ $\ln|x| = \int\frac{du}{f(u) - u}$. Recover $u(x)$ and then $y(x) = xu(x)$.

**E.g.** Solve $\frac{dy}{dx} = \frac{x + 3y}{x - y} = \frac{1 + 3\frac yx}{1 - \frac yx}$. Let $u = \frac yx$, $y = ux$: $\frac{dy}{dx} = u'x + u = \frac{1 + 3u}{1 - u}$
⇒ $u'x = \frac{1 + 3u}{1 - u} - u = \frac{u^2 + 2u + 1}{1 - u} = \frac{(u + 1)^2}{1 - u}$ (test $u = -1$: $y = -x$ is a sol. to the diff. eq.)

$$\int\frac{dx}{x} = \int\frac{1 - u}{u^2 + 2u + 1}du = \int\frac{du}{(u+1)^2} - \int\frac{u + 1 - 1}{u^2 + 2u + 1}du = -\frac{1}{1 + u} + \int\frac{du}{(1 + u)^2} - \frac12\int\frac{2u + 2}{u^2 + 2u + 1}du$$

(let $z = u^2 + 2u + 1$, $dz = (2u + 2)du$) $= -\frac{2}{1 + u} - \frac12\ln|z| = -\frac{2}{1 + u} - \frac12\ln(u + 1)^2 = -\frac{2}{1 + u} - \ln|u + 1|$.

So $\ln|x| = -\frac{2}{1 + \frac yx} - \ln\left|\frac yx + 1\right| + C$ ⇒ $\ln|x| = -\frac{2x}{y + x} - \ln|y + x| + \ln|x| + C$ ⇒ $\frac{2x}{y + x} + \ln|y + x| = C$; plus $y = -x$ → Sol.

### Bernoulli

$y' + p(t)y = q(t)y^n$, $n \ne 0, 1$ ($n = 2, 3, 4, \dots$), $p(t), q(t)$ cont.

- $n = 0$: $y' + p(t)y = q(t)$ (→ Sec. 2.1).
- $n = 1$: $y' + (p(t) - q(t))y = 0$ ⇒ $y(t) = Ce^{-\int[p(t) - q(t)]dt}$.
- $n \ne 0, 1$: $y^{-n}\frac{dy}{dt} + py^{1-n} = q$. Set $u = y^{1-n}$ ⇒ $\frac{du}{dt} = (1 - n)y^{-n}\frac{dy}{dt}$ ⇒ $\frac{1}{1 - n}\frac{du}{dt} + p(t)u = q(t)$ (→ Sec. 2.1) → solve for $u$ and then solve for $y$ from $u = y^{1-n}$.

### Riccati's equation

$\frac{dy}{dt} = q_1(t) + q_2(t)y + q_3(t)y^2$. If $y_1(t)$ is a sol., then the general sol. can be found by the substitution $y(t) = y_1(t) + \frac{1}{u(t)}$ → a linear diff. eq. in $u$.

## Sec. 2.6 Exact equations

$M(x, y) + N(x, y)\frac{dy}{dx} = 0$. Assume that $\exists\psi(x, y)$ s.t. $\psi_x(x, y) = M(x, y)$ and $\psi_y(x, y) = N(x, y)$. Then $\psi_x + \psi_y\frac{dy}{dx} = 0$, and $\frac{d}{dx}[\psi(x, y(x))] = \psi_x\frac{dx}{dx} + \psi_y\frac{dy}{dx} = \psi_x + \psi_y\frac{dy}{dx}$
⇒ $\frac{d}{dx}\psi(x, y(x)) = 0$ ⇒ $\psi(x, y(x)) = C$ implicitly defines $y = y(x)$.

Condition: $\psi_x = M \Rightarrow (\psi_x)_y = M_y$; $\psi_y = N \Rightarrow (\psi_y)_x = N_x$; but $\psi_{xy} = \psi_{yx}$ for $\psi$ smooth.
New condition: $M_y = N_x$ is sufficient for solvability. $M_y = N_x$ ⇒ $M(x, y) + N(x, y)\frac{dy}{dx} = 0$ is called **exact**.

We can solve all exact diff. eq.'s: ① $\psi_x = M(x, y)$ ⇒ $\psi = \int M(x, y)\,dx + f(y)$; ② $\psi_y = N(x, y)$ → (见 …)

## 3 Sec. 2.6 (cont.)

$M(x, y) + N(x, y)\frac{dy}{dx} = 0$ (eq. (1)); Sec. 2.2: $M(x) + N(y)\frac{dy}{dx} = 0$ separable.
(1) is solvable $\iff$ (1) is exact $\iff M_y = N_x$.
If $\exists\psi$ s.t. $\psi_x = M$ (eq. (2)), $\psi_y = N$ (eq. (3)) ⇒ then $(\psi_x)_y = M_y = N_x = (\psi_y)_x$.
($M + N\frac{dy}{dx} = 0 \iff \psi_x + \psi_y\frac{dy}{dx} = 0 \iff \frac{d}{dx}\psi(x, y(x)) = 0$ → then $\psi(x, y) = C$ is a sol.)

If $M_y = N_x$ ⇒ then eq. (2) → $\psi(x, y) = \int M(x, y)\,dx + f(y)$; $\psi_y = \int M_y(x, y)\,dx + f'(y) \overset{(3)}{=} N(x, y)$
⇒ $\int N_x(x, y)\,dx + f'(y) = N \iff N(x, y) - g(y) + f'(y) = N(x, y)$ ⇒ $f'(y) = g(y)$ ⇒ $f(y) = \int g(y)\,dy + C$.
Thus $\psi(x, y) = \int M(x, y)\,dx + \int g(y)\,dy + C$.

**E.g.** Solve $\underbrace{y\cos x + 2xe^y}_{M} + \underbrace{(\sin x + x^2e^y - 1)}_{N}\frac{dy}{dx} = 0$.
$M_y = \cos x + 2xe^y$, $N_x = \cos x + 2xe^y$ ⇒ $M_y = N_x$.
$\psi_x = M = y\cos x + 2xe^y$ ⇒ $\psi(x, y) = y\sin x + x^2e^y + f(y)$; $\psi_y = N = \sin x + x^2e^y - 1$ and $\psi_y = \sin x + x^2e^y + f'(y)$ ⇒ $f'(y) = -1$ ⇒ $f(y) = -y + C$.
⇒ The sol. is $\psi(x, y) = y\sin x + x^2e^y - y = C$.
(Implicit function: $F(x, y) = 0$, $y = \varphi(x)$ near $(x_0, y_0)$ if $F_y(x_0, y_0) \ne 0$.)

### Integrating factors

$M(x, y) + N(x, y)\frac{dy}{dx} = 0 \iff \mu(x, y)M(x, y) + \mu(x, y)N(x, y)\frac{dy}{dx} = 0$.
$N_x \ne M_y$, but (可能) $(\mu M)_y = (\mu N)_x$: $\mu_yM + \mu M_y = \mu_xN + \mu N_x$.

- If $\mu = \mu(x)$ → $\mu_y = 0$, $\mu_x = \mu'$: $\mu M_y = \mu'N + \mu N_x$ ⇒ $\frac{\mu(M_y - N_x)}{N} = \mu'$. If $\frac{M_y - N_x}{N}$ is a function of $x$ (记 $f(x)$), then $\mu f = \frac{d\mu}{dx}$ ⇒ $\int\frac{d\mu}{\mu} = \int f(x)\,dx$ ⇒ $\mu(x) = e^{\int f(x)dx}$.
- If $\mu = \mu(y)$ → $\mu_y = \mu'$, $\mu_x = 0$: $\mu'M + \mu M_y = \mu N_x$ ⇒ $\mu' = \frac{N_x - M_y}{M}\mu(y)$. If $\frac{N_x - M_y}{M}$ is a func. of $y$ (记 $g(y)$), then $\mu(y) = e^{\int g(y)dy}$.

**E.g.** Solve $\underbrace{(3xy + y^2)}_{M} + \underbrace{(x^2 + xy)}_{N}\frac{dy}{dx} = 0$.
$M_y = 3x + 2y$, $N_x = 2x + y$; $\frac{M_y - N_x}{N} = \frac{3x + 2y - (2x + y)}{x^2 + xy} = \frac{x + y}{x(x + y)} = \frac1x$
⇒ $\mu' = \frac1x\mu$ ⇒ $\int\frac{d\mu}{\mu} = \int\frac{dx}{x}$ ⇒ $\ln|\mu| = \ln|x|$ ⇒ $\mu = x$. (见 …)
