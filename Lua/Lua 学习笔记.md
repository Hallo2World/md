# Lua 学习笔记

### 注释

- 单行注释

  ```lua
  --单行注释
  ```

- 多行注释

  ```lua
  --[[
  多行注释
  多行注释
  --]]
  ```

### 数据类型

| 数据类型 | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| nil      | 表示一个无效值（在条件表达式中相当于false）                  |
| boolean  | false/true，Lua 把 false 和 nil 看作是 false，其他的都为 true，数字 0 也是 true |
| number   | 双精度浮点数                                                 |
| string   | 字符串                                                       |
| function | 函数                                                         |
| userdata | 表示任意存储在变量中的C数据结构                              |
| thread   | 表示执行的独立线路，用于执行协同程序                         |
| table    | Lua 中的表（table）其实是一个"关联数组"（associative arrays），数组的索引可以是数字、字符串或表类型。在 Lua 里，table 的创建是通过"构造表达式"来完成，最简单构造表达式是{}，用来创建一个空表。 |

- nil 

  表示一个无效值（在条件表达式中相当于false）

  ```lua
  >print(type(x))		--一个没有赋值的变量
  nil
  >
  ```

  给全局全局变量赋值nil，相当于让系统回收变量(删除变量)；给table表中的变量赋nil等同于把他们从table中删掉。

- boolean

  false/true，Lua 把 false 和 nil 看作是 false，其他的都为 true，数字 0 也是 true

  ```lua
  print(type(true))	
  print(type(false))				
  print(type(nil))				
  
  if false or nil then
      print("至少一个是true")		
  else
      print("false 和 nil 都为 false")
  end
  
  if 0 then
      print("数据字 0 是 true")
  else
      print("数据字 0 是 false")
  end
  
  --执行结果如下
  boolean
  boolean
  nil
  false 和 nil 都为 false
  数据字 0 是 true
  ```

- number

  双精度浮点数

  ```lua
  >print(type(2))
  number
  >print(type(2.2))
  number
  >print(type(0.2))
  number
  >print(type(2e+1))
  number
  >print(type(0.2e-1))
  number
  ```

- string

  字符串由一对双引号或单引号来表示

  ```lua
  string1 = "this is string1"
  string2 = 'this is string2'
  
  --也可以用2 个方括号 "[[]]" 来表示"一块"字符串
  html = [[
  <html>
  <head></head>
  <body>
      <a href="http://www.runoob.com/">Lua学习笔记</a>
  </body>
  </html>
  ]]
  ```

  在对一个数字字符串上进行算术操作时，Lua 会尝试将这个数字字符串转成一个数字

  ```lua
  >print("2"+6)
  8.0
  >print("2"+"6")
  8.0
  >print("2 + 6")
  2 + 6
  >print("-2e2" * "6")
  -1200.0
  >print("error" + 1)
  --异常报错
  ```

  使用  .. 连接字符串

  ```lua
  >print("a" .. 'b')
  ab
  >print(157 .. 428)
  157428
  ```

  使用 # 计算字符串长度

  ```
  >len = "www.hello.com"
  >print(#len)
  13
  ```

- table

  ```lua
  --创建一个空的table
  local tbl1 = {}
  --直接初始表
  local tbl2 = {"apple", 'pear', 'orange', 'grape'}
  for key, val in pairs(tbl2) do
      print(key .. ":" .. val)
  end
  --执行结果(的默认初始索引一般以 1 开始)
  1 : apple
  2 : pear
  3 : orange
  4 : grape
  ```

  ```lua
  a = {}
  a["key"] = "value"
  key = 10
  a[key] = 22
  a[key] = a[key] + 11
  for k, v in pairs(a) do
      print(k .. " : " .. v)
  end
  --执行结果
  key : value
  10 : 33
  ```

  ```lua
  a3 = {}
  for i = 1, 10 do
      a3[i] = i
  end
  a3["key"] = "val"
  print(a3["key"])
  print(a3["none"])
  --执行结果
  val
  nil
  ```

- function

  函数是被看作是"第一类值（First-Class Value）"，函数可以存在变量里

  ```lua
  function factorial1(n)
      if n == 0 then
          return 1
      else 
          return n * factorial1(n - 1)
      end
  end
  print(factorial1(5))
  factorial2 = factorial1
  print(factorial1(5))
  --执行结果
  120
  120
  ```

  function 可以以匿名函数（anonymous function）的方式通过参数传递

  ```lua
  function testFun(tab,fun)
  	for k, v in pairs(tab) do
  		print(fun(k, v))
  	end
  end
  
  tab = {key1="val1", key2="val2"}
  testFun(tab,
  function(key, val)--匿名函数
  	return key.."="..val;
  end
  );
  
  --执行结果
  key1 = val1
  key2 = val2
  ```

- userdata

  userdata 是一种用户自定义数据，用于表示一种由应用程序或 C/C++ 语言库所创建的类型，可以将任意 C/C++ 的任意数据类型的数据（通常是 struct 和 指针）存储到 Lua 变量中调用。

- thread

  在 Lua 里，最主要的线程是协同程序（coroutine）。它跟线程（thread）差不多，拥有自己独立的栈、局部变量和指令指针，可以跟其他协同程序共享全局变量和其他大部分东西。

    线程跟协程的区别：线程可以同时多个运行，而协程任意时刻只能运行一个，并且处于运行状态的协程只有被挂起（suspend）时才会暂停。

### Lua变量

- Lua 变量有三种类型：全局变量、局部变量、表中的域；
- Lua 中的变量全是全局变量，那怕是语句块或是函数里，除非用 local 显式声明为局部变量;
- 局部变量的作用域为从声明位置开始到所在语句块结束;
- 变量的默认值均为 nil。

```lua
a = 5				--全局变量
local b = 3			--局部变量

function a()
    c = 6			--全局变量
    local b = 8		--局部变量
    print(b)
end
print(b)
--执行结果
8
3
```

赋值语句

```lua
a = "hello" .. "world"
t.n = t.n + 1

--多个变量同时赋值，变量列表和值列表的各个元素用逗号分开，赋值语句右边的值会依次赋给左边的变量;当变量个数大于值个数时补nil,当变量个数小于值个数，多余的值被忽略。
a, b = 10, 2*x 		<--> a=10;b=2*x

--函数f()返回2个值依次赋给变量a和b
a, b = f()
```

### Lua循环

- while 循环

  ```lua
  while(condition)
  do
     statements
  end
  ```

- for 循环

  ```lua
  --[[数值for循环
  	var 从 exp1 变化到 exp2，每次变化以 exp3 为步长递增 var，并执行一次 "执行体"。
  	exp3 是可选	的，如果不指定，默认为1。
  --]]
  for var=exp1,exp2,exp3 do  
      statements  
  end  
  
  --[[泛型for循环
  	泛型 for 循环通过一个迭代器函数来遍历所有值
  --]]
  a = {"one", "two", "three"}
  for i, v in ipairs(a) do
      print(i, v)
  end 
  --i是数组索引值，v是对应索引的数组元素值。ipairs是Lua提供的一个迭代器函数，用来迭代数组
  ```

  

### Lua流程控制

Lua语言中 false 和 nil为假，其它都为真，包括 0

```lua
if(布尔表达式)
then
   --[ 在布尔表达式为 true 时执行的语句 --]
end

if(布尔表达式)
then
   --[ 布尔表达式为 true 时执行该语句块 --]
else
   --[ 布尔表达式为 false 时执行该语句块 --]
end

if( 布尔表达式 1)
then
   --[ 布尔表达式 1 为 true 时执行该语句块 --]
   if(布尔表达式 2)
   then
      --[ 布尔表达式 2 为 true 时执行该语句块 --]
   end
end

if( 布尔表达式 1)
then
   --[ 布尔表达式 1 为 true 时执行该语句块 --]
else if(布尔表达式 2)
   --[ 布尔表达式 2 为 true 时执行该语句块 --]
end
```



### Lua 函数

函数定义格式

```lua
[local] function function_name(argument1, argument2...,argumentn)
    function_body
    return result_params_comma_seperated
end

function max(num1, num2)
    if (num1 > num2) then
        result = num1
    else
        result = num2
    end
    return result
end
```

函数可作为参数传递给函数，类似C语言的函数指针用法

```lua
myprint = function(param)		--匿名函数赋值给myprint函数类型变量
    print("这是一个打印函数 - ##",param, "##")
end

function add(num1, num2, functionPtr)
    result = num1 + num2
    functionPtr(result)
end

myprint(10)
add(2, 5, myprint)

--运行结果
这是一个打印函数 - ##10##
这是一个打印函数 - ##7##
```

多返回值

```lua
s,e = string.find("www.xyz.com", "xyz") --string.find返回匹配字符串起始和结束位置
print(s, e)
--运行结果
5 7   --Lua数组起始从1开始
```

可变参数

select('#', …) 	返回可变参数的长度
select(n, …) 	用于可变参数N位置的参数 

```lua
function average(...)
   result = 0
   local arg={...}    --> arg 为一个表，局部变量
   for i,v in ipairs(arg) do
      result = result + v
   end
   print("总共传入 " .. #arg .. " 个数")
   return result/#arg
end

function foo(...)  
	for i = 1, select('#', ...) do  -->获取参数总数
    	local arg = select(i, ...); -->读取参数
    	print("arg", arg);  
	end  
end  
```

### 运算符

```lua
--算术运算符
+	--加法
-	--减法
*	--乘法
/	--除法
%	--取余
^	--乘幂
-	--负号

--关系运算符
==	--等于
~=	--不等于
>	--大于
<	--小于
>=	--大于等于
<=	--小于等于

--逻辑运算符
and	--逻辑与操作符
or	--逻辑或操作符
not	--逻辑非操作符

--其他运算符
.. 	连接两个字符串
#	返回字符串或表的长度

--运算符优先级
^
not		-(负号)
*	/
+	-
..
<	>	<=	>=	~=	==
and
or
```

### 字符串

```lua
string1 = "Lua 1"
print("\"字符串 1 是\"", string1)
string2 = "Lua 2"
print('字符串 2 是', string2)
string2 = [["Lua 3"]]
print('字符串 3 是', string3)
--执行结果
"字符串 1 是"Lua 1
字符串 2 是Lua 2
字符串 3 是"Lua3"

string.format("%10s", "monkey")         -- 输出    monkey
string.format("%5.3s", "monkey")        -- 输出  mon
```

### 数组

​	数组，就是相同数据类型的元素按一定顺序排列的集合，可以是一维数组和多维数组。Lua 数组的索引键值可以使用整数表示，数组的大小不是固定的。

- 一维数组

  ```lua
  array = {"Lua", "Tutorial"}
  for i=0, 2 do
      print(array[i])
  end
  --执行结果
  nil			--Lua索引值默认从1开始
  Lua
  Tutorial
  
  --数组索引可以从负数开始
  array = {}
  for i = -2, 2 do
      array[i] = i * 2
  end
  
  for i = -2, 2 do
      print(array[i])
  end
  --执行结果如下
  -4
  -2
  0
  2
  4
  ```

- 二维数组

  ```lua
  array = {}
  for i=1,3 do
      array[i] = {}
      for j=1,3 do
          array[i][j] = i*j
      end
  end
  ```

### 迭代器

​	迭代器（iterator）是一种对象，它能够用来遍历标准模板库容器中的部分或全部元素，每个迭代器对象代表容器中的确定的地址。在 Lua 中迭代器是一种支持指针类型的结构，它可以遍历集合的每一个元素。