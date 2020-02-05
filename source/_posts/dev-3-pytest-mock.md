---
title: 在 pytest 里使用 mock
date: 2019-12-02 04:24:00
categories:
  - 开发
tags:
  - Python
  - TDD
---

这遍文章研究一个问题：如何在 pytest 使用 mock。

<!-- more -->

## mock 是什么

首先，mock 是什么呢。Mock 是一个我们可以“替代品”，用来模仿或代替现实环境中的对象。

比如，我们有一段代码需要做出 http request，成功时返回一个信息，错误时返回另一个信息。

在一个单元测试里，我们不应该去真的执行这个 request，因为执行这个 request 要么成功要么失败，我们没法完全测试两种情况。而且，执行 request 存在不确定性：我们不知道需要多久才能获得 response。

这个时候，就可以用一个 mock 去代替真正的 request。

### Python 的 mock library

Mock 是 unittest 里的一个 library。虽然我使用的是 pytest，但如果想要 mock，还是得使用 mock library。这一部分总结 Mock object。假定下面的代码都使用 pytest 作为 test 工具。

mock 是一个能做任何事情的神奇对象。

{% codeblock lang:python %}
from unittest.mock import Mock

mock = Mock()
mock.name = 'yu'

def test_mock():
  assert mock.name == 'yu'
{% endcodeblock %}

我在上面的代码里做了如下事情：

- 创建了一个 mock instance，将其命名为 mock
- 让 mock 有一个 attribute `name`，且值为`yu`
- 做一个单元测试，测试 mock 确实有个 attribute `name` 且等于 `yu`
- 这个测试会通过

上面这个例子好像比较蠢，似乎没有必要。

下面给一个更真实的例子。我们写了一个函数，判断今天是不是工作日：

{% codeblock lang:python %}
import datetime

def is_weekday():
    today = datetime.datetime.today()
    return (0 <= today.weekday() < 5)
{% endcodeblock %}

这个函数里用到了 datetime package 里的 today 函数。根据测试日期的不同，我们可能会得到不同的结果。这不是我们想在单元测试里看到的。

这个时候就可以使用 mock，让 mock “替代” datetime。

测试代码如下：

{% codeblock lang:python %}
from unittest.mock import Mock

# Save a couple of test days
tuesday = datetime.datetime(year=2019, month=1, day=1)
saturday = datetime.datetime(year=2019, month=1, day=5)

datetime = Mock()

def test_is_weekday():
  datetime.datetime.today.return_value = tuesday
  assert is_weekday()
  
  datetime.datetime.today.return_value = saturday
  assert not is_weekday()
{% endcodeblock %}

代码说明：

- 第4和第5行创建了两个用于测试的日期
- 第7行把 `datetime` 变成了一个 mock，之后运行 `is_weekday()` 时不会再调用真正的 `datetime`，而是会调用这个 mock
- 第10行把 `datetime.datetime.today()` 的返回值设定为之前定义的 tuesday
- 第11行运行`is_weekday()`，这时里面的 `datetime.datetime.today()` 不会返回真的 `today`，而是会返回 tuesday
- 第13和14行重复上面的操作，只是把 mock 返回的对象变为 saturday

这段测试代码完美避免了 today 跟今天的具体日期相关的问题。

### 使用 patch

`patch()` 是 mock library 里的一个方法，可以直接用来 mock 一个 object。

可以把上面的代码用`patch()`改写：

{% codeblock lang:python %}
from unittest.mock import patch

# Save a couple of test days
tuesday = datetime.datetime(year=2019, month=1, day=1)
saturday = datetime.datetime(year=2019, month=1, day=5)

def test_is_weekday():
  with patch('datetime.datetime') as mock_datetime:
    mock_datetime.today.return_value = tuesday
    assert is_weekday()
    
    mock_datetime.today.return_value = saturday
    assert not is_weekday()
{% endcodeblock %}

我们直接 patch 了整个 `datetime.datetime` 对象，然后就能在 context manager 里随意 mock 它了。

如果不喜欢 context manager，还可以使用 patch 的 decorator 版本：

{% codeblock lang:python %}
@patch('datetime.datetime')
def test_is_weekday(mock_dateimte):
  mock_datetime.today.return_value = tuesday
  assert is_weekday()
  
  mock_datetime.today.return_value = saturday
  assert not is_weekday()
{% endcodeblock %}

## pytest-mock

pytest 有一个叫 [pytest-mock 的 plugin](https://github.com/pytest-dev/pytest-mock)，它是 mock package 的封装。

pytest-mock 提供了一个叫 mocker 的 fixture，它有跟 mock.patch 一样的 API。

如果使用 pytest-mock，上面那段关于 `is_weekday()` 的测试代码可以写作：

{% codeblock lang:python %}
def test_is_weekday(mocker):
  mock_datetime = mocker.patch('datetime.datetime')
  mock_datetime.today.return_value = tuesday
  assert is_weekday()

  mock_datetime.today.return_value = saturday
  assert not is_weekday()
{% endcodeblock %}

只要安装了 pytest-mock，就可以无需引用直接使用 mocker 对象。

### 一个例子

假设我们有如下两个函数：

{% codeblock lang:python %}
# arithmetic.py
def func_a(arg):
  return arg * 2

def func_b(arg):
  return func_a(arg) - 1
{% endcodeblock %}

其中 func_b 调用了 func_a。

单元测试里，我们希望每次只测试这个函数自身，也就是说，我们在测试 func_b 时，不希望 func_b 受到 func_a 的影响，我们最好能够假定 func_a 是正常运行的。

这时就可以使用 patch

{% codeblock lang:python %}
# test_arithmetic.py
import arithmetic 

def test_func_a():
  assert arithmetic.func_a(2) == 4
  
def test_func_b_unit(mocker):
  mock_func_a = mocker.patch('arithmetic.func_a')
  mock_func_a.return_value = 4
  result = arithmetic.func_b(2)
  expected = 3
  mock_func_a.assert_called_with(2) 
  assert result == expected
{% endcodeblock %}

第12行能证明 func_a 被成功“调用”，且参数是 2。但我们并没有真正使用 func_a。这里的关键点是，需要 `import arithmetic`，不能`from arithmetic import *`。原因嘛，估计跟 patch 的 API 有关，我先不管了。

如果不喜欢使用 pytest-mocker，可以用 `mock.patch` 来写测试：
{% codeblock lang:python %}
@patch('arithmetic.func_a')
def test_func_b_unit(mock_func_a):
  mock_func_a.return_value = 4
  result = arithmetic.func_b(2)
  expected = 3
  assert result == expected
  mock_func_a.assert_called_with(2)
{% endcodeblock %}

上面这个测试真正的单元测试。下面是一个虚假的单元测试，因为 func_b 的测试结果依赖于 func_a。

{% codeblock lang:python %}
import arithmetic 

def test_func_a():
  assert arithmetic.func_a(2) == 4

def test_func_b():
  assert arithmetic.func_b(2) == 3
{% endcodeblock %}


## 更多阅读

- [unittest.mock — mock object library](https://docs.python.org/3/library/unittest.mock.html)
- [Understanding the Python Mock Object Library](https://realpython.com/python-mock-library/)
- [pytest-mock](https://github.com/pytest-dev/pytest-mock)