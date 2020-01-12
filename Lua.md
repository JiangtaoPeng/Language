# Lua
- Lua语言的定位是一种胶水式的动态语言/脚本语言，用来控制其他语言编写的其他组件
- 脚本语言可嵌入，清亮
- 用table表示所有东西
## 优势
轻量级
可扩展性

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

## Coroutine
- 线程与协同程序的主要区别在于，一个具有多个线程的程序可以同时运行几个线程，而协同程序却需要彼此协作的运行。

- 在任一指定时刻只有一个协同程序在运行，并且这个正在运行的协同程序只有在明确的被要求挂起的时候才会被挂起。

- 协同程序有点类似同步的多线程，在等待同一个线程锁的几个线程有点类似协同。

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
eyJoaXN0b3J5IjpbNDU3NzMyMzY5LDk4NTI4ODE1OV19
-->