---
title: Promise 系列之2：是什么
date: 2019-01-20 18:58:56
categories:
  - Coding
tags:
  - JavaScript
---


上一篇说明了为什么需要 promise，这一篇介绍 promise 是什么。

可以用下面几句话来描述 promise：
- Promise 是一个 object
- 它承诺会在未来某个时间点返回一个值
- 它最后要么返回这个承诺的值，要么告诉我们为什么它不能履行承诺

可以拿生活中的例子进一步举例：

- 我在月初向老婆承诺，月底发奖金了就给她换一部手机
- 我开始干各种其他事情，就像忘掉了这个承诺
- 月底到了，我拿到了奖金，我想起了这个承诺，于是给老婆换了新手机
- 或者：月底到了，我没拿到奖金，我只能跟老婆说明原因然后道歉

下面具体描述 promise 的机制。

<!-- more -->

## 初识 Promise 

### construtor
promise 可以用 Promise contructor 创建：

{% codeblock lang:JS %}
const promise = new Promise( //函数 )
{% endcodeblock %}

`Promsie()`括号内还需要传入一个函数。

### constructor 里的函数

需要传入的函数形式如下：

{% codeblock lang:JS %}
const innerFunc = (resolve, reject) => {
  const num = 0.6
  if (num > 0.5) {
    resolve()
  } else {
    reject()
  }
}
{% endcodeblock %}

函数有两个参数：resolve 和 reject。我们可以设置满足哪些条件时 resolve，否则 reject。

在上面的函数，如果 num 大于 0.5，我们返回 resolve，否则 reject。

### 完整的 promise

把1.2的函数传入 Promise constructor，就获得了一个 promise。

{% codeblock lang:JS %}
const promise = new Promise(innerFunc)
{% endcodeblock %}

上面成功创建了一个 promise。

### resolve 之后

接下来我们可以在后面的代码里使用 promise，并约定在 resolve 时运行某一段代码：

{% codeblock lang:JS %}
promise.then(() => console.log('resolved'))
{% endcodeblock %}

我们可以用`.then()` 连接 promsie，表示"resolve 之后运行 then 里的代码"，then 里的代码需要是个回调函数。我们在上面打印了'resolved'。

一般把 `.then()` 放在第2行：

{% codeblock lang:JS %}
promise
  .then(() => console.log('resolved'))
{% endcodeblock %}

### 如果 reject

我们可能想在 reject 之后运行一段代码，这个时候可以用`.catch()`。

{% codeblock lang:JS %}
promise
  .then(() => console.log('resolved'))
  .catch(() => console.log('reject'))
{% endcodeblock %}


## then

Promise 能够自带了一个`then()`方法。

在上面的例子里，我在`then`里使用了一个回调函数，表示`resolve`之后会运行这个函数。这是一般的用法。

实际上，`then` 可以接受两个回调函数：
- onFulfilled: resolve 之后运行的回调函数
- onRejected: reject 之后运行的回调函数

第2个回调函数都是 optional。

### then 使用2个回调函数

下面在 then 里使用两个回调函数，第1个函数是 resolve 之后运行的函数，第2个是 reject 之后运行的函数。

{% codeblock lang:JS %}
const promise = new Promise((resolve, reject) => {
  // 省略
})

promise
  .then(
    () => console.log('resolve'),
    () => console.log('reject')
  )
{% endcodeblock %}

### then 返回一个 promise

个人认为最让人困扰的设定是：then 会返回一个 promise。

证明如下：
{% codeblock lang:JS %}
const promise = new Promise((resolve, reject) => {
  resolve()
})

var a = promise
  .then(() => 1)

console.log(a) // Promise { <pending> }
{% endcodeblock %}

那么，这个 promise 的 resolve 和 reject 分别是什么呢？我们可以验证。既然它是个 promise，那么它也可以用接着用 then。

{% codeblock lang:JS %}
const promise = new Promise((resolve, reject) => {
  resolve()
})

var a = promise
  .then(() => 1)

a.then(res => console.log(res)) // 1
{% endcodeblock %}

屏幕打印出`1`，说明前一个 then 返回的 promise 的 resolve 值是 1，也就是前一个 then 里回调函数的返回值。

### then 可以链式连接

既然 then 返回的也是 promise，那就可以直接在 then 之后用 then：

{% codeblock lang:JS %}
const promise = new Promise((resolve, reject) => {
  resolve()
})

promise
  .then(() => 1)
  .then(res => console.log(res))
{% endcodeblock %}

上面的代码里，第1个 then 返回了一个 `resolve(1)` 的 promise，第2个 then 打印这个 1。

### 如果 then 的回调函数里不返回值

上一个例子里，then 里的函数返回了值，这个值就变成了新 promise 的 resolve 值。如果 then 里的函数不返回值呢？

{% codeblock lang:JS %}
const promise = new Promise((resolve, reject) => {
  resolve()
})

promise
  .then(() => console.log(1))
  .then(res => console.log(res))

// 1
// undefined
{% endcodeblock %}

第6行的 then 里的回调函数没有返回值，我们在第7行打印新 promise 的resolve 值。结果：undefined。

### 如果 then 的回调函数报错

如果在 then 的回调函数里 throw error，

{% codeblock lang:JS %}
const promise = new Promise((resolve, reject) => {
  resolve()
})

promise
  .then(() => {
    throw new Error('Sorry, this is error')
  })
  .then(res => console.log(res))
  .catch(err => console.log(err))
// Error: Sorry, this is error
{% endcodeblock %}

第7行里 throw 了 error，这个 then 返回的 promise 会 reject。Reject 的值是错误信息。

### 如果 then 的回调函数返回 promise

这个是相对复杂的。

先看代码：

{% codeblock lang:JS %}
const promise1 = new Promise((resolve, reject) => {
  resolve()
})

const promise2 = new Promise((resolve, reject) => {
  resolve('promise resolve')
})

const promise3 = promise1.then(() => promise2)

promise3
  .then(res => console.log(res))
  .catch(err => console.log(err))
{% endcodeblock %}

第1个 promise1 会 resolve。

第2个 promise2 会 resolve 得到 'promise resolve'，它在 `promise1.then()` 里的回调函数返回。

第3个 promise3 是`promise1.then()`得到的回调函数。

问题来了，promise3 的 resolve 和 reject 分别是什么呢？

答案：是 promise2 的 resolve 和 reject。

上面的代码会打印出`promise resolve`。

如果把 promise2 的代码修改成：

{% codeblock lang:JS %}
const promise2 = new Promise((resolve, reject) => {
  reject('promise reject')
})
{% endcodeblock %}

前一段代码就会打印`promise reject`。
