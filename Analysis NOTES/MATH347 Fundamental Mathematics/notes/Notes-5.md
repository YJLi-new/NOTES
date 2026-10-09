# MATH347 Notes 5 — Functions (cont.), Induction, Well-ordering

> Transcribed from the handwritten notes [Notes-5.pdf](Notes-5.pdf). `(?)` marks unclear handwriting.

## Functions (cont.)

★ $\forall$ func. $f: A \to B$: $\forall a_1, a_2 \in A$, $a_1 = a_2 \Rightarrow f(a_1) = f(a_2)$ (well-definedness). $f$ is inj. $\iff$ the converse …

**Proof** (of $f$ surj., $g$ surj. ⇒ $g\circ f$ surj.). $f: A \to B$, $g: B \to C$ surj. Let $z \in C$; $g$ is surj. ⇒ $\exists y \in B$ s.t. $g(y) = z$; $f$ is surj. ⇒ $\exists x$ … $f(x) = y$. Then $g\circ f(x) = z$ ⇒ $g\circ f$ is surj.

**Thm.** ($f: A \to B$, $g: B \to C$) $\forall$ func. $g, f$: $g\circ f$ is inj. ⇒ $f$ is inj.
*Proof.* Let $g\circ f$ be inj. Let $a_1, a_2 \in A$ and assume $f(a_1) = f(a_2) \in B$; $g$ is a function ⇒ $g(f(a_1)) = g(f(a_2))$ ⇒ $g\circ f(a_1) = g\circ f(a_2)$; $g\circ f$ is inj. ⇒ $a_1 = a_2$ ⇒ $f$ is inj.

**Thm.** $\forall$ func. $g, f$: $g\circ f$ is surj. ⇒ $g$ is surj.
*Proof.* Let $\forall z \in C$; $g\circ f$ is surj. ⇒ $\exists x \in A$ s.t. $g\circ f(x) = z$. Denote $y = f(x) \in B$; then $g(y) = \cdots = z$.

(Counterexample for the converses) $f(a) = b_1$, $g(b_1) = g(b_2) = c$, $g\circ f(a) = c$ — bij.: $g\circ f$ is inj. but $g$ is not inj. ($b_1 \ne b_2$ but $g(b_1) = g(b_2) = c$); $g\circ f$ is surj. but $f$ is not surj. ($f(A) = \{b_1\} \subsetneq B$).

**(Inverse).** Let $f: A \to B$ be bij., $g: B \to A$ with $\forall a \in A$, $g(f(a)) = a$ and $\forall b \in B$, $f(g(b)) = b$. Then $g = f^{-1}$ and $f = g^{-1}$.

**Thm.** Let $A, B$ be finite sets s.t. $|A| = m$ and $|B| = n$, where $m, n \in \mathbb{N}$. (Statements equivalent:)
1. $|A| \le |B|$;
2. $\exists f: A \to B$ inj.;
3. $\exists g: B \to A$ surj.

*Proof.*
(① ⇒ ②) Assume that $m \le n$; $A = \{a_1, \dots, a_m\}$, $B = \{b_1, \dots, b_n\}$ (distinct). Define $f: A \to B$ s.t. $\forall i \in [1, m]$, $f(a_i) = b_i$. Then $\forall a_i \ne a_j$ ($i \ne j$), $b_i \ne b_j$, so $f(a_i) \ne f(a_j)$ ⇒ $f$ is inj.
(② ⇒ ③) Let $f: A \to B$ be inj. W.L.O.G. assume that $\forall i \in [1, m]$, $b_i = f(a_i)$. Define $g: B \to A$ s.t. $g(b) = a_i$ if $\exists i \in [1, m]$ s.t. $b = b_i$; $g(b) = a_1$ if $\forall i \in [1, m]$, $b \ne b_i$. So $\forall i \in [1, m]$, $a_i = g(b_i)$ ⇒ $g$ is surj.

**Corollary.** $\forall$ (finite) sets $A, B$: $|A| = |B| \Rightarrow \exists f: A \to B$ bij.
*Proof.* (⇒) Assume that $|A| = m = n = |B|$. By thm. ① ⇒ ②, $\exists f: A \to B$ inj. Then $|f(A)| = m = |A|$; $|B| = m$ ⇒ $f$ is bij. (⇐) … (见 …)

## 2 Images and inverse images

$f: A \to B$, $U \subseteq A$, $V \subseteq B$ ($f(U) = \{f(x) : x \in U\}$, $f^{-1}(V) = \{a : f(a) \in V\}$).

- $\forall X, Y \subseteq B$: $f^{-1}(X\cup Y) = f^{-1}(X)\cup f^{-1}(Y)$; $f^{-1}(X\cap Y) = f^{-1}(X)\cap f^{-1}(Y)$.
- $\forall C, D \subseteq A$: $f(C\cap D) \subseteq f(C)\cap f(D)$.
  *Proof.* (⊆) Let $y \in f(C\cap D)$; then $\exists x \in C\cap D$ s.t. $y = f(x)$; $x \in C \Rightarrow y \in f(C)$, $x \in D \Rightarrow y \in f(D)$ ⇒ $y \in f(C)\cap f(D)$.
  (⊇ if $f$ is inj.) Let $\forall y \in f(C)\cap f(D)$ ⇒ $y \in f(C) \Rightarrow \exists x_1 \in C$ s.t. $y = f(x_1)$; $y \in f(D) \Rightarrow \exists x_2 \in D$ s.t. $y = f(x_2)$ ⇒ $f(x_1) = f(x_2)$; $f$ is inj. ⇒ $x_1 = x_2$. Let $x = x_1 = x_2$; then $x \in C\cap D$ and $y = f(x)$ ⇒ $y \in f(C\cap D)$.

## Induction

$\forall n \in \mathbb{N}$, statements $P(n)$ (e.g. $P(n): \sum_{k=1}^n k = \frac{(n+1)n}{2}$). [$\forall n \in \mathbb{N}$, $(P(n) \Rightarrow P(n+1))\wedge P(1)$.]

**Principle of induction.** Let $(P(n))_{n\in\mathbb{N}}$ be a seq. of statements. If $\forall n \in \mathbb{N}$, $P(n) \Rightarrow P(n+1)$, and $P(1)$ is true, then $\forall n \in \mathbb{N}$, $P(n)$ is true.

**E.g.** 证 $P(n): \sum_{k=1}^n k = \frac{(n+1)n}{2}$.
- Base case: let $n = 1$: $\sum_{k=1}^1 k = 1$, $\frac{(1+1)\times 1}{2} = 1$, $1 = 1$ ⇒ $P(1)$ is true.
- Induction step: let $n \ge 1$; assume that $P(n)$ is true. (Goal: 证 $P(n+1): \sum_{k=1}^{n+1}k = \frac{(n+2)(n+1)}{2}$ is true.) LHS: …, RHS: … (见 …)
- (另: denote $S_n = \sum_{k=1}^n k$; then $2S_n = (1 + 2 + \cdots + n) + (n + (n-1) + \cdots + 1) = n(n+1)$ ⇒ $S_n = \frac{n(n+1)}{2}$.)

**E.g.** 证 $\forall n \in \mathbb{N}$, $P(n): \sum_{k=1}^n(3k + 2) = \frac{3n^2 + 7n}{2}$.
Base case: let $n = 1$ … Induction step: let $n \ge 1$; assume that $P(n)$ is true. $\sum_{k=1}^{n+1}(3k + 2) = \left[\sum_{k=1}^n(3k + 2)\right] + [3(n+1) + 2]$ with the bracket $= \frac{3n^2 + 7n}{2}$; LHS/RHS … (见 …)

**E.g.** 证 $\forall n \in \mathbb{N}$, $12 \mid 19^n - 7^n$ ($\iff 19^n \equiv 7^n \pmod{12} \iff 19^n - 7^n \equiv 0 \pmod{12}$).
Induction step: let $n \ge 1$; assume $P(n)$ ✓ ($12 \mid 19^n - 7^n$, i.e. $19^n - 7^n = k\cdot 12$). (WTS $P(n+1)$: $12 \mid 19^{n+1} - 7^{n+1}$.)
$19^{n+1} - 7^{n+1} = (12 + 7)\cdot 19^n - 7^{n+1} = 12\cdot 19^n + 7(19^n - 7^n) = 12(19^n + 7k)$. Hence $P(n+1)$ ✓.

**E.g.** $\forall n \in \mathbb{N}$, $\forall$ real $x_1, \dots, x_n \in [0, 1]$: $P(n): \prod_{k=1}^n(1 - x_k) \ge 1 - \sum_{k=1}^n x_k$.
*Proof.* $n = 1$: $1 - x_1 \ge 1 - x_1$ ✓. $n \ge 1$: assume $P(n)$ ✓; let real $x_1, \dots, x_{n+1} \in [0, 1]$:
$$\prod_{k=1}^{n+1}(1 - x_k) = \Big[\prod_{k=1}^n(1 - x_k)\Big](1 - x_{n+1}) \ge \Big(1 - \sum_{k=1}^n x_k\Big)(1 - x_{n+1}) = 1 + x_{n+1}\Big(\sum_{k=1}^n x_k\Big) - x_{n+1} - \sum_{k=1}^n x_k = 1 + \underbrace{x_{n+1}\sum_{k=1}^n x_k}_{\ge 0} - \sum_{k=1}^{n+1}x_k \ge 1 - \sum_{k=1}^{n+1}x_k$$
(Side lemma: $\forall i \in [1, n]$, $x_i \ge y_i$ ⇒ $P(n): \sum_{i=1}^n x_i \ge \sum_{i=1}^n y_i$; $n = 1$: $x_1 \ge y_1$ ✓; $n \ge 1$: assume $P(n)$ ✓ … ✓.)

## Well-ordered sets

**Def.** Set $A \subseteq \mathbb{R}$ is well-ordered $\iff \forall B \subseteq A$ and $B \ne \varnothing$, $\exists b = \min B$ ($b \in B$ s.t. $\forall y \in B$, $b \le y$).

- $(0, 1)$ ✗ well-o.; $[0, 1]$ ✗ well-o.
- **Axiom:** $\mathbb{N}$ is well-o.

**Thm (induction).** $P(n)$ is a seq. of statements ($n \in \mathbb{N}$): $P(1)$ ✓ and $\forall n \in \mathbb{N}$, $(P(n) \Rightarrow P(n+1))$ ✓ ⇒ $\forall n \in \mathbb{N}$, $P(n)$ ✓.
*Proof.* Assume that the thm. is ✗ ⇒ $\exists n_0 \in \mathbb{N}$ s.t. $P(n_0)$ ✗. Define $S = \{n \in \mathbb{N} : P(n) ✗\} \subseteq \mathbb{N}$. We have $n_0 \in S$ ⇒ $S \ne \varnothing$. (axiom) $\mathbb{N}$ is well-o. and $S \subseteq \mathbb{N}$ ⇒ $\exists m = \min S$; $m \in S$ ⇒ $P(m)$ ✗ … (见 …)

## Method of infinite descent

**E.g.** Assume $\sqrt2 \in \mathbb{Q}$ ⇒ $\exists x, y \in \mathbb{N}$ s.t. $\sqrt2 = \frac xy$. Then $x^2 = 2y^2$. Consider $S = \{x \in \mathbb{N} : \exists y \in \mathbb{N} \text{ s.t. } x^2 = 2y^2\}$; $x \in S$ ⇒ $S \ne \varnothing$. By well-o. of $\mathbb{N}$ ($S \subseteq \mathbb{N}$), $\exists m = \min S$ ⇒ $m \in S$ ⇒ $\exists n \in \mathbb{N}$ s.t. $m^2 = 2n^2$ ⇒ $m^2$ is even ⇒ $m$ is even ⇒ $\exists k \in \mathbb{N}$ s.t. $m = 2k$ … (⇒ $n^2 = 2k^2$, so $n \in S$ with $n < m$, contradiction.)
