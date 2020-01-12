# Lua
## 优势
- Lua语言的定位是一种胶水式的动态语言/脚本语言，用来控制其他语言编写的其他组件
- 脚本语言可嵌入，轻量，快速，开发周期短
- 用table表示所有东西
- lua解释器的核心部分是独立的应用程序(freestanding application)，容易限制程序对外部资源的访问
- lua还提供了用户自定义调试钩子去监视lua程序的运行状态
- 垃圾回收 - 标记清除
- lua使用double作为唯一的数字类型，用long作为备用数字类型
- 函数作为第一等(first-class)公民，实现闭包，函数式编程
- 使用协程做并发处理
元编程
coroutine
oop
垃圾回收

## 数据类型
- 默认是全局变量
### 类型
- nil
	- 判断无效类型：type(b) == "nil"
	- 删除对象: b = nil
- boolean
- number
- string
- function
- userdata
	- 自定义类型（C/C++中的struct/指针）
- thread
	- 在 Lua 里，最主要的线程是协同程序（coroutine）。它跟线程（thread）差不多，拥有自己独立的栈、局部变量和指令指针，可以跟其他协同程序共享全局变量和其他大部分东西。
	- 线程跟协程的区别：线程可以同时多个运行，而协程任意时刻只能运行一个，并且处于运行状态的协程只有被挂起（suspend）时才会暂停。
- table
	- "关联数组"（associative arrays）
	- Lua 里表的默认初始索引一般以 1 开始

### 作用域
- 全局变量
- 局部变量
	- 尽可能使用局部变量，避免命名冲突
	- 访问局部变量比全局变量快
- table域
	- t[i]
	- t.i 		--  当索引为字符串类型时的一种简化写法 
	- gettable_event(t,i)  --  采用索引访问本质上是一个类似这样的函数调用

### 参数
- 可变参数
	- 在函数参数列表中使用三点 ... 表示函数有可变的参数
	- {...}  表示一个由所有变长参数构成的数组
	- 可以通过 select("#",...) 来获取可变参数的数量
	- select(n, …) 用于访问 **n** 到 **select('#',…)** 的参数
	- 固定参数必须放在变长参数之前

### 迭代器
- for的执行条件：迭代函数，状态常量，控制变量
- 有状态/无状态迭代器

### Metatable
- setmetatable()

## Closure闭包
- 函数外包，内嵌具有传递性
- 词法定界lexical scoping：内嵌函数f2可以访问外包函数f1已经创建的所有局部变量
- 外部局部变量external local  variable/upvalue
- 闭包：lua在编译一个函数时，会为它生成一个原型(prototype)，其中包含了函数体对应的虚拟机指令/函数用到的常数值和一些调试信息。在运行时，每当lua执行一个形如function...end的 表达式时，它就会创建一个新的数据对象，其中包含了相应函数原型的引用/环境（用来查找全局变量的表）的引用以及一个由所有upvalue引用组成的数组，而这个数据对象就是闭包
- 函数是编译时的概念是静态的，闭包是运行期的概念，是动态的
- 在外包函数还存在时，upvalue都存在在外包函数的堆栈上，闭包引用指向这个堆栈的函数，但当外包函数消失时，闭包函数会为它分配新的内存空间
- 同一个外包函数里，同一层级的内嵌函数的upvalue是共享内存的
- 闭包常用的应用就是计数器
## Coroutine
### 概念
- 线程与协同程序的主要区别在于，一个具有多个线程的程序可以同时运行几个线程，而协同程序却需要彼此协作的运行
- 在任一指定时刻只有一个协同程序在运行，并且这个正在运行的协同程序只有在明确的被要求挂起的时候才会被挂起
- 协同程序有点类似同步的多线程，在等待同一个线程锁的几个线程有点类似协同
- 多线程技术主要是基于抢占式内存共享，而协程则通过去掉抢占式或者不共享内存来回避a=a+1都没有确定结果的问题
### 接口
- coroutine.create()
- coroutine.resume()
- coroutine.yield()
- coroutine.status() - 
### 生产者和消费者
```
local newProducer

function producer()
	local i = 0
	while  true  do
		i = i+1
		send(i)
	end
end

function consumer()
	while  true  do
		local i = receive()
		print(i)
	end
end

function send(x)
	coroutine.yield(x)
end

function receive()
	local status, value = coroutine.resume(newProducer)
	return value
end

newProducer = coroutine.create(producer)
consumer()
```

## OOP
[https://github.com/dingshukai/lua-oop](https://github.com/dingshukai/lua-oop)
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTkyODk2NzY2LC0xNTkxMzQ4ODcxLDYwND
EzOTAyMiwtMTMwMjY5Mjc3MCwtMTM1MTMwNzM4NywtMTUwMjQ4
NTM3Niw5ODUyODgxNTldfQ==
-->