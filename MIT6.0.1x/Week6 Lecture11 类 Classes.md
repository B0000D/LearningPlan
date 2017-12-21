# Week6 Lecture11 类 Classes
标签（空格分隔）： MIT6.00.1x

---
## 由用户定义的类型
将数据捆绑成单元
### 对象
python支持很多种类的数据,这些都可以成为对象.
每个对象有一个类型
一个特定的对象也可以叫类型的一个实例
类型与操作相关联 行为因类型而异
规定一些列程序说明如何与这些对象互动
### 链表
数据和指针组成一个单元,指针指向下一个单元
可以很方便地插入数据
将内部表示和对象的使用分离开来
### 面向对象编程(oop)
任何东西都是对象 而且有类型 对象是抽象
分离内部表示和我们用来访问他的接口
接口定义了行为
创建对象的实例 显示或饮食
去除对象的方式
### 垃圾回收机制
系统回收不在可访问的内存
### 优点
模块性 
> 降低复杂度 创建病实现每一种类型的对象
将一个复杂的东西看做基本类型

使代码编写更容易
> 每一个类有一个独立的环境
环境保存方法信息
继承

## 一个使用类的例子
信息 数据 方法抽象到一个包中
创建对象
### 创建新对象
除了自己的定义之外 会自动从父类继承属性
> 创建一个新的类及其初始化方法
```python
class Coordinate(object):
    def __init__(self,x,y):
        self.x=x
        self.y=y


c=Coordinate(3,4)
origin=Coordinate(0,0)
print c.x,origin.x
```
## 类的环境(框架)
类是顶层环境的引用
他指向一个框架
属性在框架内
在全局框架中获取类,指向一个新的框架,在新框架中寻找绑定的值或者方法,再指向方法内的程序段

## 给类添加新的方法
初始化方法
基本对象操作
直接输出类会怎么样?
比如之前定义的c
> __main__.Coordinate object at 0x7f6d84f53d10>
c在全局环境定义下 以及他在内存中的位置
###输出类的信息
可以添加一个字符串方法来输出类的信息
使用双下划线来重写基方法
例子中的init str都是基方法
```python
class Coordinate(object):
    def __init__(self,x,y):
        self.x=x
        self.y=y
    def __str__(self):
        return "<"+str(self.x)+","+str(self.y)+">"


c=Coordinate(3,4)
print c
#<3,4>
```
### 返回对象的类型
``` python
class Coordinate(object):
    def __init__(self,x,y):
        self.x=x
        self.y=y
    def __str__(self):
        return "<"+str(self.x)+","+str(self.y)+">"


c=Coordinate(3,4)
print type(c)
print Coordinate,type(Coordinate)
#<class '__main__.Coordinate'>
#<class '__main__.Coordinate'> <type 'type'>
```
> 使用isinstance()函数
```python
...
print isinstance(c,Coordinate)#c是Coordinate的实例吗?
#True
```
###添加自己的方法
```python
class Coordinate(object):
    def __init__(self,x,y):
        self.x=x
        self.y=y
    def __str__(self):
        return "<"+str(self.x)+","+str(self.y)+">"
    def distance(self,other):
        return math.sqrt(sq(self.x-other.x)+sq(self.y-other.y))
```
## 一个类的例子
一个整数集合
每一个特定整数只在集合中出现一次
使用列表存储集合的元素
有什么接口
> 插入
某个元素在集合中吗?(判断)
将某个元素从集合中移除
**类的定义**
```python
class intSet(object):
    def __init__(self):
        self.vals=[]
    def insert(self,e):
        if not e in self.vals:
            self.vals.append(e)
    def __str__(self):
        self.vals.sort()
        return '{'+','.join([str(e)for e in self.vals])+'}'
    def remove(self,e):
        try:
            self.vals.remove(e)
        except:
            raise ValueError(str(e)+' not found ')
    def member(self,e):
        return e in self.vals
```
**创建intSet 插入 删除 输出 检测**
```python
s=intSet()
print s
s.insert(1)
s.insert(2)
print s
s.remove(1)
print s
print(s.member(1))
print(s.member(2))
s.remove(3)
print s
#{}
#{1,2}
#{2}
#False
#True
#ValueError: 3 not found 
```
**总是使用定义好的接口**



