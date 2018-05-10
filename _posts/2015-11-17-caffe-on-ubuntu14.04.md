---
layout: post
title: "Install Caffe On Ubuntu14.04"
date: 2015-11-17
excerpt: "Install Caffe On Ubuntu14.04"
tags: [caffe]
comments: true
---



虚拟机装Ubuntu: VMware（可以官网下载再去搜序列号）和 ubuntu的镜像（官网下载对应版本）



进入ubuntu的terminal开始配置环境

首先是安装依赖

```
sudo apt-get install build-essential
sudo apt-get install vim cmake git
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev libgflags-dev libgoogle-glog-dev liblmdb-dev protobuf-compiler
sudo apt-get install libatlas-base-dev
```



然后下载Caffe：`git clone git://github.com/BVLC/caffe.git`

再转到caffe的python目录，安装python的依赖

```
cd caffe/python
sudo apt-get install python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose
```

再安装requirement里面的包，需要root权限

```
sudo su
for req in $(cat requirements.txt); do pip install $req; done
```

如果提示报错，一般是缺少必须的包引起的，直接根据提示 pip install <package-name>就行了。

安装完后退出root权限

```
exit 
```



最后编译Caffe：

(1)`cp Makefile.config.example Makefile.config `

(2) 修改Makefile.config文件：

去掉注释, CPU_ONLY:= 1

修改python包目录，这句话

```
PYTHON_INCLUDE := /usr/include/python2.7 \
　　/usr/lib/python2.7/dist-packages/numpy/core/include
```

改为

```
PYTHON_INCLUDE := /usr/include/python2.7 \
  /usr/local/lib/python2.7/dist-packages/numpy/core/include
```

(3) make all  
(4) make test  
(5) make runtest  
(6) make pycaffe     



如果都没有报错的话 caffe编译就算成功了

----

最后试试看跑mnist例子

1. ​
```
 cd $CAFFE_ROOT
./data/mnist/get_mnist.sh
```

获取minist的数据包

2. ` ./examples/mnist/create_mnist.sh`

转换成lmdb文件

3. ` ./examples/mnist/train_lenet.sh`

 训练数据

注意如果是在没有安装GUDA，也就是CPU_ONLY的情况下，需要修改examples/mnist/lenet_solver.prototxt  

把里面的模式改成CPU即可

运行完结果如下：

生成四个文件

lenet_iter_10000.caffemodel         
lenet_iter_10000.solverstate      
lenet_iter_5000.caffemodel         
lenet_iter_5000.solverstate 

屏幕显示每次.......................

I0126 17:32:32.171516 18290 solver.cpp:246] Iteration 10000, loss = 0.00453533I0126 17:32:32.171550 18290 solver.cpp:264] Iteration 10000, Testing net (#0)I0126 17:32:40.498195 18290 solver.cpp:315]     Test net output #0: accuracy = 0.9903I0126 17:32:40.498236 18290 solver.cpp:315]     Test net output #1: loss = 0.0309918 (* 1 = 0.0309918 loss)I0126 17:32:40.498245 18290 solver.cpp:251] Optimization Done.I0126 17:32:40.498249 18290 caffe.cpp:121] Optimization Done.

这样就成功了





[Bugix]:    http://zhengy.me  "ZhengYang"
