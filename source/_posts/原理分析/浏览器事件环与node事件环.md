---
title: 浏览器事件环与node事件环
date: 2020-12-01 12:33:57
categories: 原理分析
top: true
summary: 浏览器事件环与node事件环
tags: 
 - 原理分析
---

### 浏览器事件环

浏览器的进程：

* 每一个页卡都是进程 （互不影响）
* 浏览器也有一个主进程 （用户界面）
* 渲染进程 每个页卡里 都有一个渲染进程  (浏览器内核)
* 网络进程 (处理请求)
* `GPU`进程 `3d`绘制
* 第三方插件进程

渲染进程(包含多个线程)

* GUI渲染线程 (渲染页面)
* **`js`引擎线程**

> `js`引擎线程和GUI页面渲染线程是互斥的

* 事件触发线程 （独立的线程）`eventLoop`
* 事件`click`、`setTimeout`、`ajax`也是一个独立的线程

> 在js主线程执行时，会去调度`click`、`setTimeout`、`ajax`线程，将回调加入对应的任务队列，之后在由`eventloop`这个线程去调度整体线程执行

eventloop的调度图

![eventloop](/medias/imges/theory/浏览器eventloop.jpg)

执行顺序:

* js主线程(js引擎线程)从执行开始到结束，会产生宏任务和微任务。并添加到对应的队列中，当如何是系统级的api调用，例如`setTimeout\setInterval`定时器的回调函数，它会等对应线程执行完，在将它们的回调放回宏任务队列
* 依次清空微任务队列 (也就是将微任务放入主线程，这时可能再次产生微任务或宏任务，在依次入队列)
* 调用GUI线程，渲染页面
* 依次清空宏任务队列 (也就是将宏任务放入主线程中，这时可能再次产生宏任务或微任务，在依次入队列)
* 重复执行上面的 2 ，3 ，4，直到所有的宏任务和微任务都执行完

扩展：

> 宏任务：宿主环境提供的异步方法，都是宏任务，例如：`script,setTimeout,setInterval,ajax,ui渲染`
>
> 微任务：语言标准提供，例如：`promise.then`、`mutationObserver`、`queueMircoTask`、`process.nextTick`

下面列举几个例子来说明一下:

#### 1.例子：页面的颜色变化

```html
<script>
        document.body.style.background = 'red';
        console.log(1)
        Promise.resolve().then(()=>{
            console.log(2)
            document.body.style.background = 'yellow';
        })
        console.log(3);
</script>
```

> 输出顺序：1、3、2
>
> 页面的颜色是什么顺序呢？直接变黄色

改进一下例子：

```html
<body>
    <script>
        document.body.style.background = 'red';
        console.log(1)
        setTimeout(() => {
            console.log(2)
            document.body.style.background = 'yellow';
        })
        console.log(3);
        // 页面会由红色变为黄色 ？
        // 执行log 逻辑是 1 2 3 吗？
    </script>
</body>
```

> 输出顺序：1、3、2
>
> 但颜色的顺序是：由红变黄（一瞬间的事，你要重复刷新才能看到效果）

**这就是图上展示的：我们是先清除(执行)微任务队列的任务，才起渲染页面**，第一个例子`promise`是微任务，在渲染页面前已经执行了，那么页面就直接变为黄色。第二个例子`setTimeout`是宏任务，它的执行时机是在渲染页面之后，那么我们的页面就会出现一瞬间的从红色变为黄色

#### 2.例子：事件任务

```html
<button id="button">点我</button>


<script>
    button.addEventListener('click', () => {
        console.log('listener1')
        Promise.resolve().then(() => console.log('microtask1'))
    })
    button.addEventListener('click', () => {
        console.log('listener2')
        Promise.resolve().then(() => console.log('microtask2'))
    })
    // button.click();
</script>
```

> 输出顺序：listener1、microtask1、listener2、micro task2

* 当我们点击按钮时，我们的click的回调函数会进入宏任务队列。这时宏任务队列会有两个回调【fn1,fn2】。
* 当fn1被放入js的执行栈中去，会输出`listener1`并产生一个微任务`microtask1`。 当js执行栈空闲时，就去清空这一次执行栈产生的微任务输出`microtask1`，之后去调用gui线程去渲染页面。
* 之后fn2入js执行栈，并输出`listener2`并产生`microtask2`微任务，当执行栈空闲时就去清空这次执行栈产生的微任务输出`microtask2`，之后在去调用gui线程渲染页面。

* 当然真实的浏览器中会在gui线程上进行优化，把几次渲染合并为一次渲染。

> 当代码直接去触发click事件时：例如将`button.click()`注释打开，输出的顺序是 listener1、listener2 、microtask1、microtask2，这时可以认为是将两个click事件直接放入js执行栈中去，因为它并不是我们人为去触发事件执行，故他们的回调并没有调度事件线程处理故没有进入宏任务队列中，在第一次js执行栈中就已经完成了。相当于下面吗

```js
// js执行栈  
console.log('listener1')
Promise.resolve().then(() => console.log('microtask1'))
console.log('listener2')
Promise.resolve().then(() => console.log('microtask2')) 
```

#### 3. 例子：定时器任务

```html
<script>
    Promise.resolve().then(() => {
        console.log('promise1')
        setTimeout(() => {
            console.log('setTimeout1')
        }, 0)
    })

    setTimeout(() => {
        console.log('setTimeout2')
        Promise.resolve().then(data => {
            console.log('promise2')
        })
    })

    // 宏任务[]
    // 微任务[]
</script>
```

> 输出顺序： promise1 、setTimeout2 、promise2 、setTimeout1

#### 4例子：任务执行面试题

```html
<script>
    console.log(1);
    async function async () {
        console.log(2);
        await console.log(3); // Promise.resolve(console.log(3)).then(data=>{console.log(4)})
        console.log(4)
    }
    setTimeout(() => {
        console.log(5);
    }, 0);
    const promise = new Promise((resolve, reject) => {
        console.log(6);
        resolve(7)
    })
    promise.then(res => {
        console.log(res)
    })
    async (); 
    console.log(8);
</script>
```

> 输出顺序：1 6 2 3 8 7 4  5

主要将await的关键字后面的代码理解为放入到一个微任务内即可【注意：不包括 await 所在的那行，它那行会被直接调用，也就是上面的`console.log(3)`

### node事件环

![eventloop](/medias/imges/theory/nodeEventloop.png)

* V8引擎解析`javascript`脚本
* 解析后的代码，调用Node API
* libuv库负责Node API 的执行。它将不同的任务分配给不同的线程，并且提供事件循环机制，以异步的方式将任务的执行结果返回给`V8`引擎。
* V8引擎再将结果返回给客户端

下面是node将我们的宏任务进行的划分，eventloop会去依次清空对应的宏任务队列

```
    本阶段执行已经被 setTimeout() 和 setInterval() 的调度回调函数。
   ┌───────────────────────────┐
┌─>│           timers          │ 
│  └─────────────┬─────────────┘
|   执行延迟到下一个循环迭代的 I/O 回调。
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │
│  └─────────────┬─────────────┘
|   仅系统内部使用。
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │
│  └─────────────┬─────────────┘      
|  检索新的I/O事件;执行与 I/O相关的回调,I/O回调可认为是setTimeout/setTimeout/setImmediate的回调之外的回调
				 |					  ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  setImmediate() 回调函数在这里执行。   └───────────────┘
│  ┌─────────────┴─────────────┐      
│  │           check           │
│  └─────────────┬─────────────┘
|  一些关闭的回调函数
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘
```

> 这里每一个阶段都对应一个宏任务队列，当eventloop执行到某个阶段时会将当前阶段对应的队列依次执行。当该队列已用尽或达到回调限制，事件循环将移动到下一个阶段。`process.nextTick()`从技术上讲不是事件循环的一部分。

对于这些这些阶段的介绍官方说得很详细：https://nodejs.org/zh-cn/docs/guides/event-loop-timers-and-nexttick/

这里只列出几点关键点，也是读懂这个事件循环的关键点：

* 1.js主栈执行完，将对应的宏任务加入到对应的队列，【例如`setTimeout、setInterval`的回调函数到达了执行时机，就进入了`timers`队列。】,清空对应这一阶段的微任务。

* 2.eventloop在poll这个阶段会特殊处理，【也就是poll阶段线程会被阻塞进行】，在阻塞后又两种执行情况

  * 当timers有任务进来了，那么eventloop机制就会从poll阶段直接转移到`timers`阶段执行任务。后又在poll阻塞。

  * 当check | close 有新回调进来，那么eventloop机制会移动到check | close阶段去清空对应的任务。后一轮到poll阻塞

* 并不是说poll阻塞很久，node会在每次运行的事件循环之间，检查它是否在等待任何异步 I/O 或计时器，如果没有的话，则完全关闭。

* 记住这里的任务都是宏任务，那么我们在将一个任务放入主栈执行后，它会依次清空这一次产生的所有**微任务**，当然如果我们写了`process.nextTick()`的回调，那么他会优先于每次微任务队列先执行。可以将其看做微任务前面的一个队列

```js
setTimeout(() => {
    console.log('timeout')
}, 0);
Promise.resolve().then(data => {
    console.log('then')
});
// 优先级高于微任务
process.nextTick(() => {
    console.log('nextTick')
});
// 输出 nextTick then timeout
```

> 简而言之：node的eventloop是对浏览器的eventloop机制进行了细化。它将宏任务放入个个对应的队列，而不是放在一起了。当然在node(14.xxx)后，**node的事件环处理出来的结果和浏览器事件环处理的结果是一致的。**

下面2个例子

**例子1：setTimeout与setImmediate**

```js
setTimeout(() => {
    console.log('setTimeout')
}, 0); // 受性能影响 需要看循环的时候 settimeout 是否被放到了队列中 是优先于setImmediate 还是在 setImmediate 之后

setImmediate(() => {
    console.log('setImmediate')
})
// 输出顺序不确定，看机器性能
```

**例子2：在i/o回调的setTimeout与setImmediate**

```js
const fs = require('fs');
fs.readFile(__filename, () => {
    setTimeout(() => {
        console.log('timeout')
    }, 0);
    setImmediate(() => {
        console.log('immediate')
    })
})
// 输出顺序确定：immediate timeout
```

