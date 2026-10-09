# MATH347 Notes 4 — Sets and Functions

> Transcribed from the handwritten notes [Notes-4.pdf](Notes-4.pdf). `(?)` marks unclear handwriting.

## Sets

**Def.** A set is a collection of objects (elements). $x \notin A$: $x$ is not an element of $A$.

**Subset.** A set $A$ is a subset of a set $B$ ($A \subseteq B$) $\iff \forall a \in A$, $a \in B$ $\iff \forall x$, $x \in A \Rightarrow x \in B$.
- $A \not\subseteq B \iff \exists a \in A$ s.t. $a \notin B$.
- $A = B \iff A \subseteq B$ and $B \subseteq A$.

**Roster notation.** $A = \{a, b, c\} = \{c, b, a\}$; $\mathbb{N} = \{0, 1, 2, \dots, n, \dots\}$.

$P(x)$: a statement for any ($\forall$) $x$; $P(x)$, $x \in D$:
$A = \{x \in D : P(x)\} = \{x \in D \mid P(x)\}$. E.g. $\{n \in \mathbb{Z} : 7 \mid n\} = \{0, \pm7, \pm14, \dots\}$.

- $\mathbb{R}$: real; $\mathbb{Z}$: int.; $\mathbb{Q}$: rational; $\mathbb{R}\setminus\mathbb{Q}$: irr.
- $\mathbb{Z}_{\ge m} = \{n \in \mathbb{Z} : n \ge m\}$; likewise $\mathbb{Z}_{>m}$, $\mathbb{Z}_{\le m}$, $\mathbb{Z}_{<m}$.
- Intervals on $\mathbb{R}$: $(a, b) = \{x \in \mathbb{R} : a < x < b\}$, $[a, b) = \{x \in \mathbb{R} : a \le x < b\}$, …

### Finite sets

**Def.** A set $A$ is finite $\iff A$ has finitely many elements. $|A| = n \iff$ distinct $a_1, \dots, a_n$ with $A = \{a_1, \dots, a_n\}$. $A = \varnothing \iff$ set $A$ has no element.

**Cardinality (基数).** The cardinality of a finite set $A$, $|A|$, is the number of elements of $A$. $\forall$ finite sets $A$ and $B$: $A \subseteq B \Rightarrow |A| \le |B|$.

**Thm.**
1. $|A| = 0 \Rightarrow A = \varnothing$.
2. $\forall$ set $A$: $\varnothing \subseteq A$ and $A \subseteq A$.
3. $\forall$ sets $A, B, C$: $A \subseteq B$ and $B \subseteq C$ ⇒ $A \subseteq C$.

*Proof (of 2).* Assume that $\varnothing \not\subseteq A$. Then $\exists x \in \varnothing$ s.t. $x \notin A$ → $|\varnothing| \ge 1$, contradicts $|\varnothing| = 0$.
*Uniqueness (of $\varnothing$).* Let $\varnothing_1, \varnothing_2$ be sets s.t. $|\varnothing_1| = 0$ and $|\varnothing_2| = 0$. Then $\varnothing_1 \subseteq \varnothing_2$ and $\varnothing_2 \subseteq \varnothing_1$ ⇒ $\varnothing_1 = \varnothing_2$.

**Prove:** $\{n \in \mathbb{Z} : n = 2k + 1 \text{ for some integer } k\} = \{n \in \mathbb{Z} : \exists k \in \mathbb{Z} \text{ s.t. } n = 2k - 1\}$.
*Proof.* (证 ⊆) Let $n \in A$; then $\exists k \in \mathbb{Z}$ s.t. $n = 2k + 1 = 2(k + 1) - 1$. Let $m = k + 1$; $k \in \mathbb{Z} \Rightarrow m \in \mathbb{Z}$, so $n = 2m - 1 \in B$. Hence $A \subseteq B$. Let $n_2 \in B$, … (见 …)

E.g. $A = \{x \in \mathbb{R} : x^3 + x^2 - x - 1 = 0\}$ … (见 …)

### Operations

- **Intersection.** $A \cap B = \{x : x \in A \wedge x \in B\}$ ($x \in A\cap B \iff x \in A \wedge x \in B$).
- **Disjoint.** Sets $A$ and $B$ are disjoint $\iff A \cap B = \varnothing$.
- **Union.** $A \cup B = \{x : x \in A \vee x \in B\}$.
- $A \setminus B = \{x : x \in A \text{ and } x \notin B\}$; $A^c = U \setminus A$; $(A^c)^c = A$.

**Thm.** $\forall$ sets $A, B, C$:
1. $\varnothing\cup A = A$, $\varnothing\cap A = \varnothing$.
2. $A\cap B \subseteq A$ (or $B$) $\subseteq A\cup B$. $A \subseteq B$ ⇒ $A\cap B = A$ and $A\cup B = B$.
   *Proof (of the latter, $A\cup B = B$).* 1. $B \subseteq A\cup B$. 2. $\forall x \in A\cup B$: case 1: if $x \in A$, $A \subseteq B \Rightarrow x \in B$; case 2: if $x \in B$ ✓.
3. $A\cup B = B\cup A$, $A\cap B = B\cap A$.
4. $A\cup(B\cup C) = (A\cup B)\cup C$; same for $\cap$.
5. $A \subseteq B$ ⇒ $(A\cup C) \subseteq (B\cup C)$ and $(A\cap C) \subseteq (B\cap C)$. Proof: (见 …)

**Thm (de Morgan).** $\forall$ universe $U$, $\forall$ sets $A, B \subseteq U$: $(A\cap B)^c = A^c\cup B^c$; $(A\cup B)^c = A^c\cap B^c$. Proof: (★ 见 …)

- $A\cup B = (A\setminus B)\cup(B\setminus A)\cup(A\cap B)$.
- **Distributive laws.** ① $A\cap(B\cup C) = (A\cap B)\cup(A\cap C)$; ② $A\cup(B\cap C) = (A\cup B)\cap(A\cup C)$.
  *Proof (of ②).* 1. (证 ⊆) Let $x \in A\cup(B\cap C)$. Case 1: $x \in A$ ⇒ $x \in A\cup B$ and $x \in A\cup C$ ⇒ $x \in$ RHS. Case 2: $x \in B\cap C$ ⇒ $x \in B \Rightarrow x \in A\cup B$; $x \in C \Rightarrow x \in A\cup C$ ⇒ $x \in$ RHS. 2. (证 ⊇) Let $x \in (A\cup B)\cap(A\cup C)$ … (见 …)
- $A = (A\setminus B)\cup(A\cap B)$ (方法 1 / 方法 2, 见 …).
- $A \subseteq B \iff B^c \subseteq A^c$. Proof: (见 …)

## Functions

**Def.** $\forall$ sets $A$ (domain), $B$ (codomain), a func. $f$ from $A$ to $B$ ($f: A \to B$) is a rule which assigns to every element $a \in A$ exactly one element $b \in B$, $f(a) = b$.

- **Image.** Let set $U \subseteq A$, func. $f: A \to B$; the image of $U$ is $f(U) = \{f(x) \in B : x \in U\}$.
- **Range.** range($f$) $= f(A)$.
- **Inverse image.** Let set $V \subseteq B$; the inverse image of $V$ is $f^{-1}(V) = \{a \in A : f(a) \in V\}$.
- **Injective** (one-to-one): $f: A \to B$ is inj. $\iff \forall a_1, a_2 \in A$, $f(a_1) = f(a_2) \Rightarrow a_1 = a_2$ $\iff \forall a_1, a_2 \in A$, $a_1 \ne a_2 \Rightarrow f(a_1) \ne f(a_2)$.
- **Surjective** (onto): $f: A \to B$ is surj. $\iff f(A) = B \iff \forall b \in B$, $\exists a \in A$ s.t. $f(a) = b$.
- **Bijective**: $f$ is bij. $\iff f$ is inj. and $f$ is surj.
- **Composition.** $f: A \to B$, $g: B \to C$; $g\circ f: A \to C$, $\forall a \in A$, $g\circ f(a) = g(f(a))$.

**Thm.**
1. $f$ inj. and $g$ inj. ⇒ $g\circ f$ is inj.
2. $f$ surj. and $g$ surj. ⇒ $g\circ f$ is surj.
3. ⇒ $f$ bij. and $g$ bij. ⇒ $g\circ f$ is bij.

Proof (of 1): (见 …)
