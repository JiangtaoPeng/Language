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
	- next(table)

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
- coroutine.resume() 保护模式运行，不会抛出错误，返回false+错误信息，同时协程的状态变成dead
- coroutine.yield()
- coroutine.status() - running/suspended/normal/dead
	**input**
	```lua
	local co
	local co2 = coroutine.create(function() print("3."..coroutine.status(co)) end)
	co = coroutine.create(
		function ()
			print(type(coroutine.status(co)))
			print(type(coroutine.running()))
			print("2."..coroutine.status(co))
			print(coroutine.running())
			coroutine.resume(co2)
			coroutine.yield()
		end)
	print("1."..coroutine.status(co))
	coroutine.resume(co)
	print("4."..coroutine.status(co))
	coroutine.resume(co)
	print("5."..coroutine.status(co))
	```
	**output**
	```lua
	1.suspended
	string
	thread
	2.running
	thread: 0x986dd0
	3.normal
	4.suspended
	5.dead
	```
- coroutine.wrap(f)
	```lua
	local g1 = coroutine.create(f)
	coroutine.resume(g1, "input_string")
	local g2 = coroutine.wrap(f)
	g2("input_string")
	-- 两者功能一致，但resume是保护模式，wrap则不是，当发现错误，直接抛出错误
	-- resume会返回布尔值，wrap则无需bool值，因为直接抛出错误
	```
### 生产者和消费者
```lua
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
	-- 先生产，再消费
	local status, value = coroutine.resume(newProducer)
	return value
end

newProducer = coroutine.create(producer)
consumer()
```
## 文件I/O
### 简单模式
```lua
file = io.open("file_name", "r")
io.input(file)
print(io.read())
io.close(file)
file = io.open("file_name", "a")
io.output(file)
io.write("test line")
io.close(file)
```

### 完全模式
```lua
file = open("file_name", "r")
print(file:read())
file:close()
file = open("file_name", "a")
file:write("test line")
file:close()
```

## 错误处理
### assert
```lua
function add(a, b)
	assert(type(a)=="number", "a is not a number")
	assert(type(b)=="number", "b is not a number")
	return a+b
end
add(10)
```
### error
终止正在进行的程序，并返回message的内容作为错误信息
```lua
error(message[, level])
```
### pcall, xpcall, debug
- pcall - protected call 以保护模式调用第一个参数，pcall返回时已经销毁了调用栈的部分内容
- xpcall - xpcall第二个参数接受错误处理函数 ，当错误发生时，lua会在调用栈展开前调用错误处理函数
- debug
	- debug.debug()
	- debug.traceback()

## LuaJIT性能优化实践技巧
1. 减少不可预测的分之代码

2. 使用FFI实现数据结构

3. 用FFI调用C函数

4. 循环时使用 for i=start, stop, step do ... end 或者ipairs，避免使用pairs，因为在luajit2.1.0beta2版本下pairs无法生成机器码运行

5. 尽量只调用local function，常用的其他模块函数也应该缓存(cache)成local
	```lua
	local ms = math.sin
	function test()
		math.sin(1)
		ms.sin(1)
		end
	```
	math本身是一个表，math.sin本身会做一次表查找，这里会做一次消耗，而math又是一个全局变量，还要在全局表中做一次math的查找。而ms缓存过后，就省略了math.sin的查找 ，另外function上一层的变量，lua会有一个upvalue对象进行存储，在函数中找ms变量只需要在upvalue对象内找，查找范围更小更快捷

6. 避免使用自己实现的分发调用机制，尽量使用内建如metatable这样的机制
	```lua
	-- usual method
	if opcode == OP_1 then ...
	elseif opcode == OP_2 then ...
	...

	-- good practice
	local callbacks = {}
	callbacks[OP_1] = function() ... end
	callbacks[OP_2] = function() ...end
	...
	```
	表查找和metatable的查找都可以参与jit优化的，而自行实现的消息分发机制，往往会用到分支代码或其他更复杂的结构，性能上反而不如表查找+jit优化来得快

7. luajit为了提升运行效率，local变量会尽可能的使用cpu寄存器存储。而当local变量过多时，jit会放弃编译。适当控制函数作用域内的local变量时必要的。减少存活着的临时变量也是必要的，比如很多元方法会生成新的变量，大量的元方法的调用会产生大量的临时变量，先不考虑gc的压力，就是变量分配寄存器就容易出现问题

8. 对于引用别名的表达子式，编译器会放弃优化
	```lua
	x[i] = a[i] + b[i]
	y[i] = a[i] + c[i]
	-- luajit不会优化成：
	d = a[i]
	x[i] = d + b[i]
	y[i] = d + c[i]
	-- 因为引用有可能是对同一个东西的引用，比如x和a其实是对同一个东西的引用
	```
9. 减少使用高消耗或不支持jit的操作
	pairs -> ipairs
	print -> io.write
	table.insert尾部插入 

## Metatable
- 在Lua中任何一个值都有Metatable, 不同的值可以有不同的Metatable也可以共享同样的Metatable, 但在Lua本身提供的功能中, 不允许你改变除了table类型值外的任何其他类型值的Metatable, 除非使用C扩展或其他库. setmetatable和getmetatable是唯一一组操作table类型的Metatable的方法.
- lua table可以通过访问对应key来得到value的值，但无法对两个table进行操作。metatable则允许改变table的行为，每个行为关联了对应的元方法
- setmetatable(table, metatable)：对指定table设置元表，如果元表中存在_metatable键值，则set失败
	```lua
	table = {}
	metatable = {}
	mytable = setmetatable(table, metatable)
	-- mytable = setmetatable({}, {})
	```
- getmetatable(table)：返回table对象的元表
### __index
__index方法用来访问表
```lua
idxs = {foo = 3}
t = setmetatable({}, {__index = idxs})
print(t.foo)
-- 3
print(t.bar)
-- nil
```
Lua 查找一个表元素时的规则，其实就是如下 3 个步骤:

-   1.在表中查找，如果找到，返回该元素，找不到则继续
-   2.判断该表是否有元表，如果没有元表，返回 nil，有元表则继续。
-   3.判断元表有没有 __index 方法，如果 __index 方法为 nil，则返回 nil；如果 __index 方法是一个表，则重复 1、2、3；如果 __index 方法是一个函数，则返回该函数的返回值。

### __newindex
- __newindex方法用来更新表
- 在对表的缺少的索引赋值时，解释器就会查找__newindex方法，调用这个方法，而不是进行赋值操作

### __call
- 元表中定义了__call方法后，对应的就是mytable(params)


## OOP
[https://github.com/dingshukai/lua-oop](https://github.com/dingshukai/lua-oop)

## 杂
### upvalue
- L为当前线程状态lua_State
- g为全局共享状态global_State
- lua编译一个函数的时候，其中包含了函数体对应的虚拟机指令/函数用到的常量值和一些调试信息。在运行时，每当lua执行function...end这样的函数时，它就会创建一个新的数据对象，其中包含了 相应函数原型的引用/环境（用来查找全局变量的表）的引用以及一个由所有upvalue引用组成的数组，而这个数据对象就称为闭包。由此可见，函数是编译期的概念是静态的，而闭包是运行期的概念，是动态的
### 迭代器

### .和:操作
- 冒号操作会带入一个 **self**参数，用来代表**自己**。而点号操作，只是**内容**的展开。冒号方法会默认接受一个self参数，使用点号需要显式的传入self

### 调用函数前先定义函数
lua中函数的本质是变量赋值

### 函数式编程

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTg0NzE0NDgyOSwtNjM5ODM2NDI3LDE1NT
Y4MzQ5NzUsMjU0OTcyODE4LDEwNDU3NDA1OTEsLTE3ODAxNzg2
MTgsLTE2NDA4MzUzMSwtOTYxMjgyMzAzLC01OTY3NDU4MjQsLT
IyODM2OTg4Niw3NjU0OTYxOTYsMzE4OTU0NTIwLC0yMDMwNTAy
NDcwLDgyNDI1MzY0NCwtMjQxMTYxNzQwLDEzNzU4NjU3NDAsLT
EyNDQ5ODg3NTgsLTIxMDMzNDUzOTQsLTEwMzA1Njc2NzgsMTA3
MDg5OTE4Ml19
-->