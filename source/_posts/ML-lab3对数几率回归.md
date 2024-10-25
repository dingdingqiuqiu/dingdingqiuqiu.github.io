---
title: ML-lab3对数几率回归
date: 2024-09-17 23:35:23
mathjax: true
tags: 机器学习
categories:
---

本文用python代码实现了利用梯度下降法训练线性回归模型

<!--more-->

该实验的Github链接：[dingdingqiuqiu/ML](https://github.com/dingdingqiuqiu/ML)

> 文中重敲的代码可能有问题，但github上的一定都是可运行的

### 必备库导入

```python
# 导入必要的库
import numpy as np
import matplotlib.pyplot as plt
import matplotlib as mpl
from sklearn.metrics import accuracy_score
```

`np`库进行矩阵运算，`plt`模块进行2D绘图，`mpl`库用于更复杂3D图像的绘制。而`accuracy_score`函数则是`sklearn`包里的`metrics`模块中的用于评估机器学习中模型性能指标的函数。

### 读取数据文件并绘图

我们从代码入手，探讨项目文件的读取逻辑

```python
# 加载数据
def loaddata():
    # 从文本文件中加载数据
    data = np.loadtxt('data1.txt', delimiter=',')
    n = data.shape[1] - 1  # 特征数
    X = data[:, 0:n]  # 特征矩阵
    y = data[:, -1].reshape(-1, 1)  # 标签向量
    return X, y
```

代码第一行加载数据，将除了最后一列的数据作为X矩阵，即特征矩阵，将最后一列数据作为标签向量，并重塑为二维矩阵，方便运算。

我们来尝试数据图的绘制部分

```python
def plot(X, y):
    # 绘制数据点
    pos = np.where(y == 1)  # 正样本索引
    neg = np.where(y == 0)  # 负样本索引
    plt.scatter(X[pos[0], 0], X[pos[0], 1], marker='x', label='Positive')
    plt.scatter(X[neg[0], 0], X[neg[0], 1], marker='o', label='Negative')
    plt.xlabel('Exam 1 score')  # x轴标签
    plt.ylabel('Exam 2 score')  # y轴标签
    plt.legend()  # 显示图例
    # plt.show()
    plt.savefig('dataAndLine.png')
    plt.close()
```

先保存正负样本点的索引，然后在以特征矩阵X中的正负样本的第一列特征和第二列特征为x和y将点绘制为'x'和'o'设置对应标签，添加x轴的标签和y轴的标签并显示前面正负标签的图例。

### 初始状态设置

```python
X, y = loaddata()  # 再次加载数据
n = X.shape[1]  # 特征数
theta = np.zeros(n + 1).reshape(n + 1, 1)  # 初始化参数theta
iterations = 250000  # 迭代次数
alpha = 0.008  # 学习率
```

进行了各个参数的初始化，主要包括theta和iterations以及alpha。

### 梯度下降

```python
theta = gradientDescent(X, y, theta, iterations, alpha)  # 执行梯度下降
print('theta=n', theta)  # 输出theta
```

调用了gradientDescent函数，我们来看函数的定义过程。

```python
def gradientDescent(X, y, theta, iterations, alpha):
    m = X.shape[0]  # 样本数
    X = np.hstack((np.ones((m, 1)), X))  # 在X前加一列1（偏置项）
    for i in range(iterations):
        # 计算梯度并更新参数
        theta_temp = theta - (alpha / m) * np.dot(X.T, (hypothesis(X, theta) - y))  # 梯度下降更新
        theta = theta_temp  # 更新theta
        # 每10000次迭代输出一次损失值
        if (i % 10000 == 0):
            print('第', i, '次迭代，当前损失为：', computeCost(X, y, theta), 'theta=', theta)
    return theta
```

首先先给特征矩阵前添加了一列1,这是为了方便偏置项b的计算。然后，进行参数迭代更新，在每次更新时，计算梯度，并更新参数。同时每10000次输出一次损失值。下面我们深度分析梯度更新时的参数计算过程

```python
theta_temp = theta - (alpha / m) * np.dot(X.T, (hypothesis(X, theta) - y))  # 梯度下降更新
theta = theta_temp  # 更新theta

def sigmoid(z):
    # 计算Sigmoid函数
    r = 1 / (1 + np.exp(-z))  # Sigmoid公式
    return r

def hypothesis(X, theta):
    # 计算假设函数值
    z = np.dot(X, theta)  # 线性组合
    return sigmoid(z)  # 返回Sigmoid值

```

此处计算时，主要计算思路与线性方程的梯度下降计算过程基本一致，不过在进行假设函数值的计算时，我们引入了`sigmoid`函数,主要是将函数的输出映射到[0,1]之间，用来表示某样本是否属于正类的概率,同时可以利用非线性映射拟合更复杂的决策边界。由于引入了`sigmoid`函数，在计算损失函数时，计算过程也略有改变，如下:
```python
def computeCost(X, y, theta):
    m = X.shape[0]  # 样本数
    z = hypothesis(X, theta)  # 计算假设值  
    # 限制 z 的范围以避免取对数时出现问题
    epsilon = 1e-15
    z = np.clip(z, epsilon, 1 - epsilon) 
    # 计算代价函数
    cost = -np.sum(y * np.log(z) + (1 - y) * np.log(1 - z)) / m  # 代价公式
    return cost
```

我们利用交叉熵损失来描述损失，这里主要是交叉熵损失的计算公式。

### 决策边界的绘制

```python
def plotDescisionBoundary(X, y, theta):
    cm_dark = mpl.colors.ListedColormap(['g', 'r'])  # 定义颜色
    plt.xlabel('Exam 1 score')  # x轴标签
    plt.ylabel('Exam 2 score')  # y轴标签
    plt.scatter(X[:, 0], X[:, 1], c=np.array(y).squeeze(), cmap=cm_dark, s=30)  # 绘制散点图
    # 计算并绘制决策边界
    x1 = np.array([np.min(X[:, 0]), np.max(X[:, 0])])  # x轴范围
    x2 = -(theta[0] + theta[1] * x1) / theta[2]  # 决策边界方程
    plt.plot(x1, x2, label='Decision Boundary', color='blue')  # 绘制边界线
    plt.legend()  # 显示图例
    # plt.show()
    plt.savefig('side.png')
    plt.close()

plotDescisionBoundary(X, y, theta)  # 绘制决策边界
```

这段代码通过逻辑回归模型的参数 `theta`，在二维特征空间中绘制样本的散点图并显示决策边界。首先，样本点根据不同类别（`y`）使用绿色和红色区分，并标注坐标轴。然后，通过决策边界方程 $ theta_0 + \theta_1 x_1 + \theta_2 x_2 = 0 $，计算并绘制一条蓝色直线，作为模型的分类边界，将特征空间划分为两个区域。图例显示决策边界，并将结果保存为图片，展示模型的分类效果和决策规则。
### 预测函数

```python
def predict(X):
    # 预测函数
    c = np.ones(X.shape[0]).transpose()  # 创建一列全1
    X = np.insert(X, 0, values=c, axis=1)  # 在X前插入全1列
    h = hypothesis(X, theta)  # 计算假设值
    # 根据概率值决定最终的分类
    h[h >= 0.5] = 1  # 大于等于0.5为1类
    h[h < 0.5] = 0   # 小于0.5为0类
    return h
```

根据训练得到的theta参数，我们可以通过对输入的数据添加全1列来完成对新样本的预测。

### 理论支撑

在逻辑回归的梯度下降算法中，参数更新的核心是通过最小化损失函数来调整模型参数 $\theta$。损失函数使用了对数似然代价函数（log-likelihood cost function），然后通过梯度下降法进行参数优化。以下是详细的推导过程以及代码解释：

---

### 1. 假设函数

在逻辑回归中，模型的假设函数 $h_\theta(x)$ 是通过 **Sigmoid** 函数映射的线性回归模型：

$$
h_\theta(x) = \text{sigmoid}(z) = \frac{1}{1 + e^{-z}}
$$
其中，$z$ 是输入特征 $X$ 和参数 $\theta$ 的线性组合，即：

$$
z = X\theta = \theta^T X
$$

#### 对应的代码解释：

```python
def hypothesis(X, theta):
    z = np.dot(X, theta)  # 计算线性组合 X * theta
    return sigmoid(z)     # 通过 Sigmoid 函数映射输出
```

此函数通过特征矩阵 $X$ 和参数向量 $\theta$ 计算线性组合 $z$，再通过 Sigmoid 函数将结果映射为 $(0, 1)$ 之间的概率值。

---

### 2. 代价函数（Cost Function）

逻辑回归的目标是最小化代价函数，其形式为：

$$
J(\theta) = -\frac{1}{m} \sum_{i=1}^{m} \left[ y^{(i)} \log(h_\theta(x^{(i)})) + (1 - y^{(i)}) \log(1 - h_\theta(x^{(i)})) \right]
$$

其中：

- $m$ 是样本数量
- $h_\theta(x^{(i)})$ 是第 $i$ 个样本的预测值
- $y^{(i)}$ 是第 $i$ 个样本的实际标签

代价函数用于衡量预测值 $h_\theta(x)$ 与真实标签 $y$ 之间的差距，通过最小化代价函数 $J(\theta)$，可以优化模型的参数 $\theta$。

#### 对应的代码解释：

```python
def computeCost(X, y, theta):
    m = X.shape[0]  # 样本数
    z = hypothesis(X, theta)  # 计算假设值
    
    # 为避免 log(0) 导致的无穷大错误，加入 epsilon
    epsilon = 1e-15
    z = np.clip(z, epsilon, 1 - epsilon)  # 限制 z 的范围，避免 z = 0 或 1
    
    # 计算代价函数
    cost = -np.sum(y * np.log(z) + (1 - y) * np.log(1 - z)) / m
    return cost
```

在这里，通过 `np.clip(z, epsilon, 1 - epsilon)` 防止 `z` 取 0 或 1，从而避免对数函数取值为负无穷大的错误。`computeCost` 函数根据给定的参数 $\theta$ 和数据 $X$、$y$，计算代价函数值 $J(\theta)$。

---

### 3. 梯度下降更新规则

为了最小化代价函数 $J(\theta)$，使用 **梯度下降算法**。梯度下降的更新规则为：

$$
\theta := \theta - \alpha \frac{\partial J(\theta)}{\partial \theta}
$$

其中，$\alpha$ 是学习率，$\frac{\partial J(\theta)}{\partial \theta}$ 是代价函数关于 $\theta$ 的梯度。接下来推导梯度。

---

### 4. 梯度的计算

代价函数 $J(\theta)$ 关于参数 $\theta_j$ 的偏导数为：

$$
\frac{\partial J(\theta)}{\partial \theta_j} = \frac{1}{m} \sum_{i=1}^{m} \left( h_\theta(x^{(i)}) - y^{(i)} \right) x_j^{(i)}
$$

推导步骤：

- 代价函数 $J(\theta)$ 包含 $h_\theta(x)$，其中 $h_\theta(x)$ 是 Sigmoid 函数。对 $h_\theta(x)$ 求导数时，使用链式法则：
  
$$
\frac{\partial h_\theta(x)}{\partial \theta} = h_\theta(x)(1 - h_\theta(x)) \cdot x
$$

- 代价函数关于 $\theta_j$ 的偏导数最终简化为：

$$
\frac{\partial J(\theta)}{\partial \theta_j} = \frac{1}{m} \sum_{i=1}^{m} \left( h_\theta(x^{(i)}) - y^{(i)} \right) x_j^{(i)}
$$

这表示模型预测值与真实值之间的误差，用以调整参数。

---

### 5. 梯度下降的参数更新公式

最终，梯度下降更新参数的公式为：

$$
\theta_j := \theta_j - \alpha \frac{1}{m} \sum_{i=1}^{m} \left( h_\theta(x^{(i)}) - y^{(i)} \right) x_j^{(i)}
$$

向量化表示形式：

$$
\theta := \theta - \frac{\alpha}{m} X^T \left( h_\theta(X) - y \right)
$$

---

### 6. 完整的梯度下降步骤

1. 计算假设函数：$h_\theta(x) = \text{sigmoid}(X \theta)$
2. 计算代价函数 $J(\theta)$
3. 计算梯度：$\frac{\partial J(\theta)}{\partial \theta_j}$
4. 更新参数：$\theta := \theta - \frac{\alpha}{m} X^T \left( h_\theta(X) - y \right)$

通过迭代上述过程，参数 $\theta$ 会逐步调整，从而优化模型的性能。

```python
theta_temp = theta - (alpha / m) * np.dot(X.T, (hypothesis(X, theta) - y))  # 梯度下降更新
theta = theta_temp  # 更新theta
```

这段代码实现了向量化形式的参数更新公式：

- `theta_temp` 计算预测值 $h_\theta(X)$。
- `np.dot(X.T, (hypothesis(X, theta) - y))` 计算梯度。
- `alpha / m` 表示学习率 $\alpha$ 和样本数 $m$。

通过这一步，更新后的参数 $\theta$ 会用于下一次迭代。

---

### 7. 绘制决策边界

在训练逻辑回归模型后，通常需要绘制决策边界，以展示模型如何将特征空间划分为不同类别。假设逻辑回归模型的决策边界是线性分割，公式为：

$$
\theta_0 + \theta_1 x_1 + \theta_2 x_2 = 0
$$

通过解这个方程，我们可以得到 $x_2$ 关于 $x_1$ 的关系：

$$
x_2 = -\frac{\theta_0 + \theta_1 x_1}{\theta_2}
$$

对应以下代码

```python
x2 = -(theta[0] + theta[1] * x1) / theta[2]  # 决策边界方程
```

此代码通过计算 $x_2$ 的值，绘制逻辑回归模型的决策边界，并与样本散点图进行可视化对比。















