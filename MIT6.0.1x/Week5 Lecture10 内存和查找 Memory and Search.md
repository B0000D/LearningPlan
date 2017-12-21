# Week5 Lecture10 内存和查找 Memory and Search
标签（空格分隔）： MIT6.00.1x

---

## 搜索算法
### 算法和数据结构
> * 如何找到高效的算法?
很难发明新算法
将问题转化为一直的解决方案
理解问题的固有复杂度
如何将问题分解成多个子问题
### 搜索算法
> *  查找一个或一组特定的元素是否属于特定的元素或集合
> * 搜索空间集合
> * 比如平方根
### 线性搜索
> * 一个元素是否在某一个列表中
```python
def search(L,e):
    for i in range(len(L)):
        if L[i]==e:
            return True
    return False
```
复杂度为$O(len(L))$
获取列表中每一个元素的时间是恒定的吗?
通过计算元素的具体位置 在常数时间内找到元素
列表的大小不固定呢?
使用指针做到(间接引用)
## binary search 二分查找
### 列表是有序的
假设列表元素升序排列
### 使用二分查找
> * 选择将列表一分为二的下标i
> * L[i]==e?
> * 如果不,比较L[i]和e的大小
> * 根据上一步的结果,继续在i左侧或右侧的范围二分查找
> * **分治的思想**
**二分代码**
```python
def search(L,e):
    def bSearch(L,e,low,high):
        if high==low:
            return L[low]==e
        mid=low+int((high-low)/2)
        if L[mid]==e:
            return True
        if L[mid]>e:
            return bSearch(L,e,low,mid-1)
        else:
            return bSearch(L,e,mid+1,high)
    if len(L)==0:
        return False
    else:
        return bSearch(L,e,0,len(L)-1)
```
### 使用递减函数来检查递归函数是否可以终止
这里使用递减函数high-low
### 复杂度?
改变多少次上下限才能使high-low=0?
$O(log(len(L)))$
##　selection sort 选择排序
$sort(L)+log(len(L))<len(L)?$
此时先排序再二分不如直接查找
但是
如果我们要对元素k进行k次查找,那么先排序再二分的方法就会更高效
### 选择排序
> * 代码
```python
def selSort(L):
    for i in range(len(L)-1):
        minIndx=i
        minVal=L[i]
        j=i+1
        while j<len(L):
            if minVal>L[j]:
                minIndx=j
                minVal=L[j]
            j+=1
        temp=L[i]
        L[i]=L[minIndx]
        L[minIndx]=temp
```
首先将第一个位置定为最小元素的位置,之后每次都选取之后的元素中最小的与选定位置交换,最后形成升序的排列.
> * 分析
寻找循环不变量
列表前缀中的每一个元素都比后缀中的每一个元素小
知道前缀包含整个列表,后缀为空
> * 复杂度
$O(len(L)^2)$
## Amortized Cost Analysis 均摊成本分析
将大的成本均摊到一段时间内,并不意味着仅仅考虑一个操作的最坏情况,而是考虑一整个系列的操作.
## merge sort 归并排序 
比选择排序更好的方法?
###　**分而治之**
> * 列表大小是0或1,则已经有序
> * 列表中有超过一个元素,将它分成两个,分别进行排序
> * 将结果合并
每次比较两个列表的元素,优先放小的,之后若有列表有剩下的直接复制.
### 代码:
```python
import operator
def merge(left,right,compare):
    result=[]
    i,j=0,0
    while i<len(left) and j<len(right):
        if compare(left[i],right[j]):
            result.append(left[i])
            i+=1
        else:
            result.append(right[j])
            j+=1
    while i<len(left):
        result.append(left[i])
        i+=1
    while j<len(right):
        result.append(right[j])
        j+=1
    return result


def mergeSort(L,compare=operator.lt):
    if len(L)<2:
        return L[:]
    else:
        middle=int(len(L)/2)
        left=mergeSort(L[:middle],compare)
        right=mergeSort(L[middle:],compare)
        return merge(left,right,compare)
```
> * 时间复杂度:
合并的时间复杂度是$O(len(L))$
归类排序的时间复杂度是$O(len(L)*log(len(L)))$
所以总的复杂度是$O(Nlog(N))$
## Hashing 哈希
python自带数据结构用于排序,**字典**.
字典使用哈希函数来查找
查找的用时几乎和字典的规模无关,非常高效
### 什么是哈希
给定一个键,哈希函数会把他转化为整数.
哈希函数将一个巨大的输入空间转换为小的输入空间,使用空间减少冲突的频率.
一个好的哈希函数会均匀分布,将冲突发生的可能性降到最低.**均摊成本**
在某些键上使用哈希函数创建哈希表
### 时间复杂度
> * 理想情况下,直接读取,$O(1)$.
> * 每个元素都被哈希分到一个空间,那就是$O(N)$(需要线性查找)
> * 好的哈希函数会接近均匀分布,将复杂度削减至接近$O(1)$

