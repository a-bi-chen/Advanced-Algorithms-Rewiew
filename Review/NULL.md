论文证明目的：
**构造一个使用 $O(n)$ 随机比特的哈希表，对于任意正常数 $\varepsilon$ ，其失败的概率达到 $1/n^{n^{1 - \varepsilon }}$ 以下**

# 条件 / 约定：
1. 假设机器字节长为 $\Theta(\log n)$ 比特 ——> 键/值也是 $O(\log n)$ 比特
2. 要求字典最多使用线性空间（即 $O(n \log n)\ \text{bit}$）,并且字典应该是 *显式的（explicit）(指可以在 $O(n)$ 时间内初始化)* 
3. 令 $U = [\text{poly}(n)]$ *（即所有键都取自整数集合 {1，2，. . . ，$n^c$}  其中 $c$ 是某个固定常数）* 是所有可能（$\log n$）比特键的集合
4. 令 $V = [\text{ploy}(n)]$ 是所有可能($\log n$)比特值的集合
5. 一个 **字典** 是一个数据结构，它存储来自 $U$ 的一个键集合，并将每个键 $x$ 与值 $y \in V$ 关联起来。字典支持三种操作：
	1) Insert($x,y$) 将键 $x$ 添加到集合中（如果它尚不存在），并将相应的值设置为 $y$ 
	2) Delete($x$) 移除 $x$
	3) Query($x$) 报告键 $x$ 是否存在，如果存在则返回相应的值
	讨论非简洁字典时，关注固定容量字典，即允许一次最多有n个键的字典 *（虽然这里只研究固定大小的情况，但我们的结论可以推广到“动态扩容”的字典，即当元素翻倍是就重建一个更大的字典，把旧有数据搬过去）* 。除非令有说明，隐式要求字典必须最多使用线性空间比特（即$O(n \log n)$ bit）并具有 $O(1)$ 的初始化时间
6. 在讨论基数树时，我们将数组 $A_1,A_2,...,A_m$ 称为**节点**（或有时称为**内部节点** ），并将每一个 $A_i$ 的非空条目 (即包含指针的条目) 称为**球**（balls）
7.  "删除"操作：并不对数据进行物理上的抹除，而是将待删除的数据标记为已删除
8. 初始化数据结构：随机旋量 $r_i$ 可以延迟初始化，使得 $r_i$ 在节点 $i$ 第一次使用时才产生。。此外，我们实际上不必支付初始化任何数组的 成本，因为我们可以使用标准技术在常数时间内模拟零初始化的数组。因此，我们的旋转基数树可以在常数时间内初始化。  *//此处原文引用了论文来讨论常数时间初始化问题，但我认为与本文需要证明的内容关联性不大，故不对引用论文进行分析讨论，在此仅作为一个结论：可以在常数时间内初始化一个旋转基数树*



证明过程节点：
	一个核心的开放问题是是否存在一个确定性的常数时间字典

# 分析旋转基数树 
[[A_Hash_Table_Without_Hash_Functions#十一、n叉基数树（n-ary Radix Tree）*（压缩前缀树）* / 旋转基数树#使用随机性节省空间：旋转基数树(The Rotated Trie)]]

证明目标：以关于 $n$ 的高概率，$A$ 的每个条目负责存储来自最多 $\Delta = \text{poly}\log n$ 个不同 $A_i$ 的条目。

树中共有 $O(n)$ 个球 { [[球数为什么是O(n)【补充n叉树结构】]]}。每个球 $b$ 由一个对 $(s,c) \in [m] \times [n]$ 指定，其中 $s \in [m]$ 是球的**源节点**（即包含该球的节点），$c \in [n]$ 是球的 **孩子索引**（即 $A_i$ 中 $b$ 在逻辑上存储的索引，此处，节点储存子节点的数组与全局数组的大小相等）

对于 $i \in [m]$ 和 $j \in [n]$ ，令 $X_{i,j}$ 指示节点 $i$ 是否向桶 $j$ 放入一个球的 0-1 随机变量。
设桶 $j$ 中的球数是 $Y_j$ ，根据[[桶-节点间的独立性]]可知，$Y_j$ 是独立指示随机变量之和。

**原文：”$O(n)$ 个球中的每一个在桶 $j$ 中的概率是 $1/n$，所以 $E[Y_j] = O(1)$。因此，由 [[数学工具#切尔诺夫界（Chernoff Bound）]]可知，$Y_j \leq \text{poly}\log n$ 以关于 $n$ 的高概率成立。Chernoff 界实际上告诉我们 $\text{poly}\log n$ 以概率 $1/n^{\text{poly}\log n}$ 成立，因此我们甚至达到了略微亚多项式的失败概率。“**

## 计算：

设 $m$ 个节点，总球数 $B = O(n)$（[[球数为什么是O(n)【补充n叉树结构】]]）。节点 $i$ 有 $b_i$ 个球，原始位置 $c_1,\ldots,c_{b_i} \in [n]$，随机旋转量 $r_i \in [n]$ 均匀选取。

### 1. $E[Y_j] = O(1)$

球落入桶 $j$ 的条件：$(c + r_i) \bmod n = j \iff r_i \equiv j - c \pmod{n}$。因 $r_i$ 均匀分布，每个球落入桶 $j$ 的概率恰为 $1/n$。

同一节点内 $b_i$ 个球对应 $b_i$ 个**不同**的 $r_i$ 取值（$\because$ 各 $c_k$ 两两不同 $\implies$ $j - c_k \bmod n$ 两两不同），故这些事件互斥：

$$
P(X_{i,j} = 1) = \frac{b_i}{n}, \qquad E[X_{i,j}] = \frac{b_i}{n}
$$

$$
\implies E[Y_j] = \sum_{i=1}^{m} E[X_{i,j}] = \frac{1}{n}\sum_{i=1}^{m} b_i = \frac{B}{n} = O(1) \quad \text{（常数界记作 } c_0\text{）}
$$

### 2. 套用 Chernoff 界 $\implies Y_j \leq \text{poly}\log n$

由[[桶-节点间的独立性]]，各 $X_{i,j}$ 跨节点 $i$ 独立 $\implies$ $Y_j$ 是独立 Bernoulli 之和，均值 $\mu \leq c_0$。

取阈值 $\Delta = \log^k n$（$k>0$），令 $\delta = \Delta/\mu - 1$。设 $1+\delta = \Delta/\mu$，代入[[数学工具#定理陈述|Chernoff上尾界]]：

$$
\begin{aligned}
P(Y_j \geq \Delta) &\leq \left(\frac{e^{\delta}}{(1+\delta)^{1+\delta}}\right)^{\!\mu}
= \left(\frac{e^{\Delta/\mu - 1}}{(\Delta/\mu)^{\Delta/\mu}}\right)^{\!\mu}
= \frac{e^{\Delta - \mu}}{(\Delta/\mu)^{\Delta}} \\
&= e^{\Delta-\mu} \cdot \left(\frac{\mu}{\Delta}\right)^{\!\Delta}
= \exp\Big(\Delta - \mu + \Delta\ln\mu - \Delta\ln\Delta\Big) \\
&= \exp\Big(\Delta(1 + \ln\mu - \ln\Delta) - \mu\Big)
\end{aligned}
$$

现在分析指数中各项的量级：

- **主导项** $-\Delta\ln\Delta$：由于 $\Delta = \log^k n$，$\ln\Delta = k\ln\log n$，该项随 $n$ 增长至 $-\infty$。
- **次要项** $\Delta(1+\ln\mu)$ 和 $-\mu$：因为 $\mu = O(1)$（常数界），$\ln\mu$ 也为常数，故 $\Delta(1+\ln\mu) = O(\Delta)$，$\mu = O(1) \subset O(\Delta)$。

因此指数可写为 $-\Delta\ln\Delta + O(\Delta)$，而 $-\Delta\ln\Delta$ 主导（因 $\ln\Delta \to \infty$）：

$$
P(Y_j \geq \Delta) \leq \exp\Big(-\Delta\ln\Delta + O(\Delta)\Big)
= \exp\Big(-\Omega(\Delta\ln\Delta)\Big)
= \exp\Big(-\Omega(\log^k n \cdot \log\log n)\Big)
$$

即：

$$
\boxed{P\big(Y_j \geq \text{poly}\log n\big) \leq 1\,/\,2^{\Omega(\text{poly}\log n)}}
$$

### 3. Union Bound $\implies$ 全局成立

全局共 $n$ 个桶，由 Union Bound（$P(\exists j \in [n]: Y_j \geq \Delta) \leq \sum_{j=1}^n P(Y_j \geq \Delta)$）：

$$
P\big(\exists j \in [n]: Y_j \geq \Delta\big) \leq n \cdot \frac{1}{2^{\Omega(\log^k n)}} = \frac{n}{2^{\Omega(\log^k n)}}
$$

将分子 $n$ 写为 $2^{\log n}$：

$$
\frac{n}{2^{\Omega(\log^k n)}} = 2^{\log n - \Omega(\log^k n)}
$$

因为 $k > 1$ 时 $\log^k n$ 比 $\log n$ 增长更快，对充分大的 $n$ 有 $\log^k n \geq 2\log n$，因此指数 $\log n - \Omega(\log^k n) \leq -\Omega(\log^k n)$（常数因子被 $\Omega$ 吸收），于是：

$$
2^{\log n - \Omega(\log^k n)} = 2^{-\Omega(\log^k n)} = \frac{1}{2^{\Omega(\log^k n)}} = \frac{1}{n^{\Omega(\log^{k-1} n)}}
$$

最终：

$$
\boxed{P\big(\exists j: Y_j \geq \text{poly}\log n\big) \leq 1\,/\,n^{\text{poly}\log n}}
$$

> **结论**：以高概率，每个桶最多承载 $\Delta = \text{poly}\log n$ 个球。失败概率为**亚多项式级别**——对任意常数 $c>0$，当 $n$ 充分大时远小于 $1/n^c$。

