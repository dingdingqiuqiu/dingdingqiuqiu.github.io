---
title: ML-lab1_利用梯度下降法训练线性回归模型
date: 2024-09-17 23:35:23
mathjax: true
tags: 机器学习
categories:
---

本文用python代码实现了利用梯度下降法训练线性回归模型

<!--more-->

该实验的Github链接：[dingdingqiuqiu/ML](https://github.com/dingdingqiuqiu/ML)

> 文中重敲的代码可能有问题，但github上的一定都是可运行的
### 理论依据

> 我们假设有n个未确定的系数，有m个数据来帮助我们训练。

在机器学习中，梯度下降的基本公式（以最小化损失函数为例）的 LaTeX 表达式为：

$$
\theta_{i} =
\theta_{i} - \alpha \frac{ \partial} { \partial \theta_{i} } J( \theta )
$$

其中，$\theta_{j}$表示模型参数，$\alpha$是学习率，$J(\theta)$是损失函数。
假设我们要确定以下不含偏置项的多元线性方程：
$$
Y(x_{i}) = \theta_{1}x_{1}+\theta_{2}x_{2}+\theta_{3}x_{3}+ ... +\theta_{n}x_{n}
$$
对于含有偏置项b的多元线性方程，我们令第n+1项x恒为为1即可。

在线性回归方程的确定中，我们以均方误差作为损失函数，其表达式为:
$$
E(x_{i}) = \frac{\sum_{i = 1}^{m}(\hat{y}-y_{i})^2}{m}
$$
对于任意一个参数$x_{i}$求偏微分，得到：
$$
\frac{\partial}{\partial\theta_{i}}J(\theta) = \frac{2}{m} \sum_{i = 1}^{m}x_{i}(\hat{y}-y_{i})
$$
用矩阵表示为
$$
\frac{\partial}{\partial\theta}J(\theta) =  \frac{2}{m}(X^{T}(\hat{Y} - Y))
$$
或
$$
\frac{\partial}{\partial\theta}J(\theta) =  \frac{2}{m}(X^{T}(\theta X - Y))
$$
### 必备库导入

```
import numpy as np
import matplotlib.pylot as plt
```

主要引入两个缩写，一个`np`,对应`numpy`科学计算库;一个`plt`对应`matplotlib`绘图库中的`pylot`模块。

同时用下面两行代码解决了`plt`模块中的中文绘图显示异常的问题。

```python
# 设置全局字体
plt.rcParams['font.sans-serif'] = ['SimHei']
# 解决Matplotlib在使用中文字体时，负号显示为方块的问题
plt.rcParams['axes.unicode_minus'] = False
```

`pylot`模块中的`rcParams`是一个全局配置对象，实际上是一个字典对象，通过修改键值对的值。可以全局改变`matplotlib`绘图库的行为
> 在linux环境下设置字体略有问题，解决方法在下面这篇blog中：[linux中Matplotlib画图缺少字体](https://dingdingqiuqiu.github.io/2024/09/15/linux%E7%8E%AF%E5%A2%83%E4%B8%ADmatplotlib%E7%94%BB%E5%9B%BE%E7%BC%BA%E5%B0%91%E5%AD%97%E4%BD%93/#more)

### 读取数据文件
```
data = np.loadtxt('data1.txt', delimiter=',')
X = data[:, 0].reshape(-1, 1)
Y = data[:, 1].reshape(-1, 1)
```
`data1.txt`中数据如下：
```
6.1101,17.592
5.5277,9.1302
8.5186,13.662
7.0032,11.854
5.8598,6.8233
8.3829,11.886
7.4764,4.3483
8.5781,12
...
```
调用`numpy`库中的`loadtxt`函数将数据读取为n行2列的二维矩阵。
```
[[ 6.1101  17.592  ]
 [ 5.5277   9.1302 ]
 [ 8.5186  13.662  ]
 [ 7.0032  11.854  ]
 [ 5.8598   6.8233 ]
 [ 8.3829  11.886  ]
 [ 7.4764   4.3483 ]
 [ 8.5781  12.     ]
    ...            ]
```
我们利用`data[:,0]`提取第一列中的所有行，并利用reshape方法重塑矩阵为n行1列的二维矩阵。
`data[:,1]`同理提取第二列数据并重塑为n行1列的二维矩阵。第一列部分数据如下：
```
[[ 6.1101]
 [ 5.5277]
 [ 8.5186]
 [ 7.0032]
 [ 5.8598]
 [ 8.3829]
   ...   ]
```
我们添加偏置项b对应的$X_{m+1}$恒为1。并将其与$X$矩阵合并得到$X_b$矩阵。
```
X_b = np.c_[np.ones((X.shape[0], 1)), X]
```
### 初始状态设置
```
# 超参数
learning_rate = 0.01
n_iterations = 1000
m = X.shape[0]

# 初始化 theta
theta = np.random.randn(2, 1)

```
由于我们要求得两个参数的值，这里我们初始化`theta`为2行1列的二维矩阵。

### 梯度下降
```python
# 梯度下降
for iteration in range(n_iterations):
    gradients = 2/m * X_b.T.dot(X_b.dot(theta) - Y)
    theta = theta - learning_rate * gradients

print(f"训练得到的参数: {theta}")
```
由前面推导得到的矩阵形式公式，我们进行有限次数的迭代后输出结果即可。
### 结果预测



















