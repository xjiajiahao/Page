title: 卷积神经网络（CNN）漫谈
date: 2016-09-01 15:15:14
tags:
---

卷积神经网络（Convolutional Neural Network，简称CNN）是一种典型的深度学习模型，它适合处理多矩阵表示的数据，经常用于图像理解，相关的工作有将图片的信息转换为文字，人脸识别等。卷积神经网络的特点是卷积层和汇集层。卷积层的临近的单元组成特征图，特征图中的每个单元连接到上一层的一个局部区域，并共享权值。卷积层的作用是计算局部的特征。汇集层则把语义相似的特征合并。利用反向传播可以对所有层的权重进行训练。

<!--more-->

### 卷积

假设有一张$imageDim \times imageDim$的图像，我们可以一次只对一小块区域（比如说$8\times8$）进行特征学习，也就是说隐藏层的单元只连接到特定区域的输入单元，而不是全部，这样的话我们需要学习的权重的维度就变为
$$ filterDim \times filterDim \times numFilters $$
其中$numFilters$表示需要学习的特征图的数量。将特征矩阵与图像进行二维的卷积（参见[Matlab的conv2函数](http://cn.mathworks.com/help/matlab/ref/conv2.html)），得到卷积后的特征矩阵，矩阵的维度是：
$$
convDim \times convDim \times numFilters,
where \ \ convDim = imagDim - filterDim + 1
$$


### 池化
卷积后的特征图需要经过池化（Pooling），以减少特征数目并防止过拟合。假设选取的池化区域的大小为$poolDim \times poolDim$，我们把卷积后的特征图分为不相交的$poolDim \times poolDim$区域，对每个区域内的特征取平均值（或最大值），作为池化层的特征值，得到的特征值可以用于分类。Matlab代码见Github（[cnnPool.m](https://github.com/xjiajiahao/UFLDL-Exercises/blob/master/cnn/cnnPool.m)）。

### 随机梯度下降（SGD）
标准的梯度下降算法是利用整个训练集计算损失函数（cost function），然后更新参数：
$$
\theta = \theta - \alpha \nabla_{\theta}E(J(\theta))
$$

而随机梯度下降在计算损失函数时只取了一个或几个训练样例（一般会采用一小部分样例而不是一个）：
$$
\theta = \theta - \alpha \nabla_{\theta}J(\theta; x^{(i)}, y^{(i)})
$$

SGD的学习速度$\alpha$比batch gradient descent的要小得多得多。更新$\alpha$，或者说退火（annealing）的方法有几种：

1. 初始化学习率为一个足够小的常数，然后在收敛减慢的时候减半
2. 每次迭代后对验证集计算目标函数，当相邻两次迭代之间的目标函数值之差小于一个很小的阈值时，降低学习率
3. 在第$t$次迭代，将$\alpha$更新为$\frac{a}{b + t}$，其中$a$和$b$分别表示初始学习率和开始退火时的当前迭代数

#### 动量
当目标函数的优化方向是一条长长的浅沟，而两边有很多陡峭的墙壁时，标准SGD收敛会比较慢。这种情况下，采用动量的方法可以加快学习的速度。动量更新的公式如下：
$$
\nu = \gamma\nu + \alpha\nabla_{\theta}J(\theta; x^{(i)}, y^{(i)}) \ \ where \ \ \gamma \in (0, 1] \\
\theta = \theta - \nu
$$

### 卷积神经网络（CNN）
在一个卷积神经网络中，先通过一个或多个卷积层，然后再进入一个或多个完全连接层，也就是说卷积神经网络比标准的多层神经网络多了卷积层。因为卷积层和二次抽样比较特殊，因此神经网络的正向传播和反向传播（Forward Propagation）也与标准的神经网络略有不同。标准多层神经网络的正向传播是这样的：
$$
z^{(l + 1)} = W^{(l)}a^{(l)} + b \\
a^{(l + 1)} = f(z^{(l + 1)}) \\
where \ \  a^{(1)} = x, \ \ h_{W, b}(x) = f(z^{(n_{l})}) \\
$$
若第 $l$ 层为卷积层，则$z^{(l)}$和$a^{(l)}$的计算要替换为卷积和池化的计算。

误差函数的计算同多层神经网络，即平方误差加weight decay：
$$
J(W, b) = \frac{1}{m}\sum_{i = 1}^{m}(\frac{1}{2} \| h_{W, b}(x^{(i)}) - y^{(i)} \|^2) + \frac{1}{2}\sum_{l = 1}^{n_l - 1}\sum_{i = 1}^{s_l}\sum_{j = 1}^{s_{l + 1}}(W_{ji}^{(l)})^2
$$

#### 反向传播
多层神经网络的反向传播（Back Propagation）算法则是这样：

1. 先正向传播，计算每一层的激活函数

2. $$ \delta^{(n_{l})} = -(y -a^{(n_{l})}) \textstyle \bullet f'(z^{(n_{l})}) $$

3. $$\delta^{(l)} = ((W^{(l)})^{T}\delta^{(l + 1)}) \textstyle \bullet f'(z^{(l)}) $$

4. $$ \nabla_{W^{(l)}}J = \delta^{(l + 1)}(a^{(l)})^{T} \\ \nabla_{b^{(l)}}J = \delta^{(l + 1)} $$

其中，f(z)为激活函数，例如`tanh`，`sigmoid`, `rectified linear`函数等。

如果第 $l$ 层为卷积池化层，则误差需要用下式计算：
$$
\delta_k^{(l)} = \text{upsample}\left((W_k^{(l)})^T \delta_k^{(l+1)}\right) \bullet f'(z_k^{(l)})
$$
其中`upsample`是指计算进入池化层之前的每个单元的误差项，注意mean pooling和max pooling的计算会有差异。卷积层的梯度公式如下：
$$
\delta_{W_k^{(l)}}J(W, b; x, y) = \sum_{i = 1}^m(a_i^{(l)}*\text{rot90}(\delta_k^{(l + 1)}, 2), \\
\delta_{b_k^{(l)}}J(W, b; x, y) = \sum\delta_k^{(l + 1)}
$$

--- 

### 参考资料

1. LeCun, Y., Bottou, L., Bengio, Y. & Haffner, P. Gradient-based learning applied to document recognition. *Proc. IEEE* **86**, 2278–2324 (1998).
2. 深度学习教程[UFLDL](http://ufldl.stanford.edu/tutorial/): Supervised Convolutional Neural Network
3. [我的UFLDL/CNN练习代码](https://github.com/xjiajiahao/UFLDL-Exercises/tree/master/cnn)

