---
title: JavaScript：如何理解“异步”和“同步”
date: 2018-07-01 18:55:04
categories:
  - Coding
tags:
  - JavaScript
---

{% asset_img syncOrAsync.png "asyncOrSync" %}

怎么理解 async（异步） 和 sync（同步） 呢？

<!-- more -->

## 一个比喻

解释：
- sync：指代码按照顺序执行，一段代码执行完后，再去执行下一段。
- async：一行代码发出调用指令后，不去等待这段代码的执行结果，就直接去执行下一段代码。

上面的解释可能比较抽象，我们做一个比喻：想象你在星巴克排队买咖啡。

- sync：轮到你点咖啡，你选好了咖啡，告诉咖啡师，咖啡师开始做咖啡，你在柜台前等待，不能做任何别的事情，静静地等咖啡师把咖啡做完。拿到咖啡后，你才能去做下一件事情，比如找到座位开始工作。
- async：轮到你点咖啡，你选好了咖啡，告诉咖啡师，然后你找了个座位，打开电脑开始工作，咖啡师做好咖啡后，把咖啡拿给你。

Sync 的顺序如下图：
![sync 例子](sync.png)

在 sync 模式下，拿到咖啡之前，我们什么也干不了，只能干等着。

Async 的顺序如下图：
![async 例子](async.png)

在 async 模式下，咖啡师在煮咖啡，我们可以先去忙别的事情，当咖啡师做完咖啡后，我们会收到咖啡。这种模式更节约时间。

## JavaScript 的例子

我们把上面的例子用 JavaScript 代码来执行：

{% codeblock Sync lang:JS %}
console.log("点咖啡")
console.log("------等待2分钟-----")
console.log("拿到咖啡")
console.log("找座位")
console.log("打开电脑")
console.log("开始工作")

//点咖啡
//------等待2分钟-----
//拿到咖啡
//找座位
//打开电脑
//开始工作
{% endcodeblock %}

可以看到，一切都按照顺序进行。


然后是 Async 的代码
{% codeblock Async lang:JS %}
console.log("点咖啡")
setTimeout(() => {
  console.log("-----2分钟过去了-----")
  console.log("拿到咖啡")
}, 2)
console.log("找座位")
console.log("打开电脑")
console.log("开始工作")

//点咖啡
//找座位
//打开电脑
//开始工作
//-----2分钟过去了-----
//拿到咖啡
{% endcodeblock %}

这里有意思的地方是， “-----2分钟过去了-----” 和 “拿到咖啡” 在顺序上是第2和第3位，但因为异步，它们在最后才被执行。我们利用空闲的时间先处理了之后的工作。

## 什么场合使用 async 和 sync

咖啡师做咖啡是一件消耗时间的任务，在 app 开发时也有类似的任务，比如客户端从数据库获取数据，我们不知道要等待多久，这时候就可以使用 async，让我们的客户端在获取到数据前先做其他工作。

使用 async 的典型场景：
- 访问 API
- 数据库的读写
- 调用某个第三方 JavaScript 包
- ......

使用 sync 的典型场景：程序操作之间有明显的顺序关系。如用户输入账号密码、校验、登陆，这是一个明显有顺序的流程。

另一个思考方式是：当你不知道该用 async 还是 sync 时，就先默认用 sync 吧。

## 参考：

- [Is JavaScript Synchronous or Asynchronous? What the Hell is a Promise?](https://medium.com/better-programming/is-javascript-synchronous-or-asynchronous-what-the-hell-is-a-promise-7aa9dd8f3bfb)
- [JavaScript: Execution of Synchronous and Asynchronous codes](https://medium.com/@siddharthac6/javascript-execution-of-synchronous-and-asynchronous-codes-40f3a199e687)
- [FASTapi - Concurrency and async / await](https://fastapi.tiangolo.com/async/#concurrency-and-burgers)
- [Understanding Async Await](https://css-tricks.com/understanding-async-await/)