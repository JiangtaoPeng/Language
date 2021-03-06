# Python
[https://www.cnblogs.com/wangyongsong/p/7384224.html](https://www.cnblogs.com/wangyongsong/p/7384224.html)
装饰器
迭代器
面向对象
静态方法，类方法，实例方法
多线程与多进程

## Python新式类和旧式类
### 新式类和旧式类
1. Python2默认是旧式类，只有继承object时候才是新式类
Python3只有新式类
2. Python2 type(classobj)=instance python3则不是
3. 新式类搜索属性是广度优先，旧式类搜索属性是深度优先
### type()和isinstance()
- type()不会认为子类是一种父类类型
- isinstance()认为子类是一种父类类型
- Examples:
```
class A():
    pass
class B(A):
    pass
a = A()
b = B()
print(type(a))
print(type(b))
print(isinstance(a, A))
print(isinstance(b, B))
print(isinstance(b, A))
```
```
<class '__main__.A'>
<class '__main__.B'>
True
True
True
```

## 二叉树搜索
广度优先搜索，前序遍历（深度优先搜索），中序遍历，后序遍历
可以用递归的思想很容易实现
或者利用stack/queue的数据结构实现

## Python命名空间和作用域
- 命名空间 
	- 名字到对象的映射
	- 字典实现
	- 全局/局部(函数/类)/内建
	- 内建: abs(), max()...
- 命名空间的生命周期
	- 内置ns在解释器启动的时候创建，解释器退出的时候销毁
	- 全局ns在解释器读入模块的时候创建，解释器退出时销毁
	- 函数的局部命名空间，在函数调用时创建，函数返回或者由未捕获的异常时销毁；类定义的命名空间，在解释器读到类定义创建，类定义结束后销毁
- 作用域 
	- 作用域就是一段文本区域，这段区域中可以通过名字**直接访问**命名空间中的数据，
	- 作用域只是文本区域，其定义是静态的；而名字空间却是动态的，只有随着解释器的执行，命名空间才会产生。那么，在静态的作用域中访问动态命名空间中的名字，造成了作用域使用的动态性。静态的作用域，是一个或多个命名空间按照一定规则叠加影响代码区域；运行时动态的作用域，是按照特定层次组合起来的命名空间。
	- 内建/全局/闭包/局部, 先搜索Local->Enclosing->Global->Built-in
	- `nonlocal`语句使得在内层函数中重绑定外层函数作用域中的名字成为可能，即使同名的名字存在于全局作用域。
- 赋值(左值)操作只发生在当前内层作用域
- **命名空间**就**字典**
- **globals()**，**locals()**这两个方法是关于返回作用域的函数，前者返回全局作用域，后者返回当前的本地作用域
- 查看对象的命名空间，可以用**\_\_dict\_\_属性**
- Example:
```def scope_test():
    def do_local():
        spam = "local spam"
    def do_nonlocal():
        nonlocal spam
        spam = "nonlocal spam"
    def do_global():
        global spam
        spam = "global spam"
    spam = "test spam"
    do_local()
    print("After local assignment:", spam)
    do_nonlocal()
    print("After nonlocal assignment:", spam)
    do_global()
    print("After global assignment:", spam)
  scope_test()
  print("In global scope:", spam)
```
```
After local assignment: test spam
After nonlocal assignment: nonlocal spam
After global assignment: nonlocal spam
In global scope: global spam
```

## python闭包
本身是一个函数，由函数和闭包作用域中的自由变量组成
这个函数一般就是函数中的函数，外函数中的局部变量被内函数使用后，外函数再返回内函数，该局部变量再脱离外函数之后，还可以在函数之外被访问到
目的是避免使用全局变量
闭包函数的\_\_closure\_\_属性返回cell元组对象，cell对象中的cell_contents就是闭包作用域中的自由变量

```
def createCount():
	a = 0
	def counter():
		nonlocal a
		a=a+1
		return a
	return counter
```

```
def outer():
	funs = []
	for i in range(3):
		def inner():
			return i*i
		funs.append(inner)
	return funs
f1, f2, f3 = outer()
```

## python Enum
```
def enumerate(seq, start=0):
	n = start
	for i in seq:
		yield n, i
	n += 1
```
```
from enum import Enum, unique, auto
class Fruits(Enum):
    APPLE = auto()
    PEAR = auto()
    WATERMELON = auto()
    BANANA = auto()
print(Fruits.APPLE) # Fruits.APPLE
print(Fruits.APPLE.name, Fruits.APPLE.value) # APPLE 1
print(repr(Fruits.APPLE)) # <Fruits.APPLE: 1>

months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "July"]
for i, e in enumerate(months):
    print(i, ":", e)
"""
0 : Jan
1 : Feb
2 : Mar
3 : Apr
4 : May
5 : Jun
6 : July
"""
```
## python == 和is
- hashable
- \_\_hash\_\_
- \_\_eq\_\_
- id()

- is -> 比较id(object) object identity，比较是否拥有同一块内存空间
- \=\= -> 调用\_\_eq\_\_()方法

- \=\=操作符可以重载，is不可以

- string interning字符串驻留机制，当字符串比较小的时候，会保留其值的一个副本，当创建新的一样的字符串的时候直接指向该副本

## python模块的内置方法
\_\_name\_\_ 模块名字
\_\_doc\_\_  模块注释内容
\_\_file\_\_ 模块文件路径
\_\_package\_\_ 为了相对引用而设置的一个属性, 如果所在的文件是一个`package`的话, 它和`__name__`的值是一样的, 如果是子模块的话, 它的值就跟父模块一致
\_\_all\_\_ 指明有哪些模块会被import

## python class
### new和init方法
- \_\_new\_\_ 和 \_\_init\_\_，先执行new后执行init，new第一参数是类，init第一参数是实例，可以不执行init但一定会执行new
### 静态方法和类方法
- classmethod(cls, arg1, arg2, ...)
- staticmethod(arg1, arg2,...)
- 普通method(self, arg1, arg2, ...)
- 

## python else
```
a = 100
for i in range(2, a):
	for j in range(2, i):
		if i%j == 0:
			break
	else:
		print("number {} is a prime number".format(i))
```
```
try:
except:
else:
finally:
避免在finally中使用break/return
```
## python classmethod & staticmethod
类方法classmethod一般是子类共有的属性
静态方法staticmethod


## python建议
- assert -> if \_\_debug\_\_
- 不推荐使用type
- 避免使用浮点数比较
- 可以在正常逻辑不可达达地方用assert语句来发现问题
- 充分利用lazy evaluation: &&, ||, 闭包
- 学会使用\_\_init\_\_.py
	- import xx from xx
	- \_\_all\_\_ = [xxx, sxxx, xsdds]
	- 尽量使用import a, 而不是 from a import xxx (sys.modules命名空间)

## Python数据类型整理
### 标准数据类型
- Numbers
	- 不可变数据类型
	- int/long/float/complex
	- 长整数
	- 小整型对象池：-5 ～ 257
	- 没有Boolean型， True/False为关键值，实际用数字0/1表示
- String
	- 不可变
	- 可迭代可拆包
		```
		a,b,c,d,e="Hello"
		```
- List
	- 可变
	- 可迭代可拆包
- Tuple
	- 不可变
	- 可迭代可拆包
- Set
	- 可变
	- 可迭代可拆包
- Dictionary
	- 可变
	- 关键字不可变
	- 可迭代可拆包
- del只是删除对象的引用，引用计数-1
### collections容器数据类型
-   namedtuple(): 生成可以使用名字来访问元素内容的tuple子类
-   deque: 双端队列，可以快速的从另外一侧追加和推出对象
	- List的左端取值操作时间复杂度是O(n)，deque是O(1)
	- pop/popleft
	- append/appendleft
	- extend/extendleft
	- clear
	- copy
	- count(x)
	- remove/reverse/rotate
	- 限长deque提供了类似Unix `tail` 过滤功能
	- 维护一个近期添加元素的序列，通过从右边添加和从左边弹出
	- 一个 [轮询调度器](https://en.wikipedia.org/wiki/Round-robin_scheduling) 可以通过在 [`deque`](https://docs.python.org/zh-cn/3/library/collections.html#collections.deque "collections.deque") 中放入迭代器来实现。值从当前迭代器的位置0被取出并暂存(yield)。 如果这个迭代器消耗完毕，就用 [`popleft()`](https://docs.python.org/zh-cn/3/library/collections.html#collections.deque.popleft "collections.deque.popleft") 将其从对列中移去；否则，就通过 [`rotate()`](https://docs.python.org/zh-cn/3/library/collections.html#collections.deque.rotate "collections.deque.rotate") 将它移到队列的末尾
 ```
"""
下面这个是一个有趣的例子，主要使用了deque的rotate方法来实现了一个无限循环
的加载动画
"""
import sys
import time
from collections import deque

fancy_loading = deque('>--------------------')

while True:
    print('\r%s' % ''.join(fancy_loading), end=" ")
    fancy_loading.rotate(1)
    sys.stdout.flush()
    time.sleep(0.08)
 ```
-   Counter: 计数器，主要用来计数，比如统计字符串中各个字母出现的次数
 ```
from collections import Counter
s = "a long string"
c = Counter(s)
print(c.most_common(5))
```
-   OrderedDict: 有序字典
-   defaultdict: 带有默认值的字典
	- 在使用Python原生的数据结构dict的时候，如果用d[key]这样的方式访问， 当指定的key不存在时，是会抛出KeyError异常的。但是，如果使用defaultdict，只要你传入一个默认的工厂方法，那么请求一个不存在的key时， 便会调用这个工厂方法使用其结果来作为这个key的默认值
	- 原生dict
```
if key not in example: 
	ex[key] = []
ex[key].append(val)
	```
	- defaultdict直接操作
	```
	ex[key].append(val)
```
### heapq
- 找出最大/最小的n个数
```
import heapq
portfolio = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]
cheap = heapq.nsmallest(3, portfolio, key=lambda s: s['price'])
expensive = heapq.nlargest(3, portfolio, key=lambda s: s['price'])

print(cheap)
# [{'name': 'YHOO', 'shares': 45, 'price': 16.35}, {'name': 'FB', 'shares': 200, 'price': 21.09}, {'name': 'HPQ', 'shares': 35, 'price': 31.75}]
print(expensive)
# [{'name': 'AAPL', 'shares': 50, 'price': 543.22}, {'name': 'ACME', 'shares': 75, 'price': 115.65}, {'name': 'IBM', 'shares': 100, 'price': 91.1}]
```
- 对数据堆排序
```
import heapq
nums = [1, 8, 2, 23, 7, -4, 18, 23, 42, 37, 2]
heapq.heapify(nums)
print(nums) # [-4, 2, 1, 23, 7, 2, 18, 23, 42, 37, 8]
```
- heapq实现优先队列
```
import heapq

class PriorityQueue:
    def __init__(self):
        self._queue = []
        self._index = 0

    def push(self, item, priority):
        heapq.heappush(self._queue, (-priority, self._index, item))
        self._index += 1

    def pop(self):
        return heapq.heappop(self._queue)[-1]

class Item():
    def __init__(self, name):
        self.name = name
    def __repr__(self):
        return 'Item({!r})'.format(self.name)
    
q = PriorityQueue()
q.push(Item('foo'), 1)
q.push(Item('bar'), 5)
q.push(Item('cook'), 2)
q.push(Item('shell'), 2)
print(q.pop()) # Item('bar')
print(q.pop()) # Item('cook')
print(q.pop()) # Item('shell')
print(q.pop()) # Item('foo')
```

### 比较大小
#### 原生比较
```python
class Item():
    def __init__(self, name):
        self.name = name
    def __repr__(self):
        return 'Item({!r})'.format(self.name)
aItem = Item("A")
bItem = Item("B")
cItem = Item("C")

print(aItem<bItem)  # TypeError: '<' not supported between instances of 'Item' and 'Item'

aPriority = (1, aItem)
bPriority = (5, bItem)
cPriority = (2, cItem)
dPriority = (2, Item("D"))

print(aPriority < bPriority) # True
print(cPriority < dPriority) # TypeError: '<' not supported between instances of 'Item' and 'Item'

aPriority = (aItem, 1)
bPriority = (bItem, 5)
cPriority = (cItem, 2)
dPriority = (Item("D"), 2)

print(aPriority < bPriority) # TypeError: '<' not supported between instances of 'Item' and 'Item'


aIndexedPriority = (1, 0, aItem)
bIndexedPriority = (5, 1, bItem)
cIndexedPriority = (2, 2, cItem)
dIndexedPriority = (2, 3, Item("D"))

print(aIndexedPriority < bIndexedPriority)  # True
print(cIndexedPriority < cIndexedPriority)  # False
```
#### 字典关键字排序
```python
rows = [
    {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003},
    {'fname': 'David', 'lname': 'Beazley', 'uid': 1002},
    {'fname': 'John', 'lname': 'Cleese', 'uid': 1001},
    {'fname': 'Big', 'lname': 'Jones', 'uid': 1004}
]
sorted(rows, key=lambda x: x["fname"])
from operator import itemgetter
sorted(rows, key=itemgetter("fname"))
sorted(rows, key=itemgetter("fname", "lname"))
```
#### 不支持原生比较的对象
```python
class User:
    def __init__(self, user_id):
        self.user_id = user_id

    def __repr__(self):
        return 'User({})'.format(self.user_id)

def sort_notcompare():
    users = [User(23), User(3), User(99)]
    print(users)  #[User(23), User(3), User(99)]
    print(sorted(users, key=lambda u: u.user_id))
    # [User(3), User(23), User(99)]
    from operator import attrgetter
    print(sorted(users, key=attrgetter("user_id")))
    # [User(3), User(23), User(99)]
```
### groupby
```python
rows = [
    {'address': '5412 N CLARK', 'date': '07/01/2012'},
    {'address': '5148 N CLARK', 'date': '07/04/2012'},
    {'address': '5800 E 58TH', 'date': '07/02/2012'},
    {'address': '2122 N CLARK', 'date': '07/03/2012'},
    {'address': '5645 N RAVENSWOOD', 'date': '07/02/2012'},
    {'address': '1060 W ADDISON', 'date': '07/02/2012'},
    {'address': '4801 N BROADWAY', 'date': '07/01/2012'},
    {'address': '1039 W GRANVILLE', 'date': '07/04/2012'},
]
from operator import itemgetter
from itertools import groupby

# Sort by the desired field first
rows.sort(key=itemgetter('date'))
# Iterate in groups
for date, items in groupby(rows, key=itemgetter('date')):
    print(date)
    for i in items:
        print(' ', i)
### output
07/01/2012
  {'address': '5412 N CLARK', 'date': '07/01/2012'}
  {'address': '4801 N BROADWAY', 'date': '07/01/2012'}
07/02/2012
  {'address': '5800 E 58TH', 'date': '07/02/2012'}
  {'address': '5645 N RAVENSWOOD', 'date': '07/02/2012'}
  {'address': '1060 W ADDISON', 'date': '07/02/2012'}
07/03/2012
  {'address': '2122 N CLARK', 'date': '07/03/2012'}
07/04/2012
  {'address': '5148 N CLARK', 'date': '07/04/2012'}
  {'address': '1039 W GRANVILLE', 'date': '07/04/2012'}
```


## 迭代器生成器
- python2 range(100)返回list， python3返回迭代器，节省内存
## iterable和iterator
- 面向接口编程
- Iterable继承自object, Iterator继承自Iterable
- for循环中对于iterable对象有一个转化为iterator的操作
```
iterator_list = iter(input_list)
while True:
	try: 
		x = next(iterator_list)
	except StopIteration:
		break
```
```
from collections import Iterable,Iterator
list = [1,2,3,4]
list_iterator = iter(list)
print(isinstance(list,Iterable),isinstance(list,Iterator))#True False
print(isinstance(list_iterator,Iterable),isinstance(list_iterator,Iterator))#True True
```
- 可迭代对象和迭代器实例
```python
#!/usr/bin/env python
# coding=utf-8

class ExIterable():
    def __init__(self, num):
        self.data = num
        
    def __iter__(self):
        return ExIterator(self.data)
    
class ExIterator():
    def __init__(self, num):
        self.data = num
        self.new = 0
        
    def __iter__(self):
        return self
    def __next__(self):
        if self.new < self.data:
            self.new += 1
            return self.new-1
        raise StopIteration

ex_iterable = ExIterable(5)
ex_iterator = ExIterator(5)

from collections import Iterable, Iterator
print(isinstance(ex_iterable, Iterable), isinstance(ex_iterable, Iterator))   # True False
print(isinstance(ex_iterator, Iterable), isinstance(ex_iterator, Iterator))   # True True

for i in ex_iterable:
    print(i)   # 0/1/2/3/4
    
for i in ex_iterator:
    print(i)   # 0/1/2/3/4

for i in ex_iterable:
    print(i)   # 0/1/2/3/4
    
for i in ex_iterator:
    print(i)   # nothing

a,b,c,d,e = ex_iterable
print(a,b,c,d,e)  # 0 1 2 3 4
```
### iterable
- iterable可迭代对象：dict, str, list, tuple
- iterable含	\_\_iter\_\_()方法, 且返回的是一个对应的迭代器
- 可迭代对象都可拆包
### iterator
- iterator是一个迭代器对象
- 含\_\_iter\_\_和\_\_next\_\_方法，\_\_next\_\_是惰性的不会回退，\_\_iter\_\_返回迭代器对象本身
- \_\_next\_\_没有值可以访问的时候，抛出stopinteration异常
- 

## python的多进程多线程操作
### GIL全局解释器锁
- GIL 是python的全局解释器锁，同一进程中假如有多个线程运行，一个线程在运行python程序的时候会霸占python解释器（加了一把锁即GIL），使该进程内的其他线程无法运行，等该线程运行完后其他线程才能运行。如果线程运行过程中遇到耗时操作，则解释器锁解开，使其他线程运行。所以在多线程中，线程的运行仍是有先后顺序的，并不是同时进行。

- 多进程中因为每个进程都能被系统分配资源，相当于每个进程有了一个python解释器，所以多进程可以实现多个进程的同时运行，缺点是进程系统资源开销大

## 装饰器/面向切面编程


## 闭包

## Class
### \_\_new\_\_()和\_\_init\_\_()之间的区别
- \_\_init\_\_方法做的事情是在对象创建好之后初始化变量
- 真正创建实例的是\_\_new\_\_方法
- \_\_new\_\_方法是静态方法，而\_\_init\_\_是实例方法
- \_\_new\_\_ 方法创建实例对象供\_\_init\_\_ 方法使用，\_\_init\_\_方法定制实例对象; \_\_new\_\_ 方法必须返回值，\_\_init\_\_方法不需要返回值, 如果返回非None值就报错
- 如果__new__创建的是当前类的实例，会自动调用__init__函数，通过return语句里面调用的__new__函数的第一个参数是cls来保证是当前类实例，如果是其他类的类名，；那么实际创建返回的就是其他类的实例，其实就不会调用当前类的__init__函数，也不会调用其他类的__init__函数
- 什么时候使用new方法
	- 继承不可变数据类型时需要用到\_\_new\_\_方法
	- 用在元类，定制创建类对象
	- 
### self和cls
- self表示实例本身，cls表示类本身
- new方法返回self实例对象传给init方法
### 元类metaclass

## 静态方法/实例方法/类方法
- @staticmethod不需要表示自身对象的self和自身类的cls参数，就跟使用函数一样。  
- @classmethod也不需要self参数，但第一个参数需要是表示自身类的cls参数。  
- 如果在@staticmethod中要调用到这个类的**一些**属性方法，只能直接类名.属性名或类名.方法名。  
- @classmethod因为持有cls参数，可以来调用类的属性，类的方法，**实例化对象**等，避免硬编码。

## python中考虑设计模式
### singleton
```
class Singleton(object):
    _instance = None
    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = object.__new__(cls, *args, **kwargs)

        return cls._instance

s1 = Singleton()
s2 = Singleton()
print(s1)
print(s2) 
```

### factory
```
class Fruit(object):
    def __init__(self):
        pass

    def print_color(self):
        pass

class Apple(Fruit):
    def __init__(self):
        pass

    def print_color(self):
        print("apple is in red")

class Orange(Fruit):
    def __init__(self):
        pass

    def print_color(self):
        print("orange is in orange")

class FruitFactory(object):
    fruits = {"apple": Apple, "orange": Orange}

    def __new__(cls, name):
        if name in cls.fruits.keys():
            return cls.fruits[name]()
        else:
            return Fruit()

fruit1 = FruitFactory("apple")
fruit2 = FruitFactory("orange")
fruit1.print_color()    
fruit2.print_color()    
```

## 杂
### \_\_repr\_\_()


## Python自动化工具
tox
nox
invoke
[https://zhuanlan.zhihu.com/p/105263640](https://zhuanlan.zhihu.com/p/105263640)

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwMTYwMjg0NTcsODMwNjc4MTQsLTE3MT
UyNzM5MTBdfQ==
-->