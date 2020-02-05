---
title: Pytest 的项目结构
date: 2019-12-01 04:24:00
categories:
  - 开发
tags:
  - Python
  - TDD
---

Pytest 是 python 最好用的 test 工具。

这篇文章总结一个使用 pytest 的项目应该怎么设计文件夹结构。

<!-- more -->

## 文件结构

《Python Testing with Pytest》这本书推荐使用如下结构：

project root folder:

- ...
- src
  - ...
- tests
  - conftest.py
  - pytest.ini
  - func
    - \_\_init\_\_.py
    - test_xx.py
    - ...
  - unit
    - \_\_init\_\_.py
    - test_xx.py
    - ...


### tests 文件夹

所有测试都放在 tests 文件夹里。如果使用 VS Code 的话，有一步设置就是告诉 workspace 测试文件夹是哪个，这时就可以选择 tests 文件夹了。

上面的 tests 文件夹里没有 \_\_init\_\_.py，[根据 VS Code 的官网](https://code.visualstudio.com/docs/python/testing)，test discovery 可能需要在这里加入一个 \_\_init\_\_.py。


### conftest.py

`conftest.py` 用于存放整个 tests 需要的 fixture。之后我会写一篇博文总结 fixture。

test 文件会自动引用 `conftest.py` 里的内容，不需要再 import。

test 的 setup 和 teardown 一般都在 `conftest.py` 里发生。典型的 setup：连接数据库。典型的 teardown：关闭跟数据库的连接。

不需要使用 fixture 的项目，可以忽略这个文件。


### pytest.ini

`pytest.ini` 设置 pytest configuration 的地方。相关问题包括但不限于：测试文件所在的文件夹、测试文件的命名规则、忽略哪个子文件夹等等。

pytest 支持的设置可以参考[官方文档](https://docs.pytest.org/en/latest/customize.html)。

如果使用 VS Code，相关设置都已经在 workspace 设置里搞定了。如果是初级玩家，可以忽略这个文件。

### Functional Tests

Functional tests 都放在 func 文件夹里。

测试可以分为至少两种：functional test 和 unit test。

Functional test 测试的是 APP 是否按照产品文档的需求实现输入和相应的输出。Functional test 不在乎 APP 内的具体实现逻辑，只要输入和输出结果符合预期，就算通过测试。

更多可以[参考这篇文章](http://softwaretestingfundamentals.com/functional-testing/)。

### Unit Tests

Unit tests 都放在 unit 文件夹里。

Unit tests 指最小的测试单位。这里不只涉及测试，还涉及到软件开发的问题，即如何将功能单位最小化。

下面的代码就创造并测试了一个叫`plus()`的函数。这个函数的功能只有一个：做加法。这是一个可以 unit test 的对象。

{% codeblock lang:python %}
def plus(a, b):
  return a + b

def test_plus():
  """ plus() should return the sum of two numbers """
  a, b = 1, 2
  expected = 3
  assert plus(a, b) == expected
{% endcodeblock %}

再看另一个函数 `plus_and_square()`，它先求和再求一个二次方。我们仍然可以针对它写一个 unit test。

{% codeblock lang:python %}
def plus_and_square(a, b):
  return (a + b) ** 2

def test_plus_and_square():
  """ plus_and_square() should return the square of the sum of two numbers """
  a, b = 2, 1
  expected = 9
  assert plus_and_square(a, b) == expected
{% endcodeblock %}

但从功能上，我认为 `plus_and_square()` 不是一个unit，它应该被分成两个函数：`plus()` 和 `square()`。


### test_xx.py

最后一个问题是 `test_xx.py` 放什么呢？当然是测试用例。不过更进一步但问题是，该把哪些 test case 放进同一个 `test_xx.py` 文件里呢？

我暂时没在《Python Testing with Pytest》里找到答案。Google 到[一篇文章](https://medium.com/worldsensing-techblog/tips-and-tricks-for-unit-tests-b35af5ba79b1)，他的做法是根据代码逻辑所属的类型来区分，我觉得太复杂了。

我决定暂时采用跟 src 文件一一对应的方式组织并命名 `test_xx.py` 文件，感觉这样更方便对照测试代码和业务代码。

