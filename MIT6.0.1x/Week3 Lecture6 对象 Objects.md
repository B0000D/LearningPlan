# Week3 Lecture6 对象 Objects

标签（空格分隔）： MIT6.00.1x

---
### Compound data types
> Tuples
> Lists
> Dictionaries

### **Tuples** 元组
Ordered sequence of elements(similar to strings) which can be more than just characters
t1=(1,2,3),t2=(t1,4),t3=(5,)
> **Concatenation** t1+t2=((1,2,3),4)
**Indexing** (t1+t2)[2]=4
**Slicing** t1[1:3]=(2,3)
**Singletons** t3=(5,)，可以只有一个元素

### 返回两个数的公约数
```python
#Desktop/python/171022_4.py
def findDivisors(n1,n2):
    divisors=()#empty tuple
    for i in range(1,min(n1,n2)+1):
        if n1%i==0 and n2%i==0:
            divisors=divisors+(i,)
    return divisors
divisors=findDivisors(12,20)
total=0
for d in divisors:
    total+=d
print(total)
```

### **Lists** 列表
Ordered sequence of values,each identified by an index
Use [1,2,3] rather than (1,2,3)
Singletons are now just [4] rather than (4,)
**DIFFERENCE**
Lists are mutable! 可变
While tuple,int,float,str are immutable
Can be modified after they are created

**Operations on lists**
**append() 附加**  在已有列表中添加元素(列表也可以是元素)
**a(list)+b(list) 串联**  将已有的两个列表中的元素按顺序放在一个列表中
**remove() 删除**  从列表中删除某个元素
```python
#Desktop/python/171022_5.py
Techs=['MIT','Cal Tech']
Ivys=['Harvard','Yale','Brown']
print(Ivys[1])
Univs=[Techs,Ivys]
Univs1=[['MIT','Cal Tech'],['Harvard','Yale','Brown']]
print(Univs)
print(Univs1)
#However
Techs.append('RPI')
print(Univs)
print(Univs1)
#Lists are mutable!
Techs[2]='WPI'
print('new')
print(Univs)
for e in Univs:
    print('Univs contains')
    print(e)
    print(' which contains')
    for u in e:
        print(' '+u)
```

```python
#Desktop/python/171023_1.py
def removeDups(L1,L2):
    for e1 in L1:
        if e1 in L2:
            L1.remove(e1)
L1=[1,2,3,4]
L2=[1,2,5,6]
removeDups(L1,L2)
print(L1)
print(L2)
#L1 changed while the index in L1 didn't changed
#So we need to clone L1
def removeDups1(L1,L2):
    L1Start=L1[:]#use '[:]' to really make a clone rather than L1 itself
    # Otherwise if L1 changed,L1Start will also change to be same as L1
    for e1 in L1Start:
        if e1 in L2:
            L1.remove(e1)
L1=[1,2,3,4]
L2=[1,2,5,6]
removeDups1(L1,L2)
print('right:')
print(L1)
print(L2)

```
### **Functions as objects**
Functions are **first class objects**:
> Have types
Can be elements of data structures like lists
Can appear in expressions(表达式右边或者函数参数)

**Coupled with lists**
**Higher order programming**

```python
#Desktop/python/171023_2.py
def applyToEach(L,f):
    for i in range(len(L)):
        L[i]=f(L[i])
L=[1,-2,3.4]
applyToEach(L,abs)
print(L)
applyToEach(L,int)
print(L)

def applyFuns(L,x):
    for f in L:
        print(f(x))
applyFuns([abs,int],-4.3)

def mapp(f,L1,L2):
    for e in range(min(len(L1),len(L2))):
        L1[e]=f(L1[e],L2[e])
    return L1
L1=[1,28,36]
L2=[2,57,9]
print(mapp(min,L1,L2))
```

### **Dictionaries** 字典
元素可以是任何不可变的值
**键值对**的集合（键和相关联数值的配对）
'Jan':1
<键> <冒号> <值>
**Get out elements**
通过键来获取信息
信息是**无序**的，只能通过键来访问值，不能通过索引来访问
**Insertion**
**将字典中的键迭代到一个列表中**
**keys()** 返回一个存放字典中所有键的列表
**键也可以是元组**
keys must be **immutable**,so have to use a tuple,not a list.
```python
#Desktop/python/171023_3.py
monthNumbers={'Jan':1,'Feb':2,'Mar':3,1:'Jan',2:'Feb',3:'Mar'}
print(monthNumbers['Jan'])
monthNumbers['Apr']=4
print(monthNumbers)
Collect=[]
for e in monthNumbers:
    Collect.append(e)
print(Collect)
print(monthNumbers.keys())
Dict={(1,2):'twelve',(1,3):'thirteen'}
print(Dict)
print(Dict[(1,2)])
```
