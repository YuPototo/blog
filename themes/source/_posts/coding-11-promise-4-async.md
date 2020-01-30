---
title: Promise 系列之4：async/await
date: 2019-02-15 18:58:56
categories:
  - Coding
tags:
  - JavaScript
---

async/await 是 EMCA2017 的新功能，是 Promise 的语法糖，让我们写 async/await 代码更方便。

<!-- more -->

## async

普通函数是这样的：

{% codeblock lang:JS %}
const normalFunc = function() {
  return 'normal function'
}

console.log(normalFunc())
// normal function
{% endcodeblock %}


Async 函数是这样的：

{% codeblock lang:JS %}
const asyncFunc = async function() {
  return 'async function'
}
{% endcodeblock %}

`async` 会把一个函数返回的结果变成 promise。

继续刚才的代码：

{% codeblock lang:JS %}
console.log(asyncFunc())
// Promise { 'async function' }

asyncFunc()
  .then(res => console.log(res))
// async function
{% endcodeblock %}

直接打印 asyncFunc() 的返回结果，会提示这是个 Promise。

我们可以用 `.then()` 获取 promise 的 resolve 值，得到函数返回的内容`async function`。

## await 

### await 用法

刚才的 async 代码用处不大，async 的真正价值是：我们可以在 async 函数里使用 `await` keyword。

`await` 可以用在 promise 函数之前，实现如下效果：
- 暂停代码，直到 promise 从 pending 变成 settled
- 返回 promise 返回的值

先看一个不是用 async/await 的函数：

{% codeblock lang:JS %}
const normalFunc = function() {
  Promise.resolve('this is value')
    .then(value => console.log(value))
}
normalFunc()
// this is value
{% endcodeblock %}

上面的函数可以用 async/await 改写如下：

{% codeblock lang:JS %}
const asyncFunc = async function() {
  let value = await Promise.resolve('this is value')
  console.log(value)
}
asyncFunc()
// this is value
{% endcodeblock %}

后者的可读性显然更强。

### await 暂停代码？

怎么理解 await 能够暂停代码呢？

先看一个使用 promise 的代码：

{% codeblock lang:JS %}
const normalFunc = function() {
  Promise.resolve('this is resolve value')
    .then(res => console.log(res))
  console.log('this line is after promise')
}
normalFunc()

//this line is after promise
//this is resolve value
{% endcodeblock %}

上面的`normalFunc()`会先执行第4行代码，然后再打印 promise 的值。这是异步运行的结果，有的时候这是我们希望看到的，尤其是后面的代码不需要依赖 promise 返回的值的时候。

但另外一些时候，后面的代码会依赖 promise 的结果，这时上面的代码可以改成：

{% codeblock lang:JS %}
const normalFunc = function() {
  Promise.resolve('this is resolve value')
    .then(res => {
      console.log(res)
      console.log('this line is after promise')
    })
  
}
normalFunc()

//this is resolve value
//this line is after promise
{% endcodeblock %}

有了 async/await 后，上面的代码可以变得更有可读性：

{% codeblock lang:JS %}
const asyncFunc = async function() {
  const value = await Promise.resolve('this is resolve value')
  console.log(value)
  console.log('this line is after promise')
}
asyncFunc()

//this is resolve value
//this line is after promise
{% endcodeblock %}

### await 如何改变 promise 的链式操作

Promise 可以利用 then 实现链式操作：

{% codeblock lang:JS %}
const addNum = num => new Promise((resolve, reject) => {
  resolve(num + 1)
})

Promise.resolve(1)
  .then(a => addNum(a))
  .then(b => addNum(b))
  .then(c => addNum(c))
  .then(d => console.log(d))
// 4
{% endcodeblock %}

第1个 promise 返回了1，然后依次在之后的 promise 加上1，最后得到4。

下面是 async/await 的写法：

{% codeblock lang:JS %}
const addNum = num => new Promise((resolve, reject) => {
  resolve(num + 1)
})

const asyncFunc = async () => {
  const a = 1
  const b = await addNum(a)
  const c = await addNum(b)
  const d = await addNum(c)
  console.log(d)
}
asyncFunc()
// 4
{% endcodeblock %}

同样是依赖于前一个 promise 返回值的操作，后一个写法要清晰得多。

## 如何 catch reject

Promise 可以用链上 catch 来抓取 reject 信息，如下：

{% codeblock lang:JS %}
Promise.reject('reject reason')
  .then(res => console.log(res))
  .catch(err => console.log('Error: ' + err)
// Error: reject reason
{% endcodeblock %}

await 可以怎么 catch reject 的原因呢？也是用 catch，但更像正常 JavaScript 的 catch error。

{% codeblock lang:JS %}
(async () => {
  try {
    await Promise.reject('reject reason')
  } catch(err) {
    console.log('Err: ' + err)
  }
})()
// Err: reject reason
{% endcodeblock %}

上面这段代码更像正常的 JavaScript 的 try catch。


## 小结

async/await 是 Promise 的一个语法糖，大部分情况下，它可以让代码更加清晰和简洁。
