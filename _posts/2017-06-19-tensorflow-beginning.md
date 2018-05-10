---
layout: post
title: TensorFlow Beginning
description: intall and getting started
category: blog
---

[TOC]

### Install

哇安装真是简单到可以



### Getting Started

#### Tensors

**tensor** 是 tensorflow 里的核心数据单位

tensor 的 **rank** 是其纬度

```python
3 # a rank 0 tensor; this is a scalar with shape []
[1. ,2., 3.] # a rank 1 tensor; this is a vector with shape [3]
[[1., 2., 3.], [4., 5., 6.]] # a rank 2 tensor; a matrix with shape [2, 3]
[[[1., 2., 3.]], [[7., 8., 9.]]] # a rank 3 tensor with shape [2, 1, 3]
```



#### TensorFlow Core Tutorial

##### Importing TensorFlow

```python
import tensorflow as tf
```

##### The Computational Graph

可以把任何 TensorFlow 核心程序理解成两个分开的部分：

* 构建 Computational Graph
* 运行 Computational Graph

而所谓的 Computational Graph 就是一系列对于图节点的 TensorFlow 运算（感觉就是神经网络）。每个节点的输入由 0 或多个 tensor 构成，输出是 1 个 tensor。有一类比较特殊的节点是常数节点，没有输入，输出是固定的。

```python
node1 = tf.constant(3.0, dtype=tf.float32)
node2 = tf.constant(4.0) # also tf.float32 implicitly
print(node1, node2)
```

得到的结果会是

```python
Tensor("Const:0", shape=(), dtype=float32) Tensor("Const_1:0", shape=(), dtype=float32)
```

实际上 computational graph 一定要在 **session** 里面运行， **session** 将 TensorFlow 的控制和状态都有封装起来。

在 session 里面运行  computational graph， 这样会得到 [3.0, 4.0]

```python
sess = tf.Session()
print(sess.run([node1, node2]))
```

操作其实也是用节点来表示的，比如两个节点相加，然后生成一张新的图

```python
node3 = tf.add(node1, node2)
print("node3: ", node3)
print("sess.run(node3): ",sess.run(node3))
```

得到结果

```python
node3:  Tensor("Add:0", shape=(), dtype=float32)
sess.run(node3):  7.0
```

问题是这里的节点数值都被写死了，其实可以通过 **placeholder** 来占位，之后再接受外部的值。

```python
a = tf.placeholder(tf.float32)
b = tf.placeholder(tf.float32)
adder_node = a + b  # + provides a shortcut for tf.add(a, b)
```

然后就可以这样运行看看，这里有尝试不同的维度的 tensor，花括号里的是参数值，也就是`feed_dict`。

```python
print(sess.run(adder_node, {a: 3, b:4.5}))
print(sess.run(adder_node, {a: [1,3], b: [2, 4]}))
```

问题是…训练的过程中权重之类的参数都应该是不断更新的，**Variables** 可以做到，Variable 可以通过 `tf.assign` 重新赋值

```python
W = tf.Variable([.3], dtype=tf.float32)
b = tf.Variable([-.3], dtype=tf.float32)
x = tf.placeholder(tf.float32)
linear_model = W * x + b
```

然后

```python
init = tf.global_variables_initializer()
sess.run(init)
```

要注意的是 `init` 负责初始化所有全局函数，但是在 `sess.run(init)`前，变量依旧是没有被赋值的。

接下是学习如何学习写损失函数

```python
y = tf.placeholder(tf.float32)
squared_deltas = tf.square(linear_model - y)
loss = tf.reduce_sum(squared_deltas)
print(sess.run(loss, {x:[1,2,3,4], y:[0,-1,-2,-3]}))
```

这里 `y` 作为代表目标值，`reduce_sum` 的作用是求总和， 最后会得到 23.66 这一个数值

最后就是…那如何让模型自己训练找到最佳的参数使得损失最小？



#### tf.train API

TensorFlow 提供一个叫做 **optimizers** 的东西可以慢慢改变每个变量以最小化损失函数，最简单的 **optimizers** 就是 **gradient descent**

```python
optimizer = tf.train.GradientDescentOptimizer(0.01)
train = optimizer.minimize(loss)

sess.run(init) # reset values to incorrect defaults.
for i in range(1000):
  sess.run(train, {x:[1,2,3,4], y:[0,-1,-2,-3]})

print(sess.run([W, b]))
```

结果会是

```python
[array([-0.9999969], dtype=float32), array([ 0.99999082],
 dtype=float32)]
```



#### tf.contrib.learn

tf.contrib.learn 是个更高级的库，它提供很多预定义好的模型。它简化了很多东西：

- running training loops
- running evaluation loops
- managing data sets
- managing feeding

一个 linear regression 的例子：

```python
import tensorflow as tf
# NumPy is often used to load, manipulate and preprocess data.
import numpy as np

# Declare list of features. We only have one real-valued feature. There are many
# other types of columns that are more complicated and useful.
features = [tf.contrib.layers.real_valued_column("x", dimension=1)]

# An estimator is the front end to invoke training (fitting) and evaluation
# (inference). There are many predefined types like linear regression,
# logistic regression, linear classification, logistic classification, and
# many neural network classifiers and regressors. The following code
# provides an estimator that does linear regression.
estimator = tf.contrib.learn.LinearRegressor(feature_columns=features)

# TensorFlow provides many helper methods to read and set up data sets.
# Here we use two data sets: one for training and one for evaluation
# We have to tell the function how many batches
# of data (num_epochs) we want and how big each batch should be.
x_train = np.array([1., 2., 3., 4.])
y_train = np.array([0., -1., -2., -3.])
x_eval = np.array([2., 5., 8., 1.])
y_eval = np.array([-1.01, -4.1, -7, 0.])
input_fn = tf.contrib.learn.io.numpy_input_fn({"x":x_train}, y_train,
                                              batch_size=4,
                                              num_epochs=1000)
eval_input_fn = tf.contrib.learn.io.numpy_input_fn(
    {"x":x_eval}, y_eval, batch_size=4, num_epochs=1000)

# We can invoke 1000 training steps by invoking the  method and passing the
# training data set.
estimator.fit(input_fn=input_fn, steps=1000)

# Here we evaluate how well our model did.
train_loss = estimator.evaluate(input_fn=input_fn)
eval_loss = estimator.evaluate(input_fn=eval_input_fn)
print("train loss: %r"% train_loss)
print("eval loss: %r"% eval_loss)
```

当然也可以用更底层的方法来实现上述代码

```python
import numpy as np
import tensorflow as tf
# Declare list of features, we only have one real-valued feature
def model(features, labels, mode):
  # Build a linear model and predict values
  W = tf.get_variable("W", [1], dtype=tf.float64)
  b = tf.get_variable("b", [1], dtype=tf.float64)
  y = W*features['x'] + b
  # Loss sub-graph
  loss = tf.reduce_sum(tf.square(y - labels))
  # Training sub-graph
  global_step = tf.train.get_global_step()
  optimizer = tf.train.GradientDescentOptimizer(0.01)
  train = tf.group(optimizer.minimize(loss),
                   tf.assign_add(global_step, 1))
  # ModelFnOps connects subgraphs we built to the
  # appropriate functionality.
  return tf.contrib.learn.ModelFnOps(
      mode=mode, predictions=y,
      loss=loss,
      train_op=train)

estimator = tf.contrib.learn.Estimator(model_fn=model)
# define our data sets
x_train = np.array([1., 2., 3., 4.])
y_train = np.array([0., -1., -2., -3.])
x_eval = np.array([2., 5., 8., 1.])
y_eval = np.array([-1.01, -4.1, -7, 0.])
input_fn = tf.contrib.learn.io.numpy_input_fn({"x": x_train}, y_train, 4, num_epochs=1000)

# train
estimator.fit(input_fn=input_fn, steps=1000)
# Here we evaluate how well our model did. 
train_loss = estimator.evaluate(input_fn=input_fn)
eval_loss = estimator.evaluate(input_fn=eval_input_fn)
print("train loss: %r"% train_loss)
print("eval loss: %r"% eval_loss)
```



### MNIST For ML Beginners

还是稍微过一下这章