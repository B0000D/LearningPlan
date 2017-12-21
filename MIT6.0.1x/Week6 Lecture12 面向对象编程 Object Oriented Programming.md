# Week6 Lecture12 面向对象编程 Object Oriented Programming
标签（空格分隔）： MIT6.00.1x

---

## 继承
创建类的特化 共享积累属性 具有自己特有的属性
> 例子
建立一个应用 整理人事信息
基类是一个人 名字 生日 姓氏 年龄 
```python
import datetime

class Person(object):
    def __init__(self, name):
        """create a person called name"""
        self.name = name
        self.birthday = None
        self.lastName = name.split(' ')[-1]

    def getLastName(self):
        """return self's last name"""
        return self.lastName

    def setBirthday(self,month,day,year):
        """sets self's birthday to birthDate"""
        self.birthday = datetime.date(year,month,day)

    def getAge(self):
        """returns self's current age in days"""
        if self.birthday == None:
            raise ValueError
        return (datetime.date.today() - self.birthday).days

    def __lt__(self, other):
        """return True if self's ame is lexicographically
           less than other's name, and False otherwise"""
        if self.lastName == other.lastName:
            return self.name < other.name
        return self.lastName < other.lastName

    def __str__(self):
        """return self's name"""
        return self.name

# me = Person("William Eric Grimson")
# print me
# foo = 'William Eric Grimson'
# foo.split(' ')
# foo.split(' ')[-1]
# me.getLastName()
#
# me.setBirthday(1,2,1927)
# me.getAge()
#
# her = Person("Cher")
# her.getLastName()
# plist = [me, her]
# for p in plist: print p
# plist.sort()
# for p in plist: print p
```
### 列表的sort()函数
使用了一种叫做timsort的算法,对序列进行排序
对象的小于比较会被看做为一种调用
已经改写了对象中的小于判断方法

## 使用继承子类扩展方法
增加mitperson作为person类的子类
> 增加电话号码
查询电话号码
根据电话号码排序
```python
import datetime

class Person(object):
    def __init__(self, name):
        """create a person called name"""
        self.name = name
        self.birthday = None
        self.lastName = name.split(' ')[-1]

    def getLastName(self):
        """return self's last name"""
        return self.lastName

    def setBirthday(self,month,day,year):
        """sets self's birthday to birthDate"""
        self.birthday = datetime.date(year,month,day)

    def getAge(self):
        """returns self's current age in days"""
        if self.birthday == None:
            raise ValueError
        return (datetime.date.today() - self.birthday).days

    def __lt__(self, other):
        """return True if self's ame is lexicographically
           less than other's name, and False otherwise"""
        if self.lastName == other.lastName:
            return self.name < other.name
        return self.lastName < other.lastName

    def __str__(self):
        """return self's name"""
        return self.name

# me = Person("William Eric Grimson")
# print me
# me.getLastName()
# me.setBirthday(1,2,1927)
# me.getAge()
# her = Person("Cher")
# her.getLastName()
# plist = [me, her]
# for p in plist: print p
# plist.sort()
# for p in plist: print p

class MITPerson(Person):
    nextIdNum = 0 # next ID number to assign

    def __init__(self, name):
        Person.__init__(self, name) # initialize Person attributes
        # new MITPerson attribute: a unique ID number
        self.idNum = MITPerson.nextIdNum
        MITPerson.nextIdNum += 1

    def getIdNum(self):
        return self.idNum

    # sorting MIT people uses their ID number, not name!
    def __lt__(self, other):
        return self.idNum < other.idNum

p1 = MITPerson('Eric')
p2 = MITPerson('John')
p3 = MITPerson('John')
p4 = Person('John')

# print p1
# p1.getIdNum()
# p2.getIdNum()
# p1 < p2
# p3 < p2
# p4 < p1
# p1 < p4
```
### 注意的部分
> * 在mitperson中nextidnum的修改,每次初始化使用__init__()函数时就会更新一次,保证了每个mitperson的idnum是不同的
> * mitperson类作为子类,当改写_lt_的方法时,虽然父类person也有这个方法,但是子类的改写会**覆盖**父类的方法.调用时p1<p2可以理解为以p2为实参调用了p1的lt()方法,实质上是比较了p1和p2的idnum.
> * 这里有一个点,在最后运行p1<p4时,会抛出异常.而p4<p1则不会.为什么呢?如上一点所说,p4<p1实际上是以p1为实参调用了p4的lt()函数(还是person的lt函数),这时显然没有问题.然而运行p1<p4时,是以p4为实参调用p1的lt()函数,但是p4作为person类,是没有idnum这个参数的,因此p1的lt()函数(已经是mitperson的lt函数)无法进行两者的比较,因此抛出异常
## 利用继承来进行类的层次设计
进一步加入mitperson的子类student,又包含研究生和本科生
然而在添加一个新的转校生类之后,我们会发现出现了混乱
进一步思考后,我们需要添加一个研究生,本科生和转校生的超类 也就是学生.
###替代原则
任何超类的重要方法都应该被子类所继承，反之就不一定．子类的某些特定方法超类可以没有
```python
class UG(MITPerson):
    def __init__(self, name, classYear):
        MITPerson.__init__(self, name)
        self.year = classYear

    def getClass(self):
        return self.year

class Grad(MITPerson):
    pass

def isStudent(obj):
    return isinstance(obj,UG) or isinstance(obj,Grad)

s1 = UG('Fred', 2016)
s2 = Grad('Angela')
isStudent(s1)
isStudent(s2)

class TransferStudent(MITPerson):
    pass

# go back and define
# class Student(MITPerson)
# change inheritance for UG, Grad and TransferStudent
# change def isStudent(obj):
#            return isinstance(obj, Student)
```
## 例：成绩册

```python
class Grades(object):
    """A mapping from students to a list of grades"""
    def __init__(self):
        """Create empty grade book"""
        self.students = []  # list of Student objects
        self.grades = {}    # maps idNum -> list of grades
        self.isSorted = True # true if self.students is sorted

    def addStudent(self, student):
        """Assumes: student is of type Student
           Add student to the grade book"""
        if student in self.students:
            raise ValueError('Duplicate student')
        self.students.append(student)
        self.grades[student.getIdNum()] = []
        self.isSorted = False

    def addGrade(self, student, grade):
        """Assumes: grade is a float
           Add grade to the list of grades for student"""
        try:
            self.grades[student.getIdNum()].append(grade)
        except KeyError:
            raise ValueError('Student not in grade book')

    def getGrades(self, student):
        """Return a list of grades for student"""
        try:    # return copy of student's grades
            return self.grades[student.getIdNum()][:]
        except KeyError:
            raise ValueError('Student not in grade book')

    def allStudents(self):
        """Return a list of the students in the grade book"""
        if not self.isSorted:
            self.students.sort()
            self.isSorted = True
        return self.students[:] #return copy of list of students

def gradeReport(course):
    """Assumes: course if of type grades"""
    report = []
    for s in course.allStudents():
        tot = 0.0
        numGrades = 0
        for g in course.getGrades(s):
            tot += g
            numGrades += 1
        try:
            average = tot/numGrades
            report.append(str(s) + '\'s mean grade is '
                          + str(average))
        except ZeroDivisionError:
            report.append(str(s) + ' has no grades')
    return '\n'.join(report)

ug1 = UG('Jane Doe', 2014)
ug2 = UG('John Doe', 2015)
ug3 = UG('David Henry', 2003)
g1 = Grad('John Henry')
g2 = Grad('George Steinbrenner')

six00 = Grades()
six00.addStudent(g1)
six00.addStudent(ug2)
six00.addStudent(ug1)
six00.addStudent(g2)

for s in six00.allStudents():
    six00.addGrade(s, 75)
six00.addGrade(g1, 100)
six00.addGrade(g2, 25)

six00.addStudent(ug3)

print gradeReport(six00)
```
> * 依旧需要注意使用类的方法来外部访问类的数据,而不是直接查看类的数据结构
> * 这个例子使用了列表和映射来分别存储学生的名单和对应成绩,然而缺点是在输出成绩册时又创建了一个新的空列表,浪费了空间结构,还可以继续改进,只使用类原有的列表来做到输出成绩
## 生成器
如上所说,对于学生人数很多的情况,上述办法的效率很低
可以使用生成器
对任意属于类的程序 都有一个yield语句
每个生成器实例都有一个next()方法与之相连
第一次调用生成器时,会一直执行代码直到遇到yield语句
并返回值
### 例
```python
def genTest():
    yield 1
    yield 2

foo =genTest()
print foo.next()
print foo.next()
print foo.next()
#1
#2
#StopIteration
```
将生成器使用在循环结构中
```python
for n in genTest():
    print n
#1
#2
```
对此可以使用循环结构和生成器
```python
def genFib():
    fibn_1=1 #fib(n-1)
    fibn_2=0 #fib(n-2)
    while True:
        #fib(n)=fib(n-1)+fib(n-2)
        next=fibn_1+fibn_2
        yield next
        fibn_2=fibn_1
        fibn_1=next
fib=genFib()
print fib.next()
print fib.next()
print fib.next()
print fib.next()
for n in genFib():
    print n
#1
#2
#3
#5
#会不断地输出fib数
```
### 为什么使用生成器?
> *  分离计算原理和计算过程
> * 生成新的对象用于另一个计算的一部分
生成器不断地依据一个元素进行计算,从长序列的计算中分离开来
### 对之前返回学生列表方法的改进
```python
    #原代码
    def allStudents(self):
        if not self.isSorted:
            self.students.sort()
            self.isSorted = True
        return self.students[:] #return copy of list of students
    #改进之后
    def allStudents(self):
        if not self.isSorted:
            self.students.sort()
            self.isSorted = True
        for s in self.students:
            yield s
        #return copy of list of students
```
这样就不在需要一个新列表来存学生信息,而是使用yield来控制输出 输出一个学生的信息之后就停一下 也能做到输出全部的学生信息


