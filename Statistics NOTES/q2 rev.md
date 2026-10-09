# STA2003 Probability — Quiz 2 Review Notes

> Transcribed from the handwritten notes [q2 rev.pdf](q2%20rev.pdf). `(?)` marks unclear handwriting.

## Ch6

- **Survival function** of $X$: $S(t) = \Pr(X > t) = 1 - F(t)$, where $F(t) = \Pr(X \le t)$.
- **Hazard func. / mortality func. / failure func.**: $\lambda(t) = \frac{f(t)}{S(t)}$.

**Remark.** $\Pr(t < X < t + dt \mid X > t) = \frac{F(t + dt) - F(t)}{S(t)} \approx \frac{f(t)\,dt}{S(t)} = \lambda(t)\,dt$.
$\lambda(t)$ uniquely determines $F(t)$: $f(t) = F'(t) = -S'(t)$,

$$\lambda(t) = \frac{f(t)}{S(t)} = -\frac{S'(t)}{S(t)} = -\frac{d}{dt}[\ln S(t)] = -\frac{d}{dt}[\ln(1 - F(t))]\ \Rightarrow\ \int_0^x\lambda(t)\,dt = -\ln[1 - F(x)]$$

- **E.g.** $X \sim \text{Exp}(\lambda)$ ⇒ $f(t) = \lambda e^{-\lambda t}$ ⇒ $F(t) = 1 - e^{-\lambda t}$ ⇒ $S(t) = e^{-\lambda t}$ ⇒ $\lambda(t) = \frac{\lambda e^{-\lambda t}}{e^{-\lambda t}} = \lambda$.
- **E.g. 2** Linear hazard rate func. $\lambda(t) = a + bt$: $-\ln[1 - F(x)] = ax + \frac b2x^2$ ⇒ $1 - F(x) = e^{-(ax + \frac b2x^2)}$ ⇒ $F = \cdots$, $f(t) = \lambda(t)[1 - F(t)]$.

**Indicator function** of set $A$ ($A \subset \Omega$): $I_A: \Omega \to \{0, 1\}$, $I_A(x) = 1$ if $x \in A$, $0$ if $x \notin A$.
Properties: $I_{A\cap B} = \min(I_A, I_B) = I_A\times I_B$; $I_{A\cup B} = \max(I_A, I_B) = I_A + I_B - I_A\times I_B$; $I_{A^c} = 1 - I_A$; $E(I_A) = \Pr(A)$ ($= \int_{-\infty}^\infty I_Af(x)\,dx = \int_A f(x)\,dx$).
Remark: $I_A \sim \text{Ber}(\Pr(A))$; $f(x) = \{\cdots \text{ for } x \in A;\ 0 \text{ otherwise}\} \iff f(x) = \cdots\cdot I_{\{x\in A\}}$;
a r.v. $X(\omega) = c_1$ if $\omega \in A$, $c_2$ if $\omega \in B$, $c_3$ if $\omega \in C$ ⇔ $X(\omega) = c_1I_A + c_2I_B + c_3I_C$ (multiplicatively: $c_1^{I_A}\times c_2^{I_B}\times c_3^{I_C}$).

**Markov's inequality.** ($\forall\omega$, $X(\omega) \ge 0$ and $\mu = E(X) < \infty$) $\forall c > 0$: $\Pr(X \ge c) \le \frac{E(X)}{c}$.

**Chebyshev's inequality.** ($\mu < \infty$, $\sigma^2 < \infty$) $\forall k > 0$: $\Pr(|X - \mu| \ge k\sigma) \le \frac{1}{k^2}$.
Proof: ($E\left[\left(\frac{X - \mu}{\sigma}\right)^2\right] = \frac{1}{\sigma^2}E[(X - \mu)^2] = 1$, with $E[(X - \mu)^2] = \sigma^2 = E(X^2) - [E(X)]^2$.)

**Dist. of mixed type.**

**Quantiles** (分位数): $X$ is cont. & one-to-one & the inverse dist. func. $F^{-1}$ exists ⇒ the $p$-quantile of $X$ for $0 < p < 1$ (the $100p$-th percentile of $X$): $F^{-1}(p)$. ($X$ discrete: …)

**Transformation.** $Y = g(X)$, $X$ distributed on space $S$: $f_Y(y) = f_X(g^{-1}(y))\cdot\left|\frac{d}{dy}g^{-1}(y)\right|$, $\forall y \in g(S)$.

## Ch7

- **Joint pmf** of discrete $X, Y$: $p(x, y) = \Pr(X = x, Y = y)$ (and), $x \in X(\Omega)$, $y \in Y(\Omega)$. Conditions: $0 \le p(x, y) \le 1$; $\sum_x\sum_y p(x, y) = 1$; for $A \subset X(\Omega)\times Y(\Omega)$, $\Pr((X, Y) \in A) = \sum_{(x,y)\in A}p(x, y)$.
- **Marginal pmfs**: $p_X(x) = \Pr(X = x) = \sum_{y\in Y(\Omega)}p(x, y)$; $p_Y(y) = \cdots$. Remark: joint pmf → (uniquely determines) marginal pmf, but not ←.
- $X$ and $Y$ are **jointly continuous** ⇔ ∃ function $f(x, y)$ (joint pdf) s.t. $\forall$ measurable set $C \subseteq \mathbb{R}^2$, $\Pr\{(X, Y) \in C\} = \iint_{(x,y)\in C}f(x, y)\,dx\,dy$.
- Joint dist. func.: $F(x, y) = \Pr(X \le x, Y \le y) = \int_{-\infty}^y\int_{-\infty}^x f(x, y)\,dx\,dy$.

**(7.2) Independence.** (2 events) $A$ and $B$ are independent ⇔ $\Pr(A\cap B) = \Pr(A)\Pr(B)$ ⇔ $\Pr(A \mid B) = \Pr(A)$.
(2 r.v.) $X$ and $Y$ are independent ⇔ (discrete) $p(x, y) = p_X(x)p_Y(y)$ $\forall x, y$; (continuous) $f(x, y) = f_X(x)f_Y(y)$
⇔ supports of $X$ and $Y$ do not depend on each other and ∃ $g(x), h(y)$ s.t. $f(x, y) = g(x)h(y)$.

Remark — generalize joint & marginal: joint pmf $p(x_1, \dots, x_n) = \Pr(X_1 = x_1, \dots, X_n = x_n)$; joint pdf $f(x_1, \dots, x_n)$; marginal pmf $p_{X_1}(x_1) = \sum_{x_2}\cdots\sum_{x_n}p(x_1, \dots, x_n)$; marginal pdf $f_{X_1}(x_1) = \int_{-\infty}^\infty\cdots\int_{-\infty}^\infty f(x_1, \dots, x_n)\,dx_2\cdots dx_n$.

$X_1, \dots, X_n$ are mutually indep. ⇔ discrete: $p(x_1, \dots, x_n) = p_{X_1}(x_1)\cdots p_{X_n}(x_n)$ $\forall x_1, \dots, x_n$; continuous: $f(x_1, \dots, x_n) = f_{X_1}(x_1)\cdots f_{X_n}(x_n)$
⇒ $\forall A_1, \dots, A_n \subset (-\infty, \infty)$, $\Pr(X_1 \in A_1, \dots, X_n \in A_n) = \Pr(X_1 \in A_1)\cdots\Pr(X_n \in A_n)$ ⇒ $F(x_1, \dots, x_n) = F_{X_1}(x_1)\cdots F_{X_n}(x_n)$.

**Expectation.** Discrete: $E[u(X_1, \dots, X_n)] = \sum_{x_1}\cdots\sum_{x_n}u(x_1, \dots, x_n)p(x_1, \dots, x_n)$; continuous: $\int\cdots\int u(x_1, \dots, x_n)f(x_1, \dots, x_n)\,dx_1\cdots dx_n$.
Properties: $E[u_1(X) + u_2(Y)] = E[u_1(X)] + E[u_2(Y)]$; $X$ & $Y$ indep. ⇒ $E[u_1(X)u_2(Y)] = E[u_1(X)]\,E[u_2(Y)]$ ⇒ $M_{X+Y}(t) = M_X(t)M_Y(t)$; $M_{aX+bY+c}(t) = M_X(at)M_Y(bt)e^{ct}$.

**(7.4) Population covariance.**
$\sigma_{XY} = \operatorname{Cov}(X, Y) = E[(X - \mu_X)(Y - \mu_Y)] = E(XY) - \mu_X\mu_Y$, with $E(XY) = \sum_x\sum_y xy\,p(x, y)$ or $\iint xy\,f(x, y)\,dx\,dy$.
Properties: $\sigma_{XY} > 0$ ⇒ $X, Y$ 同↑↓; $\sigma_{XY} < 0$ ⇒ $X, Y$ 异↑↓; $\operatorname{Cov}(aX + b, cY + d) = ac\operatorname{Cov}(X, Y)$; $\forall$ constant $c$, $\operatorname{Cov}(X, c) = 0$; $\operatorname{Cov}(X, X) = \operatorname{Var}(X)$;
$\operatorname{Cov}\left(\sum_i a_iX_i, \sum_j b_jY_j\right) = \sum_i\sum_j a_ib_j\operatorname{Cov}(X_i, Y_j)$;
$\operatorname{Var}(X_1 + \cdots + X_n) = \operatorname{Var}(X_1) + \cdots + \operatorname{Var}(X_n) + 2\sum_{i<j}\operatorname{Cov}(X_i, X_j)$.

**Population correlation coefficient**: $\rho = \rho_{X,Y} = \operatorname{Corr}(X, Y) = \frac{\sigma_{XY}}{\sigma_X\sigma_Y} = \frac{\operatorname{Cov}(X, Y)}{\sqrt{\operatorname{Var}(X)\operatorname{Var}(Y)}}$.

**Cauchy–Schwarz inequality.** $X, Y$ r.v. with finite 2nd moments: $[E(XY)]^2 \le E(X^2)E(Y^2)$, with equality ⇔ $\Pr(X = 0) = 1$ or ∃$a$, $\Pr(Y = aX) = 1$.
Proof: $\forall t \in \mathbb{R}$, consider $0 \le E[(tX + Y)^2] = t^2E(X^2) + 2tE(XY) + E(Y^2)$.

Properties: $-1 \le \rho \le 1$; $\operatorname{Corr}(aX + b, cY + d) = \operatorname{sgn}(ac)\operatorname{Corr}(X, Y)$; $X$ & $Y$ indep. ⇒ $\operatorname{Var}(X \pm Y) = \operatorname{Var}(X) + \operatorname{Var}(Y)$; indep. ⇒ $\operatorname{Cov}(X, Y) = 0$ ⇒ $\rho = 0$ (for $X, Y$ not constants).
Remarks: $\rho = 0$ ⇏ $X, Y$ indep. (称之为 $X$ and $Y$ are uncorrelated). The correlation coeff. measures the strength of the **linear** relationship only.

## Ch8

**Conditional pmf** (discrete) of $Y$ given $X = x$ ($\forall x$, $p_X(x) > 0$):
$p_{Y|X}(y \mid x) = \Pr(Y = y \mid X = x) = \frac{\Pr(Y = y, X = x)}{\Pr(X = x)} = \frac{p_{X,Y}(x, y)}{p_X(x)}$ (joint / marginal).
$Y$ is indep. of $X$ ⇔ $p_{Y|X}(y \mid x) = p(y)$.
(continuous) $f_{Y|X}(y \mid x) = \frac{f(x, y)}{f_X(x)}$.

Properties: $F_{Y|X}(y \mid x) = \Pr(Y \le y \mid X = x) = \sum_{i\le y}p_{Y|X}(i \mid x)$ (discrete) $= \int_{-\infty}^y f_{Y|X}(t \mid x)\,dt$ (continuous);
$E[g(Y) \mid X = x] = \sum_i g(i)p_{Y|X}(i \mid x)$ or $\int_{-\infty}^\infty g(t)f_{Y|X}(t \mid x)\,dt$;
$\operatorname{Var}[Y \mid X = x] = E[(Y - E(Y \mid X = x))^2 \mid X = x] = E(Y^2 \mid X = x) - [E(Y \mid X = x)]^2$.

**Law of total expectation** (Adam's law): $E[u(X)] = E[E(u(X) \mid Y)]$.
**Law of total variance**: $\operatorname{Var}(X) = E[\operatorname{Var}(X \mid Y)] + \operatorname{Var}[E(X \mid Y)]$.

## Moment & MGF

**Ch4 (discrete).** The $r$-th moment of $X$ (about $b$): $E[(X - b)^r]$; the $r$-th central moment: $E[(X - \mu)^r]$; mgf of $X$: $M_X(t) = \sum_{x\in X(\Omega)}e^{tx}p(x) = E(e^{tX})$.
Properties: $M_X^{(r)}(0) = \frac{d^r}{dt^r}M_X(t)\big|_{t=0} = E(X^r)$; $M_{aX+b}(t) = E(e^{(aX+b)t}) = e^{bt}M_X(at)$; $M_X(t) = M_Y(t)$ ⇒ $X$ & $Y$ have the same distribution.
**cgf** of $X$: $R_X(t) = \ln(M_X(t))$. Properties: $R_X'(0) = E(X) = \mu$; $R_X''(0) = \operatorname{Var}(X) = \sigma^2$; $\frac{R_X^{(3)}(0)}{\sigma^3} = \operatorname{Skew}(X)$; $R_X^{(4)}(0) \sim \operatorname{Kurt}(X)$; $R^{(3)}(0) = E[(X - \mu)^3]$.
**pgf** of $X$: $G_X(z) = E(z^X)$ ($= E(e^{\ln(z)X}) = M_X(\ln z)$).

**Ch5 (continuous).** Expectation $E[g(X)] = \int_{-\infty}^\infty g(x)f(x)\,dx$; mgf $M_X(t) = E(e^{tX}) = \int_{-\infty}^\infty e^{tx}f(x)\,dx$, $M_X^{(r)}(0) = E(X^r)$; cgf $R_X(t) = \ln(M_X(t))$, $R_X'(0) = \mu$, $R_X''(0) = \sigma^2$.

## Tut

T10: Q3 (a) valid pdf ⇐ $f_{X,Y}(x, y) \in [0, 1]$ (as written) and $\iint_{\mathbb{R}^2}f_{X,Y}(x, y)\,dx\,dy = 1$; (b) $X, Y$ indep. ⇔ $f_{X,Y}(x, y) = f_X(x)\cdot f_Y(y)$ (joint = product of marginals).
Q1: $\Pr(c < X \le x) = \Pr(X \le x) - \Pr(X \le c)$.
