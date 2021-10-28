---
title: 你不知道的JS-读后感
date: 2021-10-23 19:21:50
categories: JS
top: false
summary: 你不知道的JS-读后感
tags: 
 - JS
---

#### JSON.stringify

JSON.stringify(target)将对象转为JSON格式，但是在转换过程中遇到`undefined|function|symbol`都会自动将其忽略。

转换过程中，如果转换对象（target）内置有`toJSON`的方法，则会调用这个方法，获取到要转换JSON的正确格式，在内部使用toString方法进行转换。

值得一提的是，JSON.stringify(target,参数二)，其值可以以下几种情况

* 不填
* 数组，例如:['a','b‘]，则在转换JSON时，只处理a,b
* 函数，function(k,v) {}，k是键名，v是键值

### && 与 || 

它们在编程语言中常常被称为逻辑运算符，但在js中它们的是特殊于其他语言的，它们回返回两个结果中的一个，并非返回的布尔值，所以在js中我更愿意称它们为**操作数选择器运算符**

* A || B

> 对于 || 来说，如果A判断为`true`，则直接返回A，否则返回B

* A && B 

> 对于&& 来说，如果A判断为`false`，则直接返回A，否则返回B