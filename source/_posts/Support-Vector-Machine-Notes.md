title: Support Vector Machine Notes
date: 2016-08-17 21:04:47
tags: [ml]
categories: Coding
---
# 支持向量机
支持向量机的基本思路是在样本空间找到一个超平面（假设样本有 $ n $ 个特征值，两类样本分别标记为`1`和`-1`），将两个类别的样本点区分开。

找到一个最好的超平面的思路则是找到刚好位于两类样本点的正中间，即鲁棒性最好。划分超平面的方程为：
$$
\begin{equation}
\boldsymbol{\omega}^T\boldsymbol{x} + b = 0
\end{equation}
$$
样本点到平面的距离为：
$$
\begin{equation}
r = \frac{\mid \boldsymbol{\omega}^T\boldsymbol{x} + b \mid}{\mid \boldsymbol{\omega} \mid}
\end{equation}
$$
<!--more-->
距离划分超平面最近的样本点被称为`支持向量`，假设样本点均被超平面正确分类，令
$$
\begin{equation}
\begin{cases}
\boldsymbol{\omega}^T\boldsymbol{x_i} + b \ge 1, y_i = 1 \\
\boldsymbol{\omega}^T\boldsymbol{x_i} + b \le -1, y_i = -1 \\
\end{cases}
\end{equation}
$$

当上式等号成立时样本点为支持向量。则两个异类的支持向量到超平面的距离之和为
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
L(\boldsymbol{\omega}, b, \boldsymbol{\alpha}) = \frac{1}{2} \mid \boldsymbol{\omega} \mid ^2 + \sum_{i=1}^{m}\alpha_i (1 - y_i(\boldsymbol{\omega}^T\boldsymbol{x_i} + b))
\end{equation}
$$
