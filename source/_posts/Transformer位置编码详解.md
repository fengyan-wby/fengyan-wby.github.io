---
title: Transformer位置编码详解
date: 2023-02-27 14:10:19
categories: 算法
tags: Transformer
mathjax: true
---

不同于RNN、CNN等模型，对于Transformer模型来说，位置编码的加入是必不可少的，因为纯粹的Attention模块无法捕捉到输入的位置信息，即无法区分不同位置的Token。为此，我们大体有两种选择：

1. 将位置信息融入到输入中，这构成了`绝对位置编码`的一般做法
2. 调整Attention模块，使其有能力分辨不同位置的Token，这构成了相对位置编码的一般做法。

<!-- more -->

## 绝对位置编码

形式上来看，绝对位置编码是相对简单的一种方案。一般来说，绝对位置编码会加到输入中：在输入的第$\textcolor{blue}{k}$个向量$\textcolor{blue}{x_k}$中加入位置向量$\textcolor{blue}{p_k}$变成$\textcolor{blue}{x_k + p_k}$，其中$\textcolor{blue}{p_k}$只依赖于位置编号$\textcolor{blue}{k}$。

### 训练式

论文地址：

* [BERT](https://arxiv.org/abs/1810.04805)

论文地址：

* [https://github.com/google-research/bert](https://github.com/google-research/bert)

很显然，绝对位置编码的一个最朴素方案是不特意去设计什么，而是直接`将位置编码当作可训练参数`，比如最大长度为512，编码维度为768，那么就初始化一个$512 \times 768$的矩阵作为位置向量，让它随着训练过程更新。现在的BERT、GPT等模型用的就是这种位置编码。

对于这种训练式的绝对位置编码，缺点是没有外推性，即如果预训练最大长度为512，那么在预测的时候最多就只能处理长度为512的句子，再长就处理不了了。

```python
if use_position_embeddings:
    assert_op = tf.assert_less_equal(seq_length, max_position_embeddings)
    with tf.control_dependencies([assert_op]):
      full_position_embeddings = tf.get_variable(
          name=position_embedding_name,
          shape=[max_position_embeddings, width],
          initializer=create_initializer(initializer_range))

      position_embeddings = tf.slice(full_position_embeddings, [0, 0], [seq_length, -1])
      num_dims = len(output.shape.as_list())

      position_broadcast_shape = []
      for _ in range(num_dims - 2):
        position_broadcast_shape.append(1)
      position_broadcast_shape.extend([seq_length, width])
      position_embeddings = tf.reshape(position_embeddings, position_broadcast_shape)
      output += position_embeddings
```

### 三角式

论文地址：

* [Attention is All You Need](https://arxiv.org/abs/1706.03762)

论文代码：

* [https://github.com/tensorflow/tensor2tensor](https://github.com/tensorflow/tensor2tensor)

三角函数位置编码，一般也称为Sinusoidal位置编码，是Google论文《Attention is All You Need》所提出来的一个位置编码方法。
$$
\textcolor{blue}{
p_{k,2i}=sin(k/10000^{2i/d}) \\
p_{k,2i+1}=cos(k/10000^{2i/d})
}
$$
其中$\textcolor{blue}{p_{k,2i},p_{k,2i+1}}$分别是位置$\textcolor{blue}{k}$的编码向量的第$\textcolor{blue}{2i,2i+1}$个分量，$\textcolor{blue}{d}$是位置向量的维度。
三角函数式位置编码的特点是有显式的生成规律，因此可以期望它拥有一定的外推性。

```python
for pos in range(vocab_size):
    for i in range(depth // 2):
      embeddings_table[pos, 2 * i] = np.sin(pos / np.power(10000, 2 * i / depth))
      embeddings_table[pos, 2 * i + 1] = np.cos(pos / np.power(10000, 2 * i / depth))
```

### 递归式

论文地址：

* [https://arxiv.org/abs/2003.09229](https://arxiv.org/abs/2003.09229)

论文代码：

* [https://github.com/rtqichen/torchdiffeq](https://github.com/rtqichen/torchdiffeq)

原则上来说，RNN模型不需要位置编码，它在结构上就自带了学习到位置信息的可能性。因此，如果在输入后面先接一层RNN，然后再接Transformer，那么理论上就不需要加位置编码了。同理，我们也可以用RNN模型来学习一种绝对位置编码，比如从一个向量$p_0$出发，通过递归格式$p_{k+1}=f(p_k)$来得到各个位置的编码向量。

理论上来说，基于递归模型的位置编码也具有比较好的外推性，同时它也比三角函数式的位置编码有更好的灵活性。但是，递归式形式的位置编码牺牲了一定的并行性，可能会带来速度瓶颈。

### 相乘式

之前说到，输入$\textcolor{blue}{x_k}$与绝对位置编码$\textcolor{blue}{p_k}$的组合方式一般是$\textcolor{blue}{x_k + p_k}$，那有没有“不一般”的组合方式呢？比如$\textcolor{blue}{x_k⊗p_k}$（逐位相乘）？我们平时在搭建模型的时候，对于融合两个向量有多种方式，相加、相乘甚至拼接都是可以考虑的。可能大家默认选择相加是因为向量的相加具有比较鲜明的几何意义，但是对于深度学习模型来说，这种几何意义其实没有什么实际的价值。

## 相对位置编码

相对位置并没有完整建模每个输入的位置信息，而是在算Attention的时候考虑当前位置与被Attention的位置的相对距离，由于自然语言一般更依赖于相对位置，所以相对位置编码通常也有着优秀的表现。

### 经典式

论文地址：

* [Self-Attention with Relative Position Representations](https://arxiv.org/abs/1803.02155)
* [NEZHA](https://arxiv.org/abs/1909.00204)

论文代码：

* [https://github.com/tensorflow/tensor2tensor](https://github.com/tensorflow/tensor2tensor)
* [https://github.com/huawei-noah/Pretrained-Language-Model/tree/master/NEZHA-TensorFlow](https://github.com/huawei-noah/Pretrained-Language-Model/tree/master/NEZHA-TensorFlow)

相对位置编码起源于Google的论文《Self-Attention with Relative Position Representations》，华为开源的NEZHA模型也用到了这种位置编码，后面各种相对位置编码变体基本也是在此基础上的简单修改。
一般认为，相对位置编码是由绝对位置编码启发而来，考虑一般的带绝对位置编码的Attention：
$$
\textcolor{blue}{
\left\{
\begin{aligned}
q_i &=(x_i+p_i)W_Q \\
k_j &=(x_j+p_j)W_K \\
a_{i,j} &=softmax(q_ik_j^\top) \\
o_i &= \sum_ja_{i,j}v_j
\end{aligned}
\right.
}
$$

这里初步展开$\textcolor{blue}{q_ik_j}$:
$$\textcolor{blue}{q_ik_j^{\top}=(x_i + p_i)W_QW_K^{\top}(x_j+p_j)^\top=(x_iW_Q + p_iW_Q)(W_K^\top x_j^\top + W_K^\top p_j^\top) \tag{1}}$$

为了引入相对位置信息，Google把第一项位置去掉，第二项$\textcolor{blue}{p_jW_K}$改为二元位置向量$\textcolor{blue}{R_{i,j}^K}$，变成：
$$\textcolor{blue}{a_{i,j}=softmax(x_iW_Q(x_jW_K+\textcolor{green}{R_{i,j}^K})^\top)}$$
以及$\textcolor{blue}{i_i=\sum_ja_{i,j}v_j=\sum_ja_{i,j}(x_jW_V+p_jW_V)}$中的$\textcolor{blue}{p_jW_V}$换成$\textcolor{blue}{R_{i,j}^V}$:
$$\textcolor{blue}{o_i=\sum_ja_{i,j}(x_jW_V+\textcolor{green}{R_{i,j}^V})}$$

所谓相对位置，是将本来依赖于二元坐标$(i,j)$的向量$\textcolor{blue}{R_{i,j}^K,R_{i,j}^V}$改为只依赖于相对距离$(i-j)$，并且通常来说会进行截断，以适应任意的距离：
$$
\textcolor{blue}{
R_{i,j}^K=p_k[clip(i-j,p_{min}, p_{max})]
R_{i,j}^V=p_v[clip(i-j,p_{min}, p_{max})]
}
$$
这样一来，只需要有限个位置编码，就可以表达出任意长度的相对位置（因为进行了截断），不管相对位置编码式可训练式的还是三角函数式的，都可以达到处理任意长度文本的需求。

```python
attention_scores = tf.matmul(query_layer, key_layer, transpose_b=True)
relative_position_embeddings = None
if use_relative_position_embeddings:
    assert from_seq_length == to_seq_length
    # `relations_key` = [F, T, H]
    relative_position_embeddings = self.get_relative_position_embeddings(
        from_seq_length, size_per_head,
        max_relative_position_embeddings=max_relative_position_embeddings,
        use_one_hot_embeddings=use_one_hot_embeddings,
        position_embedding_name=position_embedding_name)
    key_position_scores = tf.einsum("bnfh,fth->bnft", query_layer, relative_position_embeddings)
    attention_scores = attention_scores + key_position_scores



# `context_layer` = [B, N, F, H]
context_layer = tf.matmul(attention_probs, value_layer)
if use_relative_position_embeddings:
    assert from_seq_length == to_seq_length
    value_position_scores = tf.einsum("bnft,fth->bnfh", attention_probs, relative_position_embeddings)
    context_layer = context_layer + value_position_scores
```

### XLNET式

论文地址：

* [XLNET](https://arxiv.org/abs/1901.02860)

论文代码：

* [https://github.com/kimiyoung/transformer-xl](https://github.com/kimiyoung/transformer-xl)

XLNET式位置编码其实源自Transformer-XL的论文《Transformer-XL: Attentive Language Models Beyond a Fixed-Length Context》，只不过因为使用了Transformer-XL架构的XLNET模型并在一定程度上超过了BERT后，Transformer-XL才算广为人知，因此这种位置编码通常也被冠以XLNET之名。

XLNET式的位置编码源于对上述（公式(1)）$q_ik_j^\top$的完全展开：
$$
\textcolor{blue}{
q_ik_j^\top=x_iW_QW_K^{\top}x_j^{\top} + x_iW_QW_K^{\top}p_j^{\top} + p_iW_QW_K^{\top}x_j^{\top} + p_iW_QW_K^{\top}p_j^{\top} \tag{2}
}
$$

Transformer-XL的做法很简单，直接将$\textcolor{blue}{p_j}$替换为相对位置向量$R_{i-j}$，至于两个$\textcolor{blue}{p_i}$，则替换为两个可训练的向量$\textcolor{blue}{u,v}$：
$$
\textcolor{blue}{
q_ik_j^\top=x_iW_QW_K^{\top}x_j^{\top} + x_iW_QW_K^{\top}\textcolor{green}{R_{i-j}^{\top}} + \textcolor{red}{u}W_QW_K^{\top}x_j^{\top} + \textcolor{red}{v}W_QW_K^{\top}\textcolor{green}{R_{i-j}^{\top}} \tag{2}
}
$$
该编码方式中的$\textcolor{blue}{R_{i-j}}$没有进行截断，而是直接用了Sinusoidal式的生成方案。此外，$\textcolor{blue}{v_j}$上的位置偏置就直接去掉了，即直接令$\textcolor{blue}{o_i=\sum_ja_{i,j}x_jW_V}$。
`似乎从这个工作开始，后面的相对位置编码都只加到Attention矩阵上去，而不加到vj上去了。`

### T5式

论文地址：

* [T5](https://arxiv.org/abs/1910.10683)
* [TUPE](https://arxiv.org/abs/2006.15595)

论文代码：

* [https://github.com/google-research/text-to-text-transfer-transformer](https://github.com/google-research/text-to-text-transfer-transformer)
* [https://github.com/guolinke/TUPE](https://github.com/guolinke/TUPE)

T5模型出自文章《Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer》，里面用到了一种更简单的相对位置编码。仍然将$q_ik_j^\top$的完全展开：
$$
\textcolor{blue}{
q_ik_j^\top=x_iW_QW_K^{\top}x_j^{\top} + x_iW_QW_K^{\top}p_j^{\top} + p_iW_QW_K^{\top}x_j^{\top} + p_iW_QW_K^{\top}p_j^{\top}
}
$$
如果分析每一项的含义，那么可以理解为`“输入-输入”`、`“输入-位置”`、`“位置-输入”`、`位置-位置`四项注意力的结合。如果我们认为输入信息与位置信息应该式独立的，那么它们就不应该有过多的交互，所以`“输入-位置”`、`“位置-输入”`这两项可以删掉，而`位置-位置`这一项实际上是一个只依赖于(i,j)的量，我们可以直接将它作为参数训练出来，则上式可以简化为：
$$
\textcolor{blue}{
x_iW_QW_K^{\top}x_j^{\top} + \textcolor{green}{\beta_{i,j}}
}
$$
说白了，T5的相对位置编码仅仅是在Attention矩阵的基础上加一个可以训练的偏置项。和XLNET一样，对于$\textcolor{blue}{v_j}$上的位置偏置被直接去掉了。  
比较别致的是，不同于常规位置编码将$\textcolor{blue}{\beta_{i,j}}$视为(i-j)的函数并进行截断的做法，T5对相对位置进行了一个分桶的操作。具体的映射代码，可以看源码。这个设计的思路其实也很直观，就是比较邻近的位置（0～7），我们需要比较得精细一些，所以给它们都分配一个独立的位置编码，至于稍远的位置（比如8～11），我们不用区分得太清楚，所以它们可以共用一个位置编码，距离越远，共用的范围就可以越大，直到达到指定范围再clip。

### DeBERTa式

论文地址：

* [https://arxiv.org/abs/2006.03654](https://arxiv.org/abs/2006.03654)

论文代码：

* [https://github.com/microsoft/DeBERTa](https://github.com/microsoft/DeBERTa)

DeBERTa来自论文《DeBERTa: Decoding-enhanced BERT with Disentangled Attention》。
其实DeBERTa的主要改进也是在位置编码上，同样还是从$q_ik_j^\top$的完全展开出发，T5是干脆去掉了第2、3项，只保留第4项并替换为相对位置编码，而DeBERTa则刚刚相反，它扔掉了第4项，保留第2、3项并且替换为相对位置编码。
$$
\textcolor{blue}{
q_ik_j^\top=x_iW_QW_K^{\top}x_j^{\top} + x_iW_QW_K^{\top}\textcolor{green}{R_{i,j}^{\top}} + \textcolor{green}{R_{j,i}}W_QW_K^{\top}x_j^{\top}
}
$$

### 复数式

论文地址：

* [https://arxiv.org/abs/2104.09864v4](https://arxiv.org/abs/2104.09864v4)

论文代码：

* [https://huggingface.co/docs/transformers/model_doc/roformer](https://huggingface.co/docs/transformers/model_doc/roformer)
* [https://github.com/ZhuiyiTechnology/roformer](https://github.com/ZhuiyiTechnology/roformer)

LLaMA以及很多大模型都用了这篇论文提出的位置编码，RoFormer里使用的位置编码方式可以将绝对位置编码和相对位置编码融于一体。

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

由于内积满足线性叠加性，因此任意偶数维的RoPE，我们都可以表示成二维情形的拼接，即将矩阵$R_m$表示为如下形式：
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

其中论文中固定取$\theta_i=10000^{-2i/d}$，当然也可以将$\theta_i$作为可训练参数进行训练。

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
