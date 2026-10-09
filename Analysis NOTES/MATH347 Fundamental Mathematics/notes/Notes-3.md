# MATH347 Notes 3 — Divisibility, Congruence, GCD, Bézout

> Transcribed from the handwritten notes [Notes-3.pdf](Notes-3.pdf). `(?)` marks unclear handwriting.

## 1 Uniform continuity (negation practice)

$f$ is a func. def. on $\mathbb{R}$: $\forall\varepsilon > 0$, $\exists\delta > 0$ s.t. $\forall x, y \in \mathbb{R}$, $|x - y| < \delta \Rightarrow |f(x) - f(y)| < \varepsilon$.
(neg.) $\exists\varepsilon > 0$ s.t. $\forall\delta > 0$, $\exists x, y$ s.t. $|x - y| < \delta$ **and** $|f(x) - f(y)| \ge \varepsilon$.

## Divisibility

**Def.** Let $m, n \in \mathbb{Z}$ and $n \ne 0$. Then $n$ divides $m$ (notation $n \mid m$) $\iff \exists k \in \mathbb{Z}$ s.t. $m = kn$.

- $\forall n \ne 0$, $n \mid 0$ is true.

**Thm.** $\forall a, b, m, n, d \in \mathbb{Z}$ and $d \ne 0$: $d \mid a$ and $d \mid b$ ⇒ $d \mid ma + nb$.
*Proof.* Let $a, b, m, n, d \in \mathbb{Z}$ and $d \ne 0$. By def., $\exists k_1 \in \mathbb{Z}$ s.t. $a = k_1d$, $\exists k_2$ … $b = k_2d$. Then $ma + nb = (mk_1 + nk_2)d$; let $j = mk_1 + nk_2$; $m, k_1, n, k_2 \in \mathbb{Z}$ ⇒ $j \in \mathbb{Z}$. By def., $d \mid ma + nb$.

**Thm (division).** $\forall m, n \in \mathbb{N}^*$ ($> 0$), ∃ unique $q$ (quotient), $r$ (remainder) $\in \mathbb{Z}$ s.t. $m = qn + r$, $0 \le r < n$ ($\iff 0 \le r \le n - 1$). (∴ $n \mid m \iff r = 0$.)

Let $n \mid a$; then $a \ne 0 \Rightarrow |a| \ge n$: by def. $\exists k \in \mathbb{Z}$ s.t. $a = kn$; $a \ne 0 \Rightarrow k \ne 0 \Rightarrow |k| \ge 1$; then $|a| = |k|\cdot n \ge n$.

## Congruence

**Def.** For $n \in \mathbb{Z}$, $n > 0$ and $n \ne 1$ (fixed): $a$ is congruent to $b$ modulo $n$ $\iff r_n(a) = r_n(b)$ (notation: $a \equiv b \pmod n$).

E.g. ① $2 \equiv 12 \pmod{10} \equiv 22 \pmod{10} \equiv \cdots$; ② $2 \equiv -8 \pmod{10}$ ($-8 = (-1)\cdot 10 + 2$).

**Thm.** $a \equiv b \pmod n \iff n \mid b - a$ ($\iff n \mid a - b$). Proof: (见 …) … Thus $r_2 - r_1 = 0$; by def., $a \equiv b \pmod n$.

**Thm.** $\forall a, b, c, d \in \mathbb{Z}$ and $n \in \mathbb{N}$, $n > 0$:

1. $a \equiv b \pmod n$ and $c \equiv d \pmod n$ ⇒ $a + c \equiv b + d$, $a - c \equiv b - d$, $a\cdot c \equiv b\cdot d \pmod n$.
2. ($\forall a, n \in \mathbb{Z}$, $a \equiv a \pmod n$.) $a + (b + c) \equiv (a + b) + c$; $a(bc) \equiv (ab)c$; $a \equiv b \Rightarrow b \equiv a$; $a + b \equiv b + a$; $ab \equiv ba \pmod n$.

## 2

For $\forall a, b, n \in \mathbb{N}$, $n \ge 2$: $a \equiv b \pmod n \iff r_1 = r_2$, where $a = q_1n + r_1$, $b = q_2n + r_2$.

**Thm.** $a \equiv b \pmod n \iff n \mid a - b$ ($\iff n \mid b - a$).

$a \equiv b$, $c \equiv d \pmod n$ ⇒ $a \pm c \equiv b \pm d$, $ac \equiv bd \pmod n$.
*Proof.* Let $\forall k \in \mathbb{N}$: multiply $a \equiv b$ by itself $k$ times → $a^k \equiv b^k$. ($1^k \equiv 1$; $(-1)^{2k} = 1$, $(-1)^{2k+1} = -1$.)

**E.g.** ($s \equiv k \pmod n \iff n \mid s - k$) Compute $22^{49}\cdot 29^6 - 16^2 \pmod 5$:
- $16 \equiv 1 \pmod 5$ ⇒ $16^2 \equiv 1^2 = 1 \pmod 5$
- $22 \equiv 2 \pmod 5$ ⇒ $22^{49} \equiv 2^{49}$; $2^2 = 4 \equiv -1$, $2^4 = (2^2)^2 \equiv (-1)^2 = 1 \pmod 5$; $49 = 4\cdot 12 + 1$ ⇒ $2^{49} = (2^4)^{12}\cdot 2 \equiv 2 \pmod 5$
- $29 \equiv -1$ ⇒ $29^6 \equiv (-1)^6 \equiv 1 \pmod 5$

**Last digit (mod 10).** $877^{5620146} \equiv 7^{5620146}$ (since $877 \equiv 7 \pmod{10}$); $7^{4k+r} = (7^4)^k\cdot 7^r \equiv 1^k\cdot 7^r \equiv 7^r$. (见图)

**Thm.** $\forall n, k \in \mathbb{N}$: $ka \equiv kb \pmod{kn}$ ⇒ $kn \mid k(a - b)$ ⇒ $n \mid a - b$ ⇒ $a \equiv b \pmod n$.
E.g. $42 \equiv 12 \pmod{10}$ ⇐ $3\cdot 14 \equiv 3\cdot 4 \pmod{10}$ ⇐ $14 \equiv 4 \pmod{10}$. (but! $7 \not\equiv 2 \pmod{10}$ from $42 \equiv 12$ dividing by 6: 6, 10 ✗ since $\gcd = 2 > 1$; 3, 10 ✓ $\gcd = 1$ — 可约掉.)

**E.g.** Let $m \equiv 3 \pmod 4$. Prove: ∄ integer solution of $a^2 + b^2 = m$.
*Proof.* Assume $\exists a, b \in \mathbb{Z}$ s.t. $a^2 + b^2 = m$. Then $a^2 + b^2 = m \equiv 3 \pmod 4$. Let $x \in \mathbb{Z}$; then

| $x \pmod 4$ | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| $x^2 \pmod 4$ | 0 | 1 | 0 | 1 |

So $\forall a, b \in \mathbb{Z}$, $a^2 + b^2 \equiv 0$ or 1 or 2 $\pmod 4$ — 无 3 → contradiction.

## Greatest Common Divisor

**Def.** $\forall a, b \in \mathbb{Z}$, $a, b$ not both 0: $\gcd(a, b)$ ($= d$) s.t. $\forall d_1$ common divisor of $a, b$: $d \ge d_1$. ($\forall d_1$, $d_1 \mid a$ and $d_1 \mid b$ ⇒ $d_1 \mid \gcd(a, b)$.)

**Bézout identity.** $\forall m, n \in \mathbb{Z}$ not both 0, $\exists x, y \in \mathbb{Z}$ s.t. $\gcd(m, n) = xm + yn$.
($\forall a \ne 0$, $\gcd(a, 0) = |a|$; $a \mid 0$ ✓ since $0 = 0\cdot a$.)

**Cor. 3.19.** $\gcd(a, b) = 1$ and $a \mid bc$ ⇒ $a \mid c$.
*Proof.* Let $\gcd(a, b) = 1$, where $a, b \in \mathbb{Z}$. By Bézout, $\exists x, y \in \mathbb{Z}$ s.t. $1 = xa + yb$; $a \mid bc$ ⇒ $\exists k$ s.t. $bc = ka$ … (见 …: $c = xac + ybc = a(xc + yk)$).

**Def.** $n \in \mathbb{N}$ (mod $n$), let $a \in \mathbb{Z}$. If $\exists x \in \mathbb{Z}$ s.t. $xa \equiv 1 \pmod n$ ⇒ $x$ is called a **multiplicative inverse**.
E.g. 6 has no inverse (mod 10); $3\cdot 7 = 21 \equiv 1 \pmod{10}$.

## 3

**Bézout identity.** $\forall a, b$ (not both 0), $\exists x, y \in \mathbb{Z}$ s.t. $\gcd(a, b) = xa + yb$.

**Relatively prime** (coprime): $a, b$ are r. prime $\iff \gcd(a, b) = 1$.

**Lemma.** $a \mid bc$ and $\gcd(a, b) = 1$ ⇒ $a \mid c$.

**(Thm)** $a \mid c$, $b \mid c$, $\gcd(a, b) = 1$ ⇒ $ab \mid c$.
*Proof.* Assume $a \mid c$, $b \mid c$ and $\gcd(a, b) = 1$. $a \mid c$ ⇒ $\exists k \in \mathbb{Z}$ s.t. $c = ka$; $b \mid c$ ⇒ $b \mid ka$; $\gcd(a, b) = 1$ (Lemma) ⇒ $b \mid k$ ⇒ $\exists m \in \mathbb{Z}$ s.t. $k = mb$ ⇒ $c = ka = mb\cdot a = m(ab)$ ⇒ $ab \mid c$.

**E.g.** $n^2 \not\equiv n \pmod 3 \iff n \equiv 2 \pmod 3$ (见图).

**Lemma.** $\gcd(a, n) = 1$ ⇒ $\exists a^{-1} \pmod n$.
*Proof.* $\gcd(a, n) = 1$ (Bézout) ⇒ $\exists x, y \in \mathbb{Z}$ s.t. $xa + yn = 1$ ⇒ $xa \equiv 1 \pmod n$ ⇒ …

Let $n = p$ be prime, $a \equiv 1, 2, \dots, p - 1$ ($a \not\equiv 0 \pmod p$): $\forall 1 \le j \le p - 1$, $\gcd(j, p) = 1$ ⇒ $\exists a^{-1} \pmod p$.

(Converse) Assume that $\forall a \not\equiv 0 \pmod n$ has a multiplicative inverse. If $a^{-1} \pmod n$ exists ⇒ then $\gcd(a, n) = 1$ ⇒ all $j = 1, 2, \dots, n - 1$ are relatively prime with $n$. Suppose $n = k\cdot m$ with $m < n$: $\gcd(k, n) = k$ ⇒ $k = 1$, which contradicts. Hence $n$ is prime.

(Proof of Bézout, cases) Assume $\gcd(a, b) = 1$. If $a = 0$ ⇒ $b = 1$ ⇒ $x\cdot 0 + b\cdot b = 1$. If $a \ne 0$ ⇒ $b \ne 0$; if $|b| = 1$ ⇒ $0\cdot a + b\cdot b = 1$; if $|b| \ge 2$, W.L.O.G. assume $b \ge 2$ ($b > 0$) — assume the result for $b > 0$; for $b < 0$, $-b > 0$ … (见 …)
