---
title: "随机模拟与基础统计：从德州扑克到中心极限定理"
description: "用直觉、公式和例子理解蒙特卡洛模拟、期望、方差、协方差、大数定律、中心极限定理和置信区间。"
date: 2026-09-08
tags: [概率论, 统计学, 蒙特卡洛, 中心极限定理]
math: true
---

# 随机模拟与基础统计：从德州扑克到中心极限定理

> 本文对应 Chapter 3 的 Lecture 3.1。目标不是只记公式，而是理解：为什么随机模拟有效、误差如何衡量，以及为什么增加样本能让估计更稳定。

## 一、这一讲的主线

这一讲把两个主题连在一起：

1. **随机模拟（random simulation）**：无法或不想精确枚举时，通过大量随机实验估计答案。
2. **基础统计（basic statistics）**：衡量模拟结果的随机波动，并判断增加样本量能提高多少精度。

核心思想是：

> 用样本平均值估计真实平均值；样本越多，估计通常越稳定。

德州扑克只是引入问题的例子，同样的方法也用于民调、医学试验、金融风险模拟和机器学习评估。

## 二、德州扑克与蒙特卡洛模拟

假设我们的两张起手牌已经固定，想求它在双人德州扑克中的真实获胜概率 $p$。

如果直接穷举，需要考虑：

- 对手从剩余 50 张牌中拿 2 张；
- 再从剩余 48 张牌中选 5 张公共牌。

组合总数是

$$
\binom{50}{2}\binom{48}{5}=2{,}097{,}572{,}400.
$$

因此可以采用**蒙特卡洛模拟（Monte Carlo simulation）**：

1. 洗牌；
2. 发给对手两张牌，再发五张公共牌；
3. 判断我们的牌是否获胜；
4. 重复 $N$ 次；
5. 用获胜次数除以 $N$。

### 用随机变量记录输赢

定义第 $i$ 局的结果：

若第 $i$ 局获胜就记为 $X_i=1$，否则记为 $X_i=0$，因此

$$
X_i\in\{0,1\}.
$$

这种只取 0 和 1 的变量叫做**伯努利随机变量（Bernoulli random variable）**。模拟 $N$ 次后，获胜概率的估计值为

$$
\hat p_N=\frac{1}{N}\sum_{i=1}^{N}X_i.
$$

必须区分：

- $p$：真实获胜概率，是固定但未知的数；
- $\hat p_N$：模拟产生的估计值，是随机的。

每次重新模拟，$\hat p_N$ 都可能不同。讲义中，一对 A 的实验只模拟 50 局时，估计可能从 72% 到 96%；模拟 10,000 局时，结果大约集中在 84% 到 86%。

## 三、离散随机变量与期望

**离散随机变量（discrete random variable）**只能取有限个或可数多个值。若 $X$ 可以取 $x_1,x_2,\ldots$，并且

$$
p_i=P(X=x_i),\qquad p_i\ge 0,\qquad \sum_i p_i=1,
$$

那么它的**期望值（expected value）**或**均值（mean）**是

$$
\mu=E[X]=\sum_i x_i p_i.
$$

期望是按照概率加权的长期平均，并不一定是随机变量能够实际取到的值。

### 公平骰子

公平六面骰子的每个点数概率都是 $1/6$：

$$
E[X]=\frac{1+2+3+4+5+6}{6}=\frac{7}{2}=3.5.
$$

骰子不能掷出 3.5；这个数表示重复投掷很多次以后，平均点数会接近 3.5。

### 随机变量函数的期望

若对 $X$ 做变换 $g(X)$，则

$$
E[g(X)]=\sum_i g(x_i)p_i.
$$

特别重要的是**期望的线性性质（linearity of expectation）**：

$$
E[aX+bY]=aE[X]+bE[Y].
$$

这个公式不要求 $X$ 和 $Y$ 独立。

## 四、方差与标准差

平均值只描述中心，不能说明数据有多分散。**方差（variance）**定义为

$$
\operatorname{var}(X)=E[(X-\mu)^2].
$$

它是“到平均值的平方距离”的平均。平方可以防止正负偏差相互抵消，也会让较大的偏差获得更高权重。

更常用的计算公式是

$$
\operatorname{var}(X)=E[X^2]-E[X]^2.
$$

推导如下：

$$
E[(X-\mu)^2]=E[X^2]-2\mu E[X]+\mu^2=E[X^2]-\mu^2.
$$

**标准差（standard deviation）**是方差的平方根：

$$
\sigma=\sqrt{\operatorname{var}(X)}.
$$

标准差与原变量单位相同，因此通常比方差更容易解释。

### 平移和缩放

若 $Y=aX+b$，则

$$
\operatorname{var}(aX+b)=a^2\operatorname{var}(X),\qquad
\operatorname{sd}(aX+b)=|a|\operatorname{sd}(X).
$$

加上常数 $b$ 只是整体平移，不改变离散程度；乘以 $a$ 会把所有距离放大为原来的 $|a|$ 倍。

### 公平骰子的方差

$$
E[X^2]=\frac{1^2+2^2+\cdots+6^2}{6}=\frac{91}{6},
$$

所以

$$
\operatorname{var}(X)=\frac{91}{6}-\left(\frac{7}{2}\right)^2=\frac{35}{12},
\qquad \sigma=\sqrt{\frac{35}{12}}\approx1.708.
$$

## 五、伯努利随机变量

若

$$
P(X=1)=p,\qquad P(X=0)=1-p.
$$

则记作 $X\sim\operatorname{Bernoulli}(p)$。因为 $X^2=X$，所以

$$
E[X]=p,\qquad \operatorname{var}(X)=p-p^2=p(1-p).
$$

扑克实验中，每局的输赢指标 $X_i$ 就是伯努利随机变量。

## 六、协方差与独立性

**协方差（covariance）**衡量两个变量是否倾向于一起变化：

$$
\operatorname{cov}(X,Y)
=E[(X-E[X])(Y-E[Y])]
=E[XY]-E[X]E[Y].
$$

- 协方差大于 0：$X$ 较大时，$Y$ 也倾向较大；
- 协方差小于 0：$X$ 较大时，$Y$ 倾向较小；
- 协方差等于 0：没有线性的共同变化趋势。

线性组合的方差是

$$
\operatorname{var}(aX+bY)
=a^2\operatorname{var}(X)+b^2\operatorname{var}(Y)
+2ab\operatorname{cov}(X,Y).
$$

### 独立性

离散变量 $X,Y$ **独立（independent）**，意味着对所有 $i,j$，

$$
P(X=x_i,Y=y_j)=P(X=x_i)P(Y=y_j).
$$

知道 $X$ 的取值不会改变我们对 $Y$ 的概率判断。独立性推出

$$
E[XY]=E[X]E[Y],\qquad \operatorname{cov}(X,Y)=0.
$$

所以独立变量满足

$$
\operatorname{var}(X+Y)=\operatorname{var}(X)+\operatorname{var}(Y).
$$

### 零协方差不等于独立

“独立”一定推出“协方差为零”，但反方向通常不成立。

例如，让 $X$ 等概率取 $-1,0,1$，并令 $Y=X^2$。由对称性，$E[X]=E[X^3]=0$，所以 $\operatorname{cov}(X,Y)=0$。但 $Y$ 完全由 $X$ 决定，因此它们显然不独立。

> 零协方差只排除了线性关系；独立性则排除了所有统计依赖关系。

## 七、连续随机变量

**连续随机变量（continuous random variable）**通过**概率密度函数（probability density function, PDF）** $\rho(x)$ 描述：

$$
\rho(x)\ge0,\qquad \int_{-\infty}^{\infty}\rho(x)\,dx=1.
$$

区间概率等于密度曲线在该区间下方的面积：

$$
P(a\le X\le b)=\int_a^b\rho(x)\,dx.
$$

对连续随机变量，$P(X=x)=0$。因此 $\rho(x)$ 本身不是“取到 $x$ 的概率”，而是该位置附近的概率密集程度。

**累积分布函数（cumulative distribution function, CDF）**是

$$
F(x)=P(X\le x)=\int_{-\infty}^{x}\rho(t)\,dt.
$$

连续情形下，期望改写为积分：

$$
E[g(X)]=\int_{-\infty}^{\infty}g(x)\rho(x)\,dx.
$$

## 八、均匀分布

若 $X$ 在 $[a,b]$ 上均匀分布，记作 $X\sim U(a,b)$。它的密度为

$$
\rho(x)=\frac{1}{b-a}\ (a\le x\le b),\qquad \rho(x)=0\ (x\notin[a,b]).
$$

对 $U\sim U(0,1)$，

$$
E[U]=\frac{1}{2},\qquad E[U^2]=\frac{1}{3},\qquad \operatorname{var}(U)=\frac{1}{12}.
$$

若希望把 $U(0,1)$ 转换成 $U(a,b)$，使用

$$
X=a+(b-a)U.
$$

乘以 $b-a$ 把区间长度从 1 拉伸到 $b-a$，再加 $a$ 把起点平移到 $a$。因此

$$
E[X]=\frac{a+b}{2},\qquad \operatorname{var}(X)=\frac{(b-a)^2}{12}.
$$

如果 MATLAB 的 `rand` 返回 $U(0,1)$ 样本，那么 `a + (b-a)*rand` 返回 $U(a,b)$ 样本。

## 九、正态分布

若 $X\sim N(\mu,\sigma^2)$，则 $X$ 服从均值为 $\mu$、方差为 $\sigma^2$ 的**正态分布（normal distribution）**：

$$
\rho(x)=\frac{1}{\sigma\sqrt{2\pi}}
\exp\left(-\frac{(x-\mu)^2}{2\sigma^2}\right).
$$

- $\mu$ 决定中心；
- $\sigma$ 决定曲线宽窄；
- $\sigma^2$ 是方差。

$N(0,1)$ 称为**标准正态分布（standard normal distribution）**。若 $Z\sim N(0,1)$，那么

$$
\mu+\sigma Z\sim N(\mu,\sigma^2).
$$

MATLAB 的 `randn` 产生标准正态样本，所以 `mu + sigma*randn` 产生 $N(\mu,\sigma^2)$ 样本。

正态分布的 **68-95-99.7 规则**是：约 68%、95% 和 99.7% 的结果分别位于均值的一、二、三个标准差范围内。

## 十、样本平均值为什么越来越稳定？

设 $X_1,\ldots,X_N$ **独立同分布（independent and identically distributed, iid）**，满足

$$
E[X_i]=\mu,\qquad \operatorname{var}(X_i)=\sigma^2.
$$

定义**样本平均值（sample mean）**

$$
A_N=\frac{1}{N}\sum_{i=1}^{N}X_i.
$$

利用期望的线性性质：

$$
E[A_N]=\frac{1}{N}\sum_{i=1}^{N}E[X_i]=\mu.
$$

所以样本平均值不会系统性偏高或偏低，是总体均值的**无偏估计量（unbiased estimator）**。

由于各个 $X_i$ 独立，交叉协方差为零：

$$
\operatorname{var}(A_N)
=\frac{1}{N^2}\sum_{i=1}^{N}\operatorname{var}(X_i)
=\frac{\sigma^2}{N}.
$$

所以

$$
\operatorname{sd}(A_N)=\frac{\sigma}{\sqrt N}.
$$

这是本讲最重要的公式：估计误差的典型大小按 $1/\sqrt N$ 缩小，而不是按 $1/N$ 缩小。

- 样本量扩大 4 倍，误差约减半；
- 样本量扩大 100 倍，误差约缩小到原来的 $1/10$；
- 想让误差缩小 10 倍，需要大约 100 倍的数据。

这称为**平方根收敛率（square-root convergence rate）**。

## 十一、大数定律

**大数定律（Law of Large Numbers, LLN）**说，对任意 $\varepsilon>0$，

$$
P(|A_N-\mu|>\varepsilon)\longrightarrow0,\qquad N\to\infty.
$$

当样本量越来越大时，样本平均值偏离真实均值很多的概率越来越小。大数定律回答“是否会靠近真实值”，但没有具体描述误差的分布形状。

## 十二、中心极限定理

**中心极限定理（Central Limit Theorem, CLT）**说：若 $X_i$ 独立同分布，均值为 $\mu$，方差为有限且非零的 $\sigma^2$，那么当 $N$ 足够大时，

$$
A_N\approx \mathcal{N}\left(\mu,\frac{\sigma^2}{N}\right).
$$

原始数据不需要服从正态分布；大量独立样本的平均值仍会逐渐接近正态分布。

必须区分：

- $E[A_N]=\mu$：对每个 $N$ 都精确成立；
- $\operatorname{var}(A_N)=\sigma^2/N$：对每个 $N$ 都精确成立；
- $A_N$ 近似正态：通常只在 $N$ 足够大时成立。

## 十三、样本方差与标准误

观察到数据 $x_1,\ldots,x_N$ 后，定义样本均值

$$
\bar x=\frac{1}{N}\sum_{i=1}^{N}x_i,
$$

以及**样本方差（sample variance）**

$$
s^2=\frac{1}{N-1}\sum_{i=1}^{N}(x_i-\bar x)^2.
$$

分母是 $N-1$，因为我们已经用同一批数据估计了 $\bar x$。偏差满足

$$
\sum_{i=1}^{N}(x_i-\bar x)=0,
$$

所以只有 $N-1$ 个偏差能够自由变化，也就是 $N-1$ 个**自由度（degrees of freedom）**。这样定义可使 $s^2$ 成为 $\sigma^2$ 的无偏估计。

用 $s$ 代替未知的 $\sigma$，得到样本均值的**标准误（standard error, SE）**：

$$
SE(\bar x)=\frac{s}{\sqrt N}.
$$

不要混淆：

- 标准差 $s$：描述单个观测值之间有多分散；
- 标准误 $s/\sqrt N$：描述样本均值这个估计量有多不稳定。

## 十四、95% 置信区间

根据中心极限定理，

$$
Z=\frac{\bar x-\mu}{SE(\bar x)}
$$

近似服从 $N(0,1)$。标准正态分布约有 95% 的概率位于 $-1.96$ 到 $1.96$ 之间，因此总体均值的大样本近似 **95% 置信区间（95% confidence interval）**为

$$
\boxed{\bar x\pm1.96\frac{s}{\sqrt N}}.
$$

其中 $1.96s/\sqrt N$ 是**误差界（margin of error）**，也就是区间的半宽。

正确解释是：如果反复进行整个抽样过程，并每次按同样方法构造区间，那么大约 95% 的区间会包含真实均值 $\mu$。

在经典统计学中，实验结束后 $\mu$ 是固定的，区间才是随机的。因此严格来说，不应把已经得到的一个区间解释为“$\mu$ 有 95% 的概率位于其中”。

## 十五、回到扑克实验

每局输赢满足

$$
X_i\sim\operatorname{Bernoulli}(p),\qquad
E[X_i]=p,\qquad \operatorname{var}(X_i)=p(1-p).
$$

所以获胜比例近似满足

$$
\hat p_N\approx N\left(p,\frac{p(1-p)}{N}\right).
$$

真实 $p$ 未知时，用 $\hat p_N$ 代替它：

$$
SE(\hat p_N)\approx
\sqrt{\frac{\hat p_N(1-\hat p_N)}{N}}.
$$

假设一对 A 的真实胜率约为 $p=0.85$。

### 模拟 50 局

$$
SE(\hat p_{50})\approx\sqrt{\frac{0.85(0.15)}{50}}\approx0.0505.
$$

95% 范围的半宽约为 $1.96\times0.0505=0.099$，因此

$$
0.85\pm0.099=[0.751,0.949].
$$

也就是约 75.1% 到 94.9%。只模拟 50 局时，估计可能有很大波动。

### 模拟 10,000 局

$$
SE(\hat p_{10{,}000})\approx
\sqrt{\frac{0.85(0.15)}{10{,}000}}\approx0.00357.
$$

95% 范围的半宽约为 $1.96\times0.00357=0.0070$，因此

$$
0.85\pm0.007=[0.843,0.857].
$$

也就是约 84.3% 到 85.7%。

### 为什么 100 次实验的极端范围更宽？

中央 95% 范围描述一次估计的中央部分，本来就允许约 5% 的实验落在外面。重复 100 次，平均可能有约 5 次落在区间外；100 个结果中的最小值和最大值自然可能更极端。

所以，$N=50$ 时理论中央范围约为 75.1%-94.9%，而 100 次实验实际观察到的极端范围是 72%-96%，二者并不矛盾。

## 十六、术语速查表

| 中文 | English | 含义 |
|---|---|---|
| 蒙特卡洛模拟 | Monte Carlo simulation | 用重复随机实验估计目标量 |
| 随机变量 | random variable | 由随机实验结果决定的数值 |
| 期望 / 均值 | expectation / mean | 概率加权的长期平均 |
| 方差 | variance | 到均值的平方距离的平均 |
| 标准差 | standard deviation | 单个结果的典型波动尺度 |
| 协方差 | covariance | 两个变量的线性共同变化趋势 |
| 独立同分布 | independent and identically distributed, iid | 各变量同分布且相互独立 |
| 概率密度函数 | probability density function, PDF | 连续分布的概率密集程度 |
| 累积分布函数 | cumulative distribution function, CDF | 落在某个值左侧的累计概率 |
| 大数定律 | Law of Large Numbers, LLN | 样本平均值趋近真实均值 |
| 中心极限定理 | Central Limit Theorem, CLT | 大样本平均值近似正态 |
| 样本方差 | sample variance | 用样本估计总体方差 |
| 标准误 | standard error, SE | 估计量的随机波动尺度 |
| 置信区间 | confidence interval | 按固定覆盖率构造的参数区间 |
| 自由度 | degrees of freedom | 能独立变化的信息数量 |

## 十七、一页总结

1. 蒙特卡洛模拟通过大量随机实验估计难以精确计算的量。
2. 用 0-1 变量记录事件是否发生，则样本平均值就是事件概率的估计。
3. 方差衡量平方尺度上的波动，标准差衡量原单位下的典型波动。
4. 独立变量的方差可以相加；独立推出零协方差，但反向不成立。
5. 大数定律保证样本平均值靠近真实均值。
6. 中心极限定理说明大样本平均值近似正态。
7. 样本平均值的标准误按 $1/\sqrt N$ 缩小。
8. 大样本均值的 95% 置信区间约为 $\bar x\pm1.96s/\sqrt N$。
9. 想把随机误差缩小到原来的 $1/10$，通常要把独立样本量增加到原来的 100 倍。

---

*根据 Lecture 3.1《Random simulation and basic statistics》整理。*

