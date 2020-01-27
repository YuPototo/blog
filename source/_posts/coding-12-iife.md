---
title: IIFE
date: 2019-03-15 18:58:56
categories:
  - Coding
tags:
  - JavaScript
---

IIFE (Immediate Invoked Function) 是 JavaScript 里一种快捷使用函数的方式。

这里做一个介绍。

<!-- more -->

## 创建函数的几种方法

JavaScript 提供了两种创建函数的方法：
- function declaration
- function expression

### function declaration

function declaration 创建函数的方法有点像 python 的 `def`，比较容易理解。

{% codeblock lang:js %}
function one() {
  console.log(1)
}

one()
// 1
{% endcodeblock %}

Python 可以用类似的方式创建函数：

{% codeblock lang:python %}
def one():
  print(1)
  
one()
// 1
{% endcodeblock %}


### function expression

另一种创建函数的方式是 function expression：

{% codeblock lang:js %}
const one = function() {
  console.log(1)
}

one()
// 1
{% endcodeblock %}


也可以用 arrow function：

{% codeblock lang:js %}
const one = () => console.log(1)

one()
// 1
{% endcodeblock %}

这种方法更像 R 语言：

{% codeblock lang:r %}
one <- function() {
  print(1)
}

one()
// 1
{% endcodeblock %}

### 两者的区别

function declaration 和 function expression 还有另一个区别： hoisting。

如果 function 用 declaration 的方式创建，我们可以在 function 创建前就调用：

{% codeblock lang:js %}
hoist()  //1

function hoist() {
  console.log('1')
}
{% endcodeblock %}

但 function expression 不允许这样用：

{% codeblock lang:js %}
hoist() // ReferenceError: Cannot access 'hoist' before initialization

const hoist = function() {
  console.log('1')
}
{% endcodeblock %}

## IIFE

创建函数是为了能够重复利用，但有的时候，我们不需要重复利用，有时一个函数只需要使用一次。

这个时候我们可以使用 IIFE， Immediate Invoked Function：

{% codeblock lang:js %}
(function() {
  console.log(1)
})()
// 1
{% endcodeblock %}

这时会直接打印出 1。

第一个 () 里其实是函数的定义，这里也可以使用箭头函数：

{% codeblock lang:js %}
(() => {
  console.log(1)
)()
// 1
{% endcodeblock %}

效果是一样的。

IIFE 是个有意思的概念，我自己还没碰到需要用 IIFE 的时候（或者是碰到了而我没想到），但在看别人代码的时候会碰到，所以在这里记录一下。
