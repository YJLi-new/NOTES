# Biot–Savart: field on the axis of a circular loop

如何从 \overrightarrow{\boldsymbol{B}}=\frac{\mu_{0}}{4 \pi} \frac{q \overrightarrow{\boldsymbol{v}} \times \hat{\boldsymbol{r}}}{r^{2}} 推导出 B = \frac{\mu_0 I R^2}{2(R^2 + d^2)^{\frac{3}{2}}}？

为了从 \overrightarrow{\boldsymbol{B}}=\frac{\mu_{0}}{4 \pi} \frac{q \overrightarrow{\boldsymbol{v}} \times \hat{\boldsymbol{r}}}{r^{2}} 推导出 B = \frac{\mu_0 I R^2}{2(R^2 + d^2)^{\frac{3}{2}}}，我们可以使用毕奥-萨伐尔定律和电流密度的定义。

毕奥-萨伐尔定律规定，由携带电流 I 的电流元素 d\overrightarrow{\boldsymbol{l}} 在点 P 处产生的磁场 \overrightarrow{\boldsymbol{B}} 可以表示为：

\overrightarrow{\boldsymbol{B}}(P) = \frac{\mu_{0}}{4 \pi} \int \frac{I , d\overrightarrow{\boldsymbol{l}} \times \hat{\boldsymbol{r}}}{r^{2}}

其中 \hat{\boldsymbol{r}} 是从电流元素 d\overrightarrow{\boldsymbol{l}} 指向点 P 的单位向量，r 是它们之间的距离。

现在，考虑一个半径为 R 的圆形回路，携带电流 I。我们想要找到回路轴上距离回路中心 d 的点的磁场。

使用电流密度的定义，我们可以将电流 I 表示为电流密度 \overrightarrow{\boldsymbol{J}} 在回路截面积上的积分：

I = \int \overrightarrow{\boldsymbol{J}} \cdot d\overrightarrow{\boldsymbol{A}}

对于半径为 R 的圆形回路上的均匀电流密度 \overrightarrow{\boldsymbol{J}}，我们有：

I = \overrightarrow{\boldsymbol{J}} \cdot \pi R^2

这给出了：

\overrightarrow{\boldsymbol{J}} = \frac{I}{\pi R^2} \hat{\boldsymbol{\phi}}

其中 \hat{\boldsymbol{\phi}} 是方位角方向上的单位向量。

现在，考虑到离圆形回路中心 d 的点 P，我们可以选择一个坐标系，使得点 P 在 z 轴上，圆形回路位于 xy 平面上。

由于圆形回路关于 z 轴对称，因此所有电流元素在点 P 处产生的磁场将沿着 z 方向。因此，我们只需对回路上的磁场 z 分量进行积分，即可得到点 P 处的总磁场。

由圆形回路上的电流元素 d\overrightarrow{\boldsymbol{l}} 在点 P 处产生
