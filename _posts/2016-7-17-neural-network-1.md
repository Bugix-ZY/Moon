---
layout: post
title: "Neural Network (I)"
date: 2016-07-17
excerpt: "感知器、激励神经元、基本神经网络结构、梯度下降"
tags: [Deep Learning, Machine Learning]
comments: true
---

参考资料:
[Using neural nets to recognize handwritten digits--Michael Nielsen ](http://neuralnetworksanddeeplearning.com/chap1.html) 

*****

### 1. perceptrons感知器

#### 感知器 ：
(暂且称为一个做决策的小节点)输入x1,x2, .., xn，引入权重weights。加权之后和threshold做比较，然后输出一个结果。
##### 如下图：

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/netural-network-1/single-perceptron.png?raw=true" height="103" width="210" />

##### perceptron rule:

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/netural-network-1/threshold.png?raw=true" height="93" width="400">

#### 多层感知器：
就是一层多个、由多层的感知器一起来做决策。第l－1层的感知器们的输出作为第l层的感知器们的输入。每层都建立在前一层的基础上，所以可以做更复杂的决策。
##### 如下图：

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/netural-network-1/many-layer-network-of-perceptron.png?raw=true" height="141" width="360">

关于这张图有一点要说明。第一层的感知器们看似是四个箭头指向下一层像是有四个输出，实际上它们依旧是单个输出，这里只是为了表明它们的单个输出会作为下一层感知器们的输入。


为了后续数学符号上的便捷，做些细微的调整。
认为w,x分别是权重向量和输入向量, 将之前提到的threshold挪个位置并且称其为bias。这样就得到了如下的
##### perceptron rule:

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/netural-network-1/Screen%20Shot%202016-07-16%20at%2020.44.07.png?raw=true" height="109" width="434">

可以认为bias是可以衡量感知器输出1的难度的，bias越大，越容易输出1，反之就会比较困难。

另外值得一提的是，感知器可以构建任何逻辑门。

那么现在的问题就是，能不能设计出一种学习算法来自动调整上文提到的weights和biases.
答案是可以的，其神奇之处就在于不用人为地去设计逻辑门，神经网络可以自己学习去解决问题。下面就说一下是如何做到的。

*****

### 2. Sigmoid neurons 激励神经元
要解决学习问题，先引入sigmoid neurons激励神经元。
比方说我们现在有张感知器构成的网络，输入就是一个个手写数字图片的像素。怎么通过这个网络来学习weights和biases来做到最后可以正确分类呢？

首先，可以理解的是，weights或者biases的一点改变，是可以对最后结果产生一点影响的。所以，假设网络把9误判为8, 那要做的事其实就是弄明白怎么去调整一点weights和biases来让网络作出的判定更接近正确答案9，就这样一点点调整weights和biases使输出更接近9。

上问提到的感知器都是通过一些很简单的计算来做决策的，如果一张网络都是由这样的感知器构成的，那么会出现一个不可控制的情况：一个权重变了点点导致一个感知器的输出完全变了，比如0直接变成了1.然后这个感知器又去影响别的感知器，一发不可收拾。这样就很难做到理想状态中的，慢慢改变weights和biases来让输出更好。

所以我们要引入一个更智能的人工神经元————激励神经元。激励神经元和感知器很像，但是它可以做到weights或biases的一点变化只会让output也只受一点影响。这样就允许了激励神经元构成的网络可以自主学习。

那么激励神经元和感知器的不同之处就在于，它的输出不是简单的0/1，而是0－1间的任意数值，比如，0.816....也就是σ(w ⋅ x + b)
这里的激励函数定义为：

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/netural-network-1/s-function.png?raw=true" height="73" width="149">

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/netural-network-1/s-full-function.png?raw=true" height="78" width="229">

为什么要采取这个函数？可以看一下它的图像：

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/netural-network-1/sigmoid-function.png?raw=true" height="285" width="455" >

可以认为，这个激励函数使step function平滑了。平滑也就是这个函数的关键所在，因为这样可以保证weights和biases的一点变化使output只受一点影响。
通过求偏导，可以发现，Δoutput和Δw,Δb 呈线性的变化关系。

*****

### 3. the architecture of neural network 神经网络的结构
有了sigmoid neurons后，再看如何组建它们。
神经网络一般由输入层、隐藏层、输出层构成。其中隐藏层可以有多层。
输入层和输出层的构造往往简单粗暴，关键之处还是在于隐藏层的构造。之后会提到如何启发式设计隐藏层，来权衡好层数和训练时间的问题。

之前提到的前一层的输出作为下一层的输入这种网络，我们称其为__feedforward neural networks，前向神经网络__，也就是一气呵成向前传递，不回头不循环的。当然，也存在有feedback loop的神经网络，那也就是recurrent neural networks。RNN中神经元的时效性使其循环成为可能。RNN相对来说没有前向传递那么有力，但是它的时效性更像人类大脑的运作方式，所以时常可以拿来解决前向网络不能解决的问题。

*****

### 4. a simple network to classify handwritten digits
用到的网络如图:

![nn](https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/netural-network-1/3-layer-nn-recdigits.png?raw=true)

第一层是 __输入层__ 。输入是28*28=784个像素(输入神经元), 并且这些是黑白图片，0代表白色，1代表黑色，0-1间的数值衡量灰度。

第二层是 __隐藏层__ 。通过训练后发现至少需要15个神经元。

第三次是 __输出层__ 。有10个神经元,代表0-9, 哪个最后的activation value最大，那么就猜测输入的是哪个数字。

个人理解，隐藏层里的一个个神经元分别代表了输入的不同部分(特征)，然后由输出层的神经元来对所有特征加权，看到底是哪个数字。


*****

### 5. Learning with gradient descent
于是还是没有说到怎么样去训练。这里会提到用gradient descent梯度下降来进行学习。

输入x，就是一个784维的列向量；输出y,就是一个10维的列向量。
所以训练的目的就是 让得到的y和实际上的结果误差最小。

那么先定义一下用来衡量误差的代价函数(cost or loss or objective function. )。
如下图：

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/netural-network-1/cost-function.png?raw=true" height="71" width="279" />

各个符号的解释：
Here, w denotes the collection of all weights in the network, b all the biases, n is the total number of training inputs, a is the vector of outputs from the network when x is input, and the sum is over all training inputs x. y(x) is the desired output.	e.g.y(x)=(0,0,0,0,0,0,1,0,0,0)T

C被称作 __quadratic cost function__ ， 也被称为mean squared error(MSE)

训练目标很明确，C越接近0代表判断越准确。这个 __minimize C__ 的过程可以由梯度下降法来实现。

（这里作者又提到了，为什么不最大化判断对的个数来调整网络。因为一点点的权重或者偏差的改变往往不会对结果产生质的改变。正确的个数不是关于权重和偏差的smooth function!又是smooth的问题🐒蓝而quadratic function是平滑的，用它来衡量就不会有问题。最后再用正确的个数来检查分类的准确性而不是用这个准确性去训练网络）

这里的quadratic function后面还会再改，这里先理解它的意义就行。

首先，先忘掉经网络的种种，也就是说，先看如何用梯度下降法去最小化一个有很多变量的function。然后再回到神经网络中，运用梯度下降去解决问题。
巨大的神经网络会出现纬度噩梦，单单靠计算来求最小值是行不通的。

梯度下降法主要牵扯到梯度(方向)和步长。梯度∇C就是对C求偏导，可以把学习率认为是步长。
另外就是梯度下降法总是选择当前下降最快的那条路走，所以求得的是局部最优解，尽管如此，梯度下降在学习中表现已经令人满意了。

结合到神经网络。

批量梯度下降的迭代如下:

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/netural-network-1/der-wb-mgd.png?raw=true" height="123" width="254" />

因为计算C时，是对每个输入加和平均，∇C也是如此，所以当输入样本很多的时候，传统batch gradient descent计算量会非常之大，学习速度就会很慢。

下面介绍__mini-batch gradient descent__方法，也就是每次算梯度的时候只是从样本里抽取部分x出来加和平均，将其视作全部输入的近似，这样也可以取得不错的效果。
这些有幸被选中的m个样本的集合被称为__mini-batch__. 这个样本集合要大到可以作为全部的近似才可以，通常是10。

假设所有样本被分为了n/m个mini-batch, 那么每轮迭代都选一个mini-batch出来，直到所有的都被选过，这时称为完成了一个__epoch of training__。然后可以继续开始一个new training epoch. 

当mini-batch里的样本值＝1的时候 称为__stochastic gradient descent随机梯度下降__ 注意这个时候每次迭代并不是朝着最优的方向进行的 只是每次算梯度都随机拿个样本来算 这样的话需要迭代次数较多，而有时候可以避免批量下降时候遇到的局部最优解问题。还有一个__Online GD__和SGD的区别就是所有数据只用一次就丢弃。（这边Michael Nielsen好像混淆了mini-batch GD，SGD，Online GD？或者说他对这些方法的命名和普通方式不一样）

对于__三者的区别__我的理解是：
把梯度下降看作是下山问题，样本们共同决定如何下山(选取梯度)。

__批量梯度下降__ 就是每个样本都选个方向，算一下大家的平均值，然后大家集体迈出一步。每一步都是如此。

__随机梯度下降__ 就是给样本们排一个做决定的次序，每走一步都按照之前订好的次序让那个样本来做决定，大家跟着它的方向走，然后下一步再按照约定好的次序换个样本来决定怎么走。

__mini-batch GD__ 就是给样本们先进行分组，先排好小组顺序，组内投票完走哪个方向，然后大家跟着他们组走。这样一轮结束后，再重复以上步骤。重复的次数也就是一开始约定好的epcho个数。

现在将mini-batch运用到神经网络中，那么迭代方法如图：

<img src="https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/images/postImages/netural-network-1/mgd.png?raw=true" height="151" width="305" />


*****

### 6.例子
看一个MNIST的训练例子。50000做training set, 10000做validation set(这里暂且没用)。

__一些关键的地方__

1 初始化一个net, weights和biases都随机高斯分布，为了方便计算，都向量化。

    class Network(object):
    def __init__(self, sizes):
        self.num_layers = len(sizes)
        self.sizes = sizes
        self.biases = [np.random.randn(y, 1) for y in sizes[1:]]
        self.weights = [np.random.randn(y, x) 
                        for x, y in zip(sizes[:-1], sizes[1:])]

    关于numpy：
    >>> sizes
    [2, 3, 1]
    >>> sizes[1:]
    [3, 1]
    >>> sizes[:-1]
    [2, 3]
    >>> numpy.random.randn(3,1)
    array([[ 0.28596078],
            [ 1.36472491],
            [ 0.76747929]])
    >>> numpy.random.randn(2,1)
    array([[ 0.59676856],
            [-0.74950995]])

这样就理解biases和weights的维度了🐶


2 定义前馈方法，一个循环就搞定啦。

    def feedforward(self, a):
        """Return the output of the network if "a" is input."""
        for b, w in zip(self.biases, self.weights):
            a = sigmoid(np.dot(w, a)+b)
        return a

3 然后就是如何调整weights和biases的问题了

    def SGD(self, training_data, epochs, mini_batch_size, eta,
            test_data=None):
        """Train the neural network using mini-batch stochastic
        gradient descent.  The "training_data" is a list of tuples
        "(x, y)" representing the training inputs and the desired
        outputs.  The other non-optional parameters are
        self-explanatory.  If "test_data" is provided then the
        network will be evaluated against the test data after each
        epoch, and partial progress printed out.  This is useful for
        tracking progress, but slows things down substantially."""
        if test_data: n_test = len(test_data)
        n = len(training_data)
        for j in xrange(epochs):
            random.shuffle(training_data)
            mini_batches = [
                training_data[k:k+mini_batch_size]
                for k in xrange(0, n, mini_batch_size)]
            for mini_batch in mini_batches:
                self.update_mini_batch(mini_batch, eta)
            if test_data:
                print "Epoch {0}: {1} / {2}".format(
                    j, self.evaluate(test_data), n_test)
            else:
                print "Epoch {0} complete".format(j)

参数解释：epochs是epoch的数量，mini_batch_size是batch的里样本的数量，eta是学习速率。

mini_batches是k个mini_batch的集合 也就是打乱了的training_data。

可以看到每个epcho中，打乱所有训练数据，分成k个batch，用每个batch进行梯度下降。

下面解释update_mini_batch(mini_batch, eta)这个函数！

    def update_mini_batch(self, mini_batch, eta):
        """Update the network's weights and biases by applying
        gradient descent using backpropagation to a single mini batch.
        The "mini_batch" is a list of tuples "(x, y)", and "eta"
        is the learning rate."""
        nabla_b = [np.zeros(b.shape) for b in self.biases]
        nabla_w = [np.zeros(w.shape) for w in self.weights]
        for x, y in mini_batch:
            delta_nabla_b, delta_nabla_w = self.backprop(x, y)
            nabla_b = [nb+dnb for nb, dnb in zip(nabla_b, delta_nabla_b)]
            nabla_w = [nw+dnw for nw, dnw in zip(nabla_w, delta_nabla_w)]
        self.weights = [w-(eta/len(mini_batch))*nw 
                        for w, nw in zip(self.weights, nabla_w)]
        self.biases = [b-(eta/len(mini_batch))*nb 
                       for b, nb in zip(self.biases, nabla_b)]
self.backprop(x, y)牵扯到了backpropagation algorithm，bp是一个快速计算cost function的梯度的方法。

bp的理论和代码将在下一篇文章中再说🐒

现在就认为这个函数可以返回正确的梯度

最后
Networks with this kind of many-layer structure - two or more hidden layers - are called deep neural networks.people now routinely train networks with 5 to 10 hidden layers. And, it turns out that these perform far better on many problems than shallow neural networks, i.e., networks with just a single hidden layer. The reason, of course, is the ability of deep nets to build up a complex hierarchy of concepts. 

