---
title: '【文献阅读】RoFormer: Enhanced Transformer with Rotary Position Embedding'
date: 2023-06-28 11:48:46
categories: 算法
tags: ["文献阅读", "RoFormer"]
mathjax: true
---

论文地址：

* [https://arxiv.org/abs/2104.09864v4](https://arxiv.org/abs/2104.09864v4)

论文代码：

* [https://huggingface.co/docs/transformers/model_doc/roformer](https://huggingface.co/docs/transformers/model_doc/roformer)
* [https://github.com/ZhuiyiTechnology/roformer](https://github.com/ZhuiyiTechnology/roformer)

<!-- more -->

LLaMA里用了这篇论文提出的位置编码，对这篇论文里的位置编码实现方式进行解读。RoFormer里使用的位置编码方式可以将绝对位置编码和相对位置编码融于一体。

为了简单起见，先假设$q_m$，$k_n$是所在位置分别为$m$，$n$的二维向量（这里先假设dim=2），既然是二维，那么我们就可以将它当作复数来运算。

{% note info %}
对于二维向量`[x, y]`，将其表示为复数$x+yi$。
{% endnote %}

我们知道，Attention关键之处在于向量的内积，用复数表示为：
$$\langle q_m,k_n \rangle=Re[q_mk_n^*]$$
其中$\langle \rangle$表示内积，$*$是共轭复数，$Re[]$表示取结果的实部。

{% note info %}
两个二维向量的内积，等于把它们当复数看时，一个复数与另一个复数共轭的乘积的实部。
{% endnote %}

如果将$q_m$，$k_n$分别乘以$e^{im\theta}$，$e^{in\theta}$变成$q_me^{im\theta}$，$k_ne^{in\theta}$，再代入上述内积公式，得到：
$$\langle q_me^{im\theta},k_ne^{in\theta} \rangle=Re[(q_me^{im\theta})(k_ne^{in\theta})^*]=Re[q_mk_n^*e^{i(m-n)\theta}]$$

可以看到，$q_m$，$k_n$分别乘以$e^{im\theta}$，$e^{in\theta}$的过程可以看做加入了绝对位置编码的信息（因为显示依赖绝对位置$m,n$），而经过内积之后，位置编码信息就只依赖相对位置$(m-n)$了，这样就巧妙的将绝对位置编码和相对位置编码融合到一起了。

由上述结果可知，对于位置为$n$的二维向量$[x,y]$，我们将其当作复数来运算，乘以$e^{in\theta}$，得到恒等式：
$$(x+yi)e^{in\theta}=(x \cos n\theta - y \sin n\theta) + i(x \sin n\theta + y \cos n\theta)$$

这也就是意味着，通过
$$
  \begin{pmatrix}
    x \\
    y
  \end{pmatrix}\rightarrow
  \begin{pmatrix}
    xcosn\theta-ysinn\theta \\
    xsinn\theta+ycosn\theta
  \end{pmatrix}=
  \begin{pmatrix}
    x \\
    y
  \end{pmatrix}cosn\theta+
  \begin{pmatrix}
    -y \\
    x
  \end{pmatrix}sinn\theta
$$
来赋予$[x,y]$绝对位置信息，那么在Attention运算的时候就等价于使用了相对位置编码。

由于内积满足线性叠加性，因此任意偶数维的RoPE，我们都可以表示成二维情形的拼接，即
$$
  \begin{pmatrix}
    cosm\theta_0 & -sinm\theta_0 & 0 & 0 & \cdots & 0 & 0 \\
    sinm\theta_0 & cosm\theta_0 & 0 & 0 & \cdots & 0 & 0 \\
    0 & 0 & cosm\theta_1 & -sinm\theta_1 & \cdots & 0 & 0 \\
    0 & 0 & sinm\theta_1 & cosm\theta_1 & \cdots & 0 & 0 \\
    \vdots & \vdots & \vdots & \vdots & \ddots & \vdots & \vdots \\
    0 & 0 & 0 & \cdots & 0 & cosm\theta_{d/2-1} & -sinm\theta_{d/2-1} \\
    0 & 0 & 0 & \cdots & 0 & sinm\theta_{d/2-1} & cosm\theta_{d/2-1} \\
  \end{pmatrix}
  \begin{pmatrix}
    q_0 \\
    q_1 \\
    q_2 \\
    q_3 \\
    \vdots \\
    q_{d-2} \\
    q_{d-1} \\
  \end{pmatrix}
$$

也就是说，给位置为$m$的向量$q_m$乘上矩阵$R_m$，位置为$n$的向量$k_n$乘上举证$R_n$，用变换后的$q,k$矩阵做Attention，那么Attention矩阵就会自动包含相对位置信息。

由于$R_m$矩阵的稀疏性，所以直接用矩阵乘法来实现会浪费算力，可用下述等价的方式来计算：
$$
  \begin{pmatrix}
    q_0 \\
    q_1 \\
    q_2 \\
    q_3 \\
    \vdots \\
    q_{d-2} \\
    q_{d-1} \\
  \end{pmatrix}
  \begin{pmatrix}
    cosm\theta_0 \\
    cosm\theta_0 \\
    cosm\theta_1 \\
    cosm\theta_1 \\
    \vdots \\
    cosm\theta_{d/2-1} \\
    cosm\theta_{d/2-1} \\
  \end{pmatrix}+
  \begin{pmatrix}
    -q_1 \\
    q_0 \\
    -q_3 \\
    q_2 \\
    \vdots \\
    -q_{d-1} \\
    q_{d-2} \\
  \end{pmatrix}
  \begin{pmatrix}
    sinm\theta_0 \\
    sinm\theta_0 \\
    sinm\theta_1 \\
    sinm\theta_1 \\
    \vdots \\
    sinm\theta_{d/2-1} \\
    sinm\theta_{d/2-1} \\
  \end{pmatrix}
$$
