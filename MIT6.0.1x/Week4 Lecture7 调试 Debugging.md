# Week4 Lecture7 调试 Debugging

标签（空格分隔）： MIT6.00.1x

---


ways of trying code on examples to determine is running correctly
> break program into components
> document constraints on modules
### when to test?
> the code wil actually run
> have a set of correct answers
## test suites
> prove that bug exists
> **partiton** space of inputs into subsets
> random testing 
> **black-box testing** 
based on exploring paths through the **specifiactions**
> **glass-box testing**
based  on exploring paths through the **code** 
## **black-box testing**
> without looking at codes
avoid ingerent biases of implementer
designed without the knowledge of implementation
### **glass-box testing**
> **path-complete** if every potential path through the code is tested at least once(not alwats possible) more likely to find the bug
**boundary cases**
> make sure both branches of all **if** statrments
each **except** clause is executed
for each **for** loop:
    not entered
    body of loop executed exactly once
    body of loop executed more than once
for each **while** loop :
    same cases as for loops
    cases that catch all ways to exit loop
for **recursive functions**:
    test with no recursive calls,one and more

## **test drivers and stubs**
### Conducting tests
> * start with **unit testing**
check that each module works correctly
> * move to **inregration testing**
check that system as whole works correctly
> * cycle between these phases
### **test drivers and stubs**
> * **drivers** are code that 
set up environment
invoke code on predefined sequence of inputs
save results
report
**simulate parts of program that use unit being tested**
> * Stubs simulate parts of program used by unit being tested
test units that depend oin sofware not yet written
### Good testing practice
> * start with unit testing
> * integration testing
> * **regression testing**
check that program still passes all the test it used to pass
## **Debugging**
### Runtime bugs

> * Overt or Covert
**Overt** obvious: code crashes or runs forever
**Covert** not obvious: return a value which may be incorrect
> * Persistent or Intermittent
**Persistent** occurs every time code is run
**Intermittent** only occurs some times
> * **Defensive programming** try to ensure that if error is made ,bug will fall into this category(Overt and persistent)
Covert :highly dangerous
## **Debugging as search**
### DEbugging skills
> * looking for explanation for incorrect behavior
study available data
> * 建立一个和数据一致的假设
> * 设计并运行一个可重复的实验，巩固或驳回代码错误位置的假设
> * 浏览一遍之前所做的事情
> * 选出可能出现错误的范围
> * 暴露计算出现错误的中间环节 使用打印语句,二分查找等
> * 从简单的例子开始尝试
### 使用二分法查找错误
选择代码的**中点** 使用打印语句print()
找到错误之后继续二分,如此循环
### 如何思考调试?
> * 查看最常发生错误的地方
> * 关注代码在做什么
> * 排除错误可能出现的地方
> * 尝试解释遇到的问题
> * 不要相信说明文档