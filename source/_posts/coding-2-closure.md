---
title: 'Closure: 以 Python 和 JavaScript 为例'
date: 2018-08-05 00:17:33
categories:
  - Coding
tags:
  - Python
  - JavaScript
---

计算机语言里有个常用的概念，叫 closure（闭包）。这里用 python 和 JavaScript 作为对照例子，解释一下 closure 的含义。

先看一个问题，下面一段 python 代码，会在 terminal 上打印什么呢？

{% codeblock lang:python %}
a = 2
def outer():
  a = 1
  def inner():
    print(a)
  return inner

func = outer()
func()
{% endcodeblock %}

<!-- more -->

上面的代码出现了若干次 a，第1个 a 在“最外面”，第2个 a 在 outer 函数里，第3个 a 出现在 inner 函数里，而 inner 函数又在 outer 函数里。

上面这段话可以用 scope 的概念重新描述：
- 第1个 a 在 global scope 中，整段代码都可以使用
- 第2个 a 在 outer 函数的 scope 中
- 第3个 a 不是个变量，是对变量的使用，那么它会使用第1个a、还是第2个a、还是报错呢？

答案：terminal 回打印出 1，也就是说，inner 函数使用了 outer 函数里的 a。

这里有两个值得深究的地方。我们一个个看。

## 1. 向上查询

先看下面这段代码。

{% codeblock lang:python %}
a = 1

def func():
  print(a)

func()
{% endcodeblock %}

在运行 func 函数时，计算机会现在 func 的 scope 寻找 a 变量，但我们没有定义 a 变量，这时计算机会“向上一层”查询，上一层就是 global scope，我们在上一层定义了 a 并令它为 1，计算机就认为自己找到了 a，于是打印出 a 的内容：1。

同样一段代码，可以在 JavaScript 中轻松实现：

{% codeblock lang:JavaScript %}
var a = 1

function myFunc() {
  console.log(a);
}

myFunc()
{% endcodeblock %}


R 中也有类似的设计：

{% codeblock lang:r %}
a = 1

my_function <- function() {
  print(a)
}

my_function()
{% endcodeblock %}

我们运行上面3段代码时，都会得到 1。

在最开始的代码段里，上一层的 a 在 outer 函数里，是1，此时计算机不再往上寻找 a，所以就打印出了 1。如果我们把 outer 函数里的 `a = 1` 删掉呢？此时代码如下：

{% codeblock lang:python %}
a = 2
def outer():
  def inner():
    print(a)
  return inner

func = outer()
func()
{% endcodeblock %}

因为找不到计算机在 outer 里找不到 a，它继续往上寻找，在 global scope 里找到了 a，于是打印出了 2。

这样的设计乍看之下不太友好：如果我们不小心在函数里使用了某个 global scope 的变量，计算机就不会报错，而是会继续运行程序。这岂不是不利于代码的稳定性。

虽然有这个潜在风险，很多语言还是选择了这个设计，自然是因为这个设计是有价值的。价值在哪儿呢？这就涉及到示例代码中第2个值得深究的地方了。


## 2. 函数可以返回函数 

在 python、R 和 JavaScript 里，函数都可以返回函数。

{% codeblock lang:python %}
def outer():
  a = 1
  def inner():
    print(a)
  return inner

func = outer()
func()
{% endcodeblock %}

我们的 outer 函数的 return 值是一个 inner 函数。
通过 `func = outer()`，我们让 func 变成了 inner 函数。

这里值得注意的地方是，我们 return 的明明只是 inner 函数，代码里只有 `print(a)`这行，为什么这个 inner 函数会记住自己所在的 scope 里有个 a 变量呢？这就是 closure 这个概念了。

当我们在 outer 函数里返回 inner 函数时，与 inner 函数在同一个 scope 里面的变量也会被“包”进 inner 函数中，变成 inner 函数的 private variable。

这是一个非常有用的概念。

## closure 的应用

我最喜欢的 closure 应用场景，是批量生成函数。

问题场景：我需要生成一组幂函数的值，指数分别为 2、3、4, x 分别是 -10 到 10 之间的整数。

在我不知道 closure 时，我会想生成一个带有2个参数的函数，然后使用 list comprehension。

{% codeblock lang:python %}
def power_func(x, power):
  return x ** power

[ power_func(i, 2) for i in list(range(-10, 10))]
[ power_func(i, 3) for i in list(range(-10, 10))]
[ power_func(i, 4) for i in list(range(-10, 10))]
{% endcodeblock %}

这是一个看上去简单的解答，但并不是一个代码层面更“好”的解答。因为，有可能我们想要在多种场合重复使用某几个幂指数的函数，比如幂指数为2的函数，可能在我们的计算里非常常用，这时最好的做法是生成一个幂指数为2的函数，然后重复使用它。

这时就需要使用 closure了。如果使用 closure，上面的问题会这样解答：

{% codeblock lang:python %}
def power_func(power):
  def inner_func(x):
    return x ** power
  return inner_func

power_two = power_func(2)
power_three = power_func(3)
power_four = power_func(4)


[ power_two(i) for i in list(range(-10, 10))]
[ power_three(i) for i in list(range(-10, 10))]
[ power_four(i) for i in list(range(-10, 10))]
{% endcodeblock %}

这段代码看上去更长，但从“思路”上其实方便，代码的可读性也强了非常多。

类似的思考方式其实是写代码到一定阶段后必然会碰到的：如何抽象地设计出一个生成函数的函数，让我们的代码可读性更高，更加 DRY（ don't repeat yourself ）。

同样一段代码，如果使用 JavaScript，可以这样写：

{% codeblock lang:JavaScript %}
function powerFunc(power) {
  function innerFunc(x) {
    return Math.pow(x, power)
  }
  return innerFunc
}


var powerTwo = powerFunc(2)
var powerThree = powerFunc(3)
var powerFour = powerFunc(4)

var numRange = Array.from(new Array(21), (x,i) => i - 10)

console.log(numRange.map(el => powerTwo(el)))
console.log(numRange.map(el => powerThree(el)))
console.log(numRange.map(el => powerFour(el)))
{% endcodeblock %}


## JavaScript 的连续箭头函数

理解了 closure 的概念后，下面这段 JavaScript 代码就很好理解了：

{% codeblock lang:JavaScript %}
const makeAddFunc = adder => x => x + adder
{% endcodeblock %}

这其实就是 closure 的箭头函数写法，这里连续使用了两个箭头函数。完整写法如下：

{% codeblock lang:JavaScript %}
function makeAddFunc(adder) {
  function innerFunc(x) {
    return x + adder
  }
  return innerFunc
}
{% endcodeblock %}

我们可以用上面的 makeAddFunc 生成若干自己需要的函数：

{% codeblock lang:JavaScript %}
addTwo = makeAddFunc(2)
addThree = makeAddFunc(3)

console.log(addTwo(5)) // print 7
console.log(addThree(5)) // print 8
{% endcodeblock %}



## 小结

个人经验来说，closure 是一个“分水岭”，区分开新手 coder 和中级 coder。

如果我们对 closure 足够熟悉，我们会发现某些特定的场合简直是为 closure 天然而生。学会在这些场合使用 closure，会让我们的代码质量上一个台阶。
