---
title: JavaScript 里的 callback
date: 2020-01-25 17:40:13
categories:
  - Coding
tags:
  - JavaScript
---

在 JavaScript 里，函数是 object，所以 function 的参数可以是 function。如果一个 function 被作为参数传入，它就被称为 callback function。

这篇文章是 callback 的学习笔记。

<!-- more -->

## 一个例子

### 传入函数

按照 callback 的定义，我创建并执行一个需要 callback 的 function：

{% codeblock lang:JS mark:5,9%}
function someFunc() {
  console.log('this is some func')
}

function funcWithCallback(callback) {
  console.log('this is function funcWithCallback')

  console.log('before callback')
  callback()
  console.log('after callback')
}

funcWithCallback(someFunc)

//this is function funcWithCallback
//becore callback
//this is some func
//after callback
{% endcodeblock %}

上面这段代码里：
- `someFunc` 是个定义在 global scope 的函数，会打印一串文字
- `funcWithCallback` 是个需要传入 callback 的函数，注意在 argument 里有一个 `callback` 参数
- 在第9行，用 `callback()` 运行这个函数
- 最后我们给 `funcWithCallback` 传入 `someFunc` ，打印运行结果。

注意，上面的参数名 `callback` 只是约定俗成，改成别的单词如 `abcdef` 也是可以的，只要第9行同样改成 `abcdef`。因为，运行一个函数的方式，就是在函数名后加`()`。

### 在运行函数时直接定义 callback

刚才我们首先定义了 `someFunc`，然后再在 `funcWithCallback` 里使用它。另外一个常见的做法是，在需要 callback 的地方直接定义一个函数。

{% codeblock lang:JS mark:9-11 %}
function funcWithCallback(callback) {
  console.log('this is function funcWithCallback')

  console.log('before callback')
  callback()
  console.log('after callback')
}

funcWithCallback(function() {
  console.log('this is callback')
})

//this is function funcWithCallback
//before callback
//this is callback
//after callback
{% endcodeblock %}

上面这段代码里，我在运行 `funcWithCallback` 时直接定义了一个函数作为 callback。

这个做法可以用箭头函数轻易改写：

{% codeblock lang:JS %}
funcWithCallback(() => console.log('this is callback'))
{% endcodeblock %}

### setTimeout

JavaScript 里最经典的使用 callback 的函数是 `setTimeout(func, delay, ...args)`，用于推迟若干时间后执行 callback 函数。各个参数的含义是：
- func: callback 函数
- delay: 推迟执行的时间
- ...args: func 的参数
{% codeblock lang:JS %}
function sayHi(name) {
  console.log(`Hello, ${name}`)
}

setTimeout(sayHi, 1000, 'Pototo')
{% endcodeblock %}


## 为什么要使用 callback

callback 主要用于异步场景。比如从某个 API 获取数据，在获取到数据之前，我们无法进行下一步；获取到数据后，我们就能调用 callback 执行下一步操作。

### 伪代码

先看**伪代码**如下：

{% codeblock lang:JS %}
function dealWithData() {
  // some operation
}

function getData(url, callback) {
  // get data from url
  // callback()
}

getData('some-url', dealWithData)
{% endcodeblock %}

不习惯异步编程的同学可能会问，为什么不写成下面的形式呢？（还是伪代码）：

{% codeblock lang:JS %}
function dealWithData(data) {
  // some operation
}

function getData(url) {
  // get data from url, return data
}

var data = getData('some-url')
dealWithData(data)
{% endcodeblock %}

因为 `getData` 这个函数会一旦执行，会发出一个 GET request，这个 request 发出去后，我们无法确定需要多长时间执行（甚至不确定能不能正确取回数据）。用第2种写法，计算机就会等待结果的返回。假定我们在`getData`设定了最长等待时间是10秒，那么这10秒内，计算机什么也做不了。这是对计算资源的浪费，用户体验也很差。

如果使用 callback 做法，GET request 发出后，计算机就能去先做其他事情，直到获取 GET 的结果后，再继续执行 callback。

### 模拟 http request

可以用下面的代码模拟 http request 需要等待的特征：

{% codeblock lang:JS mark:8,11 %}
function getData(url, callback) {
  console.log('get data from url: ' + url)
  setTimeout(callback, 1000)
}

getData('www.twitter.com/data', () => console.log('working with data'))

console.log('do this first while waiting for data return')

//get data from url: www.twitter.com/data
//do this first while waiting for data return
//working with data
{% endcodeblock %}

我在这里用1秒钟的延迟模拟获取数据的耗时。

上面这段代码的执行顺序是：
- 去“获取数据”（假装获取数据）
- 先运行第8行的代码
- 1秒后，运行 callback

第8行代码在异步结果返回前就得到了运行，这就是 callback 的价值。
