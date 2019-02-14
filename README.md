# 异常点检测算法
---
## DBSCAN算法
### DBSCAN算法的原理如下：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;DBSCAN是基于一组邻域来描述样本集的紧密程度的，参数(ϵ, MinPts)用来描述邻域的样本分布紧密程度。其中，ϵ描述了某一样本的邻域距离阈值，MinPts描述了某一样本的距离为ϵ的邻域中样本个数的阈值。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;假设我的样本集是D=(x1,x2,...,xm),则DBSCAN具体的密度描述定义如下：

&nbsp;&nbsp;1） ϵ-邻域：对于xj∈D，其ϵ-邻域包含样本集D中与xj的距离不大于ϵ的子样本集，即Nϵ(xj)={xi∈D|distance(xi,xj)≤ϵ}, 这个子样本集的个数记为|Nϵ(xj)|　

&nbsp;&nbsp;2) 核心对象：对于任一样本xj∈D，如果其ϵ-邻域对应的Nϵ(xj)至少包含MinPts个样本，即如果|Nϵ(xj)|≥MinPts，则xj是核心对象。　

&nbsp;&nbsp;3）密度直达：如果xi位于xj的ϵ-邻域中，且xj是核心对象，则称xi由xj密度直达。注意反之不一定成立，即此时不能说xj由xi密度直达, 除非且xi也是核心对象。

&nbsp;&nbsp;4）密度可达：对于xi和xj,如果存在样本样本序列p1,p2,...,pT,满足p1=xi,pT=xj, 且pt+1由pt密度直达，则称xj由xi密度可达。也就是说，密度可达满足传递性。此时序列中的传递样本p1,p2,...,pT−1均为核心对象，因为只有核心对象才能使其他样本密度直达。注意密度可达也不满足对称性，这个可以由密度直达的不对称性得出。

&nbsp;&nbsp;5）密度相连：对于xi和xj,如果存在核心对象样本xk，使xi和xj均由xk密度可达，则称xi和xj密度相连。注意密度相连关系是满足对称性的。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从下图可以很容易看出理解上述定义，图中MinPts=5，红色的点都是核心对象，因为其ϵ-邻域至少有5个样本。黑色的样本是非核心对象。所有核心对象密度直达的样本在以红色核心对象为中心的超球体内，如果不在超球体内，则不能密度直达。图中用绿色箭头连起来的核心对象组成了密度可达的样本序列。在这些密度可达的样本序列的ϵ-邻域内所有的样本相互都是密度相连的。
### 孤立森林算法的原理如下：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;与随机森林由大量决策树组成一样，iForest森林也由大量的树组成。iForest中的树叫isolation tree，简称iTree。iTree树和决策树不太一样，其构建过程也比决策树简单，因为其中就是一个完全随机的过程。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;假设数据集有N条数据，构建一颗iTree时，从N条数据中均匀抽样(一般是无放回抽样)出ψ个样本出来，作为这颗树的训练样本。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在样本中，随机选一个特征，并在这个特征的所有值范围内(最小值与最大值之间)随机选一个值，对样本进行二叉划分，将样本中小于该值的划分到节点的左边，大于等于该值的划分到节点的右边。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这样得到了一个分裂条件和左、右两边的数据集，然后分别在左右两边的数据集上重复上面的过程，直接达到终止条件。终止条件有两个，一个是数据本身不可再分(只包括一个样本，或者全部样本相同)，另外一个是树的高度达到log2(ψ)。不同于决策树，iTree在算法里面已经限制了树的高度。当然不限制也可以，只是算法为了效率考虑，只需要达到log2(ψ)深度即可。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;把所有的iTree树构建好了，就可以对测数据进行预测了。预测的过程就是把测试数据在iTree树上沿对应的条件分支往下走，直到达到叶子节点，并记录这过程中经过的路径长度h(x)，即从根节点，穿过中间的节点，最后到达叶子节点，所走过的边的数量(path length)。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最后，将h(x)带入，计算每条待测数据的异常分数(Anomaly Score)，其计算公式为：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![68d9227a2b92d227b818d2cfc3f3a285.svg+xml](evernotecid://D62F3227-D48A-4372-A2CB-4828C93A7DD3/appyinxiangcom/23148295/ENResource/p6)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其中 ![2d1ca65e37f5c5f70440468acb4d36cb.svg+xml](evernotecid://D62F3227-D48A-4372-A2CB-4828C93A7DD3/appyinxiangcom/23148295/ENResource/p7)
 是二叉搜索树的平均路径长度，用来对结果进行归一化处理, 其中的H(k)可以通过公式
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![ef8c45fcad6e77f6bf33d9714df0ddf1.svg+xml](evernotecid://D62F3227-D48A-4372-A2CB-4828C93A7DD3/appyinxiangcom/23148295/ENResource/p8)
 来估计，![b1e5418f3a213b2e601a067f6b40a603.svg+xml](evernotecid://D62F3227-D48A-4372-A2CB-4828C93A7DD3/appyinxiangcom/23148295/ENResource/p9)是欧拉常数，其值为0.5772156649。
![202373133cebd193e8e4b11c1f13c9e1.svg+xml](evernotecid://D62F3227-D48A-4372-A2CB-4828C93A7DD3/appyinxiangcom/23148295/ENResource/p10)为路径长度，
![609d96c9e7c680e838287d9eb753b636.svg+xml](evernotecid://D62F3227-D48A-4372-A2CB-4828C93A7DD3/appyinxiangcom/23148295/ENResource/p11)为森林中所有iTree树的平均路径长度。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用异常分数，具有以下性质：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果分数越接近1，其是异常点的可能性越高；如果分数都比0.5要小，那么基本可以确定为正常数据；如果所有分数都在0.5附近，那么数据不包含明显的异常样本。上面的步骤，可以总结为两步：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;训练：从训练集中进行采样，并构建iTree树；测试：对iForest森林中的每颗iTree树进行测试，记录path length，然后根据异常分数计算公式，计算每条测试数据的anomaly score。
