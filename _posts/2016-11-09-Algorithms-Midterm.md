---
layout: post
title: "演算法期中复习 CH02-CH09" 
date: 2016-11-09
excerpt: "Introduction to alogorithms CH02-CH09"
tags: [algorithm]
comments: true
---


# 演算法复习CH02-CH09

课本：introduction to algorithms   3rd edition

---

## CH02 getting started

- 选worst case的三个理由
  1. 不会比worst case更糟了
  2. worst case常发生
  3. avg case往往和worst case一样
- insertion sort
  - worst case：O(n^2)；best case: O(n)；avg case: O(n^2)
  - in place
  - 稳定
- merge sort
  - MERGE的实现
  - MERGESORT的实现
  - 都是O(nlgn)
  - 需要额外空间 not in place
  - 稳定




---

## CH03 Growth of Function

- Asymptotic Notation的含义
  - O notation
  - Ω notation
  - Θ notation
  - o notation
  - ω notation

- 在等式中，如果出现Asymptotic Notation，左边就是for all, 右边是for some

- 会找C和n0, 会做Relational properties的证明

- **function一定可以用Asymptotic Notation表示/两个function一定可以比较大小吗？  不是，比如n^(1 + sin n)和n就无法比较，因为1 + sin n 在(0,2)之间振荡**

- 一些以后证明中可能会用到的数学定理：

  - for all real x,    e^x    ≥  1 + x

  - ![log](../../images/postImages/algorithms/log.png)

     圈出来的是不太熟的QQ

  - **Stirling approximation: n! > (n/e)^n , derive that lg(n) = Θ(nlgn)** 在ch08证明decison tree高度的时候就会用到~~（听说必考？）~~

  - Logarithms << Polynomials << exponentials




---

## CH04 Divide-and-Conquer(Recurrences)

- **Substitution Method**
  - 步骤   
    1. guess   
    2. induction
  - 大部分时候可以直接脱掉ceiling和floor
  - 过程中把Asymptotic Notation换成cn这种形式
  - 如果发现guess的东西证不出，可以尝试减掉或加上lower-order term
  - **changing variables**   m = lgn 
- **Recurrence Tree**
  -  num of levels =  height + 1 ，最后一层 T(1) = c可以用Θ notation表示
- **Master Method T(n) = aT(n/b) + f(n)**
  - case 1： f (n)  is **polynomially smaller** than nlogba, **Solution: T(n) = Θ( nlogba )**
  - case 2:    f (n) is within a **polylog** factor of  nlogba  , but not smaller **Solution: T(n) = Θ( nlogba lgk+1 n)** 
  - case 3:    f (n)  is **polynomially greater** than nlogba , **Solution: T(n) = Θ( f (n) )**和case1恰好相反
  - case 1,3大概就是取polynomial大的，case2考虑的是f(n)大出polylog倍
  - case 3还必须要满足f (n)  satisfies the regularity condition  a f ( n/b )  ≤  c f (n)  for some constant c < 1  and all sufficiently large n. **f(n) = n^k可以确定是满足的**
- **Extension of Master Method T(n) = aT(n/b) + f(n)** 【一个更神乎其技的master method】
  -  ![extendedMaster](../../images/postImages/algorithms/extendedMaster.png)




---

## CH06 Heapsort

之前有写过一篇关于heapsort的理解：[heapsort](http://bugix-zy.github.io/Heapsort)

- heap的概念 heap存在数组里

- height=最长路径,比level数量少一。 要会证明 height of root = θ(lg n)

- A.heapsize和A.length是不同的概念

- heapsort三步走 要会写pseudocode和证明时间复杂度：

  - **MAX-HEAPIFY**	 O(lgn) = O(h)
  - **BUILD-MAX-HEAP**  O(n) 这个证明会难一些
  - **HEAPSORT**               O(nlgn)
  - 插一句 倒不是说要把pseudocode背下来 只是说如果理解了算法的话那应该就能写得出大概的样子

- 把证明题列在一起好了QQ         *✅是比较熟练的  🚨是要想很久或是不会证(智商堪忧)*

  - heap的高度为什么是O(lgn)  ✅

  - 证明叶子的数量为ceiling(n/2) （和证明叶子的index是从floor(n+1)+1,..,n应该可以用同一个方法）

    以这个为base还可以证得n-element heap 在height h 最多有ceiling(n/2^(h+1))个节点。**⚠height是从下到上 Leaf的height为0**   🚨

  - MAX-HEAPIFY的时间复杂度 O(lgn)   ✅

  - MAX-HEAPIFY的时间复杂度 Ω (lgn)  ✅

  - BUILD-MAX-HEAP的时间复杂度为什么会是O(n)  需要用到第一、二题的结论+一个formula🚨 

  - heapsort的worst case -> Ω (nlgn) 这个可以由书上的定理8.1直接得出，因为任何比较排序的worst case的lower bound都是nlgn(从decision tree证过来的) ✅

  - heapsort的best case 🚨感觉这个证明好难啊怎么会出现在作业里QQ 还是贴上来好了 放弃证明![heapsort_bestcase](../../images/postImages/algorithms/heapsort_bestcase.png)

    ​	其实还是没有懂这个和best case有什么关系……best在哪里了…

    ​	另外找到一份[最早在1990年证出的best case ](ftp://ftp.cs.princeton.edu/techreports/1990/293.pdf)我估计我有生之年不会拜读……




---

## CH07 QuickSort

- **quicksort**
  - Divide: **Partition(A,p,r)**  
    - j是扫数组的那个，初始化的时候i = p - 1 【所以第一个元素会和自身交换】
    - p~i的部分是≤x的 i+1~j的部分是>x的，最后pivot归位A[i+1]<=>A[r]
  - Conquer: **Quicksort(A,p,r)**
  - 因为quicksort是in place的 不需要combine
- worst case: Θ(n^2);    best case Θ(nlgn);    avg case Θ(nlgn)  
- randomized-quicksort




---


## CH08 Sorting In Linear Time

- 定理8.1 **任何比较排序的worst case的lower bound都是nlgn**  证明很重要
- 之所以可以linear，是因为不做比较【假设元素都是不相同的】
- **CountingSort(A)** 【假设输入的是n个整数 max=k】
  - 需要辅助数组B[1....n]来存答案，辅助数组C[0...k]来数数字
  - 稳定->最后一步是for i=A.length down to 1保证了稳定性
  - 时间复杂度O(n+k), which is **Θ(n)** if k = Ο(n)
- **RadixSort**
  - 从最末的digit排起，用稳定的排序作为subroutine，比如countingsort
  - **Θ(n + k)** per pass (digits in range 0, …, k), **Θ(d(n + k)) total**,If k = Ο(n), time = **Θ(dn)**.
  - radixsort灵活的地方就在于可以break key into digits，怎么break？
  - 假设每个输入的长度是b-bit, break成r-bit, 那么复杂度就会是**Θ(b/r(n + 2^r))** 【把K换成了2^r】
  - 以上复杂度在b=O(lgn)【往往是这样】，选择r = lgn 效率会最高,实际是**Θ(n)**
- **BucketSort** 【假设输入符合均匀分布，且各项介于[0,1）】
  - 需要辅助数组B[0...n-1]当作bucket，其实理想情况是一个bucket里一个元素，既然是uniform distribution
  - 主要步骤是把A[i]插到B[nA[i]]里（这就是为啥要介于[0,1)了…毕竟index是整数）
  - 因为有假设是uniform distribution，所以可以认为对每个bucket排序都可以在常数时间内完成->O(1)，就算用的是O(n^2)的算法也是一样的结果，最后串起来就可以的。
  - 时间复杂度 avg case **Θ(n)**




---

## CH09 Medians and Order Statistics

- 选第i个小的称之为Selection problem【假设元素都是不同的】

- median其实就是中位数，如果元素个数为偶数个，那就取lower median

- 同时选最大最小可以最多只需要比较3floor(n/2)次

- **Radnomized-Select** **Θ(n)**，和quicksort不同的是，RandomizedSelect每次只处理partition的一半

  - Worst-case running time: Θ(n2), because we could be extremely unlucky and always recurse on a subarray that is only 1 element smaller than the previous subarray.

    Expected running time: RANDOMIZED-SELECT works well on average. 

- 还有种更好的的partition的方法，步骤如下

  SELECT：

  1. 5个一组 分组
  2. 通过insertion sort 得到每组的median
  3. 用SELECT递归算法得到median-of-medians =>记为x
  4. 用median-of-medians x当作pivot来做partition
  5. i=k返回x,不然就对前半段或后半段递归呼叫SELECT找第i小

- 时间复杂度分析 ~~听说必考为什么是线性的?~~

  - 第1，2，4步都是O(n)

  - 第3步 T(n/5)

  - 第5步 比x大的最少有(1/2 * ( n / 5) - 2) = 3/10 * n - 6,所以比x小的最多是T(7/10 * n + 6)；反之亦然。

    以上五项加起来 做substitution method就可以得到T(n) = O(n)

