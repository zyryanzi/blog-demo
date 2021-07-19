---
title: markdown-mathjax
date: 2021-06-07 10:42:04
tags:
    - hexo
    - next
    - markdown
    - mathjax

categories:
    - 计算机科学
    - hexo

mathjax: true
---

*上一次在抄别人文章的时候，发现数学公式没法拷贝，于是学习了一下在`hexo-next`中如何使用`mathjax`*
<!--more-->

### 配置
首先想要使用mathjax, 要打开在next主题中的配置项，如果是别的主题，应该也有类似的配置

如果`every_page`是true，就是对所有页面应用，否则还要在需要生效的页面加上一条配置，如下

### 格式
mathjax 有行内公式的格式，也有单独一行公式的格式，在视觉上区别不大，只是为了方便表达。
行内格式：

    文字文字文字$这是公式$文字文字文字（是用单个$符号包裹起来的）

单独一行：

    文字文字文字
    $$
    这是公式
    $$
    文字文字文字
    （是用两个$符号包裹起来的）

这里举一个例子：

    $\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$

实现的效果就是这样的$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$
如果是单行表示，如下：
$$
\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}
$$

**LaTax语法一般都是以`\`开始的，经常用到的如下：**

### 修饰
#### 上下标
上下标修饰相对简单，使用`^`和`_`就可以实现，如上例。

#### 矢量
矢量修饰使用`\vec`, 如`$\vec a$`就会有这样的效果$\vec a$，
也可以使用`\overrightarrow{xy}`, 将呈现$\overrightarrow{xy}$。

#### 字体
`\mathtt{A}` - $\mathtt{A}$
`\mathbb{A}` - $\mathbb{A}$
`\mathtt{A}` - $\mathsf{A}$
等等……

#### 分组
`{}`具有分组效果，例如
`10^10` - $10^10$
`10^{10}` - $10^{10}$

#### 括号
小括号、中括号可以直接写，
尖括号要这样表示：`\langle \rangle` - $\langle \rangle$，
如果需要括号大小高矮与内容相适应可以使用`\left(括号中内容\right)`，例如：
`\left(\frac{x}{y}\right)` - $\left(\frac{x}{y}\right)$
`(\frac{x}{y})` - $(\frac{x}{y})$

#### 空格
LaTax语法本身会忽略空格，可以使用专门的表达式来实现空格效果：
`a\ b` - $a\ b$（一个空格），
`a\quad b` - $a\quad b$（四个空格）


#### 字母
|显示|表达式|显示|表达式|
|---|---|---|---|
|α|\alpha|β|\beta|
|γ|\gamma|δ|\delta|
|ε|\epsilona|ζ|\zet|
|η|\eta|θ|\theta|
|ι|\iota|κ|\kappa|
|λ|\lambda|μ|\mu|
|ν|\nu|ξ|\xi|
|π|\pi|ρ|\rho|
|σ|\sigma|τ|\tau|
|υ|\upsilon|φ|\phi|
|χ|\chi|ψ|\psi|
|ω|\omega|

*若需要大写希腊字母，将命令首字母大写即可*
*若需要斜体希腊字母，将命令前加上var前缀即可*

### 表达式
#### 求和、极限、积分
`\sum_{i=1}^n{a_i}` - $\sum_{i=1}^n{a_i}$
`\lim_{x \to 0}` - $\lim_{x \to 0}$
`\int_0^\infty{fxdx}` - $\int_0^\infty{fxdx}$

#### 分式、根式
`\frac{x}{y}` - $\frac{x}{y}$
`\sqrt{x}{y}` - $\sqrt{x}{y}$

#### 特殊函数
`\sin x` - $\sin x$
`\ln x` - $\ln x$
`max(A,B,C)` - $\max(A,B,C)$

#### 常用符号
这里太多了，回头再列表
\lt \gt \le \leq \leqq \leqslant \ge \geq \geqq \geqslant \neq <, >, ≤, ≤, ≦, ⩽, ≥, ≥, ≧, ⩾, ≠. You can use \not to put a slash through almost anything: \not\lt ≮ but it often looks bad.
\times \div \pm \mp ×, ÷, ±, ∓. \cdot is a centered dot: x⋅y
\cup \cap \setminus \subset \subseteq \subsetneq \supset \in \notin \emptyset \varnothing ∪, ∩, ∖, ⊂, ⊆, ⊊, ⊃, ∈, ∉, ∅, ∅
{n+1 \choose 2k} or \binom{n+1}{2k} (n+12k)
\to \rightarrow \leftarrow \Rightarrow \Leftarrow \mapsto →, →, ←, ⇒, ⇐, ↦
\land \lor \lnot \forall \exists \top \bot \vdash \vDash ∧, ∨, ¬, ∀, ∃, ⊤, ⊥, ⊢, ⊨
\star \ast \oplus \circ \bullet ⋆, ∗, ⊕, ∘, ∙
\approx \sim \simeq \cong \equiv \prec \lhd \therefore ≈, ∼, ≃, ≅, ≡, ≺, ⊲, ∴
\infty \aleph_0 ∞ℵ0 \nabla \partial ∇, ∂ \Im \Re I, R
For modular equivalence, use \pmod like this: a\equiv b\pmod n a≡b(modn).
For the binary mod operator, use \bmod like this: a\bmod 17 amod17.
Avoid using \mod, as it produces extra space: compare the above with a\mod 17 amod17.
\ldots is the dots in a1,a2,…,an \cdots is the dots in a1+a2+⋯+an
Script lowercase l is \ell ℓ.

### 特殊符号
|显示|表达式|显示|表达式|
|---|---|---|---|
|$\infty$|`\infity`|$\cup$|`\cup`|
|$\cap$|`\cap`|$\subset$|`\supset`|
|$\subseteq$|`\subseteq`|$\supset$|`\supset`|
|$\in$|`\in`|$\notin$|`\notin`|
|$\varnothing$|`\varnothing`|$\forall$|`\forall`|
|$\exists$|`\exists`|$\lnot$|`\lnot`|
|$\nabla$|`\nabla`|$\partial$|`\partial`|

### 矩阵
需要`matrix`环境，起始标记`\begin{matrix}`，结束标记`\end{matrix}`，
每一行末尾标记`\\\\`，行间元素之间以`&`分隔，举例:

    $$
        \begin{matrix}
        1 & x & x^2 \\\\
        1 & y & y^2 \\\\
        1 & z & z^2 \\\\
        \end{matrix}
    $$

显示为：

$$
    \begin{matrix}
    1 & x & x^2 \\\\
    1 & y & y^2 \\\\
    1 & z & z^2 \\\\
    \end{matrix}
$$

#### 矩阵边框
在起始、结束标记处用下列词替换 matrix
`pmatrix` ：小括号边框
`bmatrix` ：中括号边框
`Bmatrix` ：大括号边框
`vmatrix` ：单竖线边框
`Vmatrix` ：双竖线边框

#### 省略元素
横省略号：`\cdots`
竖省略号：`\vdots`
斜省略号：`\ddots`
例如：

    $$
        \begin{bmatrix}
        1 & a & {\cdots} & a^n \\\\
        1 & b & {\cdots} & b^n \\\\
        {\vdots} & {\vdots} & {\ddots} & {\vdots} \\\\
        1 & z & {\cdots} & z^n \\\\
        \end{bmatrix}
    $$

显示为：
$$
    \begin{bmatrix}
    1 & a & {\cdots} & a^n \\\\
    1 & b & {\cdots} & b^n \\\\
    {\vdots} & {\vdots} & {\ddots} & {\vdots} \\\\
    1 & z & {\cdots} & z^n \\\\
    \end{bmatrix}
$$

### 阵列
需要`array`环境，起始、结束以`{array}`声明，
对齐方式：在`{array}`后以{}逐行统一声明，l--左对齐，c--居中，r--右对齐，
分隔线：竖直线在生命对齐方式时插入`|`建立竖直线；使用`\line`插入水平线，
例如：

    $$
        \begin{array}{c|lll}
        {↓} & {a} & {b} & {c}\\\\
        \hline
        {R_1} & {c} & {b} & {a}\\\\
        {R_2} & {b} & {c} & {c}\\\\
        \end{array}
    $$

显示为：

$$
    \begin{array}{c|lll}
    {↓} & {a} & {b} & {c}\\\\
    \hline
    {R_1} & {c} & {b} & {a}\\\\
    {R_2} & {b} & {c} & {c}\\\\
    \end{array}
$$

### 方程组
需要cases环境：起始、结束处以{cases}声明，例如

    $$
        \begin{cases}
        a_1x+b_1y+c_1z=d_1\\\\
        a_2x+b_2y+c_2z=d_2\\\\
        a_3x+b_3y+c_3z=d_3\\\\
        \end{cases}
    $$

显示为：
$$
    \begin{cases}
    a_1x+b_1y+c_1z=d_1\\\\
    a_2x+b_2y+c_2z=d_2\\\\
    a_3x+b_3y+c_3z=d_3\\\\
    \end{cases}
$$

这里是一个很好玩的网站，画出来，就可以识别出来，*http://detexify.kirelabs.org/classify.html*