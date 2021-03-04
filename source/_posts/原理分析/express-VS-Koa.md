---
title: 'express VS Koa '
date: 2021-01-19 20:18:26
tags:
---

### express Vs koa 

express 与 koa是我们node两个常用的框架，这篇我们深入源码学习，将两者的不同进行对比。

### 一、express

> express的核心是：**中间件原理和二级路由**

源码目录（核心文件）：

```
--index.js         入口
--lib              核心库文件夹
---expres.js       创建应用入口
---application.js  创建应用
--router           路由文件夹
---index.js        创建路由器
---layer.js        创建layer
---route.js        创建路由
```

原理图：

![未加入二级路由系统的express](/medias/imges/theory/express.png)

由原理图可以看出有几个作用的类：

* application  应用（app）
* Router  路由器
* layer 路径匹配层
* route 路由

实例使用案例：

```js
const express = require('./express');
const app = express(); // 最终是一个函数 http.createServer(app)

app.get('/user/remove', function(req, res) {
    console.log(req.a);
    res.end('user-remove');
})

// 错误处理中间件 (统一的处理)
app.use(function(err, req, res, next) { // 参数四个 function.length
    res.setHeader('Content-Type', 'text/html;charset=utf-8');
    res.end(err);
})

app.listen(3000)
```

它们之间的互相关系是：

> application 有一个大的路由器（router），内部会为每一个路径生成一个外层layer（路由匹配层，关注请求路径）和一个匹配路由（route），外层layer内部会通过`layer.route=route`来关联上route，而route内部又会为用户每一个匹配路由的执行函数生成一个个内层`layer`（这个layer并不关心请求路径，只关注处理方法和请求方法）。

当请求来了，它们如何进行处理：

> 当请求来了，会被http.listen方法监听到，它会去执行 `router.handler`上的方法，`hanlder`会采用`next()`回调的方案将`router.stack`内部的所有外层`layer`进行匹配【匹配请求路径是否一致】，如果匹配不到就`next()`继续向下匹配，直到越栈，会调用`应用层传入的done方法`，若匹配到了，在去调用`route.dispatch`，再次采用`next()`回调的方案将`route.stack`内部的所有内层`layer`进行匹配【匹配请求方式是否一致】，若匹配不到就`next()`往下匹配，直到越栈，`route`调用`router`传入的`next()`，若匹配到，则调用对应的方法。

![加入二级路由系统的express](/medias/imges/theory/express-up.png)

原本的核心结构并无多大变化，无非多了一个类`express.Router`，我们所有的二级路由都有它来创建，其实外层的大路由器也是有这个类构建的。

```js
function Router() { // 能充当构造函数 也可以充当类

    let router = function(req, res, next) {
        // 请求到来时，匹配到路径后会执行此方法，我想要去stack中依次执行
        router.handle(req, res, next); // 执行对应stack中的路由系统
    }
    router.stack = [];
    router.__proto__ = proto; // 代码基本没有变，但是让类支持了函数的调用
    return router; // 如果类返回的一个引用类型 当前这个返回值会作为类的实例
}
```

图的各种类之间的关系：

> application 内部包含着应该大路由器（router），router.stack内部存储着路由匹配层`layer`（这个layer只关心请求路径）其内部有一个handler函数是用于路由匹配到时，会让二级路由去筛选`route.stack`的layer（这个layer只关心请求路径），二级路由器内部的handler是对应路由的`route.dispatch`，匹配到时，会去筛选对应的`route`内部的layer，当请求方式匹配到时，则去调用。

所以说：

> 外层的`router`（大路由器）的`layer.handler`是打通了二级路由器的桥梁，而二级的路由的`layer.handler`是打通二级路由器和路由的桥梁，两个桥梁的打通才可构建层  express的路由系统

#### express中间件原理

正如上面所说，express内部是采用了`next()`回调的方式来执行中间件函数的筛选，而由于是`next()`方案则express是不支持`async+await`的形式让`next()`后面的代码有等待效果，其也无法做到。

#### express的二级路由

正如上面所说，express的二级路由只是将路由拆的更细，本质不变，在注册时，第一次的layer的handler函数是对应着二级路由的`handler函数`

```js
app.use('/user',function(){
    router.handler()
})
```

### 二、Koa

> Koa的核心：**中间件原理和上下文ctx**

源码目录（核心文件）:

```
--lib              核心库目录
---application.js  创建应用（app）
---context.js      封装和扩展ctx
---request         封装和扩展req
---response        封装和扩展res
```

#### ctx原理

context（ctx）：它就是req,res,response,request的一层代理，我们在ctx上可以获取到原生的`req,res`和koa自己封装的`request,response`

而他们之间是通过下面的代码定义到ctx上的：

```js
ctx.request = request;
ctx.req = ctx.request.req = req;

ctx.response = response;
ctx.res = ctx.response.res = res;
```

在context.js文件中，koa使用的是`__defineGetter__、__defineSetter__`来定义和设置属性

```js
const ctx = {}

function defineGetter(target, key) {
    ctx.__defineGetter__(key, function() {
        return this[target][key]
    })
}

function defineSetter(target, key) {
    ctx.__defineSetter__(key, function(value) {
        this[target][key] = value;
    })
}

defineGetter('request', 'url');
defineGetter('request', 'path');
defineSetter('response', 'body');
defineGetter('response', 'body');

// 上下文就是一个代理对象
module.exports = ctx
```

在request.js中，koa的是通过属性访问器的方式定义属性

```js
const url = require('url');
const request = {
    get path() { // 对象属性访问器 相当于 Object.defineProperty()
        return url.parse(this.req.url).pathname;
    },
    get url() {
        return this.req.url;
    }
}
// request 文件仅仅是为了对req进行扩展
module.exports = request;
```

在response.js中，koa是通过属性访问访问器来定义和设置属性

```js
const response = {
    _body: undefined,
    get body() {
        return this._body;
    },
    set body(context) {
        this.res.statusCode = 200;
        this._body = context;
    }
}

module.exports = response
```

#### koa中间件原理

在koa的中间件`app.use()`，确实内部是通过promise包裹的，所以它支持`async+await`的方式让`next()`后的代码进行等待，这是因为一个promise中返回或者`await`一个promise，前面的promise要等待后面的promise的状态改变，才能继续往下执行。其中核心代码为：

> 注意：服务端的响应是在第一个中间件函数执行完毕后调用`res.end()`。而在koa中我们是将所有的中间件函数组成一个promise套一个promise的形式，那么也可以理解成当最外层的promise的状态改变后，我们的服务端才会去调用`res.end()`结束服务器响应。

```js
compose(ctx) {
    let index = -1; // 默认没有调用过
    const dispatch = (i) => { // 1
        if (i <= index) return Promise.reject('next()  call multiples times')
        index = i;
        if (i === this.middlewares.length) return Promise.resolve()
        let middleware = this.middlewares[i]; // 数组的第一个
        return Promise.resolve(middleware(ctx, () => dispatch(i + 1))); // 当用户调用next时会取出下一个继续执行
    }
    return dispatch(0);
}
```

### 三、express Vs Koa

* 在中间件原理上，express采用的是`next()`方案，koa采用的`promise`方案
* express对`req、res`进一步的封装；koa也对`req,res`进一步封装，并扩展了`response,request`，并将四者让`ctx`代理。
* express提供了路由系统，koa不提供