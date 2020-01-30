---
title: 理解 JavaScript 的 this
date: 2018-10-20 00:53:05
categories:
 - Coding
tags:
 - JavaScript
---

{% codeblock lang:js %}
var myObject = {
  name: 'Harry Potter',
  myMethod: () => {
    console.log(this.name)
  }
}

myObject.myMethod() // undefined
{% endcodeblock %}

上面这段代码为什么会返回`undefined`呢？因为万恶的`this`。

{% asset_img this.jpeg "wtf is this" %}

MDN 对 **this** 的定义是：代码运行的环境。这句话给我的最大感觉是：what the **** is this?

这里总结下 **this** 的几个用法。

<!-- more -->

## Default Binding

在 Chrome 浏览器打开 inspector 的 console，输入如下代码：

{% codeblock lang:js %}
var count = 5
function Func() {
  console.log(this.count)
}
Func()
{% endcodeblock %}

console 会显示`5`。

因为我们的代码在环境下运行，this 默认绑定到了全局环境。

在`use strict`模式下，默认绑定不会发生。

{% codeblock lang:js %}
var count = 5
function Func() {
  `use strict`
  console.log(this.count)
}
Func()
{% endcodeblock %}

此时 Chrome 会报错。

## Implicit Binding

在 object 里创建函数时，运行函数时的`this`就是这个对象。

{% codeblock lang:js %}
var anObj = {
  name: 'Harry Potter',
  callName: function() {
    console.log(this.name)
  }
}

anObj.callName()
{% endcodeblock %}

此时屏幕会打印 Harry Potter。

## Explicit Binding

如果不想在 object 创建函数，我们可以手动把函数的`this`绑定到对象上。

可以使用`bind`、`call`或`apply`。

{% codeblock lang:js %}
var anObj = {
  name: 'Harry Potter',
}

function callName() {
  console.log(this.name)
}

callName.call(anObj) // Harry Potter
callName.apply(anObj) // Harry Potter

newCallname = callName.bind(anObj)
newCallname() // Harry Potte
{% endcodeblock %}

`call` 和 `apply` 可以直接以 object 作为 argument。
`bind` 需要指向一个新的变量。

## `new` Binding

创建 class 的 instance 时，`this` 会指向这个 instance

{% codeblock lang:js %}
class Point {
  constructor(x, y) {
    this.x = x
    this.y = y
  }

  showCoord() {
    console.log(this.x + ', ' + this.y)
  }
}

p1 = new Point(1, 2)
p1.showCoord()  // 1, 2

p2 = new Point(3, 4)
p2.showCoord()  // 3, 4
{% endcodeblock %}

每个 instance 都会有自己的 context。

## 箭头函数

箭头函数的`this`跟普通函数不一样，箭头函数会继承 parent scope 的`this`。

{% codeblock lang:js %}
var name = 'Ron Weasley'

var myObject = {
  name: 'Harry Potter',
  myMethod: () => {
    console.log(this.name)
  }
}

myObject.myMethod() // Ron Weasley
{% endcodeblock %}

在上面这段代码里，我们用箭头函数定义了 myMethod，结果屏幕打印的是 global context 里的 `name`，因为 `myObject` 位于 global context。

如果是在`use strict`下，上面这段代码会打印`undifined`。

所以，谨慎在箭头函数里使用`this`，非常容易出错啊。

既然不能使用箭头函数，上面这段的代码要怎么写才更简洁呢？用 ES6 的函数写写法：

{% codeblock lang:js %}
var myObject = {
  name: 'Harry Potter',
  myMethod() {
    console.log(this.name)
  }
}

myObject.myMethod() // Harry Potter
{% endcodeblock %}

搞清楚上面这些用法后，`this` 就没那么奇怪了。
