---
title: Python 入门 - 基础语法参考
tags:
  - Python
name: Python Basic Syntax Reference
image: images/article/python-lang-logo.jpg
categories:
  - Python
date: 2020-11-05 21:22:10
---


我有一个需求：去批量下载某网站的图片，图片名称由一定的规则组成，将下载的图片合并组成为 PDF 文件，准备使用 Python 去操作。


最近用 Python 写一些自动化的脚本，发现写一段就要去查询一下 Python 某个语法怎么写，之前使用 Python 写点脚本还写得挺顺，但是由于好久没有碰过 Python，基本连语法都忘光了。Python 写一些自动化脚本是非常方便的，比如发个网络请求，处理一些文件。编程语言总是学了就忘，所以在这里记录一下 Python 的基础语法，方便以后查阅参考。

## 数据类型

**整数**

Python 可以处理任意大小的整数，当然包括负整数，在程序中的表示方法和数学上的写法一模一样，例如：1，100，-8080，0，等等。对于很大的数，例如 10000000000，很难数清楚 0 的个数。Python 允许在数字中间以_分隔，因此，写成 `10_000_000_000` 和 `10000000000` 是完全一样的。

**浮点数**

浮点数也就是小数，之所以称为浮点数，是因为按照科学记数法表示时，一个浮点数的小数点位置是可变的，比如，1.23x10<sup>9</sup> 和12.3x10<sup>8</sup> 是完全相等的。浮点数可以用数学写法，如 1.23，3.14，-9.01，等等。


**字符串**

字符串是以单引号 `'` 或双引号 `"` 括起来的任意文本，比如 `'abc'` ，`"xyz"` 等等。请注意，'' 或 "" 本身只是一种表示方式，不是字符串的一部分，因此，字符串 'abc' 只有 a，b，c 这3个字符。如果 '本身也是一个字符，那就可以用 "" 括起来，比如 "I'm OK" 包含的字符是 I，'，m，空格，O，K这 6 个字符。

**布尔值**

布尔值和布尔代数的表示完全一致，一个布尔值只有 `True` 、 `False` 两种值。布尔值可以用 `and` 、 `or` 和 `not` 运算。

**空值**

空值是 Python 里一个特殊的值，用 `None` 表示。None 不能理解为 0，因为 0 是有意义的，而 None 是一个特殊的空值。

## 字符串和编码

由于计算机是美国人发明的，因此，最早只有127个字符被编码到计算机里，也就是大小写英文字母、数字和一些符号，这个编码表被称为 ASCII 编码。各个国家的文字又有不同的编码，因此多语言混合的文本中，显示出来会有乱码。Unicode 把所有语言都统一到一套编码里，就不会再有乱码问题，用两个字节表示一个字符（如果要用到非常偏僻的字符，就需要 4 个字节）。

新的问题：如果统一成Unicode编码，乱码问题从此消失，但是如果写的文本基本上全部是英文的话，用 Unicode 编码比 ASCII 编码需要多一倍的存储空间，在存储和传输上就十分不划算。

UTF-8编码：本着节约的精神，又出现了把 Unicode 编码转化为“可变长编码”的 UTF-8 编码，UTF-8 编码把一个 Unicode 字符根据不同的数字大小编码成 1-6 个字节，常用的英文字母被编码成 1 个字节，汉字通常是 3 个字节，只有很生僻的字符才会被编码成 4-6 个字节。

所以如果你要传输的文本包含大量英文字符，用 UTF-8 编码就能节省空间。所以我们会看到很多网页的源码上会有类似 `<meta charset="UTF-8" />` 的信息，表示该网页正是用的 UTF-8 编码。

由于 Python 源代码也是一个文本文件，所以，当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为 UTF-8 编码。当 Python 解释器读取源代码时，为了让它按 UTF-8 编码读取，我们通常在文件开头写上这两行：


```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```

格式化字符串

格式化方式和C语言是一致的，用 `%` 实现，例如 `'Hello, %s' % 'world'`，

- `%d` 整数
- `%f` 浮点数
- `%s` 字符串
- `%x` 十六进制整数

使用字符串的 `format()` 方法，例如 `'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)`

使用以 f 开头的字符串，字符串如果包含 `{xxx}` 会以对应的变量替换

```python
r = 2.5
s = 3.14 * r ** 2
print(f'The area of a circle with radius {r} is {s:.2f}')
```

## list 和 tuple

**list**

Python 内置的一种数据类型，list 是一种有序的集合，可以随时添加和删除其中的元素。`classmates = ['Michael', 'Bob', 'Tracy']`

`len()` 函数可以获得list元素的个数，用索引来访问 list 中每一个位置的元素 `classmates[0]` `classmates[1]`，取最后一个元素 `classmates[-1]` 当索引超出了范围时，Python 会报一个 IndexError 错误，要确保索引不要越界。

list 是一个可变的有序表，所以，可以往 list 中追加元素。

- `classmates.append('Adam')` 可以把元素插入到指定的位置
- `classmates.insert(1, 'Jack')` 把元素插入到指定的位置，索引号为1的位置
- `classmates.pop()` 删除 list 末尾的元素
- `classmates.pop(1)` 删除指定位置的元素，其中 i 是索引位置
- `classmates[1] = 'Sarah'` 把某个元素替换成别的元素，可以直接赋值给对应的索引位置


list 里面的元素的数据类型也可以不同，如 `L = ['Apple', 123, True]`，list 元素也可以是另一个 list，`s = ['python', 'java', ['asp', 'php'], 'scheme']`，可以看成是一个二维数，要拿到 `'php'` 使用 `s[2][1]`

**tuple**

另一种有序列表叫元组：tuple。list 非常类似，但 tuple 一旦初始化就不能修改。例如 `classmates = ('Michael', 'Bob', 'Tracy')` ，它没有 append()，insert() 这样的方法，可以下标访问，但不能赋值成另外的元素。只有 1 个元素的 tuple定 义时必须加一个逗号,，来消除歧义 `t = (1,)`。


## 流程控制

### 条件判断

if 语句

```python

# x是非零数值、非空字符串、非空list等，就判断为True，否则为False
if x:
    print('True')


age = 20
if age >= 18:
    print('your age is', age)
    print('adult')
```

if else 语句

```python
age = 3
if age >= 18:
    print('your age is', age)
    print('adult')
else:
    print('your age is', age)
    print('teenager')
```

多重 if, elif 是 else if 的简写

```python
if <条件判断1>:
    <执行1>
elif <条件判断2>:
    <执行2>
elif <条件判断3>:
    <执行3>
else:
    <执行4>
```

match 语句

```python
score = 'B'

match score:
  case 'A':
    print('score is A.')
  case 'B':
    print('score is B.')
  case 'C':
    print('score is C.')
  case _: # _表示匹配到其他任何情况
    print('score is ???.')
```

match 语句除了可以匹配简单的单个值外，还可以匹配多个值、匹配一定范围，并且把匹配后的值绑定到变量

```python
age = 15

match age:
  case x if x < 10:
    print(f'< 10 years old: {x}')
  case 10:
    print('10 years old.')
  case 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18:
    print('11~18 years old.')
  case 19:
    print('19 years old.')
  case _:
    print('not sure.')
```

match 语句还可以匹配列表，功能非常强大

```python
args = ['gcc', 'hello.c', 'world.c']
# args = ['clean']
# args = ['gcc']

match args:
  # 如果仅出现gcc，报错:
  case ['gcc']:
    print('gcc: missing source file(s).')
  # 出现gcc，且至少指定了一个文件:
  case ['gcc', file1, *files]:
    print('gcc compile: ' + file1 + ', ' + ', '.join(files))
  # 仅出现clean:
  case ['clean']:
    print('clean')
  case _:
    print('invalid command.')
```

### 循环

Python 的循环有两种，一种是 `for...in` 循环，把 list 或 tuple 中的每个元素迭代出来

```python
names = ['Michael', 'Bob', 'Tracy']
for name in names:
  print(name)
```

另一种循环是 while 循环

```python
sum = 0
n = 99

while n > 0:
  sum = sum + n
  n = n - 2

print(sum)
```

for 循环迭代

```python
d = {'a': 1, 'b': 2, 'c': 3}

# 默认迭代 key
for key in d:
  print(key)

# 迭代 value
for key in d.values():
  print(key)

# 迭代 key value
for k, v in d.items()
  print(key)

# enumerate函数可以把一个list变成索引-元素对，这样就能同时迭代索引和元素本
for i, value in enumerate(['A', 'B', 'C']):
  print(i, key)
```

Python的 for 循环不仅可以用在 list 或 tuple 上，还可以作用在其他可迭代对象上。在循环中， `break` 语句可以提前退出循环，在循环过程中，也可以通过 `continue` 语句，跳过当前的这次循环，和其他语言一样。

可以 通过 collections.abc 模块的 Iterable 类型判断一个值是否可迭代对象。

```python
from collections.abc import Iterable


isinstance('abc', Iterable)     # True
isinstance([1,2,3], Iterable)   # True
isinstance(123, Iterable)       # False
```

## dict 和 set

dict 的支持，dict 全称 dictionary ，在其他语言中也称为 map。

```python
d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
d['Michael'] #95  访问
d['Bob'] = 80 # 修改
d['Jack'] # 不存在 - 报错

# 判断
if 'Thomas' in d
  print('OK')

# 获取 不存在得到 None
d.get('Thomas')

# 若不存在则获得 -1
d.get('Thomas', -1)

# 删除
d.pop('Bob')
```

set 是一组 key 的集合，但不存储 value。由于 key 不能重复，所以，在 set 中，没有重复的 key。

```python
s = {1, 2, 3}
# 通过 list 作为输入
s = set([1, 2, 3])

# 添加元素
s.add(4)

# 移除元素
remove(key)

# set 可以做运算
s1 = {1, 2, 3}
s2 = {2, 3, 4}

s3 = s1 & s2    # {2, 3}
s4 = s1 | s2    # {1, 2, 3, 4}
```


## 函数

定义一个函数要使用 def 语句，依次写出函数名、括号、括号中的参数和冒号 : ，然后，在缩进块中编写函数体，函数的返回值用 return 语句返回。

```python
def my_abs(x):
  if x >= 0:
    return x
  else:
    return -x

print(my_abs(-99))
```

定义一个什么事也不做的空函数，可以用pass语句：
```python
def nop():
  pass
```

函数参数有 必选参数外，默认参数、可变参数和关键字参数。


默认参数

```python
def power(x, n=2):
  s = 1
  while n > 0:
    n = n - 1
    s = s * x
  return s

power(5)
power(5, 2)
power(5, 3)
```

可变参数，函数内部接收到的是一个tuple，Python 允许你在 list 或 tuple 前面加一个 `*` 号，把 list 或 tuple 的元素变成可变参数。

```python
def calc(*numbers):
  sum = 0
  for n in numbers:
    sum = sum + n * n
  return sum

calc(1, 2)
calc(1, 2, 3)
calc(1, 2, 3, 4)

nums = [1, 2, 3]
calc(*nums)
```

关键字参数，关键字参数允许你传入 0 个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个 dict。

```python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)

person('Michael', 30)
person('Bob', 35, city='Beijing')
person('Adam', 45, gender='M', job='Engineer')
```

命名关键字参数，对于关键字参数，函数的调用者可以传入任意不受限制的关键字参数。如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收city和job作为关键字参数。命名关键字参数需要一个特殊分隔符 `*`，`*` 后面的参数被视为命名关键字参数。

```python
def person(name, age, *, city, job):
  print(name, age, city, job)

person('Jack', 24, city='Beijing', job='Engineer')
```

如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符 `*` 了，命名关键字参数必须传入参数名

```python
def person(name, age, *args, city, job):
  print(name, age, args, city, job)

person('Jack', 24, city='Beijing', job='Engineer')
```

命名关键字参数可以有缺省值，从而简化调用：

```python
def person(name, age, *, city='Beijing', job):
  print(name, age, city, job)

person('Jack', 24, job='Engineer')
```

在 Python 中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。对于任意函数，都可以通过类似func(*args, **kw)的形式调用它，无论它的参数是如何定义的。

```python
def f1(a, b, c=0, *args, **kw):
  print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
  print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
```


## Python 高级特性

**切片操作**

```python
L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']

# 取前3个元素，用一行代码就可以完成切片，从索引 0 开始取，直到索引 3 为止，但不包括索引 3
L[0:3]  # ['Michael', 'Sarah', 'Tracy']
L[1:3]  # ['Sarah', 'Tracy']
L[-2:]  # ['Bob', 'Jack']

L = list(range(100))  # 先创建一个0-99的数列
L[:10]    # 取前10个数
L[-10:]   # 取后10个数
L[10:20]  # 前11-20个数
L[:10:2]  # 前10个数，每两个取一个  [0, 2, 4, 6, 8]
L[::5]    # 所有数，每5个取一个
L[:]      # 原样复制一个 list
```

- tuple 也可以用切片操作，操作的结果仍是tuple，唯一区别是tuple不可变。
- 字符串'xxx'也可以看成是一种list，每个元素就是一个字符。因此，字符串也可以用切片操作，只是操作结果仍是字符串，很多编程语言中，针对字符串提供了很多各种截取函数（例如，substring），Python 没有针对字符串的截取函数，只需要对切片进行操作。


**列表生成**

列表生成式即 List Comprehensions，是 Python 内置的非常简单却强大的可以用来创建 list 的生成式。

```python
# 生成 [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
list(range(1, 11))

# 生成 [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
[x * x for x in range(1, 11)]

# 生成 [4, 16, 36, 64, 100]   加上if判断，筛选出仅偶数的平方
[x * x for x in range(1, 11) if x % 2 == 0]

# 生成 ABC 与 XYZ 的全排列 ['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
[m + n for m in 'ABC' for n in 'XYZ']

# 列出当前目录下的所有文件和目录名
[d for d in os.listdir('.')]

# 列表生成式也可以使用两个变量来生成list
d = {'x': 'A', 'y': 'B', 'z': 'C' }
[k + '=' + v for k, v in d.items()]

# 把一个list中所有的字符串变成小写
L = ['Hello', 'World', 'IBM', 'Apple']
[s.lower() for s in L]
```

**生成器**

要创建一个 generator，有很多种方法。第一种方法很简单，只要把一个列表生成式的 [] 改成 ()，就创建了一个 generator ，通过 next() 函数获得 generator 的下一个返回值

```python
L = [x * x for x in range(10)]
g = (x * x for x in range(10))
print(g)

# 使用 for 迭代
for n in g:
  print(g)


# 普通函数
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        print(b)
        a, b = b, a + b
        n = n + 1
    return 'done'

# generator 函数
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```

**函数**

变量可以指向函数，函数名也是变量，函数可以当作参数传递，函数也可以当作返回值。

闭包：当一个函数返回了一个函数后，其内部的局部变量还被新函数引用，就是内层函数引用了外层函数的局部变量。

nonlocal

如果只是读外层变量的值，我们会发现返回的闭包函数调用一切正常：但是，如果对外层变量赋值，由于Python解释器会把x当作函数fn()的局部变量，它会报错：

```python
# 正常
def inc():
  x = 0
  def fn():
    # 仅读取x的值:
    return x + 1
  return fn

f = inc()
print(f()) # 1
print(f()) # 1
```

```python
# 报错
def inc():
  x = 0
  def fn():
    # 写入
    x = x + 1
    return x
  return fn

f = inc()
print(f()) # 1
print(f()) # 2
```
想引用 `inc()` 函数内部的 `x`，所以需要在 `fn()` 函数内部加一个 `nonlocal x` 的声明。加上这个声明后，解释器把 `fn()` 的 `x` 看作外层函数的局部变量。

```python
# 报错
def inc():
  x = 0
  def fn():
    nonlocal x
    x = x + 1
    return x
  return fn

f = inc()
print(f()) # 1
print(f()) # 2
```

**匿名函数**

传入函数时，函数作为参数时，有时候不需要显式地定义函数，直接传入匿名函数更方便。

```python
# [1, 4, 9, 16, 25, 36, 49, 64, 81]
list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))

# lambda x: x * x 等价于如下函数
def f(x):
  return x * x

# 匿名函数可以赋值给一个变量
f = lambda x: x * x

# 匿名函数可以作为返回值返回
def build(x, y):
  return lambda: x * x + y * y

```

**装饰器 - Decorator**

本质上，decorator 就是一个返回函数的高阶函数。使用 `@` 语法，把 decorator 置于函数的定义处，装饰函数。 wrapper() 函数的参数定义是 `(*args, **kw)`，因此，wrapper() 函数可以接受任意参数的调用。

```python
def log(func):
  def wrapper(*args, **kw):
    print('call %s():' % func.__name__)
    return func(*args, **kw)
  return wrapper

# 相当于 now = log(now)
@log
def now():
    print('2024-6-1')

# 调用 now() 方法将执行 log() 函数中返回的 wrapper() 函数
now()
```

如果 decorator 本身需要传入参数，那就需要编写一个返回 decorator 的高阶函数，写出来会更复杂。

```python
def log(text):
  def decorator(func):
    def wrapper(*args, **kw):
      print('%s %s():' % (text, func.__name__))
      return func(*args, **kw)
    return wrapper
  return decorator

# 相当于 now = log('execute')(now)
@log('execute')
def now():
    print('2024-6-1')

now()
# execute now():
# 2024-6-1
```

需要注意的是：函数也是对象，它有 `__name__` 等属性，但你去看经过decorator装饰之后的函数，它们的 `__name__` 已经从原来的 `'now'` 变成了 `'wrapper'`，因为返回的那个 `wrapper()` 函数名字就是 `'wrapper'`，所以，需要把原始函数的 `__name__` 等属性复制到 `wrapper()` 函数中，否则，有些依赖函数签名的代码执行就会出错。可以通过 Python 内置的 `functools.wraps` 实现。

```python
import functools

def log(func):
  @functools.wraps(func)
  def wrapper(*args, **kw):
    print('call %s():' % func.__name__)
    return func(*args, **kw)
  return wrapper
```

## 模块

为了编写可维护的代码，我们把很多函数分组，分别放到不同的文件里。很多编程语言都采用这种组织代码的方式。在 Python 中，一个 .py 文件就称之为一个模块（Module）。使用模块还可以避免函数名和变量名冲突。相同名字的函数和变量完全可以分别存在不同的模块中，因此，我们自己在编写模块时，不必考虑名字会与其他模块冲突。如果不同的人编写的模块名相同怎么办？为了避免模块名冲突，Python又引入了按目录来组织模块的方法，称为包（Package）。

现在，`abc.py` 模块的名字就变成了 `mymodule.abc`，类似的，`xyz.py` 的模块名变成了 `mymodule.xyz`。每一个包目录下面都会有一个 `__init__.py` 的文件，这个文件是必须存在，否则 Python就把这个目录当成普通目录。 `__init__.py` 可以是空文件，也可以有 Python 代码，因为 `__init__.py` 本身就是一个模块，而它的模块名就是 `mymodule`。可以有多级目录，组成多级层次的包结构。


```python
mymodule
├─ __init__.py
├─ abc.py
└─ xyz.py
```

**使用模块**

Python 本身就内置了很多非常有用的模块。通过 `import` 倒入模块，

```python
import os
import sys

print(sys.argv)
print(os.listdir('.'))

```

**作用域**

在一个模块中，会定义很多函数和变量，但有的函数和变量我们希望给别人使用，有的函数和变量希望仅仅在模块内部使用。在 Python 中，是通过 `_` 前缀来实现的。正常的函数和变量名是公开的（public），类似 `__xxx__` 这样的变量是特殊变量，可以被直接引用，比如上面的 `__author__` ， `__name__` 就是特殊变量，模块定义的文档注释也可以用特殊变量 `__doc__` 访问，我们自己的变量一般不要用这种变量名。

类似 `_xxx`和 `__xxx` 这样的函数或变量就是非公开的（private），不应该被直接引用，比如 `_abc`，`__abc` 等；private 函数和变量 “不应该” 被直接引用，而不是“不能”被直接引用，是因为 Python 并没有一种方法可以完全限制访问 private 函数或变量，但是从编程习惯上不应该引用 private 函数或变量。

```python
def _private_1(name):
    return 'Hello, %s' % name

def _private_2(name):
    return 'Hi, %s' % name

def greeting(name):
    if len(name) > 3:
        return _private_1(name)
    else:
        return _private_2(name)
```


## 面向对象编程

**类定义**

在 Python中，所有数据类型都可以视为对象，当然也可以自定义对象。自定义的对象数据类型就是面向对象中的类（Class）的概念。

类的定义：`class` 后面紧接着是类名，即 `Student`，类名通常是大写开头的单词，紧接着是 `(object)`，表示该类是从哪个类继承下来的。如果没有合适的继承类，就使用 `object` 类，这是所有类最终都会继承的类。通过 `ClassName()` 获得该类实例。

```python
# 定义 Student 类
class Student(object):
  def __init__(self, name, score):
    self.name = name
    self.score = score
  
  def print_score(self):
    print('%s: %s' % (self.name, self.score))

  def get_grade(self):
    if self.score >= 90:
      return 'A'
    elif self.score >= 60:
      return 'B'
    else:
      return 'C'

bart = Student('Bart Simpson', 59)
lisa = Student('Lisa Simpson', 87)
bart.print_score()
lisa.print_score()
```

`__init__` 是该类的构造函数，第一个参数永远是 `self`，表示创建的实例本身。调用的时候 `self` 不需要传，Python 解释器自己会把实例变量传进去。和普通的函数相比，在类中定义的函数只有一点不同，就是第一个参数永远是实例变量 `self`，类中的函数称为类的方法。

和静态语言不同，Python 允许对实例变量绑定任何数据，也就是说，对于两个实例变量，虽然它们都是同一个类的不同实例，但拥有的变量名称都可能不同。

**访问限制**

如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线 `__`，在Python中，实例的变量名如果以 `__` 开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问。

```python
class Student(object):
  def __init__(self, name, score):
    self.__name = name
    self.__score = score

  def print_score(self):
    print('%s: %s' % (self.__name, self.__score))

bart = Student('Bart Simpson', 59)
```

对于外部代码来说，没什么变动，但是已经无法从外部访问 `bart.__name` 和 `bart.__score` 了。这样就确保了外部代码不能随意修改对象内部的状态，这样通过访问限制的保护。如果外部代码要获取 `name` 和 `score` ，可以给 Student 类增加 `get_name` 和 `get_score` 这样的方法；如果又要允许外部代码修改 `score`，可以再给 Student 类增加 `set_score`  方法。

```python
class Student(object):
  def __init__(self, name, score):
    self.__name = name
    self.__score = score

  def get_name(self):
    return self.__name

  def get_score(self):
    return self.__score

  def set_score(self, score):
    self.__score = score

  def print_score(self):
    print('%s: %s' % (self.__name, self.__score))

bart = Student('Bart Simpson', 59)
```

注意：双下划线开头的实例变量并不是一定不能从外部访问，不能直接访问 `__name` 是因为 Python 解释器对外把 `__name` 变量改成了 `_Student__name` ，所以，仍然可以通过 `bart._Student__name` 来访问 `__name` 变量，但是不建议这么干，因为不同版本的 Python 解释器可能会把 `__name` 改成不同的变量名。Python 本身没有任何机制阻止你干坏事，一切全靠自觉。


**实例属性和类属性**

给实例绑定属性的方法是通过实例变量，或者通过 `self` 变量，如果类本身需要绑定一个属性，可以直接在 class 中定义属性，这种属性是类属性，归类所有。也就类似于静态属性。

需要注意的是：定义了一个类属性后，这个属性虽然归类所有，但类的所有实例都可以访问到。不要对实例属性和类属性使用相同的名字，因为相同名称的实例属性将屏蔽掉类属性，但是当你删除实例属性后，再使用相同的名称，访问到的将是类属性。

```python
class Student(object):
  name = 'Student'

s = Student() # 创建实例s
print(s.name) # 打印name属性，因为实例并没有name属性，所以会继续查找class的name属性
# Student
print(Student.name) # 打印类的name属性
# Student
s.name = 'Michael' # 给实例绑定name属性
print(s.name) # 由于实例属性优先级比类属性高，因此，它会屏蔽掉类的name属性
# Michael
print(Student.name) # 但是类属性并未消失，用Student.name仍然可以访问
# Student
del s.name # 如果删除实例的name属性
print(s.name) # 再次调用s.name，由于实例的name属性没有找到，类的name属性就显示出来了
# Student
```


```python
# 错误示例。不要这样做
class Student(object):
  name = 'Student'
  def __init__(self, name):
    self.name = name
```


### 面向对象高级

**`__slots__`**

想要限制实例的属性怎么办？比如，只允许对 Student 实例添加 name 和 age 属性，Python 允许在定义 class 的时候，定义一个特殊的 `__slots__` 变量，来限制该 class 实例能添加的属性。

```python
class Student(object):
  __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
```

**`@property`**

对于 `get_name` / `set_name`  `set_score` / `get_score`，这样实现，调用方法又略显复杂，没有直接用属性这么直接简单。能不能用类似属性这样简单的方式来访问类的变量。Python 内置的 `@property` 装饰器就是负责把一个方法变成属性调用的。

```python
class Student(object):
  @property
  def score(self):
    return self._score

  @score.setter
  def score(self, value):
    if not isinstance(value, int):
      raise ValueError('score must be an integer!')
    if value < 0 or value > 100:
      raise ValueError('score must between 0 ~ 100!')
    self._score = value
```

把一个 getter 方法变成属性，只需要加上 `@property` 就可以了，`@property` 本身又创建了另一个装饰器 `@score.setter`，负责把一个 setter 方法变成属性赋值。

**多重继承**

Python 支持多重继承

```python
class Animal(object):
    pass

# 大类:
class Mammal(Animal):
    pass

class Bird(Animal):
    pass

class Runnable(object):
    def run(self):
        print('Running...')

class Flyable(object):
    def fly(self):
        print('Flying...')

class Dog(Mammal, Runnable):
    pass
class Bat(Mammal, Flyable):
  pass
```

**枚举类**

Python 提供了 Enum 类

```python
from enum import Enum

Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))

```

如果需要更精确地控制枚举类型，可以从 Enum 派生出自定义类，`@unique` 装饰器可以帮助我们检查保证没有重复值。

```python
from enum import Enum, unique

@unique
class Weekday(Enum):
    Sun = 0 # Sun的value被设定为0
    Mon = 1
    Tue = 2
    Wed = 3
    Thu = 4
    Fri = 5
    Sat = 6
```


## 错误、调试和测试

**捕获错误**

Python提供了一套 `try...except...finally...` 的错误处理机制。如果发生了不同类型的错误，应该由不同的 `except` 语句块处理。以有多个 `except` 来捕获不同类型的错误。可以通过 `logging.exception(e)` 把错误堆栈打印出来。

```python
try:
  print('try...')
  r = 10 / 0
  print('result:', r)
except ValueError as e:
  print('ValueError:', e)
except ZeroDivisionError as e:
  print('ZeroDivisionError:', e)
  logging.exception(e)
finally:
  print('finally...')
print('END')
```

可以在 `except` 语句块后面加一个 `else` ，如果没有错误发生，当没有错误发生时，会自动执行 `else` 语句。

```python
try:
  print('try...')
  r = 10 / 0
  print('result:', r)
except ValueError as e:
  print('ValueError:', e)
except ZeroDivisionError as e:
  print('ZeroDivisionError:', e)
finally:
  print('finally...')
print('END')
```

Python所有的错误都是从 `BaseException` 类派生的，常见的错误类型和继承关系看这里 - https://docs.python.org/3/library/exceptions.html#exception-hierarchy


**抛出错误**

要抛出错误，首先根据需要，可以定义一个错误的 `class`，选择好继承关系，然后，用 `raise` 语句抛出一个错误的实例。`raise` 语句后面如果不带参数，就会把当前错误原样抛出。此外，`raise` 可以另一个新的 `Error` ，这样就把一种类型的错误转化成另一种类型。

```python
class FooError(ValueError):
    pass

def foo(s):
    n = int(s)
    if n==0:
        raise FooError('invalid value: %s' % s)
    return 10 / n

foo('0')
```

**断言**

`assert` 的意思是，表达式 `n != 0` 应该是 `True`，否则，根据程序运行的逻辑，后面的代码肯定会出错。如果断言失败，`assert` 语句本身就会抛出 `AssertionError`

```python
def foo(s):
  n = int(s)
  assert n != 0, 'n is zero!'
  return 10 / n

def main():
  foo('0')
```

## IO

读写文件是最常见的IO操作。Python内置了读写文件的函数，用法和 C 是兼容的。

以读文件的模式打开一个文件对象，使用 Python 内置的 `open()` 函数，传入文件名和标示符，如果文件不存在，open() 函数就会抛出一个 IOError 的错误，并且给出错误码和详细的信息告诉你文件不存在

```python
f = open('/path/to/file', 'r')
# 读取文件内容
f.read()
# 每次最多读取 100 个字节的内容
f.read(100)
# 每次读取一行内容
f.readline()
# 一次读取所有内容并按行返回list
f.readlines()

# 传入标识符'w' 以写的模式打开文件
f = open('/path/to/file', 'w')
f.write('Hello, world!')
```

数据读写不一定是文件，也可以在内存中读写。

- `StringIO` 在内存中读写 str
- `BytesIO` 在内存中读写 bytes

### 操作文件和目录

操作文件和目录的函数一部分放在 `os` 模块中，一部分放在 `os.path` 模块中。


- `os.path.abspath('.')` 查看当前目录的绝对路径
- `os.path.join('/Users/michael', 'testdir')` 把目录的完整路径表示出来
- `os.mkdir('/Users/michael/testdir')` 创建一个目录
- `os.rmdir('/Users/michael/testdir')` 删掉一个目录
- `os.rename('test.txt', 'test.py')` 对文件重命名
- `os.remove('test.py')` 删除文件

