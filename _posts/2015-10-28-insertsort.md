---
layout: post
title: "Insert Sort"
date: 2015-10-28
excerpt: "直接、折半、二路、表插入、希尔排序"
tags: [algorithm]
comments: true
---

### 插入排序
__示例的数据结构__

```

    #define MAXSIZE 10

    #define EQ(a,b) ((a) == (b))
    #define LT(a,b) ((a) <  (b))
    #define LQ(a,b) ((a) <= (b))

    typedef struct{
       int key;
    }ElemType;

    typedef struct{
      ElemType data[MAXSIZE + 1];
    }myList,*List;
```

---

## 一、插入排序

### 1.直接插入排序 Straight Insertion Sort

```
    void InsertSort(myList &p)
    {
        int j;
        for(int i = 2; i <= MAXSIZE; i++){
          if(LT(p.data[i].key,p.data[i - 1].key)){
              p.data[0] = p.data[i];
              for(j = i - 1; LT(p.data[0].key,p.data[j].key); j--){
                  p.data[j + 1] = p.data[j];
              }
              p.data[j + 1] = p.data[0];
          }
      }
    }
```
    
直接插入排序简单易懂，时间复杂度为O(n^2)，但是比较和移动次数略大，约为n^2/4，不适合数据很多的时候使用。
下面考虑*折半插入*能否优化。


### 2.折半插入排序 Binary Insertion Sort

```
     void BInsertSort(myList &p)
    {
        int low,high,mid,j;
        for(int i = 2; i <= MAXSIZE; i++){
            p.data[0] = p.data[i];
            low = 1;
            high = i - 1;
            while(low <= high){
                mid = (low + high) / 2;
                if(LT(p.data[0].key,p.data[mid].key)){
                    high = mid - 1;
                }
                else{
                    low = mid + 1;
                }
            }
            for(j = i - 1; j >= high + 1; j--){
                p.data[j + 1] = p.data[j];
            }
           p.data[high + 1] = p.data[0];
        }
    }
```
    
折半插入排序时间复杂度依然为O(n^2)。比较次数相对直接插入排序有所减少，但是记录的移动次数没变。
接下来考虑减少移动次数的方法——*2-路插入排序*。


### 3.二路插入排序 
为了减少移动次数，根据时间空间守恒定理（雾），需要n个记录的辅助空间，比如数组d。
选取表中第一个元素赋给d[1]，遍历表中元素，如果小于d[1]就插在d[1]前面，反之插在后面。
把d想象成循环向量，设两个指针first和final分别指排序过程中得到的有序序列的第一个和最后一个记录。


```
    void BiInsertSort(myList &p)
    {
        myList temp;
        int first = 1,final = 1;
        temp.data[1] = p.data[1];//d[1]
        for(int i = 2; i <= MAXSIZE; i++){
            if(LT(p.data[i].key, temp.data[first].key)){
                first = (first - 1 + MAXSIZE) % MAXSIZE;
                temp.data[first] = p.data[i];
            }
            else if(!LT(p.data[i].key, temp.data[final].key)){
                final++;
                temp.data[final] = p.data[i];
            }
            else if(!LT(p.data[i].key, temp.data[first].key) && LT(p.data[i].key, temp.data[final].key)){
                int j = final;
                final = final + 1;
                while(LT(p.data[i].key,temp.data[j].key)){
                    temp.data[(j + 1) % MAXSIZE] = temp.data[j];
                    j = (j - 1 + MAXSIZE +1) % MAXSIZE;
                }
                temp.data[j + 1] = p.data[i];
            }
        
        }
        //改变原表中的顺序
        for(int i = 1; i <= MAXSIZE; i++){
            p.data[i] = temp.data[(first++)% MAXSIZE];
        }
    }
```


在二路插入排序中，移动记录的次数约为n^2/8。当d[1]恰好是待排序记录中最小或者最大元素的时候，就失去了该算法的优越性。
因此，若希望在排序过程中不移动记录，只有改变存储结构，进行*表插入排序*。


### 4. 表插入排序
存储结构：

```
    #define SIZE 10 //静态链表容量
    
    typedef struct{
      RcdType rc;   //记录项
      int next;     //指针项
    }SLNode;
    
    typedef struct{
      SLNode r[SIZE];
      int length;
    }SLinkListType;
```
å
表插入排序的过程：
首先将静态链表中数组下标为1的结点和表头结点构成一个循环链表，然后依次将下标为2至n的结点按记录关键字非递减有序插入到循环链表中。
表插入排序的时间复杂度仍是O(n2)。
表插入排序的结果只是求得一个有序链表，只能顺序查找。为了能实现有序表的折半查找，需要对记录进行__重新排列__。
重排记录的做法：顺序扫描有序链表，将链表中第i个结点移动至数组的第i个分量中。

[表插入排序示例图](http://see.xidian.edu.cn/cpp/uploads/allimg/120301/1-12030113494G29.jpg)


### 5. 希尔排序
该方法的基本思想是：先将整个待排元素序列分割成若干个子序列（由相隔某个“增量”的元素组成的）分别进行直接插入排序，然后依次缩减增量再进行排序，待整个序列中的元素基本有序（增量足够小）时，再对全体元素进行一次直接插入排序。因为直接插入排序在元素基本有序的情况下（接近最好情况），效率是很高的。
 
 [动画演示](http://www.tyut.edu.cn/kecheng1/site01/suanfayanshi/shell_sort.asp) 
 
```
    void shell_pass(myList &p,int d)
    {
        int j,k;
        for(j = d + 1; j <= MAXSIZE; j++){
            p.data[0] = p.data[j];
            k = j - d;
            while(k > 0 && LT(p.data[0].key,p.data[k].key)){
                p.data[k + d] = p.data[k];
                k -= d;
            }
           p.data[k + d] = p.data[0];
       }
    }
    void ShellSort(myList &p,int dk[],int t) // e.g. dk[3] = {5,3,1}，最后一个增量必须为1
    {
        for(int i = 0; i < t; i++){
            shell_pass(p,dk[i]);
        }
    }
```
    
希尔排序的比较和移动次数约为n^1.3次，n->无穷大则约为n(log2n)^2。时间复杂度为O(n^1.5)。
