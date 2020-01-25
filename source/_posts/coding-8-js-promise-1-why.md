---
title: Promise 系列之1：为什么
date: 2020-01-25 18:58:56
categories:
  - Coding
tags:
  - JavaScript
---

Promise 是 JavaScript ES6 的新特性，是一个异步操作的解决方案。对于我这个没有什么开发经验的小白，promise 实在有点难理解。这个系列笔记会总结 promise 的背景和应用。

第1个问题：为什么需要 promise。

<!-- more -->

## Callback Sucks

最重要的原因：回调函数不好用。

创建回调函数很容易，但回调函数用起来并不容易。

典型场景：
- 需要先获取数据 a 
- 然后利用数据 a 获取数据 b
- 然后利用数据 b 获取数据 c
- 然后利用数据 c 获取数据 d
- 然后利用数据 d 获取数据 e
- 展示数据 e

其实这么恶心的业务逻辑我还没碰到过，不过看到上面这段需求我就已经感觉到反胃了。

上面的需求如果用 callback 风格，**伪代码**如下：

{% codeblock Callback Hell lang:JS %}
// 第1段代码
getData(url, a => {
  getMoreData(a, b => {
    getMoreData(b, c => {
      getMoreData(c, d => {
        getMoreData(d, e => {
          console.log(e)
        })
      })
    })
  })
})
{% endcodeblock %}

- 第1行：通过 GET url 获取到了 a，然后调用 callback，参数为 a
- 第2行：用a获取到了b，然后调用 callback，参数为 b
- ...

上面这段逻辑，又被成为“回调地狱”，需要维护的人估计都想哭。

## Into promise

先不讨论 promise 的定义。这里只需要直到，promise 能够很好地解决上面的问题。

Promise 提供了一个 then 方法，这个方法支持“链式调用”。

使用链式调用后，上面的“回调地狱”可以被写成：

{% codeblock Promise lang:JS %}
getData()
  .then(a => getMoreData(a))
  .then(b => getMoreData(b))
  .then(c => getMoreData(c))
  .then(d => getMoreData(d))
  .then(e => console.log(e))
{% endcodeblock %}

即使我们完全不同 promise，也能感觉到后一段代码的可读性更强。

在写了一段时间 promise 后，大家发现 promise 也有一些痛点，比如业务逻辑需要在 `then()` 中执行，如果业务逻辑复杂，代码的可读性也会变差。于是人们想到了新的方案：async/await。

## Even better：async/await

async/await 只是 promise 的语法糖，并没有引入新的东西。

使用 async 和 await 后，代码又可以修改成：

{% codeblock async/await lang:JS %}
(async () => {
  const a = await getData()
  const b = await getMoreData(a)
  const c = await getMoreData(b)
  const e = await getMoreData(c)
  console.log(e)
})()
{% endcodeblock %}

这段代码的大括号部分，看上去非常像平时写的同步代码了。业务逻辑按顺序描述，且“使用数据 a 获取数据 b” 的概念也能得到更清晰的展示。

## 题外话

从 callback 到 promise，再从 promise 到 async/await，让我想到了增长经济学里的“技术进步”：即使时间和资本投入不改变，通过更加先进的技术，经济也能得到发展。

传统的 callback 代码，程序员可能需要10个小时去创造并维护；进化到 promise 后，也许只需要8个小时；再进化到 async/await，也许只需要6个小时。对全世界所有的技术项目，这都是40%的成本压缩，人们所付出的，只是熟悉新功能需要的十几个小时。

这种技术进步，也可能是程序员并不会随着年龄变得更吃香的原因：年轻人一旦掌握了 async/await，写 callback 的老年程序员就变成了又贵又落后的原始产能。
