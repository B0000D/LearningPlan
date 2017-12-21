# Week3 Lecture5 递归

标签（空格分隔）： MIT6.00.1x

---
## **递归实例**
### **towers of hanoi**
###**汉诺塔问题**
移动64个圆盘
递归思想：将n-1个圆盘放到空闲的柱子上，再移动底下的，移动最底下的一个，再把n-1个移过去。
不断解决规模更小的问题。
使用递归可以简单的实现
**代码**
```python
# Desktop/python/171019_1.py
def printMove(fr,to):
    print(' move from '+str(fr)+' to '+str(to))
def Towers(n,fr,to,spare):
    if n==1:
        printMove(fr,to)
    else:
        Towers(n-1,fr,spare,to)
        Towers(1,fr,to,spare)
        Towers(n-1,spare,to,fr)
```

### **Fibonacci**
```python
# -*- coding: utf-8 -*-
#Desktop/python/171022_1.py
def fib(x):
    assert type(x)==int and x>=0 #断言：如果表达式为真就会运行之后的代码段
    if x==0 or x==1:
        return 1
    else:
        return fib(x-1)+fib(x-2)
n=0
while n<10:
    print(fib(n))
    n=n+1
```
**如果n>=2,fib(n)=fib(n-1)+fib(n-2)**

### **palindrome** **回文串**
```python
#Desktop/python/171022_2.py
def isPalindrome(s):
    def toChars(s):
        s= s.lower()
        ans=""
        for c in s:
            if c in 'abcdefghijkmnopqrstuvwxyz':
                ans=ans+c
        return ans
    
    def isPal(s):
        if len(s)<=1:
            return True
        else:
            return s[0]==s[-1] and isPal(s[1:-1])
    return isPal(toChars(s))
```
 ### **总结**
 Solve a hard problem by **breaking** it into a set of sub-problems such that:
 Sub-problems are easier to solve than the original
 Solution of the sub-problems can be **combined** to solve the original
 
 ### **Global vatiables** **全局变量**
name is defined at the outermost scope of the progeam,rather than a scope of function where it appears
**使用全局变量来计算fib函数的调用次数**
```python
#Desktop/python/171022_3.py
def fibMetered(x):
    global numCalls
    numCalls+=1
    if x==0 or x==1:
        return 1
    else:
        return fibMetered(x-1)+fibMetered(x-2)
def testFib(n):
    for i in range(n+1):
        global numCalls
        numCalls=0
        print('fib of '+ str(i)+' = '+str(fibMetered(i)))
        print('fib called '+str(numCalls)+' times')
```
testFib(n)函数返回计算第n项fib数时需要调用多少次FibMetered函数
**Destroy locality of code**
**Since can be modified or read in a wide range of places, can be easy to break locality and introduce bugs!**
