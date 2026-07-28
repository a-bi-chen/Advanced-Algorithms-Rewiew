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
9. $\ell = \text{ploy}\log n$ ~~（因为第四节直接用到了）~~~



证明过程节点：
	一个核心的开放问题是是否存在一个确定性的常数时间字典

# 分析旋转基数树 
（旋转基数树的构造背景参见[[A_Hash_Table_Without_Hash_Functions#使用随机性节省空间：旋转基数树(The Rotated Trie)]]）

证明目标：以关于 $n$ 的高概率，$A$ 的每个条目负责存储来自最多 $\Delta = \text{poly}\log n$ 个不同 $A_i$ 的条目。

树中共有[O(n)个球](球数为什么是O(n)【补充n叉树结构】.md#所以球的总数)。每个球 $b$ 由一个对 $(s,c) \in [m] \times [n]$ 指定，其中 $s \in [m]$ 是球的**源节点**（即包含该球的节点），$c \in [n]$ 是球的 **孩子索引**（即 $A_i$ 中 $b$ 在逻辑上存储的索引，此处，节点储存子节点的数组与全局数组的大小相等）

对于 $i \in [m]$ 和 $j \in [n]$ ，令 $X_{i,j}$ 指示节点 $i$ 是否向桶 $j$ 放入一个球的 0-1 随机变量。
设桶 $j$ 中的球数是 $Y_j$ ，根据[桶-节点间的独立性](桶-节点间的独立性.md#为什么在节点-i-之间是独立的)可知，$Y_j$ 是独立指示随机变量之和。

**原文：”$O(n)$ 个球中的每一个在桶 $j$ 中的概率是 $1/n$，所以 $E[Y_j] = O(1)$。因此，由[切尔诺夫界](数学工具.md#切尔诺夫界)可知，$Y_j \leq \text{poly}\log n$ 以关于 $n$ 的高概率成立。Chernoff 界实际上告诉我们 $\text{poly}\log n$ 以概率 $1/n^{\text{poly}\log n}$ 成立，因此我们甚至达到了略微亚多项式的失败概率。“**

## 计算：

设 $m$ 个节点，总球数 $B = O(n)$ 。节点 $i$ 有 $b_i$ 个球，原始位置 $c_1,\ldots,c_{b_i} \in [n]$，随机旋转量 $r_i \in [n]$ 均匀选取。

### 1. $E[Y_j] = O(1)$

球落入桶 $j$ 的条件：$(c + r_i) \bmod n = j \iff r_i \equiv j - c \pmod{n}$。因 $r_i$ 在 $[n]$ 上均匀分布，每个球落入桶 $j$ 的概率为 $1/n$。

同一节点 $i$ 内 $b_i$ 个球的原始位置 $c_1,\ldots,c_{b_i}$ 两两不同，故所需的 $r_i$ 取值 $j-c_1, j-c_2, \ldots, j-c_{b_i} \pmod n$ 也两两不同——但 $r_i$ 只能取一个值，因此这些事件互斥（至多一个球落入桶 $j$）。由互斥事件概率的可加性：

$$
P(X_{i,j} = 1) = \sum_{k=1}^{b_i} P(r_i \equiv j - c_k \pmod n) = \sum_{k=1}^{b_i} \frac{1}{n} = \frac{b_i}{n}
$$

$X_{i,j}$ 是 0-1 随机变量，故 $E[X_{i,j}] = 1 \cdot P(X_{i,j}=1) + 0 \cdot P(X_{i,j}=0) = b_i / n$。由期望的线性性：

$$
E[Y_j] = E\!\left[\sum_{i=1}^{m} X_{i,j}\right] = \sum_{i=1}^{m} E[X_{i,j}] = \sum_{i=1}^{m} \frac{b_i}{n} = \frac{1}{n}\sum_{i=1}^{m} b_i = \frac{B}{n}
$$

由 $B = O(n)$，存在常数 $c_0$ 使 $B \leq c_0 n$（对充分大的 $n$），故 $E[Y_j] = B/n \leq c_0 = O(1)$。记 $\mu = E[Y_j] \leq c_0$。

### 2. 套用 Chernoff 界 $\implies Y_j \leq \text{poly}\log n$

由[桶-节点间的独立性](桶-节点间的独立性.md#为什么在节点-i-之间是独立的)，各 $X_{i,j}$ 跨节点 $i$ 独立，故 $Y_j = \sum_{i=1}^{m} X_{i,j}$ 是独立 Bernoulli 随机变量之和，均值 $\mu \leq c_0$。

取阈值 $\Delta = \log^k n$（$k>0$），令 $\delta_c = \Delta/\mu - 1$（此处 $\delta_c$ 为 Chernoff 界的参数，勿与扇出参数 $\delta$ 混淆），则 $1+\delta_c = \Delta/\mu$。

<切尔诺夫界>$P\big(X \geq (1+\delta)\mu\big) \leq \left(\frac{e^{\delta}}{(1+\delta)^{1+\delta}}\right)^{\mu}$[引用自](数学工具.md#切尔诺夫界),将 $X = Y_j$、$\delta = \delta_c$ 带入得：

$$
P(Y_j \geq \Delta) = P\big(Y_j \geq (1+\delta_c)\mu\big) \leq \left(\frac{e^{\delta_c}}{(1+\delta_c)^{1+\delta_c}}\right)^{\!\mu}
$$

代入 $\delta_c = \frac{\Delta}{\mu}-1$，$1+\delta_c = \frac{\Delta}{\mu}$，逐步化简：

$$
\begin{aligned}
\left(\frac{e^{\delta_c}}{(1+\delta_c)^{1+\delta_c}}\right)^{\!\mu}
= \left(\frac{e^{\frac{\Delta}{\mu}-1}}{\big(\frac{\Delta}{\mu}\big)^{\!\frac{\Delta}{\mu}}}\right)^{\!\mu}
= \frac{e^{(\frac{\Delta}{\mu}-1)\mu}}{(\frac{\Delta}{\mu})^{\frac{\Delta}{\mu}\cdot\mu}}
= \frac{e^{\Delta - \mu}}{(\frac{\Delta}{\mu})^{\Delta}}
= e^{\Delta-\mu} \cdot \left(\frac{\mu}{\Delta}\right)^{\!\Delta}
\end{aligned}
$$

写成指数形式并整理：

$$
\begin{aligned}
e^{\Delta-\mu} \cdot \left(\frac{\mu}{\Delta}\right)^{\!\Delta}
= \exp\!\Big(\Delta - \mu + \Delta(\ln\mu - \ln\Delta)\Big) 
= \exp\!\Big(\Delta(1 + \ln\mu - \ln\Delta) - \mu\Big)
\end{aligned}
$$

因此：

$$
P(Y_j \geq \Delta) \leq \exp\!\Big(\Delta(1 + \ln\mu - \ln\Delta) - \mu\Big)
$$

现在分析指数 $E = \Delta(1 + \ln\mu - \ln\Delta) - \mu$ 中各项的量级。$-\Delta\ln\Delta = -\log^k n \cdot k\ln\log n \to -\infty$（主导项）；由 $\mu = O(1)$ 知 $\Delta(1+\ln\mu) = O(\Delta)$，$\mu = O(1) \subset O(\Delta)$。因此：

$$
E = -\Delta\ln\Delta + O(\Delta)
$$

由于 $\ln\Delta = k\ln\log n \to \infty$，主导项 $-\Delta\ln\Delta$ 的量级为 $\Theta(\Delta\ln\Delta)$，而剩余项 $\Delta(1+\ln\mu) - \mu$ 的量级仅为 $O(\Delta)$。二者之比为 $\Theta(\ln\log n) \to \infty$，故 $-\Delta\ln\Delta$ 主导：

$$
E = -\Omega(\Delta\ln\Delta)
$$

> **$\pmb{\Omega}$ 注释**：$\Omega(f(n))$ 表示"至少是 $f(n)$ 的常数倍"，即存在常数 $c > 0$，使对充分大的 $n$ 有 $g(n) \geq c \cdot f(n)$。此处 $-\Delta\ln\Delta + O(\Delta) = -\Omega(\Delta\ln\Delta)$ 的含义是：存在 $c > 0$，使 $-\Delta\ln\Delta + O(\Delta) \leq -c \cdot \Delta\ln\Delta$。即指数以**至少** $c \cdot \Delta\ln\Delta$ 的速率趋于 $-\infty$。$\Omega$ 吸收了低阶项 $O(\Delta)$ 和所有常数因子。

于是：

$$
P(Y_j \geq \Delta) \leq \exp\!\Big(-\Omega(\Delta\ln\Delta)\Big) = \exp\!\Big(-\Omega(\log^k n \cdot \log\log n)\Big)
$$

换用 2 为底数（$\exp(x) = 2^{x/\ln 2}$，常数因子被 $\Omega$ 吸收）：

$$
\boxed{P\big(Y_j \geq \text{poly}\log n\big) \leq 1\,/\,2^{\Omega(\text{poly}\log n)}}
$$

### 3. Union Bound $\implies$ 全局成立

全局共 $n$ 个桶。

<并集界(Union Bound)>$P\!\left(\bigcup_{j=1}^{n} \mathcal{B}_j\right) \leq \sum_{j=1}^{n} P(\mathcal{B}_j)$[引用自](数学工具.md#union-bound-并集界-boole-不等式),令 $\mathcal{B}_j = \{Y_j \geq \Delta\}$，将数据带入得：

$$
P\big(\exists j \in [n]: Y_j \geq \Delta\big) \leq \sum_{j=1}^{n} P(Y_j \geq \Delta) \leq n \cdot \frac{1}{2^{\Omega(\log^k n)}} = \frac{n}{2^{\Omega(\log^k n)}}
$$

将 $n = 2^{\log n}$ 代入：

$$
\frac{n}{2^{\Omega(\log^k n)}} = \frac{2^{\log n}}{2^{\Omega(\log^k n)}} = 2^{\log n - \Omega(\log^k n)}
$$

当 $k > 1$ 时 $\log^k n$ 比 $\log n$ 增长更快；$k=1$ 时取 $\Omega$ 隐含常数 $c \geq 2$ 仍有 $\log n - c\log n \leq -\frac{c}{2}\log n = -\Omega(\log n)$。故对充分大的 $n$，$\log n - \Omega(\log^k n) = -\Omega(\log^k n)$。于是：

$$
2^{\log n - \Omega(\log^k n)} = 2^{-\Omega(\log^k n)} = \frac{1}{2^{\Omega(\log^k n)}}
$$

换底 $n$（$2^A = (2^{\log n})^{A / \log n} = n^{A / \log n}$）：

$$
\frac{1}{2^{\Omega(\log^k n)}} = \frac{1}{n^{\Omega(\log^k n) / \log n}} = \frac{1}{n^{\Omega(\log^{k-1} n)}}
$$

其中 $\frac{\Omega(\log^k n)}{\log n} = \Omega(\log^{k-1} n)$（$\frac{c \cdot \log^k n}{\log n} = c \cdot \log^{k-1} n$）。由于 $k$ 可取任意大的常数，$\Omega(\log^{k-1} n)$ 是 $\log n$ 的任意次幂——即 $\text{poly}\log n$：

$$
\boxed{P\big(\exists j: Y_j \geq \text{poly}\log n\big) \leq 1\,/\,n^{\text{poly}\log n}}
$$

> **结论**：以高概率，每个桶最多承载 $\Delta = \text{poly}\log n$ 个球。失败概率为**亚多项式级别**——对任意常数 $c>0$，当 $n$ 充分大时远小于 $1/n^c$。


# 放大旋转基数树(The Ampliﬁed Rotated Trie)

> **【论文目标】** 将失败概率从第 3 节的亚多项式级别（$1/n^{\text{poly}\log n}$）进一步压低至**双指数级小**：
> $$
\Pr[q \geq n^{1-\varepsilon}] \leq O(1/n^{n^{1-\varepsilon}}) $$
> 其中 $\varepsilon > 0$ 为任意正常数。这是通过对旋转基数树做两处改动实现的——缩小扇出以增加随机比特、用 McDiarmid 不等式处理球间依赖。

为了严格证明该数据结构在最坏情况下的空间消耗上界是线性的，我们必须证明（[为什么？](线性空间以及概率与不等式的关系.md)）：
$$
\Pr[q \geq n^{1-\varepsilon}] \leq O(1/n^{n^{1-\varepsilon}})
$$

> **【论文策略概览】** 旋转基数树的扇出从 $n$ 降至 $n^\delta$（$0 < \delta < 1$）。此举将内部节点数从 $m \approx O(1)$ 提升至 $m \geq n^{1-\delta}$，从而大幅增加独立随机比特的数量，削弱球与球之间的依赖关系。此时单个旋量 $r_s$ 的改变至多影响 $n^\delta$ 个球（有界差分常数 $L = n^\delta$），配合 McDiarmid 不等式即可获得 $n$ 的正幂次集中指数，最终将失败概率压至双指数级小。

---

## 界定溢出球的数量

### 约定：

1. $\Delta = \text{poly}\log n$ 为每个桶的容量（继承自第 3 节）。
2. 扇出（基数）降至 $n^\delta$（$\delta > 0$ 常数），每个内部节点至多 $n^\delta$ 个球，内部节点数 $m \geq n^{1-\delta}$。
3. 球 $(s,c)$ 落入桶 $\phi(s,c) = (r_s + c) \bmod n$（$s \in [m]$ 为源节点，$c \in [n^\delta]$ 为孩子索引，$r_s \in [n]$ 均匀选取）。
4. 桶满（$\geq \Delta$ 个球）后，新增球进入辅助 $n^\delta$ 叉基数树 $Q$，称为**溢出球**。$Q$ 的空间 $\propto q \cdot n^\delta$，故必须证明 $q = O(n^{1-\varepsilon})$ 以高概率成立（[线性空间约束](线性空间以及概率与不等式的关系.md)）。

---

### 推导

设总球数 $B = O(n)$。令 $q = f(r_1, \ldots, r_m)$ 为溢出球总数。

> **【为什么不能用 Chernoff？】** $q$ 不是独立随机变量之和——同一源节点的所有球共享同一个旋量 $r_s$，它们的去向彼此耦合。Chernoff 界要求被加项相互独立，此处不满足。

> **【为什么改用 McDiarmid？】** McDiarmid 不等式不要求 $f$ 具有加法结构，只要求每个变量单独改变时 $f$ 的波动幅度可控——这正是"有界差分条件"。扇出降至 $n^\delta$ 后，改变单个 $r_s$ 至多影响 $n^\delta$ 个球，波动可控，McDiarmid 得以生效。

---

#### 步骤 1/4：确定有界差分常数

> **【论文结论】** 改变单个旋量 $r_s$，至多影响源节点 $s$ 内的 $n^\delta$ 个球 → $L = n^\delta$

改变单个旋量 $r_s$，至多影响源节点 $s$ 内的 $n^\delta$ 个球（它们集体换桶，最坏情况下全部改变溢出状态）。

[有界差分条件](数学工具.md#有界差分条件) : $|f(\mathbf{x}) - f(\mathbf{x}')| \leq L$，其中 $\mathbf{x}$ 与 $\mathbf{x}'$ 仅在第 $i$ 个坐标不同,取 $f = q$、第 $i$ 个坐标为 $r_i$，则改变单个 $r_i$ 至多改变 $q$ 的值 $n^\delta$：

$$
\boxed{L = n^\delta} \tag{1.1}
$$

> **【$L$ 为什么是 $n^\delta$ 而非 $n$？——扇出缩小的决定性意义】**
>
> McDiarmid 的指数是 $\frac{2t^2}{mL^2}$。若扇出仍为 $n$（$\delta = 1$），则 $L = n$、$m = O(n)$，指数 $\sim n^{2-2\varepsilon} / n^3 = n^{-1-2\varepsilon} \to 0$，$\exp(-\text{指数}) \to 1$——界是**平凡的**（什么都没保证）。
>
> 将扇出压至 $n^\delta$ 后 $L = n^\delta$，指数 $\sim n^{2-2\varepsilon} / n^{1+2\delta} = n^{1-2\varepsilon-2\delta}$。只要 $1 - 2\varepsilon - 2\delta > 0$，指数就是 $n$ 的**正幂次** → 集中极强 → 失败概率双指数级小。这就是"放大"二字的数学本质。

---

#### 步骤 2/4：估计 $\mathbb{E}[q]$

> 由第 3 节 Chernoff 结论，$\mathbb{E}[q]$ 远小于阈值 $n^{1-\varepsilon}$，故 McDiarmid 中的偏差 $t = n^{1-\varepsilon} - \mathbb{E}[q] = \Theta(n^{1-\varepsilon})$（期望项可忽略）。

由[第 3 节](#计算：)，单个桶 $j$ 球数超限的概率（取 $\Delta = \text{poly}\log n$）：

$$
P(Y_j \geq \Delta) \leq 1\,/\,2^{\Omega(\text{poly}\log n)} \tag{2.1}
$$

记 $\mathcal{O}_j$ 为桶 $j$ 的溢出球数。$\mathcal{O}_j \leq Y_j$，且仅当 $Y_j > \Delta$ 时非零。最坏情况下一个桶至多容纳 $O(n)$ 个球，故：

$$
\mathbb{E}[\mathcal{O}_j] \leq O(n) \cdot P(Y_j \geq \Delta) \tag{2.2}
$$

将 (2.1) 代入 (2.2)，将多项式因子 $O(n) = 2^{O(\log n)}$ 写为指数形式。由于 $\Omega(\text{poly}\log n) = \Omega(\log^k n)$（$k \geq 2$ 可取任意大）的增长速度远快于 $O(\log n)$，多项式因子被吸收：

$$
\mathbb{E}[\mathcal{O}_j] \leq \frac{O(n)}{2^{\Omega(\text{poly}\log n)}} = \frac{2^{O(\log n)}}{2^{\Omega(\log^k n)}} = 2^{\,O(\log n) - \Omega(\log^k n)} = \frac{1}{2^{\Omega(\text{poly}\log n)}} \tag{2.3}
$$

> **【为什么 $O(\log n)$ 被 $\Omega(\log^k n)$ 吞掉？】** 取 $k \geq 2$，$\log^k n = \log n \cdot \log^{k-1} n$，而 $\log^{k-1} n \to \infty$。故 $O(\log n) - \Omega(\log^k n) = -\Omega(\log^k n)$——"小的减大的，剩下负的大的"。

对全部 $n$ 个桶求和，得到溢出球总数的期望：

$$
\mathbb{E}[q] = \sum_{j=1}^{n} \mathbb{E}[\mathcal{O}_j] \leq n \cdot \frac{1}{2^{\Omega(\text{poly}\log n)}} = \frac{n}{2^{\Omega(\log^k n)}} \tag{2.4}
$$

**验证 $\mathbb{E}[q] \ll n^{1-\varepsilon}$（论文的关键前提）：**

将两者都写成以 2 为底的指数形式，直接比较指数：

$$
\mathbb{E}[q] \leq 2^{\,\log n - \Omega(\log^k n)}, \qquad n^{1-\varepsilon} = 2^{\,(1-\varepsilon)\log n}
$$

$$
\frac{\mathbb{E}[q]}{n^{1-\varepsilon}} \leq \frac{2^{\,\log n - \Omega(\log^k n)}}{2^{\,(1-\varepsilon)\log n}} = 2^{\,\varepsilon\log n - \Omega(\log^k n)} \tag{2.5}
$$

取 $k \geq 2$，$\log^k n$ 比 $\varepsilon\log n$ 增长更快（$\frac{\log^k n}{\log n} = \log^{k-1} n \to \infty$），故 $\varepsilon\log n - \Omega(\log^k n) = -\Omega(\log^k n) \to -\infty$：

$$
\boxed{\frac{\mathbb{E}[q]}{n^{1-\varepsilon}} \leq 2^{-\Omega(\log^k n)} \to 0 \quad\Longrightarrow\quad \mathbb{E}[q] \ll n^{1-\varepsilon}} \tag{2.6}
$$

>  $\mathbb{E}[q] \approx n / 2^{\text{poly}\log n}$ 的分母是**超多项式**级别——比任何 $n^c$ 都大。而 $n^{1-\varepsilon}$ 只是多项式级别。因此 $\mathbb{E}[q]$ 相比 $n^{1-\varepsilon}$ 完全可以忽略。这保证了 McDiarmid 中 $t = n^{1-\varepsilon} - \mathbb{E}[q] = \Theta(n^{1-\varepsilon})$，偏差 $t$ 几乎就是阈值本身。

---

#### 步骤 3/4：代入 McDiarmid 不等式

> **【论文中间结论】** $\Pr[q \geq n^{1-\varepsilon}] \leq \exp\!\left(-\Omega(n^{1-2\varepsilon-2\delta})\right)$

[McDiarmid不等式](数学工具.md#mcdiarmid不等式) ：$\displaystyle \Pr[f - \mathbb{E}[f] \geq t] \leq \exp\!\left(-\frac{2t^2}{\sum_{i=1}^{m} c_i^2}\right)$

逐一带入已知数据：

- **函数**：$f = q$（溢出球总数）
- **偏差**：$t = n^{1-\varepsilon} - \mathbb{E}[q]$。由 (2.6) 知 $\mathbb{E}[q] \ll n^{1-\varepsilon}$，故 $t = n^{1-\varepsilon}(1 - o(1)) = \Theta(n^{1-\varepsilon})$
- **有界差分常数**：$c_i = L = n^\delta$（步骤 1），共 $m$ 个节点
- **节点数上界**：$m \leq B = O(n)$（每个节点至少含 1 个球），故存在常数 $c_m > 0$ 使 $m \leq c_m \cdot n$（对充分大的 $n$）

**分母上界**（由 $m \leq c_m n$）：

$$
\sum_{i=1}^{m} c_i^2 = m \cdot (n^\delta)^2 = m \cdot n^{2\delta} \leq c_m \cdot n \cdot n^{2\delta} = c_m \cdot n^{1+2\delta} \tag{3.1}
$$

**分子下界**（由 $t = n^{1-\varepsilon}(1 - o(1))$，存在常数 $a > 0$ 使 $t^2 \geq a \cdot n^{2-2\varepsilon}$）。

将 $f = q$、$t = n^{1-\varepsilon} - \mathbb{E}[q]$ 带入 McDiarmid 不等式。注意 $\Pr[q \geq n^{1-\varepsilon}] = \Pr[q - \mathbb{E}[q] \geq n^{1-\varepsilon} - \mathbb{E}[q]]$（两边同减常数 $\mathbb{E}[q]$，事件等价）：

$$
\Pr[q \geq n^{1-\varepsilon}] \leq \exp\!\left(-\frac{2\big(n^{1-\varepsilon} - \mathbb{E}[q]\big)^2}{\displaystyle\sum_{i=1}^{m} c_i^2}\right) \tag{3.2}
$$

**计算指数中分式的量级**——分子取下界、分母取上界，得到分式的下界：

$$
\frac{2t^2}{\sum c_i^2}
\;\geq\; \frac{2 \cdot a \cdot n^{2-2\varepsilon}}{c_m \cdot n^{1+2\delta}}
\;=\; \frac{2a}{c_m} \cdot n^{\,1 - 2\varepsilon - 2\delta}
\;=\; \Omega\!\left(n^{\,1 - 2\varepsilon - 2\delta}\right) \tag{3.3}
$$

> **【方向检查】** 分子取下界（$t^2 \geq a n^{2-2\varepsilon}$，分式变小）+ 分母取上界（$\sum c_i^2 \leq c_m n^{1+2\delta}$，分式也变小）= 分式的**保守下界**。由于 $\exp(-x)$ 递减，下界代入后给出概率的**有效上界**：$\geq$ 取负变 $\leq$，取 $\exp$（递增）保持方向。

将下界代入 (3.2)，常数 $\frac{2a}{c_m}$ 被 $\Omega$ 吸收：

$$
\boxed{\Pr[q \geq n^{1-\varepsilon}] \leq \exp\!\left(-\Omega\!\left(n^{\,1 - 2\varepsilon - 2\delta}\right)\right)} \tag{3.4}
$$

> **【验证论文中间结论】** (3.4) 与论文的 McDiarmid 结论一致：指数为 $n$ 的正幂次（$1 - 2\varepsilon - 2\delta > 0$），远强于第 3 节 Chernoff 给出的 $\text{poly}\log n$ 级别。若扇出仍为 $n$（$\delta = 1$），指数退化为 $1 - 2\varepsilon - 2 = -1 - 2\varepsilon < 0$，界平凡——这从反面印证了缩小扇出的必要性。

---

#### 步骤 4/4：参数选取与换底——到达目标概率

> **【论文最终形式】** 取 $\varepsilon = \delta$（以扇出参数统一表示），得 $e^{-\Omega(n^{1-4\delta})}$，换底后即 $O(1/n^{n^{1-\delta}})$

由 (3.4)，McDiarmid 给出 $\Pr[q \geq n^{1-\varepsilon}] \leq \exp\!\left(-\Omega(n^{1-2\varepsilon-2\delta})\right)$。要使指数为正，需要 $2\varepsilon + 2\delta < 1$。

**线性空间约束**：$Q$ 的空间为 $q \cdot n^\delta$。若 $q \leq n^{1-\varepsilon}$ 以高概率成立，则 $Q$ 的空间 $\leq n^{1-\varepsilon+\delta}$。为保证线性（$\subset O(n)$），需 $1 - \varepsilon + \delta \leq 1$，即 $\delta \leq \varepsilon$。以下参数选取均满足此约束。

---

**参数选取**。展开 $\Omega$：存在常数 $c > 0$ 使 $\Pr[q \geq n^{1-\varepsilon}] \leq \exp(-c \cdot n^{1-2\varepsilon-2\delta})$（对充分大的 $n$）。

**方案 A（$\delta = \varepsilon/5$，双参数）**——这是原文为保证指数为正所设的具体数值：

$$
\delta = \frac{\varepsilon}{5} \;\Longrightarrow\; 1 - 2\varepsilon - 2\delta = 1 - 2.4\varepsilon \tag{4.1}
$$

代入 McDiarmid 界：

$$
\Pr[q \geq n^{1-\varepsilon}] \leq \exp\!\left(-c \cdot n^{\,1 - 2.4\varepsilon}\right) \tag{4.2}
$$

**方案 B（$\varepsilon = \delta$，单参数，原文采用）**——将阈值也以扇出参数表示，指数化为最简洁形式：

$$
\varepsilon = \delta \;\Longrightarrow\; 1 - 2\delta - 2\delta = 1 - 4\delta \tag{4.3}
$$

$$
\boxed{\Pr[q \geq n^{1-\delta}] \leq \exp\!\left(-c \cdot n^{\,1 - 4\delta}\right) = e^{-\Omega(n^{1-4\delta})}} \tag{4.4}
$$

> 方案 A 与方案 B 渐近等价（均为 $\exp(-\Omega(n^{\text{正常数}}))$）。方案 B 参数更少、形式更紧凑，即原文采用的写法。方案 A 保留 $\varepsilon$ 与 $\delta$ 便于展示阈值与扇出的独立可调性。下文以方案 A 为例完成换底，方案 B 的推导完全类似。

---

**换底（$\exp \to n$，到达目标概率）**。由 (4.2)：

$$
\Pr[q \geq n^{1-\varepsilon}] \leq \exp\!\left(-c \cdot n^{\,1 - 2.4\varepsilon}\right) \tag{4.5}
$$

换底 $\exp(A) = n^{A / \ln n}$（$\ln n = \Theta(\log n)$，常数因子与 $c$ 合并后重记为 $c'$）：

$$
\exp\!\left(-c \cdot n^{\,1 - 2.4\varepsilon}\right)
= n^{-c' \cdot n^{1 - 2.4\varepsilon} / \log n} \tag{4.6}
$$

现在**直接比较**指数中 $n^{1-2.4\varepsilon} / \log n$ 与目标指数 $n^{1-\varepsilon}$ 的大小：

$$
\frac{n^{1-2.4\varepsilon} / \log n}{n^{1-\varepsilon}}
= \frac{n^{\varepsilon - 2.4\varepsilon}}{\log n}
= \frac{n^{0.6\varepsilon}}{\log n} \;\xrightarrow{n \to \infty}\; \infty \qquad(\text{因 } \varepsilon > 0 \text{ 是常数}) \tag{4.7}
$$

因此对充分大的 $n$，$c' \cdot n^{1-2.4\varepsilon} / \log n \geq n^{1-\varepsilon}$。由于 $n^{-x}$ 是递减函数，指数越大 → 值越小：

$$
n^{-c' \cdot n^{1-2.4\varepsilon} / \log n} \;\leq\; n^{-n^{1-\varepsilon}} = \frac{1}{n^{\,n^{1-\varepsilon}}} \tag{4.8}
$$

联立 (4.5)–(4.8)，取 $O$ 记号即得目标形式：

$$
\boxed{\Pr[q \geq n^{1-\varepsilon}] \leq O\!\left(1 / n^{\,n^{1-\varepsilon}}\right)} \tag{4.9}
$$

> **【换底为什么这样写是对的】** 之前的版本尝试写成 $n^{-\Omega(n^{1-\varepsilon})} = O(1/n^{n^{1-\varepsilon}})$——这是**不成立的**。$\Omega$ 记号只保证存在某个常数 $c > 0$ 使界 $\leq n^{-c \cdot n^{1-\varepsilon}}$，但 $O(1/n^{n^{1-\varepsilon}})$ 要求 $c \geq 1$（或至少 $n^{-c n^{1-\varepsilon}} = O(n^{-n^{1-\varepsilon}})$）。正确的做法是**不通过 $\Omega$ 间接比较，而是直接比较指数大小**：$n^{1-2.4\varepsilon} / \log n$ 与 $n^{1-\varepsilon}$ 孰大孰小，由 (4.7) 可知前者远大于后者，代入递减函数 $n^{-x}$ 后方向翻转，得到更小的上界——恰好满足目标。

---

### 总结

$Q$ 的空间以极高概率维持在 $O(n^{1-\varepsilon} \cdot n^\delta) = O(n^{1-\varepsilon+\delta}) \subset O(n)$，整体字典保持线性空间。

**两个关键设计决策及其数学后果：**

1. **扇出 $n \to n^\delta$**：将 McDiarmid 的指数从 $n^{-1-2\varepsilon}$（平凡）提升至 $n^{1-2\varepsilon-2\delta}$（$n$ 的正幂次），使集中从"不存在"变为"极强"。
2. **McDiarmid 替代 Chernoff**：Chernoff 要求独立和（$q$ 不满足），McDiarmid 只需有界差分（扇出缩小后 $q$ 满足 $L = n^\delta$）。这是处理球间依赖关系的正确工具。

最终效果：失败概率从第 3 节的亚多项式级别（$1/n^{\text{poly}\log n}$）推至双指数级小（$O(1/n^{n^{1-\varepsilon}})$）。

****



## 引理3证明

### 约定：
1. 令 $\varepsilon > 0$ 为任意正常数
2. 假设机器字长为 $\omega = \Theta(\log n)$ 比特
3. 假设存在一个随机化线性空间字典，可以一次存储最多 $n$ 个 $logn$ 比特的键/值，且失败概率为 $1/n^{n^\varepsilon}$ 
4. 为了区分随机化字典和我们正在构造的确定性字典，我们将前者称为哈希表， 后者称为字典

/////////////////////////////////////这部分都需要改///////////////////////////////////////////
### 证明目标
存在一个确定性（不一定显式）的线性空间字典，存储 $n$ 个 $\log n$ 比特键/值，
失败概率不超过 $1/n^{n^\varepsilon}$。

### 步骤 1：生命周期归约
- 每 $O(n)$ 次操作重建字典（参见第 2 节约定），故不失一般性假设字典生命周期 $\le O(n)$ 次操作

### 步骤 2：用容量 $n'=cn$ 的哈希表实现字典
- $c$ 为待定大常数
- 哈希表失败概率：$1/(n')^{(n')^\varepsilon} = 1/(cn)^{(cn)^\varepsilon}$

### 步骤 3：操作序列计数
- 每个操作作用于 $\log n$ 比特键/值 → 单个操作至多 $n^{O(1)}$ 种可能
- 长度 $O(n)$ 的操作序列总数 $\le n^{O(n)}$

### 步骤 4：Union Bound
- 单个序列失败概率 $\le O(n) \cdot 1/(cn)^{(cn)^\varepsilon} \le 1/n^{cn^{\varepsilon/2}}$
- 存在某序列失败的概率 $\le n^{O(n)} / n^{cn^{\varepsilon/2}}$

### 步骤 5：参数选取
- 取 $c$ 足够大 → 上式 $\le 1/2$
- 故存在某种随机比特选择，使哈希表在**所有**操作序列上都常数时间

### 步骤 6：硬编码得确定性字典
- 将该随机比特选择硬编码 → 确定性常数时间字典

### 步骤 7：空间分析
- 哈希表在 $O(n)$ 操作上总耗时 $O(n)$ → 使用随机比特 $\le O(nw) = O(n\log n)$
- 故确定性字典可在线性空间内硬编码这些随机比特

$$\boxed{\text{存在确定性线性空间常数时间字典，失败概率} \le 1/n^{n^\varepsilon}}\quad$$
/////////////////////////////////////////////////////////////////////////////////////////////

---
# 预算旋转基数树

## 关于 $a_i$ 的证明

### 约定

### 证明

### 结论

## 关于 $b_i$ 的证明

### 约定

### 证明

### 结论
## 定理5证明

### 约定

### 证明

### 结论