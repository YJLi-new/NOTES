# MAT4001 Midterm Review

> Transcribed from the handwritten review notes [Midterm-Review.pdf](Midterm-Review.pdf). `(?)` marks unclear handwriting.

**6 questions in the midterm exam** — Q1: 8 subquestions (25 points); Q2–Q6: computation (3), proof (2). 2 hrs.

## 1st week

- **Computation error**: truncation error ($f(x) = P_n(x) + R(x)$), round-off error ($\frac13 = 0.3333\cdots3$, unavoidable).
- absolute error $|p - p^*|$; relative error $\frac{|p - p^*|}{|p|}$.
- **Significant digits**: $\frac{|p - p^*|}{|p|} \le 5\times 10^{-t}$.
- Chopping vs. rounding (四舍五入).
- **Avoid loss of accuracy due to round-off error.** Example: $x = \frac{-b + \sqrt{b^2 - 4ac}}{2a}$ with $b = 62.01$ (?), $a = c = 1$. Reformulate:
  $$x = \frac{b^2 - b^2 + 4ac}{2a(-b - \sqrt{b^2 - 4ac})} = \frac{-2c}{b + \sqrt{b^2 - 4ac}}$$
- $\frac xy$ when $y \ll 1$: $\frac{x + \varepsilon_x}{y + \varepsilon_y} = \frac{x}{y + \varepsilon_y} + \frac{\varepsilon_x}{y + \varepsilon_y}$.
- **Rewrite polynomials into nested form** — more efficient and reduces round-off error. Ex. $f(x) = x^3 - 6.1x^2 + 3.2x + 1.5 = ((x - 6.1)x + 3.2)x + 1.5$.
- Avoid large numbers eating small numbers.

## 2nd week

1. **Bisection method**: ① existence; ② the algorithm (checking the interval); ③ error analysis $|p_n - p| \le \frac{b - a}{2^n}$ → analyse the error / find some $N$ to achieve some accuracy.
2. **Fixed-point iteration**: ① existence of the fixed point; ② algorithm $x^{(n+1)} = g(x^{(n)})$; ③ uniqueness ($|g'(x)| \le k < 1$); ④ convergence analysis; ⑤ convergence rate $|p_n - p| \le \frac{k^n}{1 - k}|p_1 - p_0|$.

## 3rd + 4th week

- **Newton's method**: ① 2nd-order method; ② geometric interpretation (tangent lines); ③ convergence proof; ④ convergence rate.
- **Secant method** ← order $\frac{1 + \sqrt5}{2}$ (the proof; the algorithm).
- **Müller's method** ← order 1.84.
- **False position method**.

## 5th week

- **Interpolation: Lagrange** — $L_2 = \frac{(x - x_0)(x - x_1)}{(x_2 - x_0)(x_2 - x_1)}$, $L_0 = \cdots$, $L_1 = \cdots$ ✓.
- Error analysis + proof ✓.
- Runge's phenomenon?
- **Neville's method** (table $Q_{00}$; $Q_{10}, Q_{11}$; $Q_{20}, Q_{21}, Q_{22}$).
- **Divided difference** $f[x_1, x_2] = \frac{f(x_2) - f(x_1)}{x_2 - x_1}$. Theorem on page 9 of week 5-2.

## 6th week

- Hermite polynomial; divided difference.

## 7th week

- Spline: natural spline vs. clamped spline? Construction?

## 8th week

- **Numerical differentiation**: forward/backward difference, three-point … (1st-order derivative); three-point formula → 2nd order.
- Truncation error analysis.
- Richardson extrapolation.
