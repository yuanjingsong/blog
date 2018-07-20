---
title: HW1作业
date: 2018-07-20
---
本文是台湾大学李宏毅所授的机器学习 (2017 Fall) 的 HW1。[相关链接](https://ntumlta.github.io/2017fall-ml-hw1/)

### 理论

#### 定义一个 function set

由于题目限制，这里必须是一个线性方程。

使用前 9 小时的污染物来预测第 10 小时的 pm2.5，所以定义方程为

$y_n = b + w^1_{n-1} *pm2.5_{n-1} + w^1_{n-2} * pm2.5_{n-2} + ... + w^1_{n-9} * pm2.5_{n-9}+...$

可以写成矩阵形式

$$y_n = \left[ \begin{matrix}  1 & pm2.5_{n-1} & ... & pm2.5_{n-9} & ... &\end{matrix}\right] \times \left[ \begin{matrix} b \\w_{n-1} \\... \\ w_{n-9} \\ ...\end{matrix}\right] $$

#### 定义 Loss function

$L = \sum(y - (b + w_ix_i))^2$

#### 计算 Gradient Descend

目的：找到一个 $\theta^{\star}$ 有 $\theta^{\star} = argminL(\theta)$

根据梯度下降，有

$$\theta_{1} = \theta_{0} - η \nabla L(\theta_0)$$

其中  $\eta$  为 learning rate

#### 引入 Adagrad 算法

$\theta_1 = \theta_0 - \frac{\eta}{\sigma_0} g_0$ ，其中 $g_0 = \nabla L(\theta_0)$  $\sigma_0 = \sqrt{g_0^2}$

$\theta_2 = \theta_1 - \frac{\eta}{\sigma_1}g_1$，其中 $g_1 = \nabla L (\theta_1)$    $\sigma_1 = \sqrt{\frac{1}{2} ({g_0}^2 + {g_1}^2)} $

进行迭代有

$\theta_n = \theta_{n-1} - \frac{\eta}{\sigma_{n-1}}g_{n-1}$ ，其中 $g_{n-1}  = \nabla L (\theta_{n-1})$  $\sigma_{n-1} = \sqrt{\frac{1}{n} \sum_{i=0}^{n-1} g_{i}^2}$

### 计算

#### 导入包

`````python
import csv 
import numpy as np
from numpy.linalg import inv
import random
import math
import sys
`````

#### 提取所有的污染物数据

```python
data = []

for i in range(18):
    data.append([])
	#每一个 list 记录一种污染物 
    
n_row = 0
text = open("./train.csv", 'r', encoding="big5")
row = csv.reader(text, delimiter=",")
for r in row:
    #数据格式第一行没有row
    if n_row != 0:
        for i in range(3, 27):
            if r[i] != "NR":
                data[(n_row - 1)%18].append(float(r[i]))
            else:
                data[(n_row - 1)%18].append(float(0))
    n_row += 1
text.close()
```

#### 转化为 (x, y)

```python
#x is train_x
#y is train_y
x = []
y = []
#一共 20 * 24 = 480 条数据
#但每个月前9小时无法预测，所以是 480 - 9 条
#每个月要预测471条数据
for i in range(12):
    for j in range(480 - 9):
        x.append([])
        for t in range(18):
            for s in range(9):
                x[471*i+j].append(data[t][480*i+j+s])
        y.append(data[9][480*i+j+9])
x = np.array(x)
y = np.array(y)

#add bias
x = np.concatenate((np.ones((x.shape[0], 1)), x), axis = 1)
```

此时 x 是个5652行，162列的数组，每一行代表18种不同的数据，每种数据有9个小时的数据。

y 是个5652行，1列的数组，每一列代表对应的值。

#### 设定 learning rate，迭代次数，和 w 初始值

````python
w = np.zeros(len(x[0]))
l_rate = 10
repeat = 10000

````

#### 进行迭代

```python
x_t = x.transpose()
s_gra = np.zeros(len(x[0]))

for i in range(repeat):
    hypo = np.dot(x,w)
    loss = hypo - y
    cost = np.sum(loss**2) / len(x)
    cost_a  = math.sqrt(cost)
    gra = np.dot(x_t,loss)
    s_gra += gra**2
    ada = np.sqrt(s_gra)
    w = w - l_rate * gra/ada
    print ('iteration: %d | Cost: %f  ' % ( i,cost_a))
```

迭代完最后就可以得到 w 矩阵，进行预测。
