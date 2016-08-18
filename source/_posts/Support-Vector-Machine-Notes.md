title: Support Vector Machine Notes
date: 2016-08-17 21:04:47
tags: [ml]
categories: Coding
---
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  TeX: { equationNumbers: { autoNumber: "AMS" } }
});
</script>
支持向量机的基本思路是在样本空间找到一个超平面（假设样本有 $n$ 个特征值，两类样本分别标记为`1`和`-1`），将两个类别的样本点区分开。

找到一个最好的超平面的思路则是找到刚好位于两类样本点的正中间，即鲁棒性最好。划分超平面的方程为：
$$
\begin{equation}
f(\boldsymbol{x}) = \boldsymbol{\omega}^T\boldsymbol{x} + b = 0
\end{equation}
$$
样本点到平面的距离为：
$$
\begin{equation}
r = \frac{\mid \boldsymbol{\omega}^T\boldsymbol{x} + b \mid}{\mid \boldsymbol{\omega} \mid}
\end{equation}
$$
距离划分超平面最近的样本点被称为**支持向量**，假设样本点均被超平面正确分类，令
$$
\begin{equation}
\begin{cases}
\boldsymbol{\omega}^T\boldsymbol{x_i} + b \ge 1, y_i = 1 \\
\boldsymbol{\omega}^T\boldsymbol{x_i} + b \le -1, y_i = -1 \\
\end{cases}
\end{equation}
$$

当上式等号成立时样本点为**支持向量**。则两个异类的支持向量到超平面的距离之和为
$$
\begin{equation}
\gamma = \frac{2}{\mid \omega \mid}
\end{equation}
$$
令上式取得最大值，即
$$
\begin{equation}
 \min_{\boldsymbol{\omega}, b} \frac{1}{2} \mid \boldsymbol{\omega} \mid ^2 \\
 s.t. \ \ y_i(\boldsymbol{\omega}^T\boldsymbol{x_i} + b) \ge 1
\end{equation}
$$

根据拉格朗日乘子法，上式的拉格朗日函数为
$$
\begin{equation}
L(\boldsymbol{\omega}, b, \boldsymbol{\alpha}) = \frac{1}{2} \mid \boldsymbol{\omega} \mid ^2 + \sum_{i=1}^{m}\alpha_i (1 - y_i(\boldsymbol{\omega}^T\boldsymbol{x_i} + b)), \ \ \alpha_i \ge 0
\end{equation}
$$

我们可以先将$\boldsymbol{\omega}$和$b$当作常数, 这时拉格朗日函数可以看成是仅关于$\boldsymbol{\alpha}$的函数.  函数取得最大值的时候显然有(6)式的第二项为0, 因为(5)式的约束条件下(6)式的第二项不可能为正.  这时有如下等式成立:
$$
\begin{equation}
\max_\boldsymbol\alpha L(\boldsymbol{\omega}, b, \boldsymbol{\alpha}) = \frac{1}{2} \mid \boldsymbol{\omega} \mid ^2
\end{equation}
$$

对(7)式求最小值, 即:
$$
\begin{equation}
\min_{\boldsymbol{\omega}, b}\max_\boldsymbol\alpha L(\boldsymbol{\omega}, b, \boldsymbol{\alpha})
\end{equation}
$$

求解(8)式我们可以这样做: 令$L(\boldsymbol{\omega}, b, \boldsymbol{\alpha})$对$\boldsymbol{\omega}$和$b$的偏导数为0得

$$
\begin{equation}
\boldsymbol{\omega} = \sum_{i=1}^{m}\alpha_i  y_i \boldsymbol{x}_i
\end{equation}
$$

$$
\begin{equation}
0 = \sum_{i=1}^{m}\alpha_i y_i \\
\end{equation}
$$
<!--more-->
联立(6), (9), (10)式得(5)式的**对偶问题**:
$$
\begin{equation}
\max_\boldsymbol\alpha\sum_{i=1}^{m}\alpha_i - \frac{1}{2}\sum_{i=1}^{m}\sum_{j=1}^{m}\alpha_i\alpha_jy_iy_j\boldsymbol{x}_i^T\boldsymbol{x}_j \\
s.t. \ \ \sum_{i=1}^{m}\alpha_iy_i = 0, \alpha_i \ge 0, i = 1, 2, ..., m
\end{equation}
$$

注意上式必须满足以下KKT条件:
$$
\begin{equation}
\begin{cases}
\alpha_i \ge 0  \\
y_if(\boldsymbol{x}_i) - 1 \ge 0  \\
\alpha_i(y_if(\boldsymbol{x}_i) - 1) = 0
\end{cases}
\end{equation}
$$

### Sequential Minimal Optimization(SMO) 算法
利用SMO算法可以快速求解(11)式.  SMO算法的思路是: 每次选取两个变量$\alpha_i$和$\alpha_j$, 固定其他变量, 这时问题转化为求解单变量的二次规划问题.  

### 核方法
对线性不可分的样本, 可以将样本空间映射到高维空间, 然后利用核函数代替内积进行计算.  简单来说就是将(11)式改写成以下式子:
$$
\begin{equation}
\max_\boldsymbol\alpha\sum_{i=1}^{m}\alpha_i - \frac{1}{2}\sum_{i=1}^{m}\sum_{j=1}^{m} \alpha_i \alpha_j y_i y_j k(\boldsymbol{x}_i, \boldsymbol{x}_j) \\
s.t. \ \ \sum_{i=1}^{m}\alpha_iy_i = 0, \alpha_i \ge 0, i = 1, 2, ..., m
\end{equation}
$$

常用的核函数有高斯核函数, 即
$$
k(\boldsymbol{x}_i, \boldsymbol{x}_j) = e^{-\frac{\mid \boldsymbol{x}_i - \boldsymbol{x}_j \mid}{2\sigma^2}}
$$
