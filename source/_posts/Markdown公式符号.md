---
title: Markdown公式符号
date: 2023-04-27 14:20:23
categories: 博客
tags: ["Markdown"]
mathjax: true
---

整理Markdown 公式编辑常用数学符号。

<!-- more -->

## 字体样式

| 显示                  | 符号                          | 字体                         |
| --------------------- | ----------------------------- | ---------------------------- |
| $ABCabc$              | `\$ABCabc\$`                    | 直接写                       |
| $\text{ABCabc}$       | `\text{ABCabc}`                 | 文本格式（内部可以再使用\$） |
| $\mathbf{ABCabc}$     | `\mathbf{ABCabc}`               | 加粗                         |
| $\boldsymbol{\alpha}$ | `\boldsymbol{\alpha}`           | 加粗（其它字符）             |
| $\mathit{ABCabc}$     | `\mathit{ABCabc}`               | 斜体                         |
| $\pmb{ABCabc}$        | `\pmb{ABCabc}`                  | 粗斜体                       |
| $\mathbb{ABCabc}$     | `\mathbb{ABCabc}或\Bbb{ABCabc}` | blackboard bold font         |
| $\mathtt{ABCabc}$     | `\mathtt{ABCabc}`               | typewriter font              |
| $\mathrm{ABCabc}$     | `\mathrm{ABCabc}`               | roman font                   |
| $\mathsf{ABCabc}$     | `\mathsf{ABCabc}`               | sans-serif font              |
| $\mathcal{ABCabc}$    | `\mathcal{ABCabc}`              | calligraphic letters         |
| $\mathscr{ABCabc}$    | `\mathscr{ABCabc}`              | script letters               |
| $\mathfrak{ABCabc}$   | `\mathfrak{ABCabc}`             | Frakur letters               |

## 希腊字母

| 小写       | 符号     | 大写       | 符号     | 小写       | 符号     | 大写       | 符号     |
| ---------- | -------- | ---------- | -------- | ---------- | -------- | ---------- | -------- |
| $\alpha$   | `\alpha`   | A          | `\Alpha`   | $\beta$    | `\beta`    | B          | `\Beta`    |
| $\gamma$   | `\gamma`   | Γ          | `\Gamma`   | $\delta$   | `\delta`   | Δ          | `\Delta`   |
| $\epsilon$ | `\epsilon` | E          | `\Epsilon` | $\zeta$    | `\zeta`    | Z          | `\Zeta`    |
| $\eta$     | `\eta`     | H          | `\Eta`     | $\theta$   | `\theta`   | Θ          | `\Theta`   |
| $\iota$    | `\iota`    | I          | `\Iota`    | $\kappa$   | `\kappa`   | K          | `\Kappa`   |
| $\lambda$  | `\lambda`  | Λ          | `\Lambda`  | $\mu$      | `\mu`      | M          | `\Mu`      |
| $\nu$      | `\nu`      | N          | `\Nu`      | $\xi$      | `\xi`      | Ξ          | `\Xi`      |
| $\pi$      | `\pi`      | Π          | `\Pi`      | $\rho$     | `\rho`     | P          | `\Rho`     |
| $\sigma$   | `\sigma`   | Σ          | `\Sigma`   | $\tau$     | `\tau`     | T          | `\Tau`     |
| $\upsilon$ | `\upsilon` | Υ          | `\Upsilon` | $\phi$     | `\phi`     | Φ          | `\Phi`     |
| $\chi$     | `\chi`     | X          | `\Chi`     | $\psi$     | `\psi`     | Ψ          | `\Psi`     |
| $\omega$   | `\omega`   | Ω          | `\Omega`   | $\omicron$ | `\omicron` | O          | `\Omicron` |

## 算术运算

| 显示        | 符号      | 显示        | 符号      | 显示      | 符号    |
| ----------- | --------- | ----------- | --------- | --------- | ------- |
| $\times$    | `\times`    | $\div$      | `\div`      | $\cdot$   | `\cdot`   |
| $<$         | `<`         | $>$         | `>`         | $\ll$     | `\ll`     |
| $\gg$       | `\gg`       | $\lll$      | `\lll`      | $\pm$     | `\pm`     |
| $\le$       | `\le或\leq` | $\ge$       | `\ge或\geq` | $\mp$     | `\mp`     |
| $\leqq$     | `\leqq`     | $\geqq$     | `\geqq`     | $\neq$    | `\neq`    |
| $\leqslant$ | `\leqslant` | $\geqslant$ | `\geqslant` | $\approx$ | `\approx` |
| $\sum$      | `\sum`      | $\prod$     | `\prod`     |           |           |

## 集合

| 显示          | 符号        | 显示        | 符号      | 显示          | 符号        |
| ------------- | ----------- | ----------- | --------- | ------------- | ----------- |
| $\complement$ | `\complement` | $\in$       | `\in`       | $\notin$      | `\notin`      |
| $\subset$     | `\subset`     | $\subseteq$ | `\subseteq` | $\subsetneq$  | `\subsetneq`  |
| $\cap$        | `\cap`        | $\cup$      | `\cup`      | $\varnothing$ | `\varnothing` |
| $\emptyset$   | `\emptyset`   |

## 逻辑运算

| 显示      | 符号          | 显示      | 符号       | 显示    | 符号        |
| --------- | ------------- | --------- | ---------- | ------- | ----------- |
| $\land$   | `\land或\wedge` | $\lor$    | `\lor或\vee` | $\lnot$ | `\lnot或\neg` |
| $\forall$ | `\forall`       | $\exists$ | `\exists`    | $\top$  | `\top`        |
| $\vdash$  | `\vdash`        | $\vDash$  | `\vDash`     | $\bot$  | `\bot`        |

## 音调

| 显示        | 符号      | 显示        | 符号      |
| ----------- | --------- | ----------- | --------- |
| $\bar a$    | `\bar a`    | $\acute{a}$ | `\acute{a}` |
| $\check{a}$ | `\check{a}` | $\grave{a}$ | `\grave{a}` |
| $\breve{a}$ | `\breve{a}` | $\dot{a}$   | `\dot{a}`   |
| $\ddot{a}$  | `\ddot{a}`  |
| $\hat{a}$   | `\hat{a}`   | $\tilde{a}$ | `\tilde{a}` |

## 上、下划线和上、下括号

| 显示                             | 符号                              |
| -------------------------------- | -------------------------------- |
| $\dot{h}$                        | `\dot{h}`                        |
| $\vec{h}$                        | `\vec{h}`                        |
| $\overline{h i j}$               | `\overline{h i j}`               |
| $\underline{h i j}$              | `\underline{h i j}`              |
| $\widehat{h i j}$                | `\widehat{h i j}`                |
| $\widetilde{h i j}$              | `\widetilde{h i j}`              |
| $\underbrace{a+b+\cdots+z}_{10}$ | `\underbrace{a+b+\cdots+z}_{10}` |
| $\overbrace{a+b+\cdots+z}^{10}$  | `\overbrace{a+b+\cdots+z}^{10}`  |

## 阵列

语法： `$$\begin{array}…\end{array}$$`，`r`右对齐，`l`左对齐，`c`居中，`|`垂直线，`\hline`横线，`\\`换行，元素之间以`&`间隔。

示例：

```markdown
$$
  \begin{array}{c|lcr}
    n & \text{Left} & \text{Center} & \text{Right} \\
    \hline
    1 & 0.24 & 1 & 125 \\
    2 & -1 & 189 & -8 \\
    3 & -20 & 2000 & 1+10i
  \end{array}
$$
```

$$
  \begin{array}{c|lcr}
    n & \text{Left} & \text{Center} & \text{Right} \\
    \hline
    1 & 0.24 & 1 & 125 \\
    2 & -1 & 189 & -8 \\
    3 & -20 & 2000 & 1+10i
  \end{array}
$$

## 矩阵

语法： `$$\begin{matrix}…\end{matrix}$$`，每行以`\\`结尾，元素之间以`&`间隔。

示例：

```markdown
$$
  \begin{matrix}
    1 & x & x^2 \\
    1 & y & y^2 \\
    1 & z & z^2 \\
  \end{matrix}
$$
```

$$
  \begin{matrix}
    1 & x & x^2 \\
    1 & y & y^2 \\
    1 & z & z^2 \\
  \end{matrix}
$$

***添加括号：***

* `pmatrix`: ( )
* `bmatrix`: [ ]
* `Bmatrix`: { }
* `vmatrix`: | |
* `Vmatrix`: ‖ ‖

***添加省略号：***

```markdown
$$
  \begin{pmatrix}
    1 & a_1^2 & a_1^2 & \cdots & a_1^2 \\
    1 & a_2^2 & a_2^2 & \cdots & a_2^2 \\
    \vdots & \vdots & \vdots & \ddots & \vdots \\
    1 & a_n^2 & a_n^2 & \cdots & a_n^2 \\
  \end{pmatrix}
$$
```

$$
  \begin{pmatrix}
    1 & a_1^2 & a_1^2 & \cdots & a_1^2 \\
    1 & a_2^2 & a_2^2 & \cdots & a_2^2 \\
    \vdots & \vdots & \vdots & \ddots & \vdots \\
    1 & a_n^2 & a_n^2 & \cdots & a_n^2 \\
  \end{pmatrix}
$$

***水平增广矩阵： 使用阵列语法***

```markdown
$$ 
  \left[
    \begin{array}{cc|c}
      1&2&3\\
      4&5&6
    \end{array}
  \right] 
$$
```

$$
  \left[
    \begin{array}{cc|c}
      1&2&3\\
      4&5&6
    \end{array}
  \right]
$$

***垂直增广矩阵：***

```markdown
$$
  \begin{pmatrix}
    a & b\\
    c & d\\
    \hline
    1 & 0\\
    0 & 1
  \end{pmatrix}
$$
```

$$
  \begin{pmatrix}
    a & b\\
    c & d\\
    \hline
    1 & 0\\
    0 & 1
  \end{pmatrix}
$$

***在行内插入矩阵：***
`$\big( \begin{smallmatrix} a & b \\ c & d \end{smallmatrix} \big)$`

## 等式对齐

语法： `\begin{align}…\end{align}`，每行以`\\`结尾，元素之间以`&`间隔。

示例：

```markdown
$$
  \begin{align}
    \sqrt{37} & = \sqrt{\frac{73^2-1}{12^2}} \\
     & = \sqrt{\frac{73^2}{12^2}\cdot\frac{73^2-1}{73^2}} \\ 
     & = \sqrt{\frac{73^2}{12^2}}\sqrt{\frac{73^2-1}{73^2}} \\
     & = \frac{73}{12}\sqrt{1 - \frac{1}{73^2}} \\ 
     & \approx \frac{73}{12}\left(1 - \frac{1}{2\cdot73^2}\right)
  \end{align}
$$
```

$$
  \begin{align}
    \sqrt{37} & = \sqrt{\frac{73^2-1}{12^2}} \\
     & = \sqrt{\frac{73^2}{12^2}\cdot\frac{73^2-1}{73^2}} \\
     & = \sqrt{\frac{73^2}{12^2}}\sqrt{\frac{73^2-1}{73^2}} \\
     & = \frac{73}{12}\sqrt{1 - \frac{1}{73^2}} \\
     & \approx \frac{73}{12}\left(1 - \frac{1}{2\cdot73^2}\right)
  \end{align}
$$

## 分段函数

语法： `\begin{cases}…\end{cases}`，每行以`\\`结尾，元素之间以`&`间隔。

示例：

```markdown
$$
  f(n) =
    \begin{cases}
      n/2,  & \text{if $n$ is even} \\
      3n+1, & \text{if $n$ is odd}
    \end{cases}
$$
```

$$
  f(n) =
    \begin{cases}
      n/2,  & \text{if $n$ is even} \\
      3n+1, & \text{if $n$ is odd}
    \end{cases}
$$

```markdown
$$  
  \left.
    \begin{array}{l}
    \text{if $n$ is even:}&n/2\\
    \text{if $n$ is odd:}&3n+1
    \end{array}
  \right\}
  =f(n)
$$
```

$$  
  \left.
    \begin{array}{l}
    \text{if $n$ is even:}&n/2\\
    \text{if $n$ is odd:}&3n+1
    \end{array}
  \right\}
  =f(n)
$$
