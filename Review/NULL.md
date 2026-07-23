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
（旋转基数树的构造背景参见 [A_Hash_Table_Without_Hash_Functions 中"使用随机性节省空间：旋转基数树"一节](A_Hash_Table_Without_Hash_Functions.md#使用随机性节省空间旋转基数树-The-Rotated-Trie)。）

证明目标：以关于 $n$ 的高概率，$A$ 的每个条目负责存储来自最多 $\Delta = \text{poly}\log n$ 个不同 $A_i$ 的条目。

树中共有[O(n)个球](球数为什么是O(n)【补充n叉树结构】.md#所以球的总数)。每个球 $b$ 由一个对 $(s,c) \in [m] \times [n]$ 指定，其中 $s \in [m]$ 是球的**源节点**（即包含该球的节点），$c \in [n]$ 是球的 **孩子索引**（即 $A_i$ 中 $b$ 在逻辑上存储的索引，此处，节点储存子节点的数组与全局数组的大小相等）

对于 $i \in [m]$ 和 $j \in [n]$ ，令 $X_{i,j}$ 指示节点 $i$ 是否向桶 $j$ 放入一个球的 0-1 随机变量。
设桶 $j$ 中的球数是 $Y_j$ ，根据[桶-节点间的独立性](桶-节点间的独立性.md#为什么在节点-i-之间是独立的)可知，$Y_j$ 是独立指示随机变量之和。

**原文：”$O(n)$ 个球中的每一个在桶 $j$ 中的概率是 $1/n$，所以 $E[Y_j] = O(1)$。因此，由[切尔诺夫界](数学工具.md#切尔诺夫界-Chernoff-Bound)可知，$Y_j \leq \text{poly}\log n$ 以关于 $n$ 的高概率成立。Chernoff 界实际上告诉我们 $\text{poly}\log n$ 以概率 $1/n^{\text{poly}\log n}$ 成立，因此我们甚至达到了略微亚多项式的失败概率。“**

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

取阈值 $\Delta = \log^k n$（$k>0$），令 $\delta = \Delta/\mu - 1$，则 $1+\delta = \Delta/\mu$。由 [Chernoff 上尾界](数学工具.md#定理陈述)：

$$
P(Y_j \geq \Delta) = P\big(Y_j \geq (1+\delta)\mu\big) \leq \left(\frac{e^{\delta}}{(1+\delta)^{1+\delta}}\right)^{\!\mu}
$$

代入 $\delta = \frac{\Delta}{\mu}-1$，$1+\delta = \frac{\Delta}{\mu}$，逐步化简：

$$
\begin{aligned}
\left(\frac{e^{\delta}}{(1+\delta)^{1+\delta}}\right)^{\!\mu}
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

由于 $\ln\Delta = k\ln\log n \to \infty$，$|-\Delta\ln\Delta| / |O(\Delta)| = \Theta(\ln\log n) \to \infty$，故 $-\Delta\ln\Delta$ 主导：

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

全局共 $n$ 个桶，由 Union Bound：

$$
P\big(\exists j \in [n]: Y_j \geq \Delta\big) \leq \sum_{j=1}^{n} P(Y_j \geq \Delta) \leq \frac{n}{2^{\Omega(\log^k n)}}
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


为了严格证明该数据结构在最坏的情况下的空间消耗上界是线性的，我们必须证明（[为什么？](线性空间以及概率与不等式的关系.md)）：
$$
Pr[q>=n^{1-\varepsilon}] \leq O(1/n^{n^{1-\varepsilon}})
$$
## 界定溢出球的数量：

### 约定：

- $\Delta = \text{poly}\log n$ 为每个桶的容量。
- 扇出降至 $n^\delta$（$\delta > 0$ 常数），每个内部节点至多 $n^\delta$ 个球，内部节点数 $m \geq n^{1-\delta}$。
- 球 $(s,c)$ 落入桶 $\phi(s,c) = (r_s + c) \bmod n$（$s \in [m]$ 为源节点，$c \in [n^\delta]$ 为孩子索引，$r_s \in [n]$ 均匀选取）。
- 桶满（$\geq \Delta$ 个球）后，新增球进入辅助 $n^\delta$ 叉基数树，称为**溢出球**。$Q$ 空间 $\propto q \cdot n^\delta$，故必须证明（[线性空间约束](线性空间以及概率与不等式的关系.md)）
---

### 计算：

设总球数 $B = O(n)$。令 $q = f(r_1, \ldots, r_m)$ 为溢出球总数。$q$ 不是独立随机变量之和（同一源节点的球共享 $r_s$），无法直接用 Chernoff 界，转而使用 [McDiarmid 不等式](数学工具.md#McDiarmid-不等式McDiarmids-Inequality)。

#### 1. 有界差分常数

改变单个旋量 $r_s$，至多影响源节点 $s$ 内的 $n^\delta$ 个球——它们集体换桶，最坏情况下全部改变溢出状态。故 $f$ 满足 $n^\delta$-[有界差分条件](数学工具.md#有界差分条件)：

$$
L = n^\delta
$$

> **$L$ 的作用**：McDiarmid 的指数为 $2t^2/(mL^2)$，$L$ 在分母上取平方——$L$ 越小，集中越强。若扇出仍为 $n$，则 $L = n$，指数 $\sim n^{1-2\varepsilon} / n^2 = n^{-1-2\varepsilon} \to 0$，界是平凡的。将扇出压至 $n^\delta$ 后 $L = n^\delta$，指数 $\sim n^{1-2\varepsilon-2\delta}$，取 $\delta = \varepsilon/5$ 即得正的 $n$ 的幂次——这是放大旋转基数树能将失败概率压至双指数级小的根本原因。

#### 2. $\mathbb{E}[q]$ 的估计

由[第 3 节 Chernoff 分析](#计算)，单个桶 $j$ 球数超限的概率：

$$
P(Y_j \geq \Delta) \leq 1\,/\,2^{\Omega(\text{poly}\log n)}
$$

记 $\mathcal{O}_j$ 为桶 $j$ 的溢出球数。$\mathcal{O}_j \leq Y_j$，且仅当 $Y_j > \Delta$ 时非零。最坏情况下 $Y_j \leq O(n)$，故：

$$
\mathbb{E}[\mathcal{O}_j] \leq O(n) \cdot P(Y_j \geq \Delta) \leq \frac{O(n)}{2^{\Omega(\text{poly}\log n)}} = \frac{1}{2^{\Omega(\text{poly}\log n)}}
$$

对 $n$ 个桶求和：

$$
\boxed{\mathbb{E}[q] = \sum_{j=1}^{n} \mathbb{E}[\mathcal{O}_j] \leq \frac{n}{2^{\Omega(\text{poly}\log n)}} = \frac{n}{\text{poly } n} \ll n^{1-\varepsilon}}
$$

#### 3. 代入 McDiarmid

由 [McDiarmid 单侧界](数学工具.md#定理陈述)，取 $t = n^{1-\varepsilon} - \mathbb{E}[q]$。$\mathbb{E}[q] \ll n^{1-\varepsilon}$，故 $t = \Theta(n^{1-\varepsilon})$。代入 $m \leq B = O(n)$、$L = n^\delta$：

$$
\begin{aligned}
\Pr[q \geq n^{1-\varepsilon}]
\leq \exp\!\left(-\frac{2\big(n^{1-\varepsilon} - \mathbb{E}[q]\big)^2}{m \cdot L^2}\right) = \exp\!\left(-\Omega\!\left(\frac{n^{2-2\varepsilon}}{n \cdot n^{2\delta}}\right)\right) = \exp\!\left(-\Omega\!\left(n^{\,1 - 2\varepsilon - 2\delta}\right)\right)
\end{aligned}
$$

#### 4. 参数选取与最终形式

指数 $1 - 2\varepsilon - 2\delta$ 必须为正。取 $\delta = \varepsilon / 5$（原文设定）：

$$
1 - 2\varepsilon - 2\cdot\frac{\varepsilon}{5} = 1 - 2.4\varepsilon
$$

$\varepsilon < 1/2.4$ 时指数为正，更精细的分析可将 $\varepsilon$ 推至任意正常数。于是：

$$
\Pr[q \geq n^{1-\varepsilon}] \leq \exp\!\left(-\Omega\!\left(n^{\,1 - O(\varepsilon)}\right)\right)
$$

换底（$\exp(x) = n^{x / \ln n}$，$\ln n = \Theta(\log n)$）：

$$
\exp\!\left(-\Omega(n^{1-O(\varepsilon)})\right) = n^{-\Omega(n^{1-O(\varepsilon)} / \log n)} = n^{-\Omega(n^{1-\varepsilon})}
$$

调整 $\varepsilon$ 即得目标形式：

$$
\boxed{\Pr[q \geq n^{1-\varepsilon}] \leq O\!\left(1 / n^{\,n^{1-\varepsilon}}\right)}
$$

---

### 总结：

$(★)$ 式成立。$Q$ 的空间以极高概率维持在 $O(n^{1-\varepsilon} \cdot n^\delta) = O(n^{1-\varepsilon+\delta}) \subset O(n)$，整体字典保持线性空间。关键：McDiarmid 的指数含 $n$ 的正幂次（$\Omega(n^{1-O(\varepsilon)})$），远大于第 3 节 Chernoff 给出的 $\text{poly}\log n$ 级别，将失败概率从亚多项式推至双指数级小。