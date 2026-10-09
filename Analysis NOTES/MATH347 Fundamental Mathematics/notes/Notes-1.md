# MATH347 Notes 1 — Statements, Logic, Proof Methods

> Transcribed from the handwritten notes [Notes-1.pdf](Notes-1.pdf). `(?)` marks unclear handwriting.

## 1 Vocabulary

- thm. / def.
- **primitive notion** (undef.)
- **axiom** (postulates): facts ✓ without proof
- **statement** (proposition): a sentence which is ✓ or ✗, but not both
- **universal statement**: (false) $\forall f(x)$ on $\mathbb{R}$ is cont.; (true) $\exists f(x)$ on $\mathbb{R}$ is not cont.
- lemma ⊂ thm.; corollary (推论); conjecture → T (becomes a thm.) / F

**E.g.** $\forall x, y \in \mathbb{N}$ with $x, y$ odd, $x + y$ is even.

def. $m \in \mathbb{R}$ is an even int. $\iff \exists k \in \mathbb{N}$ s.t. $m = 2k$; odd ⇔ … $m = 2k + 1$.

*Proof.* $\forall m, n \in \mathbb{N}^*$, let $m, n$ be odd int. ⇒ $\exists k \in \mathbb{N}^*$ s.t. $m = 2k + 1$, $\exists j$ … ⇒ $m + n = 2(k + j + 1)$; $k, j, 1 \in \mathbb{N}^*$ ⇒ $(k + j + 1) \in \mathbb{N}^*$ ⇒ by def., $m + n$ is even.

**False conjecture, e.g.** $\forall n \in \mathbb{Z}$ and $n > 0$, $n^2 + n + 41$ is prime. (false: e.g. $n = 40$ gives $41^2$)

## 2 Logic

$P, Q, R, \dots$ statements (prop.'s).

### Truth tables

| $P$ | $Q$ | $P\wedge Q$ (and) | $P\vee Q$ (or) | $\neg P$ (not) | $P\Rightarrow Q$ | $P\Leftarrow Q$ | $(\neg P)\vee(\neg Q)$ |
|---|---|---|---|---|---|---|---|
| ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ | ✗ |
| ✓ | ✗ | ✗ | ✓ | ✗ | ✗ | ✓ | ✓ |
| ✗ | ✓ | ✗ | ✓ | ✓ | ✓ | ✗ | ✓ |
| ✗ | ✗ | ✗ | ✗ | ✓ | ✓ | ✓ | ✓ |

- $(P \Leftrightarrow Q) \iff (P\Rightarrow Q)\wedge(P\Leftarrow Q)$
- (★) $(R\vee Q) \iff (\neg R\Rightarrow Q)$; $(P\Rightarrow Q) \iff (\neg P\vee Q)$
- $\neg(P\Rightarrow Q) \iff \neg(\neg P\vee Q) \iff P\wedge\neg Q$, i.e. $\neg(\text{if } P \text{ then } Q) \iff P\wedge\neg Q$
- **converse** of $P\Rightarrow Q$: $Q\Rightarrow P$; **contrapositive** of $P\Rightarrow Q$: $\neg Q\Rightarrow\neg P$; $(P\Rightarrow Q) \iff (\neg Q\Rightarrow\neg P)$
- **de Morgan**: $\neg(P\wedge Q) \iff (\neg P)\vee(\neg Q)$; $\neg(P\vee Q) \iff (\neg P)\wedge(\neg Q)$

### Direct proof of $(P\Rightarrow Q)$

1. Assume $P$ is ✓ … (prove $Q$ is ✓).

E.g. the product of 2 odd int. is odd ⇔ $\forall$ odd $m, n$, $(m\cdot n)$ is odd.
*Proof.* Let $\forall$ odd $m, n$. def. ⇒ $\exists k \in \mathbb{Z}$ s.t. $m = 2k + 1$, $\exists j$ … $n = 2j + 1$; $m\cdot n = (\cdots)(\cdots) = 2(\cdots) + 1$ ⇒ def. ⇒ $mn$ is odd.

**Corollary.** $\forall$ odd $n$, $n^2$ is odd ($\iff \forall$ even … contrapositive form). *Proof:* $n = 2k + 1$, $n^2 = (4k^2 + 4k) + 1 = 2(2k^2 + 2k) + 1$.

## 3 Proof methods for $P\Rightarrow Q$

1. 直接: assume $P$, prove $Q$.
2. contrapositive ($\iff \neg Q\Rightarrow\neg P$) — 反证: assume $Q$ is false ($\neg Q$), prove $P$ is false ($\neg P$).
3. by contradiction (反例): prove stmt $S$ is true: assume $\neg S$, arrive at a contradiction ⇒ $S$ is true.
   For $S: P\Rightarrow Q$: assume $P\wedge\neg Q$ ⇒ $\neg Q$ is true → 用反证 → $\neg P$ → $P\wedge\neg P$ 矛盾 → $(P\Rightarrow Q)$ is true.

**E.g.** $\forall x \in \mathbb{Z}$, $3x + 8$ is even ($P$) ⇒ $x$ is even ($Q$) — call this $S$.
- 用反证: $\iff \forall x \in \mathbb{Z}$, $x$ is odd ⇒ $3x + 8$ is odd. ∵ $\exists k \in \mathbb{N}$ (is int) s.t. $x = 2k + 1$ ⇒ $3x + 8 = \cdots$ is odd.
- 用反例: assume that $S$ is false → the negation is true → $\exists x \in \mathbb{Z}$ s.t. $3x + 8$ is even and $x$ is odd → … → $3x + 8$ is odd → contradiction → $S$ is true.
- 直接证: …

**E.g.** $\forall x, y \in \mathbb{Z}$, $xy$ is even $\iff$ ($x$ is even $\vee$ $y$ is even).
1. (证 ⇐) Assume $x$ is even $\vee$ $y$ is even. WLOG, we may assume that $x$ is even (if we exchange $x$ and $y$, same process, so we have symmetry). By def. $\exists k \in \mathbb{N}$ s.t. $x = 2k$ ⇒ $xy = (2k)y = 2(ky)$, $ky \in \mathbb{N}$ ⇒ $xy$ is even.
2. (证 ⇒, contrapositive) $\forall x, y \in \mathbb{Z}$, ($x$ is odd $\wedge$ $y$ is odd) ($\neg Q$) ⇒ $xy$ is odd ($\neg P$) $\iff$ ($\forall x, y \in \mathbb{Z}$, $xy$ is even ($P$) ⇒ ($x$ is even $\vee$ $y$ is even) ($Q$)).

## Rational numbers

$x \in \mathbb{R}$ is rational ($x \in \mathbb{Q}$) $\iff \exists m, k \in \mathbb{Z}$, $k \ne 0$, s.t. $x = \frac mk$ ($\iff \exists m, k \in \mathbb{Z}$, $k > 0$, s.t. $x = \frac mk$).

需证: $\forall n \in \mathbb{N}$, $\forall x \in \mathbb{R}$, $x^n \notin \mathbb{Q} \Rightarrow x \notin \mathbb{Q}$.

1. (contrapositive) $\iff \forall n, \forall x$, $x \in \mathbb{Q} \Rightarrow x^n \in \mathbb{Q}$.
   证: let $n \in \mathbb{N}$ and $x \in \mathbb{R}$; assume that $x \in \mathbb{Q}$ ⇒ $\exists k \in \mathbb{N}$, $m \in \mathbb{Z}$ s.t. $x = \frac mk$ ⇒ $x^n = \frac{m^n}{k^n}$ with $m^n \in \mathbb{Z}$, $k^n \in \mathbb{N}$ ⇒ $x^n \in \mathbb{Q}$.
2. (contradiction) negation: $\exists n, \exists x$ s.t. $x^n \notin \mathbb{Q}$ and $x \in \mathbb{Q}$ … (见 …)
3. (proof by cases) (见 …)
