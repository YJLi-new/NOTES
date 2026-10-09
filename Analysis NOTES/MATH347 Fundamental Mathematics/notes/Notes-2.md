# MATH347 Notes 2 — Quantifiers, Negation, Proof Techniques

> Transcribed from the handwritten notes [Notes-2.pdf](Notes-2.pdf). `(?)` marks unclear handwriting.

## 1 Open sentences / quantifiers

(func.) $\forall x \in D$, $P(x)$ → a statement.

**E.g.**

1. $\forall x \in \mathbb{R}$, $P(x) \iff \sin x = 0$. If $x = 0$: $\sin 0 = 0$ ⇒ $P(0)$ is true. If $x = \frac\pi4$: $\sin\frac\pi4 = \frac{\sqrt2}{2} \ne 0$ ⇒ $P(\frac\pi4)$ is false.
2. $\forall x \in D$, $P(x)$ (is true) → (neg.) $\exists x \in D$ s.t. $\neg P(x)$ (i.e. $P(x)$ is false).
3. 证 $\exists x \in D$ s.t. $Q(x)$ → show: 取一 $x_0 \in D$ with $Q(x_0)$ (true) — an example.
4. $\forall x \in D$, $P(x)$ → (counterex.) $\exists x_0 \in D$, $\neg P(x_0)$.
5. $\forall n \in \mathbb{Z}$, $n$ is odd ⇒ $n^2$ is odd → (neg.) $\exists n \in \mathbb{Z}$ s.t. $n$ is odd and $n^2$ is even.

### Proper order

$\forall\alpha\cdots, \exists\beta$ s.t. $\forall\gamma, \exists\delta$ s.t. … ($\beta = \beta(\alpha)$, $\delta = \delta(\alpha, \beta, \gamma)$).

**E.g.** Let set $S \subset \mathbb{R}$.

1. $\exists M \in \mathbb{R}$ s.t. $\forall x \in S$, $x \le M$ ($M$ is an upper bd. of $S$).
2. $\forall x \in S$, $\exists M(x) \in \mathbb{R}$ s.t. $x \le M(x)$: let $x \in S$, take $M(x) = x$; $x \le x$ ✓ (always true).
3. (1) $\iff$ (3): $\forall x \in S$, $\exists M \in \mathbb{R}$ s.t. $x \le M$ **and $M$ does not depend on $x$**.

**E.g.** There is a prime $p$ between every positive int. $n$ and $2n$.
- proper order: $\forall n \in \mathbb{N}^*$, $\exists$ prime $p$ s.t. $n \le p \le 2n$ (and).
- neg.: $\exists n \in \mathbb{N}^*$ s.t. $\forall$ prime $p$, $p < n$ or $p > 2n$.

**E.g.** $\forall x \in \mathbb{R}$ for which $\exists y \in \mathbb{R}$ s.t. $x = y^2$, $x \ge 0$
$\iff \forall x \in \mathbb{R}$, ($\exists y \in \mathbb{R}$ s.t. $x = y^2$) ⇒ $x \ge 0$.
- neg.: $\exists x \in \mathbb{R}$ s.t. $\exists y \in \mathbb{R}$ s.t. $x = y^2$ and $x < 0$.
- neg.: $\exists x \in \mathbb{R}$ for which $\exists y \in \mathbb{R}$ s.t. $x = y^2$, $x < 0$.

**Continuity.** $f(x)$ (on $D$) is cont. at $a \in D$ if $\forall\varepsilon > 0$, $\exists\delta > 0$ s.t. ($\forall x \in D$) if $|x - a| < \delta$, then $|f(x) - f(a)| < \varepsilon$.
neg.: $\exists\varepsilon > 0$ s.t. $\forall\delta > 0$, $\exists x \in \mathbb{R}$ s.t. $|x - a| < \delta$ and $|f(x) - f(a)| \ge \varepsilon$.

$f(x) \to 10$ as $x \to \infty$: $\forall\varepsilon > 0$, $\exists M \in \mathbb{R}$ s.t. $\forall x \in \mathbb{R}$, if $x \ge M$ then $|f(x) - 10| < \varepsilon$.

**Method of exhaustion:** $S$ limit. $S$: $x_1, \dots, x_n$ … (见图)

## 3 Examples of proofs

**E.g.** $x^5 + 8x^3 + 1 = 0$ has no real root … (as written: $\iff \forall x \in \mathbb{R}$, $x^5 + 8x^3 + 1 = 0 \Rightarrow x < 0$)
$\iff$ (contrapos.) $\forall x \in \mathbb{R}$, $x \ge 0 \Rightarrow x^5 + 8x^3 + 1 \ne 0$.
Let $x \in \mathbb{R}$, assume $x \ge 0$. Then $x^5 \ge 0$ and $8x^3 \ge 0$, so $x^5 + 8x^3 + 1 > 0$ ⇒ $\ne 0$.
By contradiction: assume the contrary. Then $\exists x \in \mathbb{R}$ s.t. $x^5 + 8x^3 + 1 = 0$ and $x \ge 0$; $x \ge 0$ ⇒ $x^5 + \cdots > 0$ ⇒ $\ne 0$, a contradiction.

$t^2$ is ↑ (strictly increasing) for $t \ge 0$.

**Lemma.** Let $x, y \in \mathbb{R}$ s.t. $x, y \ge 0$. Then $x \le y \iff x^2 \le y^2$.
*Proof.* Assume $x, y \ge 0$.
(证 ⇒) Assume $x \le y$ (条件): ① $x^2 \le xy$; ② $xy \le y^2$; by transitivity, $x^2 \le y^2$.
(证 ⇐) Assume $(0 \le)\ y < x$; in particular $x > 0$. ① by $x > 0$, $xy < x^2$; ② by $y \ge 0$, $y^2 \le xy$. Thus $y^2 < x^2$, contradiction (反证).

**AM–GM inequality.** $\forall x, y \in \mathbb{R}$ with $x, y \ge 0$: $\sqrt{xy} \le \frac{x + y}{2}$, and $\sqrt{xy} = \frac{x + y}{2} \iff x = y$.
*Proof.* Let $x, y \ge 0$. $0 \le \sqrt{xy} \le \frac{x + y}{2} \overset{\text{Lemma}}{\iff} xy \le \left(\frac{x + y}{2}\right)^2 \iff 4xy \le (x + y)^2 \iff 0 \le x^2 - 2xy + y^2 \iff 0 \le (x - y)^2$, true ($x - y \in \mathbb{R}$).
(证 $= \iff x = y$: 同理: $\iff (x - y)^2 = 0 \iff x = y$.)

**Triangle inequality.** $\forall x, y \in \mathbb{R}$, $|x + y| \le |x| + |y|$. *Proof:* (Lemma) ⇔ … (见 …)

**Lemma.** $\forall n \in \mathbb{Z}$, $n^2$ is even ⇒ $n$ is even.

**Theorem.** $\sqrt2$ is irrational.
*Proof.* Assume $\sqrt2$ is rational, so $\exists m, n \in \mathbb{N}$ s.t. $\sqrt2 = \frac mn$. W.L.O.G. we may assume that 1 is the greatest common factor of $m$ and $n$ (otherwise, divide out the common factor). Then $2 = \frac{m^2}{n^2}$, so $m^2 = 2n^2$ ⇒ $m^2$ is even $\overset{\text{Lemma}}{\Rightarrow}$ $m$ is even. By def. ($\exists j$, $m = 2j$) ⇒ $4j^2 = 2n^2$ ⇒ $n^2 = 2j^2$ is even ⇒ $n$ is even — contradicts gcd = 1.

A **prime** is an int. $n > 1$ s.t. the only positive factors are 1 and $n$.

**Theorem.** There are infinitely many primes.
*Proof.* Assume there are only finitely many: $\exists n$ s.t. $p_1, \dots, p_n$ are all the primes.
Claim: $\forall k \in \mathbb{Z}$ and $k > 1$, $k$ has at least 1 prime factor. If $k$ is not a prime: let $j = \min\{d \ge 2 : d \mid k\}$; then $j$ is prime and $j \ge 2$. (见图 — consider $p_1p_2\cdots p_n + 1$.)
