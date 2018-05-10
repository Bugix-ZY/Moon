---
layout: post
title: Deep Learning Specialization  - Improving Deep Neural Networks
description: Improving Deep Neural Networks — Andrew Ng 系列课程
category: blog
---


**Reference**:

- [Coursera - Neural Networks and Deep Learning](https://www.coursera.org/learn/neural-networks-deep-learning/home/info)

---

毕业设计会是深度学习相关，因此想系统学习深度学习领域的知识。正好 Andrew Ng 现在开了 Deep Learning Specialization 系列课程，一共有五门课程：

1. Neural Networks and Deep Learning
2. Improving Deep Neural Networks：Hyperparameter tuning, Regularization and Optimization
3. Structuring your Machine Leaning Project
4. Convolutional Neural Networks
5. Natural Language Processing: Building equence models

这篇博文针对第二门课 —— **Improving Deep Neural Networks**



##  

### 1.1 Train / Dev / Test Sets

Data : 

- Training set 

- Hold-out cross validation (Development set "Dev")

- Test Set // 估计正确率，不一定需要

    ! **要确保 dev  test 来自同一个分布**



### 1.2 偏差／方差

**high bias** - underfitting

**high variance** - overfitting



### 1.3 机器学习基础

**Recipe：**

1. high bias? (training data performance)-> bigger network/ train longer
2. high variance (dev set performance) -> more data/ regularization

### 1.4 -1.6 正则化

* dropout 正则化
  * 每个样本对应一个随机精简过的网络
  * inverted dropout (正／反传播时消除)
    * easier for test



### 1.8 其他正则化方法

* Data augmentation
* Early Stopping：在防overfitting的时候，无法optimize J



### 1.9 归一化输入

* 可以加速训练过程

  * 减掉mean

  * 除以方差

    ！ ⚠️：要用同样的 mean 和 variance 去处理 test set

    ​

### 1.10 梯度消失／爆炸

- 导数很大／很小（增大训练难度）

   $W^L$ : 假设每个 W 都一样，在网络中传播到 L 层

  - W > 1， 传到最后就会变得非常大
  - W < 1， 传到最后就会变得非常小 

- 通过初始权重改善这个问题

  - $Var(W) := \frac{constant}{n^{[l-1]}}$
    - Relu: constant = 2
    - tanh: constant = 1
  - Xavier: $Var(W) := \frac{2}{n^{[l-1]}}$



### 1.12-13 梯度检验

**如何确保backprop 正确实施？**

- 梯度的数值逼近
  - $g(\theta) = \frac{f(\theta + \epsilon) - f(\theta - \epsilon)}{2* \epsilon}$
  - 约等于 < 10^-7/10^-5





# Week 2  Optimization Algorithms

### 2.1 MINI-BATCH

深度学习需要big data，但是数据越多，计算越慢。用 Optimization Algorithms 来提速。

- In **Batch gradient descent** We run the gradient decent on the whole dataset.

- While in **Mini-Batch gradient descent** We run the gradient decent on the mini datasets.

  

- Guidelines for using mini batch:
  - It has to be a power of 2 to take advantage of vectorization: `64, 128, 256, 512, 1024....`
  - Make sure mini-batch fits in CPU/GPU
- Mini batch size is a Hyperparameter.

 

### 2.2 Exponetially Weighed Averages

* ```
  V(t) = beta v(t-1) + (1-beta) ceta(t)
  ```

* 加上偏差修正会更精准：

  *  V(t) = (beta v(t-1) + (1-beta) ceta(t)) / (1 - beta^t)



### Gradient Descent With Momentum

**动量梯度下降算法**：

计算梯度的指数加权平均，用这个梯度来更新

