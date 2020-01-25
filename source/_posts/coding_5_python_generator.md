---
title: Python 的 Generator
date: 2018-11-17 21:52:13
categories:
  - Coding
tags:
  - Python
---

问题：x 是一个由数字组成的 list，我们想求出每个元素的平方。

一个解决方法是：

{% codeblock lang:python %}
def square_numbers(nums):
    result = []
    for i in nums:
        result.append(i * i)
    return result

my_nums = square_numbers([1,2,3,4])
print(my_nums) # [1, 4, 9, 16]
{% endcodeblock %}

这是个正确的答案，但不完美。我们可以使用 generator 写出更简洁的答案：

{% codeblock lang:python %}
def square_numbers(nums):
    for i in nums:
        yield(i * i)

my_nums = square_numbers([1,2,3,4])
print(list(my_nums))
{% endcodeblock %}

<!-- more -->

## generator 函数

普通的函数使用 `return` 返回结果，**generator 函数**使用 `yield` 返回结果。

generator 函数可以使用 `next()` 获取下一个返回值。

{% codeblock lang:python %}
def dumb_generator():
  yield 1
  yield 2
  yield 3

a = dumb_generator()

print(next(a)) # 1
print(next(a)) # 2
print(next(a)) # 3
print(next(a)) # 报错：StopIteration
{% endcodeblock %}

上面这段代码会在运行`next(a)`依次返回1、2、3，然后报错。

我们可以直接把 generator 函数的返回对象用在 for loop 里：


{% codeblock lang:python %}
def dumb_generator():
  yield 1
  yield 2
  yield 3

a = dumb_generator()

for i in a:
  print(i)
{% endcodeblock %}

for loop 在完成循环后会自动停止，不会报错。

## generator comprehension

文章最开始的问题，其实可以用 list comprehension 快速解决：

{% codeblock lang:python %}
my_nums = [x * x for x in [1,2,3,4]]
{% endcodeblock %}

generator 也可以用 comprehension 的形式快速生成：

{% codeblock lang:python %}
my_nums = (x * x for x in [1,2,3,4]) # 这是一个 generator
{% endcodeblock %}

## 为什么使用 generator

除了方便之外，generator 还有什么好处呢？最大的好处是，节约内存。

假设生成从1到100万的 list，这个 list 会被存在内存里，占据大量空间。
如果生成从1到100万的 generator，内存占用量几乎可以忽略不计。

另外，generator 还可以用来表达数学上的无穷数列，比如 [Finabocci 数列](https://en.wikipedia.org/wiki/Fibonacci_number)。

{% codeblock lang:python %}
def fib():
    a, b = 0, 1
    while 1:
        yield a
        a, b = b, a + b

for i in fib():
  print(i)
  if i > 1000:
    break
{% endcodeblock %}

上面的代码会打印出从0开始的 Finabocchi 数列，直到数列大于1000（第18个数字就会大于1000）。
