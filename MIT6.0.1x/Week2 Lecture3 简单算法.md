# Week2 Lecture3 简单算法
# **Simple Algorithms**

标签（空格分隔）： MIT6.00.1x

---
## **2017/09/28**
## **Floating Point Accuracy**
### **浮点数的处理**
十进制转换到二进制 通过2的n次幂来转换
### **Python代码：十进制数转换成二进制数（包括正负浮点数）**

```python
#Desktop/python/17928_2.py
x=input()
p=0
result=""
if x==0:
    isNeg=False
    result='0'
else:   
    isNeg=False
    if x<0:
        isNeg=True
        x=abs(x) 
    result=""
    if 0<x<1:
        while((2**p)*x%1!=0):
            p=p+1
        num=int(x*(2**p))
        while num>0:
            result=str(num%2)+result
            num=num/2
        for i in range(p-len(result)+1):
            result='0'+result
        result=result[0]+'.'+result[1:]
    else:
        k=int(x)
        x=x-k
        p=0
        while((2**p)*x%1!=0):
            p=p+1
        num=int(x*(2**p))
        while num>0:
            result=str(num%2)+result
            num=num/2
        for i in range(p-len(result)):
            result='0'+result
        result='.'+result
        while k>0:
            result=str(k%2)+result
            k=k/2
if isNeg:
    result='-'+result
    print(str(result))
else:
    print(str(result))
```
**对于某些浮点数，找不到对应的2的n次幂，那么python会使用一个近似值来表示。**
**因此，对于浮点数的比较，需要规定一个无穷小量（大小按情况而定）**

**注意！使用 abs(x-y) < eps(比如0.001)而不是x==y来比较浮点数。否则会因为近似值的原因导致比较错误。当使用print(0.1)输出浮点数0.1时，python会自动去掉一些位，因此输出的实际上并非精确的0.1**


##**2017/9/29**
## **Approximation Methods**
设置步长，使用**guess+check**的方法得到浮点数平方根的近似值。
### **代码**
``` python
#Desktop/python/17929_1.py
x=12345
eps=0.01
step=eps**2
numGuesses=0
ans=0.0
while(abs(ans**2-x))>=eps and ans<=x:
    ans+=step
    numGuesses+=1
print(str(numGuesses))
if abs(ans**2-x)>=eps:
    print("Fail")
else:
    print(str(ans))
```
**步长的设置**是关键，步长太大会影响答案的准确性，步长太小会大大增加寻找近似答案的时间。需要另外的方法来更高效地寻找答案。

##**Bisection Search**
##**二分查找**
每次都能将搜索的范围减少一半。
### **代码**
```python
#Desktop/python/17929_2.py
x=25
eps=0.01
numGuesses=0
low=0.0
high=x
ans=(high+low)/2.0
while abs(ans**2-x)>=eps:
    print("low = "+str(low)+" high= "+str(high)+" ans= "+str(ans))
    numGuesses+=1
    if ans**2<x:
        low=ans
    else:
        high=ans
    ans=(low+high)/2.0
print('numGuesses= '+str(numGuesses))
print(str(ans))
```
对于x=25，这样只需要**13**次猜测就可以得到较为精确的平方根的值，而原来需要**49990**次。
**减少了计算的次数**
**待搜索的数据需要有序**
## **Newton-Raphson Root Finding**
## **牛顿-拉夫逊算法**
求一个x的值使得一个x的值为0。
对于一个多项式p(x)=$a_nx^n+a_{n-1}x^{n-1}+...+a_1x+a_0$
需要找到一个$r$使得$p(r)=0$,
那么如果g是r的一个近似值，有：
$g-p(g)/p(g')$
是一个更精确的值（离目标值r更近）
###**代码**
```python
#Desktop/python/17929_3.py
eps=0.01
y=24.0
guess=y/2.0
num=0
while abs(guess*guess-y)>=eps:
    guess=guess-(((guess**2)-y)/(2*guess))
    num+=1
print("num to guess= "+str(num))
print(str(guess))
```
对于数十规模的数只需要几次就能得出结果，横向对比的话需要的次数是这几个算法中最少的。效率非常高。用于处理可以转化为多项式求值的一些问题求解。