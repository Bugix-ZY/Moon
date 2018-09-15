---
layout: post
title: "Neural Network (II)"
date: 2016-07-17
excerpt: "harmard product、反向传播算法"
tags: [Deep Learning, Machine Learning]
comments: true
---
参考资料:

[How the backpropagation algorithm works--Michael Nielsen ](https://neuralnetworksanddeeplearning.com/chap2.html) 

[反向传导算法 -- Uflfl](https://deeplearning.stanford.edu/wiki/index.php/%E5%8F%8D%E5%90%91%E4%BC%A0%E5%AF%BC%E7%AE%97%E6%B3%95)

*****


### 反向传播算法

反向传播算法

#### 1. 关于cost function的两个假设

反向传播算法是拿来快速计算梯度的，也就是∂Cx/∂w和∂Cx/∂b。需要注意两点:

⚠️  C是Cx的加和平均

⚠️  C = C(al) C是关于output activations a的function, 而y只是帮忙定义了这个函数。
为什么不是关于y的？因为x是固定的，那么y也是固定的参数，不是神经网络要学的部分。


#### 2. 矩阵的Hadamard积
如图：
<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/neural-network-2/harmard.png?raw=true" height="70" width="266" />

****
#### 3. BP算法背后的四个基本等式

bp主要是为了算那两个偏导，为了计算这些，我们先引入一个中间量，叫做“残差”。δljδjl 第l层的第j个神经元

BP可以来计算这些残差 并且把这些残差联系到∂Cx/∂w和∂Cx/∂b去。

首先来理解残差到底是什么？

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/neural-network-2/error-story.png?raw=true" height="728" width="496" />

残差的四个公式:
<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/neural-network-2/4bp.png?raw=true" height="284" width="511" />


*****
#### 4. BP算法

步骤如下，

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/neural-network-2/bp-algorithm.png?raw=true" height="403" width="602" />

所以其实BP算法就是先前向得到所有的激活值，然后根据这个算出最后一层的(输出层)残差，从后往前递推算出所有残差，最后算出需要的偏导。


******
#### 5. BP算法的代码实现

```
class Network(object):
    ...
    def backprop(self, x, y):
       """Return a tuple "(nabla_b, nabla_w)" representing the
    gradient for the cost function C_x.  "nabla_b" and
    "nabla_w" are layer-by-layer lists of numpy arrays, similar
    to "self.biases" and "self.weights"."""
    nabla_b = [np.zeros(b.shape) for b in self.biases]
    nabla_w = [np.zeros(w.shape) for w in self.weights]
    # feedforward
    activation = x
    activations = [x] # list to store all the activations, layer by layer
    zs = [] # list to store all the z vectors, layer by layer
    for b, w in zip(self.biases, self.weights):
        z = np.dot(w, activation)+b
        zs.append(z)
        activation = sigmoid(z)
        activations.append(activation)
    # backward pass
    delta = self.cost_derivative(activations[-1], y) * \
        sigmoid_prime(zs[-1])
    nabla_b[-1] = delta
    nabla_w[-1] = np.dot(delta, activations[-2].transpose())
    # Note that the variable l in the loop below is used a little
    # differently to the notation in Chapter 2 of the book.  Here,
    # l = 1 means the last layer of neurons, l = 2 is the
    # second-last layer, and so on.  It's a renumbering of the
    # scheme in the book, used here to take advantage of the fact
    # that Python can use negative indices in lists.
    for l in xrange(2, self.num_layers):
        z = zs[-l]
        sp = sigmoid_prime(z)
        delta = np.dot(self.weights[-l+1].transpose(), delta) * sp
        nabla_b[-l] = delta
        nabla_w[-l] = np.dot(delta, activations[-l-1].transpose())
    return (nabla_b, nabla_w)

         ...

    def cost_derivative(self, output_activations, y):
        """Return the vector of partial derivatives \partial C_x /
        \partial a for the output activations."""
        return (output_activations-y) 


    def sigmoid(z):
        """The sigmoid function."""
        return 1.0/(1.0+np.exp(-z))


    def sigmoid_prime(z):
        """Derivative of the sigmoid function."""
        return sigmoid(z)*(1-sigmoid(z))

```

BP到底哪里快了？可以发现，求得所有偏导，只需要前向传播一次和后向传播一次。

更棒的是，后向传播的时候，所有的偏导可以同时都算出来。

