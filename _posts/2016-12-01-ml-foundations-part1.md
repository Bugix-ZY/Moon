# Machine Learning Foundations （1）

**参考资料**：

Machine Learning Foundations (機器學習基石) 	林轩田 

“Learning From Data”  Yaser Abu-Mostafa等著

---

该系列(Machine Learning Foundations)所有文章:



---

这个学期在老师和同学们的带领下终于有认真看林老师的教学影片了XD。结果拖到现在才来写文 ，感觉再不写，之前看的就要忘光了（。

虽说是基石，但绝对不是简单的意思QQ非常偏理论，数学推导还蛮多，以至于想开个微积分&概率统计的坑了。

预计会一共po出4-5篇小结(不偷懒的话)，其主题和林老师该系列影片所设计的四大主题相对应：

- **When** Can Machines Learn?
- **Why** Can Machines Learn?
- **How** Can Machines Learn?
- Why Can Machines Learn Better?

​    

---

## When Can Machines Learn？

本模块主要叙述机器学习要学什么，什么时候可以用上机器学习等，共分以下4个lecture：

- Lecture 1: The Learning Problem
- Lecture 2: Learning to Answers Yes/No
- Lecture 3: Types of Learning
- Lecture 4: Feasibility of Learning

大致的内容为：先是在第一讲中给学习下定义，引出learning model，接着在第二讲中介绍了PLA和pocket两种算法来，让同学们看到机器学习确实可以通过学习取得更好的表现。在第三讲中介绍了不同的学习类型，第四讲中使用霍夫定不等式来试图证明学习是可能的，并同时引向第二个部分Why can machines learn

  

### Lecture 1: The Learning Problem

这一讲我觉得最关键的内容是要厘清机器学习问题中的components

从data ——>  ML ——> skill 这里出发，抽象成 **A takes D and H to get g (g越接近f越好)**

再次强调->**A takes D and H to get g❗️**  

   

这堆符号的意思是：

$$ D = \{(x_1, y_1), (x_2, y_2) , … , (x_N, y_N)\} $$  表示**data/training examples**，其中x为输入 y为输出 

机器学习模型中的还包括 **learning Algorithm(A)** 以及 **hypothesis set(H)**，H中的一个**hypothesis**为h

f是假象中的一个完美符合训练集合的pattern，称其为**target function**

g则是我们努力用对于D，用A在H中挑选出来的表现最好的h（之后会介绍如何衡量h的好坏->Ein）

   

典型疑问：



### Lecture 2: Learning to Answer Yes/No

在上一讲刚介绍完基本的机器学习模型组成后，这一讲立即讲解了一个机器学习算法的实例PLA()

PLA的例子贯穿在了后面的很多讲中 算是个非常典型的例子

PLA是拿来做binary classification的



