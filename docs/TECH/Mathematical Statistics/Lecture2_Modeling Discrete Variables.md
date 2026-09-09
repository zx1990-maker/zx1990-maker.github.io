---
title: "离散变量建模：从概率质量函数到极大似然估计"
description: "用直观例子理解离散随机变量、经验概率质量函数、参数模型、极大似然估计，以及训练集与测试集。"
tags: [概率论, 数理统计, 离散随机变量, 极大似然估计]
---

# 离散变量建模：从概率质量函数到极大似然估计

> 本文根据课程笔记 *Modeling Discrete Variables* 整理。目标不是只记公式，而是理解一条完整的统计建模链路：**怎样描述不确定性，怎样从数据估计分布，怎样拟合模型，以及怎样判断模型能否推广到新数据。**

## 1. 什么是离散随机变量？

很多现实问题的结果只能取一个个分开的值，例如：

- 一节课来了多少名学生；
- 一场足球比赛进了多少球；
- 旧金山一年发生多少次地震；
- 连续命中多少次罚球后第一次失手。

这类数量可以用**离散随机变量（discrete random variable）**表示。

### 1.1 确定性变量与随机变量

- **确定性变量（deterministic variable）**表示固定但可能尚未写明的数值。例如 $x=3$。
- **随机变量（random variable）**表示结果尚不确定的数值。例如掷骰子的点数 $\tilde X$。

随机变量不能在观测前被简单地说成“等于 3”。更准确的表达是：

> 随机变量 $\tilde X$ 取值为 3 的概率是 0.5。

从严格数学角度看，随机变量是把概率空间中的每个基本结果 $\omega$ 映射成数值的函数：

\[
\tilde X:\Omega\to\mathcal A,
\]

其中 $\Omega$ 是**样本空间（sample space）**，$\mathcal A$ 是 $\tilde X$ 的可能取值集合。若 $\mathcal A$ 是有限集或可数集，$\tilde X$ 就是离散随机变量。

从数据科学角度，也可以把它理解为：一个不确定的离散量，其各种可能取值用概率描述，而这些概率通常需要从数据中估计。

---

## 2. 概率质量函数：离散分布的完整说明书

描述离散随机变量最核心的工具是**概率质量函数（probability mass function, PMF）**。

对离散随机变量 $\tilde X$，其 PMF 定义为

\[
p_{\tilde X}(x)
:=\mathbb P(\tilde X=x)
=\mathbb P\bigl(\{\omega:\tilde X(\omega)=x\}\bigr).
\]

它回答的问题是：**随机变量取到某个具体值 $x$ 的概率是多少？**

例如，若 $\tilde X$ 是一枚公平六面骰子的点数，那么

\[
p_{\tilde X}(x)=\frac16,\qquad x\in\{1,2,3,4,5,6\}.
\]

一个合法的 PMF 必须满足两个条件：

1. **非负性（nonnegativity）**

   \[
   p_{\tilde X}(x)\ge 0.
   \]

2. **归一化（normalization）**，即所有可能取值的概率之和为 1

   \[
   \sum_{x\in\mathcal A}p_{\tilde X}(x)=1.
   \]

这里的 “mass” 可以理解为把总量为 1 的“概率质量”分配到不同离散点上。

### 易错点：PMF 不是概率密度函数

- 离散变量使用**概率质量函数（PMF）**，单点概率 $P(\tilde X=x)$ 可以大于 0。
- 连续变量常使用**概率密度函数（probability density function, PDF）**，密度值本身不是单点概率。

---

## 3. 不预设分布形状：经验概率质量函数

假设我们观察到数据

\[
X=\{x_1,x_2,\ldots,x_n\},
\]

最直接的想法是：某个值出现得越频繁，就给它越大的概率。由此得到**经验概率质量函数（empirical probability mass function / empirical PMF）**：

\[
p_X(a):=\frac1n\sum_{i=1}^{n}\mathbf 1(x_i=a).
\]

其中 $\mathbf 1(x_i=a)$ 是**指示函数（indicator function）**：

\[
\mathbf 1(x_i=a)=
\begin{cases}
1,&x_i=a,\\
0,&x_i\ne a.
\end{cases}
\]

因此，经验 PMF 本质上就是

\[
p_X(a)=\frac{\text{数据中等于 }a\text{ 的次数}}{\text{数据总数}}.
\]

例如数据为

\[
1,2,1,1,2,1,
\]

那么 1 出现了 4 次，共有 6 个观测，所以

\[
p_X(1)=\frac46=\frac23.
\]

经验 PMF 确实是一个合法 PMF，因为每个频率都非负，而且所有类别的出现次数相加恰好为 (n)，故概率和为 1。

### 3.1 为什么叫非参数模型？

经验 PMF 是一种**非参数估计（nonparametric estimation）**。这里的“非参数”并不是说完全没有需要估计的数，而是说：

> 模型没有预先规定一个由少量参数控制的固定分布形状。

如果随机变量可能取 55 个值，经验 PMF 基本上需要分别估计这 55 个位置的概率；而一个参数模型可能只需估计 1 个参数。

### 3.2 优点与风险

- 优点：非常灵活，能贴近训练数据中的复杂形状。
- 风险：样本较少时，偶然波动会被当成真实规律，即产生**过拟合（overfitting）**。
- 未在训练数据中出现的取值会被赋予概率 0，这未必符合真实世界。

---

## 4. 参数模型：用假设换取更稳定的估计

课程用 NBA 球员 Kevin Durant 的连续罚球数据举例。目标是描述：连续命中若干次后，下一球失手的概率规律。

设：

- 每次罚球命中的概率固定为 $\theta$；
- 各次罚球相互**独立（independent）**。

如果连续命中 (s) 次，然后第 (s+1) 次失手，那么根据独立性，

\[
\begin{aligned}
p_\theta(s)
&=P(\text{前 }s\text{ 次命中，下一次失手})\\
&=\underbrace{\theta\cdots\theta}_{s\text{ 次}}(1-\theta)\\
&=\theta^s(1-\theta),\qquad s=0,1,2,\ldots
\end{aligned}
\]

这就是一个只有一个未知参数 $\theta$ 的**参数模型（parametric model）**。

### 4.1 它和几何分布是什么关系？

标准的**几何分布（geometric distribution）**常写成

\[
p(a)=(1-\alpha)^{a-1}\alpha,qquad a=1,2,3,\ldots
\]

其中 $a$ 表示“直到第一次成功所需的试验次数”，$\alpha$ 是每次试验成功的概率。

在罚球例子中，我们把“失手”当作终止事件，因此令

\[
\alpha=1-\theta.
\]

同时，课程里的 (s) 数的是失手前的命中次数，从 0 开始；标准形式中的 (a) 把最后那次终止试验也算进去，从 1 开始。因此

\[
a=s+1.
\]

代入后：

\[
(1-\alpha)^{a-1}\alpha
=\theta^s(1-\theta).
\]

两种写法完全一致，只是计数方式不同。这是理解本节时最容易出现的“差 1”问题。

### 4.2 建模假设不一定真实，但仍可能有用

“命中率永远固定”和“每次罚球完全独立”在现实中未必严格成立：球员可能疲劳、调整动作，比赛压力也会变化。

统计模型并不要求假设百分之百真实。关键是：这些简化是否保留了主要规律，并能在新数据上做出足够好的预测。

---

## 5. 常见的离散参数分布

课程提到三类常用模型：

| 分布 | 英文 | 典型用途 | 主要参数 |
|---|---|---|---|
| 伯努利分布 | Bernoulli distribution | 一次试验只有 0/1、失败/成功两种结果 | 成功概率 $\theta$ |
| 二项分布 | Binomial distribution | 固定次数独立试验中的成功总次数 | 试验次数 $n$、成功概率 $\theta$ |
| 泊松分布 | Poisson distribution | 固定时间或空间范围内的事件计数 | 平均发生率 $\lambda$ |

本节随后以伯努利分布为例讲解如何从数据估计参数。

### 5.1 伯努利分布

**伯努利随机变量（Bernoulli random variable）**只取 0 和 1：

\[
p_\theta(1)=\theta,\qquad p_\theta(0)=1-\theta.
\]

例如把“命中”记为 1、“失手”记为 0，$\theta$ 就是命中概率。

---

## 6. i.i.d. 假设：为什么联合概率可以写成乘积？

设有随机变量

\[
\tilde X_1,\tilde X_2,\ldots,\tilde X_n.
\]

它们若具有相同的 PMF，就称为**同分布（identically distributed）**；若一个变量的结果不改变其他变量的概率规律，就称它们**独立（independent）**。

两者合在一起，称为**独立同分布（independent and identically distributed, i.i.d.）**。

在 i.i.d. 假设下，观测到 $x_1,\ldots,x_n$ 的联合概率可以分解为

\[
P(\tilde X_1=x_1,\ldots,\tilde X_n=x_n)
=\prod_{i=1}^{n}p_\theta(x_i).
\]

注意：

- “同分布”使每个观测都使用同一个 $p_\theta$；
- “独立”使联合概率能够写成各项概率的乘积。

若数据存在时间依赖、群组效应或趋势，i.i.d. 假设就可能不成立。

---

## 7. 似然函数：固定数据，反过来看参数

给定数据集 $X=\{x_1,\ldots,x_n\}$，定义**似然函数（likelihood function）**

\[
L_X(\theta):=\prod_{i=1}^{n}p_\theta(x_i).
\]

这里有一个重要的视角转换：

- 在 PMF $p_\theta(x)$ 中，通常把参数 $\theta$ 固定，研究不同 $x$ 的概率；
- 在似然 $L_X(\theta)$ 中，已经观测到的数据 $X$ 固定，研究不同 $\theta$ 对这批数据的解释能力。

似然值越大，表示在该参数下，当前数据越“容易出现”。但似然不是“参数的概率分布”，因此不要把 $L_X(\theta)$ 直接解释为 $P(\theta\mid X)$。

### 7.1 为什么使用对数似然？

定义**对数似然（log-likelihood）**：

\[
\ell_X(\theta):=\log L_X(\theta)
=\sum_{i=1}^{n}\log p_\theta(x_i).
\]

使用对数有三个好处：

1. 对数严格递增，最大化 $L_X(\theta)$ 与最大化 $\log L_X(\theta)$ 得到同一参数；
2. 乘积变成求和，求导更容易；
3. 避免许多小概率相乘造成计算机数值下溢。

---

## 8. 极大似然估计

选择使观测数据似然最大的参数，称为**极大似然估计（maximum likelihood estimation, MLE）**：

\[
\hat\theta_{\mathrm{ML}}
:=\arg\max_\theta L_X(\theta)
=\arg\max_\theta \ell_X(\theta).
\]

其中 $\arg\max$ 返回的是“让函数达到最大值的参数”，不是最大函数值本身。

### 8.1 伯努利模型的 MLE 推导

假设数据中有 $n_1$ 个 1 和 $n_0$ 个 0。伯努利似然为

\[
L_X(\theta)=\theta^{n_1}(1-\theta)^{n_0}.
\]

取对数：

\[
\ell_X(\theta)=n_1\log\theta+n_0\log(1-\theta).
\]

一阶导数为

\[
\ell_X'(\theta)=\frac{n_1}{\theta}-\frac{n_0}{1-\theta}.
\]

令它等于 0：

\[
\frac{n_1}{\theta}=\frac{n_0}{1-\theta}
\quad\Longrightarrow\quad
n_1(1-\theta)=n_0\theta.
\]

因此

\[
\boxed{
\hat\theta_{\mathrm{ML}}=\frac{n_1}{n_0+n_1}
}
\]

也就是说，伯努利成功概率的 MLE 就是样本中的成功比例。

二阶导数为

\[
\ell_X''(\theta)
=-\frac{n_1}{\theta^2}-\frac{n_0}{(1-\theta)^2}<0,
\]

所以对数似然是凹的，该驻点确实是最大值。

例如有 60 个 1 和 40 个 0，则

\[
\hat\theta_{\mathrm{ML}}=\frac{60}{100}=0.6.
\]

### 8.2 罚球连中模型的 MLE

若观测到 $n$ 段连中长度 $x_1,\ldots,x_n$，且

\[
p_\theta(x_i)=\theta^{x_i}(1-\theta),
\]

则

\[
\begin{aligned}
\ell_X(\theta)
&=\sum_{i=1}^{n}\log\bigl(\theta^{x_i}(1-\theta)\bigr)\\
&=\left(\sum_{i=1}^{n}x_i\right)\log\theta+n\log(1-\theta).
\end{aligned}
\]

令

\[
n_{\text{made}}=\sum_{i=1}^{n}x_i,
\qquad
n_{\text{missed}}=n,
\]

就得到与伯努利模型相同的形式：

\[
\ell_X(\theta)
=n_{\text{made}}\log\theta
+n_{\text{missed}}\log(1-\theta).
\]

因此

\[
\hat\theta_{\mathrm{ML}}
=\frac{n_{\text{made}}}
{n_{\text{made}}+n_{\text{missed}}}.
\]

课程数据得到 $\hat\theta_{\mathrm{ML}}=0.875$，也就是总罚球中的命中比例。

---

## 9. 为什么不能用同一批数据训练和评价模型？

经验 PMF 在训练数据上几乎拥有“完美拟合”：它就是按照训练数据的频率构造的。但训练数据拟合得好，不代表对未来数据预测得好。

这正是模型评价的核心问题：我们关心的不只是**训练误差（training error）**，更关心**泛化能力（generalization）**。

因此应把数据分为：

- **训练集（training set）**：用于估计经验 PMF 或拟合参数；
- **测试集（test set）**：不参与训练，只用于评价模型在新数据上的表现。

如果先反复查看测试集、再据此调整模型，那么测试集也间接参与了训练，评价会再次变得过于乐观。

---

## 10. 用 RMSD 比较两个 PMF

课程使用**均方根差（root mean square difference, RMSD）**衡量估计 PMF $p_{\mathrm{est}}$ 与测试集经验 PMF $p_{\mathrm{test}}$ 的距离：

\[
\operatorname{RMSD}(p_{\mathrm{est}})
=\sqrt{
\frac1L\sum_{\ell=0}^{L}
\left(p_{\mathrm{est}}(\ell)-p_{\mathrm{test}}(\ell)\right)^2
}.
\]

直观上，它依次比较每个可能取值上的概率差异，平方后求平均，再开平方。RMSD 越小，两条 PMF 越接近。

> 记号提示：如果求和确实包含 $0,1,\ldots,L$ 共 $L+1$ 项，严格的“均方”分母通常应写成 $L+1$。课件写作 $1/L$，可理解为其 $L$ 代表所比较的类别总数，或只是索引记法上的轻微不一致。实际计算时要明确类别数。

在课程的罚球例子中：

- 非参数经验 PMF 的测试 RMSD 为 $7.67\times10^{-3}$；
- 参数模型的训练 RMSD 为 $5.46\times10^{-3}$；
- 参数模型的测试 RMSD 为 $5.61\times10^{-3}$。

参数模型的测试误差更小，说明在这组数据中，简单的几何形状虽然不可能贴合每一个训练波动，却更好地抓住了可推广的规律。

---

## 11. 参数模型与非参数模型：如何选择？

| 方面 | 参数模型（parametric model） | 非参数模型（nonparametric model） |
|---|---|---|
| 分布形状 | 预先规定，例如几何分布 | 不预设固定形状 |
| 参数数量 | 通常较少 | 通常随取值种类或样本量增长 |
| 数据需求 | 相对较少 | 通常需要更多数据 |
| 优势 | 稳定、易解释、不易过拟合 | 灵活，能学习复杂结构 |
| 风险 | 假设错误时会欠拟合 | 数据不足时容易过拟合噪声 |

这里的两种典型失败分别是：

- **过拟合（overfitting）**：模型把训练样本中的随机噪声也学了进去；
- **欠拟合（underfitting）**：模型过于简单，连真正的信号也没有表达出来。

课程给出的实用判断是：

- 数据较少，但对**数据生成过程（data-generating process）**有较可靠的认识：优先考虑参数模型；
- 数据很多，但对生成机制了解较少：可以考虑更灵活的非参数模型。

现实中还可以采用半参数模型、正则化、贝叶斯方法或交叉验证，因此这不是绝对二选一，而是一种理解“假设强度与数据量如何交换”的基本框架。

---

## 12. 一条完整的建模流程

把本节内容串起来，可以得到以下工作流：

1. 明确要描述的离散随机变量及其可能取值；
2. 用 PMF 表示每个取值的概率；
3. 收集样本并计算经验 PMF，观察分布形状；
4. 根据生成机制提出参数模型与独立性等假设；
5. 在 i.i.d. 假设下写出似然函数；
6. 最大化对数似然，得到参数的 MLE；
7. 用独立测试集和合适的评价指标比较模型；
8. 检查是否过拟合或欠拟合，并重新审视假设。

---

## 13. 关键术语速查

| 中文术语 | English | 简短解释 |
|---|---|---|
| 离散随机变量 | discrete random variable | 只能取有限个或可数个值的随机变量 |
| 概率质量函数 | probability mass function, PMF | 给出每个离散取值的概率 |
| 样本空间 | sample space | 所有基本随机结果组成的集合 |
| 指示函数 | indicator function | 条件成立取 1，否则取 0 |
| 经验概率质量函数 | empirical PMF | 用样本频率估计各取值概率 |
| 参数模型 | parametric model | 由有限个参数控制固定形状的模型 |
| 非参数模型 | nonparametric model | 不预设有限维固定分布形状的模型 |
| 伯努利分布 | Bernoulli distribution | 描述一次二元试验 |
| 几何分布 | geometric distribution | 描述首次终止事件前的等待次数 |
| 独立同分布 | independent and identically distributed, i.i.d. | 样本彼此独立且来自同一分布 |
| 似然函数 | likelihood function | 固定数据后，衡量不同参数解释力的函数 |
| 对数似然 | log-likelihood | 似然取对数，将乘积化为和 |
| 极大似然估计 | maximum likelihood estimation, MLE | 选择使观测数据似然最大的参数 |
| 训练集 | training set | 用于拟合模型的数据 |
| 测试集 | test set | 用于评价泛化能力的独立数据 |
| 均方根差 | root mean square difference, RMSD | 衡量两个函数在各点差异的指标 |
| 过拟合 | overfitting | 过度学习训练数据中的噪声 |
| 欠拟合 | underfitting | 模型过于简单，未能抓住真实结构 |

---

## 14. 最后总结

本节最重要的并不是记住某一个公式，而是建立三层认识：

1. **表示层**：离散不确定性用随机变量和 PMF 表示；
2. **估计层**：既可以直接使用经验频率，也可以借助参数模型和 MLE；
3. **评价层**：训练数据上的贴合度不是最终目标，测试数据上的泛化表现才是关键。

经验 PMF 与参数模型之间的选择，本质上是**灵活性（flexibility）**与**结构性假设（structural assumptions）**之间的权衡。数据少时，合理假设能够显著提高估计稳定性；数据充足时，更灵活的模型则有机会捕捉预设分布无法表达的规律。
