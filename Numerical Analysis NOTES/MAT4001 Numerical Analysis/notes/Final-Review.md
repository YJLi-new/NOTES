# MAT4001 Final Exam Review

> Transcribed from the handwritten review notes [Final-Review.pdf](Final-Review.pdf). `(?)` marks unclear handwriting.

**Final exam: 5 questions** — Q1: 45 (→ 11?), Q2: 15, Q3: 15, Q4: 15, Q5: 10.

## Numerical integration

- (Newton–Cotes) trapezoidal rule, Simpson's rule, midpoint rule
- + error terms + analysis of the error
- + composite rules (error)

## Gaussian quadrature

- (derivation?) $\int_a^b f(x) \approx \sum_{i=1}^n w_if(x_i)$
- degree of precision; orthogonal polynomials

## Richardson extrapolation

- (definition, algorithm)

## Numerical linear algebra

- nonsingular / pivoting / partial pivoting / total …
- permutation matrix / orthogonal matrix / p.d. /
- norm (induced norm) (Frobenius, $\|A\|_2$, $\|A\|_1$, $\|A\|_\infty$)
- condition number / convergent matrix /

## Gaussian elimination ⇔ LU factorization

- How to compute? How to use? (forward substitution, backward …)
- $LL^T$ … (p.d.) → Crout … (tridiagonal)

## Iterative methods

- (Jacobi, Gauss–Seidel, SOR): $x^{(k+1)} = Tx^{(k)} + c$
- + convergence ($\rho(T) < 1$) + error estimate $\|x^{(k)} - x\| \le \frac{\|T\|^k}{1 - \|T\|}\|x^{(1)} - x^{(0)}\|$
- $\rho(A) \le \|A\|$ for any induced norm ← ? (definition)

## Proofs related to the condition number
