# Week4 Lecture8 断言和异常 Assertions and Exceptions

标签（空格分隔）： MIT6.00.1x

---

## 异常
### 什么是异常
> * 如何处理执行过程中的异常情况
下标(索引)错误
类型错误
命名错误
...
### 我们应该怎么做?
> * 继续 然后失败= =(bad)
> * 返回一种特殊的错误值
返回什么值?选择困难
> * 停止运行并发出错误情况的信号
**raise an exception**
### 处理异常
``` python
try:
    f=open('grades.txt')
    #处理grades的代码
except:
    raise Exception("Can't open grades file")
```
对于特定异常:
```python
try:
    f=open('grade.txt')
    #处理grades的代码
    except IOerror e:
        print "Can't open grades file:"+str(e)
        sys.exit(0)
    except ArithmeticError e:
        raise ValueError("Bug in grades calculation"+ str(e))
```
### 异常的种类
> * 语法错误 无法转换程序
> * 命名错误 存在未绑定的变量名
> * 属性错误
> * 类型错误
> * 数值错误
> * 输入输出错误
### 使用try还能..
> * 使用else语句 try实行完毕且没有异常时执行else代码块
> * 使用finally语句
总会在try else except后执行finally语句,即使抛出另一个错误或中断,继续,返回
清理无论发生什么都要执行的代码.
### 例
```python
def divide(x, y):
    try:
        result = x/y
    except ZeroDivisionError, e:
        print "division by zero! "+str(e)
    else:
        print"result is", result
    finally:
        print "executing finally clause"
'''
divide(3,0)
division by zero! integer division or modulo by zero
executing finally clause
'''
'''
然而对于字符串作为形参的情况:
divide('3','4')
executing finally clause
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "/home/hjj/文档/Python/MIT6.00.1X/8.2.py", line 3, in divide
    result = x/y
TypeError: unsupported operand type(s) for /: 'str' and 'str'
'''
```
```python
#改进的形式
def divideNew(x, y):
    try:
        result = x/y
    except ZeroDivisionError, e:
        print "division by zero! "+str(e)
    except TypeError:
        divideNew(int(x),int(y))
    else:
        print"result is", result
    finally:
        print "executing finally clause"
'''
divideNew('3','4')
result is 0
executing finally clause
executing finally clause
注意这里输出了两遍,因为finally语句执行了两次

divideNew(3,0)
division by zero! integer division or modulo by zero
executing finally clause
'''
```
##Error Handling
###小例子
假设我们有某一科目的班级列表,包含两个子列表:一个包含学生的姓和名字,另一个记录相应的作业成绩.
现在我们需要创建一个新的科目列表,包含名字,成绩和加权平均值(其中每个作业有不同的权重).
```python
def getSubjectStats(subject, weights):
    return [[elt[0],elt[1],avg(elt[1],weights)]for elt in subject]
def dotProduct(a,b):
    result=0.0
    for i in range(len(a)):
        result+=a[i]*b[i]
        return result
def avg(grades, weights):
    return dotProduct(grades, weights)/len(grades)


test=[[['fred', 'flint'], [10.0, 5.0, 85.0]], [['barney','rubble'],[10.0,8.0,74.0]],[['wilma','flint'],[8.0,10.0,96.0]],[['dino'],[]]]
weights=[.3, .2, .5]
'''
然而,当学生的成绩为空时,会出现错误
getSubjectStats(test,weights)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "/home/hjj/文档/Python/MIT6.00.1X/8.3.1.py", line 2, in getSubjectStats
    return [[elt[0],elt[1],avg(elt[1],weights)]for elt in subject]
  File "/home/hjj/文档/Python/MIT6.00.1X/8.3.1.py", line 9, in avg
    return dotProduct(grades, weights)/len(grades)
TypeError: unsupported operand type(s) for /: 'NoneType' and 'int'
'''
```
报错:
**TypeError: unsupported operand type(s) for /: 'NoneType' and 'int'**
对于这种情况,我们就可以使用异常来修复.
```python
def getSubjectStats(subject, weights):
    return [[elt[0],elt[1],avgNew(elt[1],weights)]
            for elt in subject]
def dotProduct(a,b):
    result=0.0
    for i in range(len(a)):
        result+=a[i]*b[i]
    return result
def avgNew(grades, weights):
    try:
        return dotProduct(grades,weights)/len(grades)
    except ZeroDivisionError:
        print 'no grades data'
        return 0.0
    except TypeError:
        newgr=[convertLetterGrade(elt)for elt in grades]
        return dotProduct(newgr,weights)/len(newgr)
def convertLetterGrade(grade):
    if type(grade)==int:
        return grade
    elif grade=='A':
        return 90.0
    elif grade=='B':
        return 80.0
    elif grade=='C':
        return 70.0
    elif grade=='D':
        return 60.0
    else:
        return 50.0

#例
test=[[['fred', 'flint'], [10.0, 5.0, 85.0]],
      [['barney','rubble'],[10.0,8.0,74.0]],
      [['wilma','flint'],[8.0,10.0,96.0]],
      [['dino'],[]]]
weights=[.3, .2, .5]
'''
输出结果:
>>>getSubjectStats(test,weights)
no grades data
[[['fred', 'flint'], [10.0, 5.0, 85.0], 15.5], [['barney', 'rubble'], [10.0, 8.0, 74.0], 13.866666666666667], [['wilma', 'flint'], [8.0, 10.0, 96.0], 17.466666666666665], [['dino'], [], 0.0]]
'''

'''
增加了异常处理,如果出现除0的情况,则说明这个同学没有成绩,将该同学的平均成绩置为0.0.如果同学的成绩用字母表示,会出现类型错误,利用异常先把字母表示的成绩转换成数字表示,再计算平均值.
'''
```
## **Exceptions as control flow**
###例
```python
def getRatios(v1,v2):
    #v1 and v2 are lists of equal length
    #returns a list containing the meaningful values of v1[i]/v2[i]
    ratios=[]
    for index in range(len(v1)):
        try:
            ratios.append(v1[index]/float(v2[index]))
        except ZeroDivisionError:
            ratios.append(float('NaN'))#NaN=Not a Number
        except:
            raise ValueError('getRatios called with bad arg')
    return ratios
try:
    print getRatios([1.0,2.0,7.0,6.0],[1.0,2.0,0.0,3.0])
    print getRatios([],[])
    print getRatios([1.0,2.0],[3.0])
except ValueError,msg:
    print msg
'''
输出:
[1.0, 1.0, nan, 2.0]
[]
getRatios called with bad arg

'''
```
### 与传统代码相比...
> * 可读性更好,更容易维护和修改,效率更高
> * 抽象地思考处理数据的方式简单得多
> * 包含控制流(编写处理器)的处理过程

## **Assertions** **断言**
使用断言声明计算所需的数据类型,如果不符合就抛出一个断言错误异常.很好的**防御式编程**
###例子(改进之前的成绩统计函数)
```python
def avgNew(grades,weights):
    assert not len(grades)==0,'no grades data'
    assert len(grades)==len(weights),'wrong number grades'
    newgr=[convertLetterGrade(elt)for elt in grades]
    result=dotProduct(newgr,weights)/len(newgr)
    assert 0.0<=result<=100.0
    return result
def getSubjectStats(subject, weights):
    return [[elt[0],elt[1],avgNew(elt[1],weights)]
            for elt in subject]
def dotProduct(a,b):
    result=0.0
    for i in range(len(a)):
        result+=a[i]*b[i]
    return result
def convertLetterGrade(grade):
    if type(grade)==int:
        return grade
    elif grade=='A':
        return 90.0
    elif grade=='B':
        return 80.0
    elif grade=='C':
        return 70.0
    elif grade=='D':
        return 60.0
    else:
        return 50.0
test=[[['fred', 'flint'], [10.0, 5.0, 85.0]],
      [['barney','rubble'],[10.0,8.0,74.0]],
      [['wilma','flint'],[8.0,10.0,96.0]],
      [['dino'],[]]]
weights=[.3, .2, .5]
'''
getSubjectStats(test,weights)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "/home/hjj/文档/Python/MIT6.00.1X/8.5.1.py", line 10, in getSubjectStats
    for elt in subject]
  File "/home/hjj/文档/Python/MIT6.00.1X/8.5.1.py", line 2, in avgNew
    assert not len(grades)==0,'no grades data'
AssertionError: no grades data
'''
```
> * 降低了一点效率
> * 很好的防御型编程
即使有输出,也不会传递坏值
### 什么时候使用断言?
> * 尽早发现错误并找到它发生的位置
> * 对于不好的数据输入,使用**抛出异常**,之后使用**断言**来检查违背程序限制的情况






