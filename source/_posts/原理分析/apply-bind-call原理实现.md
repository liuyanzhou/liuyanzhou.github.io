---
title: apply_bind_call原理实现
date: 2020-12-02 16:18:43
categories: 原理分析
top: false
summary:  apply_bind_call
tags: 
 - 原理分析
---

### apply_bind_call 

平时中要改变一个函数的指向我们要怎么做？是不是大家脑袋中会出现三个方法`apply`,`call`,`bind`。那平时大家都会用的同时，知道其内部原理么。下面我以`myCall`、`myApply`、`myBind`来叙述

### call

```js
Function.prototype.myCall = function(ctx, ...args) {
    // 1.将方法挂载到ctx对象上，为了改变this指向
    ctx.fn = this;
    // 2.将挂载以后的方法调用，并传入外部参数
    ctx.fn(...args);
    // 3.将添加的属性删除
    delete ctx.fn
}

function sayHi(...args) {
    console.log(args)
    console.log(this.name)
}

sayHi.myCall({ name: 'gruel' }, 'call1', 'call2')
```

> 输出：[ 'call1', 'call2' ]  gruel

其实就是在内部通过ctx这个中间量来扭转`this`的指向问题。

### apply

apply与call不同的只是传入的参数要是数组类型。内部原理是一样的

```js
Function.prototype.myApply = function(ctx, args = []) {
    // 判断入参是否是数组类型
    if (args && !(args instanceof Array)) {
        throw ('apply值接收数组的作为参数')
    }
    // 1.将方法挂载到ctx对象上，为了改变this指向
    ctx.fn = this
    // 2.将挂载以后的方法调用，并传入外部参数
    ctx.fn(...args)
    // 3.将添加的属性删除
    delete ctx.fn
}

function sayHi(...args) {
    console.log(args)
    console.log(this.name)
}

sayHi.myApply({ name: 'gruel' }, ['apply1', 'apply2'])
```

> 输出：[ 'apply1', 'apply2' ] gruel

### bind

bind与call的不同是它先返回的是一个函数再让我们调用这个函数。

```js
Function.prototype.myBind = function(ctx, ...args1) {
    // 使用高阶函数
    return (...args2) => {
        // 1.将方法挂载到ctx对象上，为了改变this指向
        ctx.fn = this
        // 2.将挂载以后的方法调用，并传入外部参数
        ctx.fn(...args1.concat(args2))
        // 3.将添加的属性删除
        delete ctx.fn
    }
}

function sayHi(...args) {
    console.log(args)
    console.log(this.name)
}
let bind = sayHi.myBind({ name: 'gruel' }, 'bind1')
bind('bind2', 'bind3')
```

> 输出：[ 'bind1', 'bind2', 'bind3' ]  gruel

### 总结

其他它们三者的原理都是一样的。只是入参的方式不同。