---
title: JavaScript 其实很像 python
date: 2020-01-18 20:21:29
---

{% asset_img js-py.jpg  "js-python" %}

在我刚入门 JavaScript 的时候，感觉 JavaScript 是个莫名其妙的语言。在那之前，我真正称得上了解的语言只有 python。写 python 是一种享受，相比之下，写 JavaScript 的体验很“怪异”。

后来我才直到，JavaScript 是一个在很短时间内创造的语言，是为了在浏览器上运行。我所学习的 JavaScript，还是20多年前的版本。拿老版本的 JavaScript 和今天的 python3 去比较，是挺不合理的。

在了解到最新的 JS 功能后，JavaScript 突然变得“可爱”了，甚至在某些地方很像 python。

<!-- more -->

## 多行 string

问题：如何给 string 换行呢？

{% codeblock python lang:python %}
poem = """Programming is fun
Except for syntax errors
Missing curly brace""
{% endcodeblock %}

老版本的 JavaScript 不支持，但现在支持了：

{% codeblock JavaScript lang:JavaScript %}
var poem = `Programming is fun
Except for syntax errors
Missing curly brace`;
{% endcodeblock %}


## Expression Interpolation

问题：如何方便地在 string 中使用变量呢？

{% codeblock python lang:python %}
a = 5
b = 10
print(f'Fifteen is {a + b} and not {2 * a + b}.')
{% endcodeblock %}

上面这个功能很实用，让代码的可读性提高了几个档次。JavaScript 也有类似的写法：

{% codeblock JavaScript lang:JavaScript %}
var a = 5
var b = 10
console.log(`Fifteen is ${a + b} and not ${2 * a + b}.`)
{% endcodeblock %}

## Destructuring

问题：如何同时 assign 几个变量的值呢？

{% codeblock python lang:python %}
numbers = (1, 2, 3)
x, y, z = numbers
{% endcodeblock %}

Destructuring 在某些场合有用，ES2017 里有了类似的设计：

{% codeblock JavaScript lang:JavaScript %}
var numbers = [1, 2, 3]
var [x, y, z] = numbers
{% endcodeblock %}

## Spread Operator

问题：如何 assign 数组里的一个变量，并忽略其他变量呢？

{% codeblock python lang:python %}
numbers = [1, 2, 3, 4]
first, *remaining = numbers
{% endcodeblock %}

下面是 JavaScript 的写法：

{% codeblock JavaScript lang:JavaScript %}
var numbers = [1, 2, 3, 4]
var [first, ...remaining] = numbers
{% endcodeblock %}

## Rest Operator

问题：如何给函数任意多个 argument 呢？

{% codeblock python lang:python %}
def myFun(*argv):  
    for arg in argv:  
        print (arg)

myFun('a', 'b', 'c')
{% endcodeblock %}

在 python 里这个规则叫 *wargs。在 JavaScript 里，使用 `...`，这三个点叫 **rest operator**

{% codeblock JavaScript lang:JavaScript %}
function product(...args) {
  var arg
  for (arg of args) {
    console.log(arg)
  }
}

product('a', 'b', 'c')
{% endcodeblock %}

## 箭头函数

问题：如何把一个数组里的数字全部开方


{% codeblock python lang:python %}
numbers = [1, 2, 3, 4]

list(map(lambda x: x * 2, numbers))
# or [x * 2 for x in numbers]
{% endcodeblock %}

JavaScript 里使用**箭头函数**：=>

{% codeblock JavaScript lang:JavaScript %}
var numbers = [1, 2, 3, 4]
numbers.map(v => v * 2)
{% endcodeblock %}

相比 lambda，箭头函数甚至更加 elegant。

## Class

问题：如何创建一个 class

{% codeblock python lang:python %}
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    def __str__(self):
        return "({x}, {y})".format(x=self.x, y=self.y)
{% endcodeblock %}

在 ES2017 之前，JavaScript 的做法：

{% codeblock JavaScript lang:JavaScript %}
function Point(x, y) {
  this.x = x
  this.y = y
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};
{% endcodeblock %}

第一次看到上面这段代码时我非常费解，这是什么鬼:cold_sweat:？现在有新的写法了：


{% codeblock JavaScript lang:JavaScript %}
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
{% endcodeblock %}

习惯 python 的开发者可以无缝理解了:satisfied:。

## 小结

在 Hacker News 上看到过一句话：

> 我招人的时候从来不问对方会什么编程语言，因为只要你会一门语言，拿掌握其他语言都很容易。作为一个工程师，真正重要的不是你会什么语言或框架，而是......

这句话我只记得前半句，现在有点理解为什么编程语言不重要了。不过重要的到底是什么呢？虽然我不是软件工程师，不过还是对这个答案很好奇。

如果我需要招一个软件工程师，我会希望他/她有什么特质？
