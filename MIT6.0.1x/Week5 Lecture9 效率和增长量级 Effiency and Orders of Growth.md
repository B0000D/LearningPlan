# Week5 Lecture9 效率和增长量级 Effiency and Orders of Growth

标签（空格分隔）： MIT6.00.1x

---
##**Measuring Complexity**
###设计程序的目标
> * 只要输入合法 程序就能返回正确答案
> * 高效地进行计算
### 算法的效率?
> * 运行时间 
> * 机器内存
> * 可读性和复杂度的平衡
### 怎么测量复杂度?
> * **随机存取机器模型(RAM)**
执行的基本步骤数量
输入的规模
### 例子
```python
def linearSearch(L,x):
    for e in L:
        if e==x:
            return True
        return False
```
这段代码的复杂度和列表L的大小有关.
需要一个通用的办法衡量复杂度
**最佳案例**
**最坏的情况**(关键)
**一般(预期)的情况**
### **在最坏的情况下测量基本步骤**
```python
def fact(n):
answer=1 #赋值语句
while n>1: 
    answer*=n  #乘法和赋值
    n-=1   #减法和赋值
return answer
```
这是一段阶乘的程序,一共执行5*n+2步,当n足够大时,常数2可忽略,进一步的,乘法中的常量5也可以忽略
## **Asymptotic notatio**
需要一种正规的方式来寻找规模个时间的关系
使用渐进复杂度
使用$O$符号
## Complexity Classes
###对数复杂度$O(logN)$
> * 二分查找
> * 例
复杂度$O(logi)$
```python
#将整型数转化为字符串输出
def intToStr(i):
    digits='0123456789'
    if i==0:
        return '0'
    result=""
    while i>0:
        result=digits[i%10]+result
        i=i/10
    return result
```
### 线性复杂度$O(N)$
### 多项式复杂度
### 指数复杂度$O(a^N)$
递归函数
> * 例 求出给定集合所有子集的函数
```python
def genSubsets(L):
    res=[]
    if len(L)==0:
        return [[]]
    smaller=genSubsets(L[:-1])
    extra=L[-1:]
    new=[]
    for small in smaller:
        new.append(small+extra)
    return smaller+new
L=[1,2,3,4]
'''
genSubsets(L)
[[], [1], [2], [1, 2], [3], [1, 3], [2, 3], [1, 2, 3]]

'''
```
考虑更小问题的集合
复杂度为$O(2^N)$
## Comparing complexity classes
### 复杂度比较
在规模很大的情况下,复杂度的区别会很显著.
对数复杂度的增长会十分缓慢
指数复杂度的算法代价非常高