---
title: Promise 系列之3：进阶
date: 2019-01-30 18:58:56
categories:
  - Coding
tags:
  - JavaScript
---

上一篇简单介绍了 promise 和 then 方法。

这一篇介绍几个进阶话题：
- Promise 的原理
- Promise 的 constructor methods
- Promise 的 prototype methods

<!-- more -->

## Promise 的原理

Promise 有三种状态：
1. pending：最初状态
1. fulfilled：resolve 对应的状态，会返回一个值
1. rejected：reject 对应的状态，会返回错误原因

当 Promise 进入 fulfilled 或 rejected 状态，又被称为 settled。

{% codeblock lang:JS %}
const promise = new Promise((resolve, reject) => {
  resolve()
})

console.log(promise)
// Promise { undefined }
{% endcodeblock %}

上面显示 promise 处于 undefined 状态。

JavaScript 不支持返回 Promise 的状态，我们要把 Promise 当作一个黑箱子，用 then 方法 Promise 处理 fufilled 或 rejected 之后的事情。

## Promise 的 constructor methods

### Promise.resolve()

`Promise.resolve(value)` 是一个快捷方法，会得到一个 resolve 值为 value 的 promise。

原始方法：用`new`创建一个 promise：

{% codeblock lang:JS %}
const promise = new Promise((resolve, reject) =>{
  resolve('resolve value')
})

promise
  .then(res => console.log(res))
{% endcodeblock %}

改进版，跟上面一个效果：用`Promise.resolve(value)`

{% codeblock lang:JS %}
const promise = Promise.resolve('resolve value')

promise
  .then(res => console.log(res))
{% endcodeblock %}

还可以更精简一些：

{% codeblock lang:JS %}
Promise.resolve('resolve value')
  .then(res => console.log(res))
{% endcodeblock %}


### Promise.reject()

`Promise.reject(value)` 也是快捷方法，是`Promise.resovle(value)`的 reject 版。

原始方法：用`new`创建一个 promise：

{% codeblock lang:JS %}
Promise.reject('reject value')
  .then(res => console.log(res))
  .catch(err => console.log(`Error: ${err}`))
// Error: reject value.
{% endcodeblock %}

### Promise.all()

`Promise.all()` 需要一个 iterable 作为参数，iterable 内包含一组 promise。

`Promise.all()` 返回一个新的 promise，如果它参数内的 promise 全部 resolve，它就返回所有的 resolve 值。

{% codeblock lang:JS %}
const promise1 = Promise.resolve(1)
const promise2 = Promise.resolve(2)
const promise3 = Promise.resolve('b')

Promise.all([promise1, promise2, promise3])
  .then(
    res => console.log(res)
  )
// [1, 2, 'b']
{% endcodeblock %}

如果参数内的 promise 出现 reject，`Promise.all()` 也会 reject，并以参数内 promise 的 reject 的值作为自己的 reject 值。

{% codeblock lang:JS %}
const promise1 = Promise.resolve(1)
const promise2 = Promise.reject('reject info One')
const promise3 = Promise.reject('reject info Two')

Promise.all([promise1, promise2, promise3])
  .then(
    res => console.log(res)
  )
  .catch(
    err => console.log(err)
  )

// reject info One
{% endcodeblock %}

上面有两个会 reject 的 promise，`Promise.all()` 会以第1个 reject 的值作为返回值。


### Promise.race()

`Promise.race()` 也接受一个 iterable 作为参数，它会 resolve 或 reject 最快进入 settled 状态的值。

{% codeblock lang:JS %}
const promise1 = new Promise((resolve, reject) => {
  setTimeout(resolve, 500, 'one')
})

const promise2 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 'two')
})

Promise.race([promise1, promise2])
  .then(res => console.log(res))

// 'two'
{% endcodeblock %}

因为第2个 promise 比第1个 promise 更快 settle，所以`Promise.race()`以第2个 promise 的 resolve 值作为自己的 resolve 值。

## Promise 的 prototype methods

Promise 有3个 prototype methods：
- then
- catch
- finally

上一篇已经详细介绍了 `then()` 。

### catch()

`catch()` 会在 promise 被 reject 时运行，除此之外，`catch()`也会返回一个 promise。

{% codeblock lang:JS %}
Promise.reject('reject value')
  .catch(err => `catch ${err}`)
  .then(res => console.log('resolve value is :' + res))
// resolve value is :catch reject value
{% endcodeblock %}

第2行的 `catch()`会返回一个新的promise，对应的 resolve 值是回调函数的返回值。

### finally()

还有一个 prototype 方法：`finnaly()`，其中的回调函数无论 promise 结果是什么都会运行。

{% codeblock lang:JS %}
const promise = new Promise((resolve, reject) => {
  const rand = Math.random()
  if (rand > 0.5) {
    resolve()
  } else {
    reject()
  }
})

promise
  .then(() => console.log('resolve'))
  .catch(() => console.log('reject'))
  .finally(() => console.log('finally done'))
{% endcodeblock %}

上面这段代码里，无论 promise 是 reject 还是 resolve，finally 都会打印出`finally done`。


## 更多阅读

Promise 系列：

1. {% post_link coding-8-js-promise-1-why 为什么需要 promise %} 
1. {% post_link coding-9-promise-2-what 什么是 promise %} 
1. {% post_link coding-10-promise-3-more promise 进阶 %} (本篇)
1. {% post_link coding-11-promise-4-async async/await %} 