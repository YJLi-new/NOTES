Existence of solutions

<img src="Lecture-Notes_files/media/image1.png" style="width:4.35993in;height:0.96982in" alt="图形用户界面, 文本 描述已自动生成" />

<img src="Lecture-Notes_files/media/image2.png" style="width:4.35972in;height:0.56756in" alt="文本 描述已自动生成" />

Uniqueness of solutions

(If the model has at least one solution)

<img src="Lecture-Notes_files/media/image3.png" style="width:4.18261in;height:0.83113in" alt="文本 描述已自动生成" />

Thm 4.1

（给first-order linear ODE）

func. p,q are cont. on (α, β) ⊂ ℝ

⇒ ∀t0 ∈ (α, β), y0 ∈ ℝ,

∃ unique func. y,

s.t.

> <img src="Lecture-Notes_files/media/image4.png" style="width:2.63043in;height:0.63795in" />

and the solution is defined throughout the interval (α, β).

<img src="Lecture-Notes_files/media/image5.png" style="width:3.02095in;height:1.23044in" alt="文本 描述已自动生成" />

<img src="Lecture-Notes_files/media/image6.png" style="width:2.87054in;height:1.47826in" alt="文本 描述已自动生成" />

（给non-linear ODE）

<img src="Lecture-Notes_files/media/image7.png" style="width:4.45652in;height:2.79691in" alt="文本 描述已自动生成" />

<img src="Lecture-Notes_files/media/image8.png" style="width:3.53044in;height:1.48579in" alt="文本 描述已自动生成" />

……

Slide 6

-P1 Homogeneous equations with constant coefficients

The general second-order linear homogeneous ODE <img src="Lecture-Notes_files/media/image9.png" style="width:1.59376in;height:0.19792in" />

the fundamental set of solutions for it always exist, but there’s no method to find them explicitly.

<u>ODE (1):</u> <img src="Lecture-Notes_files/media/image10.png" style="width:1.3125in;height:0.22361in" />

substituting a <u>**trial** function y(t) = exp(rt)</u> for some constant r into the ODE (1)

<img src="Lecture-Notes_files/media/image11.png" style="width:1.70314in;height:0.21875in" />

exp(rt) is positive → <img src="Lecture-Notes_files/media/image12.png" style="width:1.12501in;height:0.1875in" />: the **<u>characteristic equation</u>** for the ODE (1)

Slide 7

-P1 Non-homogeneous equations

Non-homogeneous equations: <img src="Lecture-Notes_files/media/image13.png" style="width:1.73439in;height:0.22396in" /> **<u>(1)</u>**

(given functions p, q and r that are continuous in an interval I)

The corresponding homogeneous equation: <img src="Lecture-Notes_files/media/image14.png" style="width:1.57814in;height:0.1875in" /> **<u>(2)</u>**

Let Z_1 and Z_2 be solutions to the non-homogeneous (1).

> <img src="Lecture-Notes_files/media/image15.png" style="width:0.89584in;height:0.1875in" />
>
> <img src="Lecture-Notes_files/media/image16.png" style="width:2.1771in;height:0.19792in" />
>
> → Z satisfies the homogeneous (2)
>
> →→ ∃constants c<sub>1</sub>, c<sub>2</sub>,
>
> s.t. <img src="Lecture-Notes_files/media/image15.png" style="width:0.89584in;height:0.1875in" /> **=** <img src="Lecture-Notes_files/media/image17.png" style="width:2.21356in;height:0.22396in" />
>
> Y(t) is a solution to (1), (y_1, y_2) is a fundamental set of solutions to (2)
>
> → <img src="Lecture-Notes_files/media/image18.png" style="width:2.09377in;height:0.18229in" />

Complementary solution (of (1))

Def.: <img src="Lecture-Notes_files/media/image19.png" style="width:1.71876in;height:0.22917in" />

Particular solution (of (1))

Def.: **Y(t)** which satisfies <img src="Lecture-Notes_files/media/image20.png" style="width:2.07814in;height:0.21875in" />

……

**<u>Case 1 -</u>** <img src="Lecture-Notes_files/media/image21.png" style="width:0.83412in;height:0.15399in" />

**Y(t) =** <img src="Lecture-Notes_files/media/image22.png" style="width:0.58768in;height:0.1478in" />

The **value** for **s**:

<img src="Lecture-Notes_files/media/image23.png" style="width:1.76303in;height:0.81986in" />

……

Case 2

……

Non-homogeneous second order linear ODEs

<img src="Lecture-Notes_files/media/image24.png" style="width:2.32227in;height:0.55363in" alt="文本 描述已自动生成" />

<img src="Lecture-Notes_files/media/image25.png" style="width:2.51659in;height:0.39569in" alt="文本 中度可信度描述已自动生成" />

Thm. 7.2

<img src="Lecture-Notes_files/media/image26.png" style="width:2.05634in;height:1.19796in" alt="文本, 信件 描述已自动生成" />

-P2 Variation of parameters

(((?) requires that the non-homogeneous term r(t) to be in a special form))

(?) a more general method that in principle can be applied to any equation

General Theory

<img src="Lecture-Notes_files/media/image27.png" style="width:4.94795in;height:4.95316in" />
