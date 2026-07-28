
如果按照标准旋转树的做法，为了生成这两个序列，我们需要为每一个内部节点 $s$ 独立地从真随机源中取出：
$\log n^\delta$ 比特给 $a_s$ 
$\log n^{1-\delta}$ 比特给 $b_s$
共需要 $m \times (\delta\log n + (1-\delta)\log n)=m\times \log n$ 个真随机比特
因为 $m = n/\text{poly}\log n$ , 所以总随机比特约为 $n/\text{poly}\log n \times \log n$ , 这依然是一个线性的量级，很大