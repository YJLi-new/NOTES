# STA2003 Probability — Review 3 (Ch10–11: Sampling Distributions, Limit Theorems)

> Transcribed from the handwritten notes [rv3.pdf](rv3.pdf). `(?)` marks unclear handwriting.

## Ch10

Vocabulary: population; sample; random sample; parameter; statistic; statistical inference; sampling distribution.

$\{X_1, \dots, X_n\}$ is a random sample from $f(x)$: $E(X_i) = \mu$, $\operatorname{Var}(X_i) = \sigma^2$;
$\bar X = \frac1n\sum_{i=1}^n X_i$, $E(\bar X) = \mu$, $\operatorname{Var}(\bar X) = \frac{\sigma^2}{n}$.

- Standard error of the sample mean: $SE(\bar X) = \frac{\sigma}{\sqrt n} = \frac{S}{\sqrt n}$.
- Sample variance: $S^2 = \frac{1}{n - 1}\sum_{i=1}^n(X_i - \bar X)^2$, $E(S^2) = \sigma^2$.
- $M_{\bar X}(t) = \left[M_X\left(\frac tn\right)\right]^n$.

For $X_i \overset{\text{i.i.d.}}{\sim} N(\mu, \sigma^2)$:
- $\bar X \sim N\left(\mu, \frac{\sigma^2}{n}\right)$ (使用 mgf 得到 dist. 中参数!)
- $\bar X$ and $S^2$ are independent (E.g. 10.8)
- $\frac{(n - 1)S^2}{\sigma^2} \sim \chi^2(n - 1)$ (E.g. 10.9)

**Statistic** $T_n$: any func. of a sample (a set of r.v.'s from the population); $T_n$ is also a r.v.
**Sampling distribution** — to find $\Pr(T_n \le t)$ (★★★): ① 找 mgf; ② large sample approximation.
Large sample theory: seq. of r.v.s $Y_1, \dots, Y_n$; usually the $Y_i$'s are statistics calculated from a sample with size $n$. How to define the limit of the seq.? The limit of the seq. of statistics is used to approximate the sampling dist. of the statistic.

Simple random sample (?): $E(\bar X) = \mu$, $\operatorname{Var}(\bar X) = \left(\frac{N - n}{N - 1}\right)\frac{\sigma^2}{n}$. (未完) (To E.g. 11.2, Ch11)

## Modes of convergence (strongest at the bottom)

- $X_n$ **converges in distribution** to $X$ ⇔ $\lim_{n\to\infty}F_{X_n}(x) = F_X(x)$, $\forall x$ (at continuity points) ⇔ $X_n \xrightarrow{d} X$.
- $X_n$ **converges in probability** to $X$ ⇔ $\forall\varepsilon$, $\lim_{n\to\infty}\Pr(|X_n - X| \ge \varepsilon) = 0$ ⇔ $X_n \xrightarrow{p} X$ (化为 $F_{X_n}(\cdot)$ ?).
- $X_n$ **converges almost surely** to $X$ ⇔ $\Pr(\lim_{n\to\infty}X_n = X) = 1$ ⇔ $X_n \xrightarrow{a.s.} X$ ⇔ $\lim_{n\to\infty}X_n(\omega) = X(\omega)$, $\forall\omega \in \Omega$ with $\omega \notin S$, where $S \subset \Omega$ and $\Pr(S) = 0$.

a.s. ⇒ in prob. ⇒ in dist.

## LLN (Law of Large Numbers)

- **WLLN** (weak): $\bar X_n \xrightarrow{p} \mu = E(X_i)$, i.e. $\lim_{n\to\infty}\Pr(|\bar X_n - \mu| \ge \varepsilon) = 0$.
  (By Chebyshev's inequality $\Pr(|X - \mu| \ge k\sigma) \le \frac{1}{k^2}$: $\Pr(|\bar X_n - \mu| \ge \varepsilon) \le \frac{\sigma^2}{n\varepsilon^2} \to 0$.)
- **SLLN** (strong): $\bar X_n \xrightarrow{a.s.} \mu = E(X_i)$, i.e. $\Pr(\lim_{n\to\infty}\bar X_n = \mu) = 1$. (More general version: holds ⇔ $E(|X_i|) < \infty$ and $E(X_i) = \mu$.)

## CLT (Central Limit Theorem)

$\mu = E(X_i)$, $\sigma^2 = \operatorname{Var}(X_i)$:

$$\frac{\bar X_n - \mu}{\sigma/\sqrt n} = \frac{\sqrt n(\bar X_n - \mu)}{\sigma} \xrightarrow{d} N(0, 1)\quad\text{as } n \to \infty$$

i.e. $\lim_{n\to\infty}\Pr\left(\frac{\sqrt n(\bar X_n - \mu)}{\sigma} \le x\right) = \Phi(x)$, $\forall -\infty < x < \infty$.

**E.g.** Indep. Bernoulli trials $X_1, \dots, X_n \overset{\text{i.i.d.}}{\sim} \text{Ber}(\frac12)$: $E(X_i) = \frac12$, $\operatorname{Var}(X_i) = \frac14$. Find $n$ s.t. $\Pr(\bar X_n > 0.6) \le 0.025$.
CLT: $\frac{\bar X_n - \frac12}{\sqrt{1/4}/\sqrt n} \xrightarrow{d} N(0, 1)$.

$$\Pr(\bar X_n > 0.6) = \Pr\left(\frac{\bar X_n - \frac12}{\frac{1/2}{\sqrt n}} > \frac{0.6 - \frac12}{\frac{1/2}{\sqrt n}}\right) = \Pr(Z > 0.2\sqrt n) = 1 - \Phi(0.2\sqrt n) \le 0.025$$

$0.2\sqrt n \ge \Phi^{-1}(0.975) = 1.96$ ⇒ $n \ge \left(\frac{1.96}{0.2}\right)^2 = 96.04$; $n \in \mathbb{Z}$ ⇒ $n = 97$.
