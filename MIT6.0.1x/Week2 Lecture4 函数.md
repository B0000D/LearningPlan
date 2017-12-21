# Week2 Lecture4 函数

标签（空格分隔）： MIT6.00.1x

---

**give us abstraction-allow us to capture computation and treat as if primitive**
提供方法捕捉计算和封装
**函数的定义**
def <函数名>(<形参（可以没有）>):
<函数部分代码>
**简单的最大值函数**
```python
def max(x,y):
    if x>y:
        return x
    else:
        return y
```
![image_1bs0etdvouhidtb1jon16t01lto9.png-151.6kB][1]
**黑盒**
封装可以保护内部细节
函数对应局部环境 和全局环境无关
函数中（局部环境中）形参的变化不影响全局变量
**一个例子：**
```python
#desktop/python/17109_1.py
def f(x):
    y=1
    x=x+y
    print('x= '+str(x))
    return x
x=3
y=2
z=f(x)
print('z= '+str(z))
print('x= '+str(x))
print('y= '+str(y))
```
**程序输出结果：**
x= 4(形参x)
z= 4(全局变量z)
x= 3(全局变量x)
y= 2(全局变量y)
![image_1bs0ga2i31g6di04fm3ncqpbgm.png-131.2kB][2]
调用函数会构成一个新的框架，创建一个新的局部环境也叫作用域（静态域），读取形参进行计算
**多重函数的调用**
![image_1bs0h012g82hfp4isf77lf813.png-122.4kB][3]
每一个函数的作用域相互独立，调用函数时再去对应的作用域读取形参，因此可以在不同的函数中使用相同的形参名，互不影响。
**二分法求正负浮点数的n次方根（函数形式）**
```python
#Desktop/python/17109_2.py
def findRoot(x,power,eps):
    """x(int or float),eps(int or float),power(int)
    eps>0 && power>=1
    return ans(float),ans**power is within eps of x
    if such ans does not exist,return None
    """
    if x<0 and power%2==0:
        return None
    low=min(-1,x)
    high=max(1,x)
    ans=(high+low)/2.0
    while abs(ans**power-x)>eps:
        if ans**power<x:
            low=ans
        else:
            high=ans
        ans=(high+low)/2.0
    return ans
```
"注释"的重要性：编写者和用户的沟通，函数的假设和保证
两个很好的属性
分解：将问题分解成独立的模块
抽象:隐藏细节，简化设计，压缩不必要的细节部分
将大量的函数集中放在一个py文件中，导入文件中的函数即可。
使用 import <文件名>即可
或者导入所有内容 from <文件名> import *
**模块名必须带有英文**
![image_1bs0ipgi6oe715h6pqq1r1r1s1g.png-111.2kB][4]
![image_1bs0iqb7r7b41godj6hc1cj51t.png-77kB][5]
```python
#Desktop/python/17109_3.py
pi=3.14159
def area(r):
    return pi*(r**2)
def circumference(r):
    return 2*pi*r
```



  [1]: http://static.zybuluo.com/B0000D/eakq2bgvzmvyycnx9h7q3imc/image_1bs0etdvouhidtb1jon16t01lto9.png
  [2]: http://static.zybuluo.com/B0000D/3b3xxv0e95gpjay78zzb3vbx/image_1bs0ga2i31g6di04fm3ncqpbgm.png
  [3]: http://static.zybuluo.com/B0000D/xqz11vf5c4r3qubwl6216xgf/image_1bs0h012g82hfp4isf77lf813.png
  [4]: http://static.zybuluo.com/B0000D/yxq52g6wmlaa950o6b78q7oi/image_1bs0ipgi6oe715h6pqq1r1r1s1g.png
  [5]: http://static.zybuluo.com/B0000D/o213u0v8lmxbg27sewm7633d/image_1bs0iqb7r7b41godj6hc1cj51t.png