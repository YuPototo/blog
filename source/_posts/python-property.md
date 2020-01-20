---
title: Python OOP 之 Property
date: 2020-01-20 09:54:12
categories:
  - Coding
tags:
  - Python
---

今天学习 Python OOP 编程的一个 interface：property。

OOP 编程有两个常用的概念：getter 和 setter。Property 装饰器是 Python 实现 getter 和 setter 的方式。

这篇文章用一个例子说明如何使用 property。

<!-- more -->

## 业务代码 v1

背景：初出茅庐的你，在为公司写一个记录员工信息的系统。

第1版系统里需要记录员工的姓名和收入。

员工可以用一个 class 表示，每个 instance 都包含员工的姓名和收入。

初步掌握 OOP 编程的你，很快完成了第1版代码：

{% codeblock lang:python %}
class Person():
  
  def __init__(self, name, income):
    self.name = name
    self.income = income
  
  def __repr__(self):
    return f'Person {self.name} with income {self.income}'

Tom = Person(name = 'Tom',
             income = 3000)
print(Tom) # Person Tom with income 3000
Tom.income = 5000
print(Tom.income)  # 5000
{% endcodeblock %}

然后老板提出了一个需求：我们是社会主义国家的企业，为了体现社会主义的先进性，员工工资只能增加，不能减少，你要把这个特点写到系统里。

这个神奇的需求当然很容易实现，不需要修改 `Person` 这个对象的代码，只需要在客户端用 POST 方法修改工资时做一个条件判断，这个需求大概只需要3分钟完成。

可是，你仔细思考了老板的话，如果只是在 Restful API 上做检查，岂不只是“表面文章”，社会主义的先进性，难道不是应该体现在 object 层面吗？

于是，你决定实现一个功能：修改 income 时，如果 income 不增加，会报错。

## 测试逻辑

除了实现老板的需求外，你还有另一个追求：你希望 `Person` 对象的 API 不会改变，也就是说，新版本的 `Person` 对象会实现对低版本的兼容。

完成新的`Person`后，你要做两个测试。

第1个测试：

{% codeblock lang:python %}
Tom = Person(name = 'Tom',
             income = 3000)
print(Tom) # Person Tom with income 3000
Tom.income = 5000
print(Tom.income)  # 5000
{% endcodeblock %}

上面这段代码的运行结果，要跟第1个版本一样。


第2个测试：

{% codeblock lang:python %}
Tom = Person(name = 'Tom',
             income = 3000)
Tom.income = 1000
{% endcodeblock %}

在运行到`Tom.income = 1000`时，会报错。

## 业务代码 v2

然后，你开始研究怎么实现新的需求。聪明的你很快发现，Property 是实现上述需求的好方法。

新版本代码如下：

{% codeblock lang:python %}
class Person():
  def __init__(self, name, income):
    self.name = name
    self._income = income  #第4行
  
  @property  #第6行
  def income(self):
    return self._income
  
  @income.setter  #第10行
  def income(self, value):
    if value < self.income :
      raise ValueError("收入不能减少")
    self._income = value
    
  def __repr__(self):
    return f'Person {self.name} with incom {self.income}'
{% endcodeblock %}

我们依次查看这段代码里的新东西。

### 第4行：`self._income = income `

这里用 \_ 表示 private variable。

Python 其实没有真正的 private variable。下面这段代码会正常返回 `_income`。

{% codeblock lang:python %}
class Person():
  def __init__(self, name, income):
    self.name = name
    self._income = income 

Tom = Person(name='Tom', income = 3000)
print(Tom._income) # 打印出 3000
{% endcodeblock %}

使用 \_ 表示 private variable，其实是一种约定俗成。Python 有非常多的约定俗成，比如 class 里的`self`，其实也可以用其他字段代替。

### 第6行：`@property`

`@property` 装饰器把 income 变成了一个 **getter**。

{% codeblock lang:python %}
# ....省略 class 的代码

Tom = Person(name='Tom', income = 3000)

print(Tom.income) # 打印出 3000
{% endcodeblock %}

此时我们不需要使用`Tom._income`，只需要像第1版一样用`Tom.income`就能获取 `_income`的内容。

如果没有`@property`装饰器，`income`就变成了一个**方法**，只能用`Tom.income()`的方式调用。


### 第10行：`@income.setter`

这是 setter 装饰器，把下面的函数变成了 income 的 setter。

setter 这个名称很直观，我们会在满足条件后设置 income 的值。

在之后的函数里，我们检查了新的值是否小于原先的 income，如果不满足条件，我们就 raise 一个错误。

## 小结

一顿操作后，第2版的业务代码使用 property 实现了老板的需求和 API 的兼容。非常完美。

## 参考

- [Geeksforgeeks: Python | property() function](https://www.geeksforgeeks.org/python-property-function/)
- [Programiz : Python @property](https://www.programiz.com/python-programming/property)
