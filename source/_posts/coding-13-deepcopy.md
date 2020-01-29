---
title: Shallow Copy 和 Deep Copy
date: 2019-05-01 04:57:40
categories:
  - Coding
tags:
  - Python
  - JavaScript
  - R
---

在玩 JavaScript 时碰到了 deep copy 的问题，想起学 python 时也碰到过，遂有此文，互相印证。

本文依次讨论：
- variable assignment
- shallow copy
- deep copy

<!-- more -->

## Variable Assignment

在理解 shallow copy 和 deep copy 之前，需要先理解：assignment 不是复制，只是映射。

但对 primitive data type，assignment 会实际上造成“复制”的效果。

### Primitive Data Type

Python 有如下基础数据结构：

- integers
- floats
- strings
- booleans

下面这段代码的打印结果是新手都能猜到的：

{% codeblock Python lang:Python %}
a = 1
b = a
a = 3 
print(b) # 1
{% endcodeblock %}

把 `a` 的值赋给 `b` 之后，再改变 `a` 的值，并不会改变 `b` 的值。

JavaScript 也有 primitive data type：

- Number
- String
- Boolean

所以下面的代码也会打印出 1。

{% codeblock JavaScript lang:JS %}
var a = 1
var b = a
a = 3
console.log(b) // 1
{% endcodeblock %}

R 语言也是这样（下面这段代码跟 python 的例子一摸一样 :smirk:)：

{% codeblock R lang:r %}
a = 1
b = a
a = 3 
print(b) # 1
{% endcodeblock %}

初学者经常会有种错误：上面的“模式”在任何情境下都会成立。对 R 是这样，但对 python 和 JS 不是。

### Python: non-primitive data type

Python 的 non-primitive data type 包括但不限于 list, dictionary, class 等等。可以认为不是 primitive 的就是 non-primitive。

以 list 为例，python 会怎么处理 `b = a` 的变量赋值问题？

{% codeblock Python lang:Python %}
a = [1, 2, 3]
b = a
a[0] = 100
print(b)
# [100, 2, 3]
{% endcodeblock %}

依次解读代码如下：
- 第1行：创建了一个 list 对象`[1, 2, 3]`，并创建了一个 variable `a`，然后把 `a` 映射到 list 对象
- 第2行：创建了一个新的 varialbe `b`，把 `b` 映射到 `a` 所映射的对象`[1, 2, 3]`
- 第3行：修改了`[1, 2, 3]`对象的第一个元素为100
- 第4行：打印变量`b`，结果： [100, 2, 3]

`b = a` 这行代码并没有真的*复制*一个对象，只是复制了*映射*。

下面我们证明 `a` 和 `b` 映射到了一个地址：

{% codeblock Python lang:Python %}
a = [1,2,3]
b = a 
print(a is b) # True
{% endcodeblock %}

`is` operator 可以检查两个 variable 是否映射到同一个位置。
另一个证明方法是用`id()`返回变量所在的位置。

### JavaScript: non-primitive data type

JavaScript 的 non-primitve data 嘛，理解为 Object 就差不多了。毕竟，function、array 等等都是 Object。

{% codeblock JavaScript lang:JS %}
var a = [1, 2, 3]
var b = a
a[0] = 100
console.log(b)
// [100, 2, 3]
{% endcodeblock %}

逻辑几乎跟 python 一样。

但我没能在 JavaScript 里找到类似 Python 里的 `is` 和 `id()`。

### R

R 不一样。

不管是 primitive data 还是 non-primitive data，R 都会当作 primitive data 一样复制：

{% codeblock R lang:r %}
a <- c(1, 2, 3)
b <- a 
a[1] = 100
print(b)
# 1 2 3
{% endcodeblock %}

Python 和 Javascript 的 `b` 会随着 `a` 一起变化，R 却不会。是不是可以推测说 `b <- a` 这行代码会创建新的 object 呢？

并不是，我们可以查看两个变量指向的位置：

{% codeblock R lang:r %}
a <- c(1, 2, 3)
b <- a 
print(lobstr::obj_addr(a)) #"0x7fbd42342078"
print(lobstr::obj_addr(b)) #"0x7fbd42342078"

a[1] <- 100
print(lobstr::obj_addr(a)) #"0x7fbd42342058"
print(lobstr::obj_addr(b)) #"0x7fbd42342078"
{% endcodeblock %}

第3行和第4行说明两个`a`和`b`映射到了同一个地址。
但第8行里，a 映射的地址变了。`a[1] = 100` 创造出了一个新的对象。

虽然`b <- a` 是把 `b` 映射到了 `c(1,2,3)`，但如果修改`a`或`b`的其中任何一个，都会再创建一个新的对象。

所以在 R 里我们可以写 `df2 <- df1` 这样的语句，然后不担心修改 `df2` 会影响 `df1`。

**这个例子可以用来说明为什么 R 语言更适合当作统计学初学者的学习工具**，因为它不存在其他语言为了优化性能而设计的种种“反直觉设定”。

## Shallow Copy

### Python 

刚才已经证明了`a = b`不能实现 copy，只是复制了一个映射。

下面研究如何复制。

如果想复制 `list`，`dictionary`或`set`，可以直接用`list()`,`dict()`或`set()`。

{% codeblock Python lang:Python %}
a = [1,2,3]
b = list(a)

print(b is a) # False

a[0] = 100
print(b) # [1, 2, 3]
{% endcodeblock %}

另外一个方法，是用 `copy` library 里的 `copy()` 函数。

{% codeblock Python lang:Python %}
import copy

a = [1, 2, 3]
b = copy.copy(a)
print(a is b) # False
{% endcodeblock %}

如果是其他 object，比如 class，没有类似`list()`这样的函数，就可以直接用`copy()`实现复制。

{% codeblock Python lang:Python mark:12,15 %}
import copy

class Person():
  
  def __init__(self, name, gender):
    self.name = name
    self.gender = gender
    
tom = Person('Tom', 'Female')

tom_2 = tom
print(tom is tom_2) # True

tom_3 = copy.copy(tom)
print(tom is tom_3) # False
{% endcodeblock %}

第12行：class 的 instance 在使用`a = b`时也只会复制映射，不会复制一个新的 instance。
第15行：用 `copy()` 可以复制一个新的 instance。

**但这还没完**，真正的坑点在这里：上面的 copy 方法都是 shallow copy，shallow copy 只会**复制对象的一层**，对象内嵌套的对象都只会复制映射。

看下面的代码：

{% codeblock Python lang:Python %}
import copy

a = [[1, 2, 3], [4, 5, 6],[7, 8, 9]]
b = copy.copy(a)  # 等价于 b = list(a) 
print(a is b)   # False
print(a[0] is b[0])   # True

a[0][0] = 100
print(b)  # [[100, 2, 3], [4, 5, 6], [7, 8, 9]]
{% endcodeblock %}

这段代码里有趣的地方：
- 先创建了一个元素为 list 的 list，映射到`a`
- 然后复制出 `b`
- 第5行：证明 `a` 和 `b` 没有映射到同一个位置
- 第6行：证明 `a` 和 `b` 的元素映射到了同一个位置
- 第8和第9行：更直观地证明上面的观点

那么问题来了，如何让 `b` 从外到内地复制 `a`，而不只是复制“表面一层”呢？这就是 deep copy 的工作。

在介绍怎么用 python 做 deep copy 前，先看看 JavaScript 的 shallow copy。


### JavaScript

JavaScript 提供了两种 shallow copy 的方式：

- `Object.assign()`
- Spread Operator: `...`

先看 `Object.assign()`

{% codeblock JavaScript lang:JS mark:2 %}
var a = [1, 2, 3]
var b = Object.assign([], a)
a[0] = 100
console.log(b) // [1, 2, 3]
{% endcodeblock %}

再看 Spread Operator：

{% codeblock JavaScript lang:JS mark:2 %}
var a = [1, 2, 3]
var b = [...a]
a[0] = 100
console.log(b) // [1, 2, 3]
{% endcodeblock %}

对于 shallow copy，JS 也有跟 Python 一样的设定：只会复制第1层，如果 Object 内包含 Object，就只会复制映射。

{% codeblock JavaScript lang:JS %}
var a = [[1, 2, 3], [4, 5, 6]]
var b = Object.assign([], a)
a[0][0] = 100
console.log(b) // [ [ 100, 2, 3 ], [ 4, 5, 6 ] ]
{% endcodeblock %}

下面进入 Deep Copy 部分。

## Deep Copy

Deep copy 能够“完整复制”一个对象。

### Python

Python 可以使用 copy 包里的 `deepcopy()` 函数：

{% codeblock Python lang:Python mark:4 %}
import copy

a = [[1, 2, 3], [4, 5, 6],[7, 8, 9]]
b = copy.deepcopy(a)  
print(a is b)   # False
print(a[0] is b[0])   # False

a[0][0] = 100
print(b)  # [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
{% endcodeblock %}

上面的代码实现了 deep copy。

### JavaScript

JavaScript 没有 built-in module 里有类似 python 的 `deepcopy` 这样的函数，但主流第三方 module 里都提供了这个函数，我们不需要重复造轮子。

如果 Object 的 value 只有 primitive data，我们可以用一个简单的方法：`JSON.parse(JSON.stringify(object))`:

{% codeblock JavaScript lang:JS mark:2 %}
var a = [[1, 2, 3], [4, 5, 6]]
var b = JSON.parse(JSON.stringify(a))
a[0][0] = 100
console.log(b) // [ [ 1, 2, 3 ], [ 4, 5, 6 ] ]
{% endcodeblock %}

注意：如果 value 值是日期、regex 等等非 primitive data 的对象，这个方法不能使用。

参考下面的例子：

{% codeblock JavaScript lang:JS mark:2 %}
var person = {
  name: 'YuYu', 
  func: new Function()
}

var personCopy = JSON.parse(JSON.stringify(person))

console.log(person) // { name: 'YuYu', func: [Function: anonymous] }
console.log(personCopy) // { name: 'YuYu' }
{% endcodeblock %}

复制之后，`func` 这个 key-value 丢失了。

最保险的方法还是使用第三方工具，比如 lodash：

{% codeblock JavaScript lang:JS mark:11 %}
var cloneDeep = require('lodash/cloneDeep')

var person = {
  name: 'YuYu', 
  book: {
    title: 'JS is cool',
    pages: 300,
  },
  func: new Function()
}
var personCopy = cloneDeep(person)

console.log(person) 
console.log(personCopy) 
{% endcodeblock %}

上面这个例子里，嵌套的 object 以及 non-primitive 值也能保留。


## 参考

- [Shallow vs Deep Copying of Python Objects](https://realpython.com/copying-python-objects/)
- [How to deep clone a JavaScript object](https://flaviocopes.com/how-to-clone-javascript-object/)