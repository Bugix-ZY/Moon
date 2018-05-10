---
layout: post
title: Ubuntu14.04 + intel caffe (CPU only)
description: VM8.0
category: blog
---

reference :

[intel caffe 官方安装指南](https://software.intel.com/zh-cn/articles/training-and-deploying-deep-learning-networks-with-caffe-optimized-for-intel-architecture#Installation)  
[BLVC caffe 安装指南](http://caffe.berkeleyvision.org/install_apt.html)  
[tiangolo 写的安装过程](https://github.com/tiangolo/caffe/blob/ubuntu-tutorial-b/docs/install_apt2.md)    

---

前年装BLVC caffe折腾了三天，这次配intel caffe（手动微笑）还是折腾了三天

前两天都是尝试在 MacOS 10.12.4 上装，但是编译器的问题最后还是没解决，就在ubuntu上先装装看

ubuntu 上 intel caffe 一个晚上是能搞定了 撒花



这篇文章主要是提一下踩过的坑

* 安装 general dependencies
* 如果是显卡不支持，只想 CPU only 的话，关于 CUDA 什么的安装全都可以省略掉。比如我的 Mac Pro显卡是 Intel Iris 就是不支持 CUDA的， CUDA 支持哪些显卡可以在他的官网上看
* 用 OpenBLAS 代替默认的 ATLAS 
  * 默认的 ATLAS 也是蜜汁 reference undefined 怀疑是路径写错， 总之干脆换成了 OpenBLAS 听说性能更好嘛
  * 下载 OpenBLAS，可以参考 tiangolo 的教程，如果不指定安装目录的话，会默认装在/opt/OpenBLAS/下，因为 CPU only 所以make的时候需要指明OPENMP为1，即`make USE_OPENMP=1`
  * 如果在后续的过程中（没记错的话是 runtest 的时候）出现找不到 libopenblas.so 的话 就把OpenBLAS 安装的地方的 libopenblas.so 软连接到 /usr/lib下
* runtest 成功后的样子 ![runtest](../../images/postImages/caffe/runtest.png)


* 最后是用 python  import caffe 出了好几个错

  * 第一个是找不到 _caffe 这个模块，原因是PYTHONPATH 出错，把正确的写进 ./bashsrc就可以了
  * 第二个是和 MKLDNN 相关的错，具体的问题和解决方法：https://github.com/intel/caffe/issues/64 
  * 最后一个问题是： https://github.com/nylas/sync-engine/issues/432 ，**shchukax**有给出解决方法

  然后就能import成功咯 ![import-caffe](../../images/postImages/caffe/import-caffe.png)




* minst demo

  * 训练模型![intel-caffe-mnist](../../images/postImages/caffe/intel-caffe-mnist.png)