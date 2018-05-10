---
layout: post
title: Cryptographic Hash Function
description: Hash Function、 Message Authentication、MD5、SHA-512
category: blog

---

### Cryptographic Hash Function 





偷懒放直接我做的slide => [Cryptographic Hash Function](https://github.com/Bugix-ZY/Bugix-ZY.github.io/blob/master/docs/hash%20function.pdf)  

主要是介绍了密码学中的hash function  

内容比较基础，有介绍hash的**基本概念**、hash function的**应用场景**和两个比较有名的**hash算法**(MD5和SHA-2)   

slide里面没有具体写message authentication的部分，只给出了图，但是从图中也可以大概了解到，光传message+hash value是不行的，还是会被人从中间拦截篡改信息。所以会采取一些加密手段。  

但是加密是很费时间的，所以之后大概会研读一下MAC之类的认证方法， 以及思考在网络环境中如何提供安全的登陆（保护user的password不外泄）？