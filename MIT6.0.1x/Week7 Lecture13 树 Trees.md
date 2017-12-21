# Week7 Lecture13 树 Trees
标签（空格分隔）： MIT6.00.1x

---
## 树的介绍
一个非常有用的数据结构
存储具有层次的数据
将问题转化为决策问题(二选一或多选一)
### 树的定义
> * 有一个或多个结点来存储值 (具体或抽象 任何类型)
> * 结点由分支相连
> * 从根节点开始(最高层的节点)
> * 除了叶子结点,每个结点都有至少一个子结点
> * 在树中,每个子节点都只有一个父节点(扩展到图)
> * 没有循环
### 二叉树
每个节点只有0个或者2个子节点
树的一个特殊版本
如果数据按顺序排列,可以使用二叉树来存储数据
小于或大于当前数值?二选一
### 二叉树的代码定义
一个简单版本
```python
class binaryTree(object):
    def __init__(self, value):
        self.value = value
        self.leftBranch = None
        self.rightBranch = None
        self.parent = None

    def setLeftBranch(self, node):
        self.leftBranch = node

    def setRightBranch(self, node):
        self.rightBranch = node

    def setParent(self, parent):
        self.parent = parent
        def getValue(self):
        return self.value

    def getLeftBranch(self):
        return self.leftBranch

    def getRightBranch(self):
        return self.rightBranch

    def getParent(self):
        return self.parent

    def __str__(self):
        return self.value
```
### 构造一棵树
```python
n5 = binaryTree(5)
n2 = binaryTree(2)
n1 = binaryTree(1)
n4 = binaryTree(4)
n8 = binaryTree(8)
n6 = binaryTree(6)
n7 = binaryTree(7)
n3 = binaryTree(3)

n5.setLeftBranch(n2)
n2.setParent(n5)
n5.setRightBranch(n8)
n8.setParent(n5)
n2.setLeftBranch(n1)
n1.setParent(n2)
n2.setRightBranch(n4)
n4.setParent(n2)
n8.setLeftBranch(n6)
n6.setParent(n8)
n6.setRightBranch(n7)
n7.setParent(n6)
n4.setLeftBranch(n3)
n3.setParent(n4)
```
实际建立的树如图
![13.3.1.jpg-16.8kB][1]
## 树的搜索
如何检索一棵树
### **深度优先搜索**
在回溯前一直搜索到最深
使用**栈**这种数据结构追踪为探索的结点 
**后进先出(LIFO)**
代码:
```python
def DFSBinary(root, fcn):
    queue = [root]
    while len(queue) > 0:
        print 'at node ' + str(queue[0].getValue())
        if fcn(queue[0]):
            return True
        else:
            temp = queue.pop(0)
            if temp.getRightBranch():
                queue.insert(0, temp.getRightBranch())
            if temp.getLeftBranch():
                queue.insert(0, temp.getLeftBranch())
    return False
def find6(node):
#为了简洁,使用该函数来判断结点的值是不是6
    return node.getValue() == 6
def find10(node):
#为了简洁,使用该函数来判断结点的值是不是10
    return node.getValue() == 10
print 'DFS'
DFSBinary(n5, find6)#查找值为6的结点在不在树内
DFSBinary(n5, find10)#查找值为10的结点在不在树内
'''
DFS
at node 5
at node 2
at node 1
at node 4
at node 3
at node 8
at node 6
True

at node 5
at node 2
at node 1
at node 4
at node 3
at node 8
at node 6
at node 7
False
'''
```
每次检查栈顶的结点,如果是目标值则查找成功,否则当前结点出栈,并把它的子节点入栈(使用插入操作在列表头添加),知道栈为空则查找结束
稍加修改
可以修改这个函数的输出路径,即如果找到目标结点,则输出根节点到目标结点的深搜路径,使用一个额外的递归函数来做到这一点
```python
def DFSBinary(root, fcn):
    ...
    while len(queue) > 0:
        if fcn(queue[0]):
            return TracePath(queue[0])
    ...


def TracePath(node):
#使用递归来不断地回溯到当前结点的父节点并输出
    if not node.getParent():
        return [node]
    else:
        return [node] + TracePath(node.getParent())

print''
print 'DFS path'
pathTo6 = DFSBinaryPath(n5, find6)
print [e.getValue() for e in pathTo6]

'''
DFS path
[6, 8, 5]
'''
```
### **广度优先搜索**
依次检查每个子节点
使用**队列**来实现
**先进先出(FIFO)**
代码:
```python
def BFSBinary(root, fcn):
    queue = [root]
    while len(queue) > 0:
        print 'at node ' + str(queue[0].getValue())
        if fcn(queue[0]):
            return True
        else:
            temp = queue.pop(0)
            if temp.getLeftBranch():
                queue.append(temp.getLeftBranch())
            if temp.getRightBranch():
                queue.append(temp.getRightBranch())
    return False
def find6(node):
#为了简洁,使用该函数来判断结点的值是不是6
    return node.getValue() == 6
print 'BFS'
print BFSBinary(n5, find6)
'''
BFS
at node 5
at node 2
at node 8
at node 1
at node 4
at node 6
True
'''
```
使用列表来模拟队列,首先根节点入队,每次检查队首的结点,如果是要查找的值则查找成功,否则当前结点出队,并将它的子节点入队(即列表的添加操作,从尾部添加模拟入队过程),重复这个过程.直到队列为空,则查找结束.

### 顺序搜索
**有序树**
> 所有结点的左子节点比该节点小,右结点比该节点大
如果对有序树进行检索,在检查当前的结点时,如果当前结点比目标结点大,则只需要查找它的左子树(加入左结点),反之则只需要查找当前结点的右子树即可(加入右结点),这样就可以进一步缩小需要查找的范围.

##决策树
> * 一种特殊的二叉树
> * 每个结点对应一种选择,对应着它的左子树和右子树
> * 我们可以到达对应的满足一定要求的结点,从这个结点回溯到根节点的路径对应着问题的解决方案
###建立决策树
> * 建立一棵对应的树并检索它
> * 简单地隐式建立一棵树,仅在需要的时候创建分支,之后使用路径来确定解决方案
> * 以背包问题为例

### 背包问题
> * 现在有一些东西需要放到一个背包内,每个东西都有对应的重量和价值
> * 背包的容量是有限的
> * 现在需要使用有限容量的背包来装价值尽可能高的东西
### 使用决策树解决背包问题
> * 建立一棵对应的决策树
* 根节点代表是否装入第一件物品,是(左子树),否(右子树)
* 对于第n层结点,同样对应是否装入第n件物品
* 这样就可以使用决策树来代表所有可能的选择
**代码**
```python
def buildDTree(sofar, todo):
#sofar代表决定要装入背包的物品,todo代表待决定的物品
    if len(todo) == 0:
    #所有物品都已经作出决定
        return binaryTree(sofar)
    else:
        withelt = buildDTree(sofar + [todo[0]], todo[1:])
        withoutelt = buildDTree(sofar, todo[1:])
        here = binaryTree(sofar)
        #左子树代表装入当前物品
        here.setLeftBranch(withelt)
        #右子树代表不要当前物品
        here.setRightBranch(withoutelt)
        return here
```
使用递归的形式来建造决策树,每次创建一个代表当前状态的结点,左子树代表加入当前物品的决策树,右子树为不加入当前物品的决策树,返回当前的结点.直到返回的结点为根节点
建立的决策树包含了所有可能的情况
### 如何利用决策树查找最好的决策?
> * 使用**深度优先搜索**查找最优解
```python
def DFSDTree(root, valueFcn, constraintFcn):
    queue = [root]
    best = None
    visited = 0
    while len(queue) > 0:
        visited += 1
        if constraintFcn(queue[0].getValue()):
            if best == None:
                best = queue[0]
                print best.getValue()
            elif valueFcn(queue[0].getValue()) > valueFcn(best.getValue()):
                best = queue[0]
                print best.getValue()
            temp = queue.pop(0)
            if temp.getRightBranch():
                queue.insert(0, temp.getRightBranch())
            if temp.getLeftBranch():
                queue.insert(0, temp.getLeftBranch())
        else:
            queue.pop(0)
    print 'visited', visited
    return best

```
> * 使用**广度优先搜索**查找最优解
```python
def BFSDTree(root, valueFcn, constraintFcn):
    queue = [root]
    best = None
    visited = 0
    while len(queue) > 0:
        visited += 1
        if constraintFcn(queue[0].getValue()):
            if best == None:
                best = queue[0]
                print best.getValue()
            elif valueFcn(queue[0].getValue()) > valueFcn(best.getValue()):
                best = queue[0]
                print best.getValue()
            temp = queue.pop(0)
            if temp.getLeftBranch():
                queue.append(temp.getLeftBranch())
            if temp.getRightBranch():
                queue.append(temp.getRightBranch())
        else:
            queue.pop(0)
    print 'visited', visited
    return best
```
> * 测试
```python
a = [6, 3]
b = [7, 2]
c = [8, 4]
d = [9, 5]

treeTest = buildDTree([], [a, b, c, d])


def sumValues(lst):
    vals = [e[0] for e in lst]
    return sum(vals)


def sumWeights(lst):
    wts = [e[1] for e in lst]
    return sum(wts)


def WeightsBelow10(lst):
    return sumWeights(lst) <= 10


def WeightsBelow6(lst):
    return sumWeights(lst) <= 6

'''
-----------------------
'''
print ''
print 'DFS decision tree'
foobar = DFSDTree(treeTest, sumValues, WeightsBelow10)

'''
DFS decision tree
[]
[[6, 3]]
[[6, 3], [7, 2]]
[[6, 3], [7, 2], [8, 4]]
[[6, 3], [7, 2], [9, 5]]
visited 31
----------------------
'''

print ''
print 'BFS decision tree'
foobarnew = BFSDTree(treeTest, sumValues, WeightsBelow10)
'''
BFS decision tree
[]
[[6, 3]]
[[6, 3], [7, 2]]
[[6, 3], [7, 2], [8, 4]]
[[6, 3], [7, 2], [9, 5]]
visited 31
---------------------------
'''
```
深搜和广搜的原理是相似的,以深搜为例,形参为决策树,估值函数和限制函数,每次判断栈顶的元素,首先判断是否满足限制函数的条件.
如果不满足条件,则显然这种方案的子树也一定不满足限制函数的条件,直接出栈.
如果满足条件,比较已有的最佳方案和当前的估值,并根据结果更新最佳的方案,并将当前结点出栈,将其左右子树入栈.
一直进行查找直到栈为空,则查找结束.
广搜查找的原理类似,只是栈换成了队列,每次从队尾添加待查找元素.
## 隐式搜索
以上的搜索无论是深搜还是广搜都只能找出最佳的方案,如果只是找出一些方案,并控制查找的停止应该怎么办呢.可以使用隐式搜索来做到
###代码
```python
#深度优先搜索的改进
def DFSDTreeGoodEnough(root, valueFcn, constraintFcn, stopFcn):
    stack = [root]
    best = None
    visited = 0
    while len(stack) > 0:
        visited += 1
        if constraintFcn(stack[0].getValue()):
            if best == None:
                best = stack[0]
                print best.getValue()
            elif valueFcn(stack[0].getValue()) > valueFcn(best.getValue()):
                best = stack[0]
                print best.getValue()
            if stopFcn(best.getValue()):
                print 'visited', visited
                return best
            temp = stack.pop(0)
            if temp.getRightBranch():
                stack.insert(0, temp.getRightBranch())
            if temp.getLeftBranch():
                stack.insert(0, temp.getLeftBranch())
        else:
            stack.pop(0)
    print 'visited', visited
    return best

def atLeast15(lst):
    return sumValues(lst) >= 15


print ''
print 'DFS decision tree good enough'
foobar = DFSDTreeGoodEnough(treeTest, sumValues, WeightsBelow10,
                            atLeast15)
print foobar.getValue()

'''
DFS decision tree good enough
[]
[[6, 3]]
[[6, 3], [7, 2]]
[[6, 3], [7, 2], [8, 4]]
visited 4
[[6, 3], [7, 2], [8, 4]]
'''
```
```python
def BFSDTreeGoodEnough(root, valueFcn, constraintFcn, stopFcn):
    queue = [root]
    best = None
    visited = 0
    while len(queue) > 0:
        visited += 1
        if constraintFcn(queue[0].getValue()):
            if best == None:
                best = queue[0]
                print best.getValue()
            elif valueFcn(queue[0].getValue()) > valueFcn(best.getValue()):
                best = queue[0]
                print best.getValue()
            if stopFcn(best.getValue()):
                print 'visited', visited
                return best
            temp = queue.pop(0)
            if temp.getLeftBranch():
                queue.append(temp.getLeftBranch())
            if temp.getRightBranch():
                queue.append(temp.getRightBranch())
        else:
            queue.pop(0)
    print 'visited', visited
    return best

def atLeast15(lst):
    return sumValues(lst) >= 15

print ''
print 'BFS decision tree good enough'
foobarnew = BFSDTreeGoodEnough(treeTest, sumValues, WeightsBelow10,
                               atLeast15)
print foobarnew.getValue()

'''
BFS decision tree good enough
[]
[[6, 3]]
[[6, 3], [7, 2]]
[[6, 3], [7, 2], [8, 4]]
visited 8
[[6, 3], [7, 2], [8, 4]]

'''
```
与之前的查找方法类似,只是多了一个停止函数.当目前的最佳方案的价值已经超过停止函数的限制后,就立即停止查找.
从这里可以看出深度优先和广度优先搜索经过的结点个数不同,深搜基本要比广搜快一点,这由问题的具体规模来决定.
### 决策树的改进
到目前为止,决策树的构造其实是很不高效的,每次需要解决问题时,都要先构造出一棵完整的决策树,再进行查找.如果可以只构造需要的决策树结点,就能显著提高效率.
### 背包问题的隐式搜索
代码:
```python
def DTImplicit(toConsider, avail):
    if toConsider == [] or avail == 0:
        result = (0, ())
    elif toConsider[0][1] > avail:
        result = DTImplicit(toConsider[1:], avail)
    else:
        nextItem = toConsider[0]
        withVal, withToTake = DTImplicit(toConsider[1:], avail - nextItem[1])
        withVal += nextItem[0]
        withoutVal, withoutToTake = DTImplicit(toConsider[1:], avail)
        if withVal > withoutVal:
            result = (withVal, withToTake + (nextItem,))
        else:
            result = (withoutVal, withoutToTake)
    return result
```
一种递归形式的隐式搜索.toconsider表示还没有考虑的物品,avail表示剩余的可用空间.从前往后判断每一件物品,如果物品重量大于剩余容量,就直接考虑后面的物品,否则就使用递归比较装这件物品和不装这件物品的最高价值,根据最高价值的比较决定装不装.如果还没考虑的物品已经没了或者可用空间耗尽,则搜索结束.
只需要构造需要的决策树结点,提高了效率
测试
```python
stuff = [a, b, c, d]

val, taken = DTImplicit(stuff, 10)

print ''
print 'implicit decision search'
print 'value of stuff'
print val
print 'actual stuff'
print taken
'''
implicit decision search
value of stuff
22
actual stuff
([9, 5], [7, 2], [6, 3])
'''
```

## 树的扩展
树中允许有环的存在?
### **图**
> * 图的搜索问题
> * 带环二叉树的搜索
使用深搜或广搜可能会死循环,取决于树的结构和搜索的要求
### 对深搜的改进
```python
def DFSBinaryNoLoop(root, fcn):
    queue = [root]
    seen = []
    while len(queue) > 0:
        print 'at node ' + str(queue[0].getValue())
        if fcn(queue[0]):
            return True
        else:
            temp = queue.pop(0)
            seen.append(temp)
            if temp.getRightBranch():
                if not temp.getRightBranch() in seen:
                    queue.insert(0, temp.getRightBranch())
            if temp.getLeftBranch():
                if not temp.getLeftBranch() in seen:
                    queue.insert(0, temp.getLeftBranch())
    return False

```
在原有深搜的基础上,增加了一个seen列表在储存已经到达过的结点,在对结点的子树进行搜索前,首先检查子树中是够有已经到达过的结点,如果有就不再搜索该子树.这样就能避免重复搜索死循环的情况





  [1]: http://static.zybuluo.com/B0000D/4p5j7fdaxlfgwvhe1byxgiu0/13.3.1.jpg