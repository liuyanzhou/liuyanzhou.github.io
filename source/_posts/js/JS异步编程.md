---
title: JS异步编程
date: 2020-08-04 19:16:59
categories: JS
top: false
summary: JS数据结构与算法
tags: 
 - JS
---

###	JS异步编程

### 浏览器的线程

转载至：https://segmentfault.com/a/1190000013083967

一个进程有一个或多个线程，线程之间共同完成进程分配下来的任务，打个比方：

* 假如进程是一个工厂，工厂有它的独立资源
* 工厂之间相互独立
* 线程是工厂中的工人，多个工人协作完成任务
* 工厂内有一个或多个工人
* 工人之间共享空间

在完善一下概念：

* 工厂的资源 -> 系统分配的内存 （独立的一块内存）
* 工厂之间的相互独立 -> 进程之间相互独立
* 多个工人协作完成任务 -> 多个线程在进程中协作完成任务
* 工厂内有一个或多个工人 -> 一个进程由一个或多个线程组成
* 工人之间共享空间 -> 同一个进程下的各个线程之间共享程序的内存空间 （包括代码段，数据集，堆等）

进程是 `cpu`资源分配的最小单位 (是能拥有资源和独立运行的最小单位)，线程是`cpu`调度的最小单位（线程是建立在进程的基础上的一次程序运行单位)

#### 浏览器的进程

知道了进程和线程之间的关系之后，下面是浏览器与进程的关系了。首先，浏览器是多进程的，之所以浏览器能够运行，是因为系统给浏览器分配了资源，如 cpu 、 内存 ，简单的说就是，浏览器每打开一个标签页，就相当于创建一个独立浏览器进程，例如：我们查看`chrome`里面的任务任务管理器

**注意：**在这里浏览器应该也有自己的优化机制，有时候打开多个tab页后，可以在 Chrome任务管理器中看到，有些进程被合并了 （譬如打开多个空白标签页后，会发现多个空白标签页被合并成了一个进程）所以每个Tab 标签对应一个进程并不一定是绝对的

除了浏览器的标签页进程之外，浏览器还要一些其他进程来辅助支撑标签页的进程，如下

* Browser进程：浏览器的主进程（负责协调、主控），只有一个，作用有：
  * 负责浏览器页面显示，与用户交互。如前进、后退等
  * 负责各个页面的管理，创建和销毁其他进程
  * 网络资源的管理，下载等
* 第三方插件进程：每种类型的插件对应一个进程，仅当使用该插件时才创建
* GPU 进程：最多一个，用户3D绘制
* 浏览器渲染进程（浏览器内核），Renderer进程，内部是多线程的，也就是我们每个标签页所拥有的进程，互不影响，负责页面渲染，脚本执行，事务处理等

![任务管理器](/medias/imges/js/promise/任务管理器.png)

#### 浏览器内核

浏览器内核，即我们的渲染进程，有名`Renderer`进程，我们页面的渲染，js的执行，事件的循环都在这一进程内进行，也就是说该进程下面拥有多个线程，靠着这些线程共同完成渲染任务，那么这些线程是什么呢？如下：

* 图形用户界面GUI渲染线程

  * 负责渲染浏览器界面，包括解析HTML、CSS、构建DOM树，Render树、布局与绘制等
  * 当界面需要重绘（Repaint) 或由于某种操作引发回流 (reflow) 时，该线程就会执行
* JS 引擎线程

  * JS内核，也称 JS 引擎，负责处理执行 JS 脚本
  * 等待任务队列的任务的到来，然后加以处理，浏览器无论什么时候都只有一个JS引擎在运行js程序
* 事件触发线程

  * 听起来像JS的执行，但是其实归属于浏览器，而不是JS引擎，用来控制时间循环（可以理解，JS引擎自己都忙不过来，需要浏览器另开线程协助）
* 当 JS 引擎执行代码块如`setTimeout`时，（也可来着浏览器内核的其他线程，如鼠标点击，AJAX异步请求等），会将对应任务添加到事件线程中
  * 当对应的事件符合触发条件被触发时，该线程会把事件添加到待处理队列的队尾，等待JS引擎的处理
* 注意：由于JS 的单线程关系，所以这些待处理队列中的事件都得排队等待JS引擎处理（当JS引擎空闲时才会去执行）
* 定时器触发器线程
  * `setInterval`与`setTimeout`所在线程
  * 定时器计时并不是由JS引擎计时的，因为如果 JS引擎是单线程的，如果JS 引擎处理堵塞状态，那会影响到计时的准确
  * 当计时完成被触发，事件会被添加到事件队列，等待JS引擎空闲了执行
  * 注意：W3C的HTML标准中规定，setTimeout中与4ms的时间间隔算为4ms
* 异步HTTP请求线程
  * 在XMLHttpRequest在连接后新启动的一个线程
  * 线程如果检测到请求的状态变更，如果设置有回调函数，该线程会把回调函数添加到事件队列，同理，等待JS引擎空闲了执行·

浏览器内核，放图加强记忆：

![浏览器内核](/medias/imges/js/promise/浏览器线程.png)

   #### 为什么JS引擎是单线程的

JavaScript 作为一门客户端的脚本语言，主要的任务是处理用户交互，而用户的交互无非就是响应DOM的增删改，使用事件队列的形式，一次事件循环只处理一个事件响应，使得脚本执行相对连续。如果JS引擎被设计为多线程的，那么DOM 之间必然存在资源竞争，那么语言的实现会变得比较臃肿，在客户端跑起来，资源的消耗和性能将会是不太乐观的，故设计为单线程的形式，并附加一些其他的线程来实现异步的形式，这样的运行成本相对于使用JS多线程来说降低了很多

#### 浏览器内核中线程之间的关系

##### GUI渲染线程与JS引擎线程互斥

因为JS引擎可以修改DOM树，那么如果JS 引擎在执行修改了DOM结构的同时，GUI线程也在渲染页面，那么这样就会导致渲染线程获取的DOM的元素信息可能与JS引擎操作DOM后的结果不一样。为了防止这种现象，GUI线程与JS 线程需要设计为互斥关系，当JS引擎执行的时候，GUI线程需要被冻结，但是GUI的渲染会被保存在一个队列中，等待JS引擎空闲的时候执行渲染。

由此也可以推出，如果JS引擎正在进行CPU密集型计算，那么JS引擎将会阻塞，长时间不空闲，导致渲染进程执行不能执行渲染，页面就会看起来卡顿卡顿的，渲染不连贯，所以，要尽量避免JS执行时间过长

##### JS引擎线程与时间触发线程、定时器触发器线程、异步HTTP请求线程

事件触发线程、定时器触发器线程、异步HTTP请求线程，三个线程有一个共同点，那就是使用回调函数的形式，当满足了特定的条件，这些回调函数会被执行。这些回调函数被浏览器内核理解为事件，在浏览器内核中拥有一个事件队列，这三个线程当满足内部特定的条件，会将这些回调函数添加到事件队列中，等待JS引擎空闲执行。例如异步HTTP请求线程，线程如果检测到请求的状态变更，如果设置有回调函数，回调函数就会被添加事件队列中，等待JS引擎主线程空闲时在来执行。但是JS引擎对事件队列（宏任务）与JS引擎内的任务（微任务）执行存在这先后顺序，会每次执行时间队列的里的任务，都是先执行完微任务，在去执行所有的产生的宏任务。

##### WebWorker

因为JS引擎是单线程的，当JS执行时间过长会页面阻塞，那么JS就真的堆CPU密集型计算无能为力了么？并不是，HTML5中支持了 Web Worker

来自MDN的官方解释

> Web Worker 使得一个web应用程序可以在于主执行线程分离的后台线程中运行一个脚本操作，这样做的好处是可以在一个单独的线程中执行费时的处理任务，从而允许主（通常UI）线程运行而不被阻塞 / 放慢

注意点：

* Web Worker  可以向浏览器申请一个子线程，该子线程服务于主线程，完全受主线程控制
* JS引擎线程与worker 线程间通过特定的方式通信（postMessage API） 需要通过序列化对象来与线程交互特定的数据

所以，如果需要进行一些高耗时的计算时，可以单独开启一个Web Worker 线程，这样不管这个Web Worker 子线程怎么密集计算、怎么阻塞、都不会影响JS引擎主线程，只主要等计算结束，将结果postMessage 传输给主线程就可以了。

另外，还有个对象叫`shareWorker`，与Web Worker 在概念所不同

* Web Worker 只属于某一个页面，不会喝其他标签页的Renderer 进程共享，`Web Worker`死属于`Renderer`进程创建的进程
* Share Worker 是由浏览器单独创建的进程来运行的JS程序，它被所有的Renderer进程所共享，在浏览器中，最多只能存在一个`shareWorker`进程

ShareWorker 由进程管理，Web Worker 是某一个`Renderer`进程下的线程

##### 浏览器的渲染流程

每个浏览器内核的渲染流程不一样，下面我们主要以`webkit`为主。首选是渲染的前奏：

1. 浏览器输入Url ，浏览器主进程接管，开了一个下载线程
2. 然后进行HTTP请求 （DNS查询，IP寻址等等），等待响应，开启下载响应报文
3. 将下载完的内容转交给Renderer 进程管理
4. 开始渲染....

在选渲染之前，需要理解一些概念：

* DOM Tree ：浏览器将HTML解析成树形的数据结构
* CSS Rule Tree：浏览器将CSS解析成树形的数据结构
* Render Tree ： DOM 树和CSS规则树合并后生成Render 树
* layout：有了Render Tree ，浏览器已经能知道页面中有哪些节点、各个节点的CSS定义以及他们的从属关系，从而去计算出每个节点在屏幕中的位置
* painting：按照算出的规则，通过显卡，把内容画到屏幕上
* reflow(回流)：当浏览器发现某个部分发生了点变化影响了布局，需要倒回去重新渲染，被人称这个回退的过程叫`reflow`。例如读取页面body高度`document.body,offsetWidth`就会触发浏览器的`reflow`行为，reflow 会从`<html>`这个root frame 开始递归往下，一次计算所有的节点几何尺寸和位置。reflow 几乎是无法避免的。现在界面上流行的一些效果，比如树状目录的折叠，展示（实质上是元素的显示和隐藏等）斗湖引起浏览器的reflow。鼠标滑过，点击....只要这些行为引起了页面上某些元素的占位面积、定位方式、边距等属性的变化，都会引起它内部、周围甚至整个页面的重新渲染。通常我们都无法预估浏览器到底会 reflow 哪一部分的代码，它们都彼此相互影响着。
* repaint (重绘) : 改变某个元素的背景色，文字颜色，边框颜色等等不影响它周围或内部布局的属性时，屏幕的一部分要重画，但元素的几何尺寸没有变。

注意：`display:none`的节点包含被加入 `Render Tree` ，而`visibility:hidden`则会，所以`display:none`会被触发`reflow`，`visibility:hidden`会触发`repaint`

浏览器内核拿到响应报文之后，渲染大概分为以下步骤：

1. 解析html 生成DOM树
2. 解析CSS规则
3. 根据DOM Tree 和 CSS Tree生成Render Tree
4. 根据Render 书进行layout ，负责各个元素节点的尺寸、位置计算
5. 绘制Render 树(painting)，绘制页面像素信息
6. 浏览器会将各层的信息发给GPU，GPU会将各层合成`composite`，显示在屏幕上

详细步骤略去，大概步骤如下，渲染完毕后JS引擎开始执行`load`事件，绘制流程见下图

![浏览器渲染流程](/medias/imges/js/promise/浏览器渲染过程.png)

由图中可以看出，css在加载过程中不会影响到DOM树生成，但是会影响到Render 树的生成，进而影响到 layout ，所以一般来说 style 的link 标签需要尽量放在head 里面，因为解析DOM树的时候是自上而下的，而css样式又是通过异步加载的，这样的话，解析DOM树下的body节点和加载css样式能尽可能的并行，加快 Render 树的生成的速度，当然，如果css是通过js动态添加进来的，会引起页面的重绘或重新布局。

从有html标准用来到目前为止，标准一直是规定了style元素不应该出现在body元素中。

前面提到的`load`事件，那么`DOMCotnetLoaded`事件有什么分别

* 当DOMContentLoaded 事件触发时，仅当DOM加载完成，不包括样式表，图片。（譬如如果有async加载的脚本就不一定完成）
* 当 onload 事件触发，页面上的所有DOM、样式表、脚本、图片都已经加载完成了。（渲染完毕）

顺序是：DOMContentLoaded ---> load 





### 一、JS特点

JS是一门单线程语言，它原本是被设计在浏览器端运行的脚本语言，单线程非常符合我们对DOM操作，体现简单并安全的特点，缺点：若出现长耗时的请求代码，JS只能等待长耗时的代码执行完，才能往下执行其他代码。由于JS会出现等待长时间的代码执行，这对我们的用户体验是极其不爽的，由此出现了异步模式的代码，由于JS是单线程语言这是不可更改的事实，但我们的浏览器可以多开一个线程让异步代码执行，自此我们的代码可分为同步模式，异步模式

同步模式：

* 排队执行
* 易阻塞

异步模式：

* 不会等待
* 后续任务交给回调函数
* 没有执行顺序
* Event loop
  * 调用栈执行完毕。才起作用
  * 监听调用栈和消息队列

在最开始我们是通过回调函数获取到异步任务执行后返回的结果，但当多个异步任务具有相互依赖的关系，这就不免出现多层嵌套的异步关系(回调地狱),即不方便连接代码的执行顺序，也不方便后期的维护，由此我们采用另一种更为高效的方案`Promise`来解决异步回调嵌套过深的情况。

### 二、Promise

#### 2.1 Promise 概述

Promise可以理解为内部对外部的一种承诺，它具有三种状态`Pending`、`Fulfilled`、`Rejected`，执行的结果只能有一种状态不能修改。进入任务队列的是promise的回调函数`resolve`和`reject`

> 注意：**Promise**的内部的同步代码还是会跟js代码执行而执行，而异步代码则会让其他会开启其他线程去完成，之后在结果在返回到消息队列中，在根据事件循环将异步代码执行好的结果压入调用栈执行。而Promise的`resolve`和 `reject`的回调函数属于微任务，它可以被理解为：**在调用栈执行所有的同步代码之后的代码尾巴开始执行，优先于消息队列中的宏任务执行。**

基本写法：

```js
const p = new Promise((resolve,reject)=>{
    // 由于promise的状态只有一个，成功或失败，所以resolve 和 reject 只能执行其中的一个
    resolve(100)
    reject(new Error('promise rejected'))
})

p.then(function(v){
    console.log(v)
},function(e) {
    console.log(e)
})
```

踩坑：杜绝 Promise 嵌套写法，使用	Promise 链式写法

```js
// Promise 常见误区
function ajax (url) {
  return new Promise(function (resolve, reject) {
    var xhr = new XMLHttpRequest()
    xhr.open('GET', url)
    xhr.responseType = 'json'
    xhr.onload = function () {
      if (this.status === 200) {
        resolve(this.response)
      } else {
        reject(new Error(this.statusText))
      }
    }
    xhr.send()
  })
}

// 嵌套使用 Promise 是最常见的误区,这样Promise就失去出现的意义
ajax('/api/urls.json').then(function (urls) {
  ajax(urls.users).then(function (users) {
    ajax(urls.users).then(function (users) {
      ajax(urls.users).then(function (users) {
        ajax(urls.users).then(function (users) {

        })
      })
    })
  })
})

// 正确写法，但也不是太简便，后面有更加简便写法 async await
ajax('/api/users.json')
  .then(function (value) {
    console.log(1111)
    return ajax('/api/urls.json')
  }) // => Promise
  .then(function (value) {
    console.log(2222)
    console.log(value)
    return ajax('/api/urls.json')
  }) // => Promise
  .then(function (value) {
    console.log(3333)
    return ajax('/api/urls.json')
  }) // => Promise
  .then(function (value) {
    console.log(4444)
    return 'foo'
  }) // => Promise
  .then(function (value) {
    console.log(5555)
    console.log(value)
  })
```

注意：

* 每个`then()`方法都返回一个全新的 Promise 链式写法

* 链式写法，后一个`then()`方法都是为上一个`then`方法返回的promise 对象添加明确状态后的回调，前面`then`方法中回调的返回值会作为后面`then`方法回调函数的参数，如果前面的`then`方法的回调函数返回的也是一个 promise 对象，那么后面`then`方法的回调函数会等待它的结束

#### 2.2 Promise 异常处理

promise的异常处理有两种方式，一种是在`then()`方法的第二个参数(回调函数)接收错误对象，另一种是在用链式调用的链式调用`catch`方法接收

*  then 第二参数接收，这种方法只会接收当前 Promise 对象注册的失败回调，而不会接收下一个promise对象

```js
// Promise 异常处理
function ajax (url) {
  return new Promise(function (resolve, reject) {
    // foo()
    // throw new Error()
    var xhr = new XMLHttpRequest()
    xhr.open('GET', url)
    xhr.responseType = 'json'
    xhr.onload = function () {
      if (this.status === 200) {
        resolve(this.response)
      } else {
        reject(new Error(this.statusText))
      }
    }
    xhr.send()
  })
}

ajax('/api/users11.json')
  .then(function onFulfilled (value) {
    console.log('onFulfilled', value)
  }, function onRejected (error) {
    console.log('onRejected', error)
  })

```

* 链式调用`catch`方法接收，因为 Promise 链条上的任何一个异常都会被一直向后传递，直至被捕获 ，这样注册相当于给整个 Promise 链条注册失败回调 

``` js
// Promise 异常处理

function ajax(url) {
    return new Promise(function(resolve, reject) {
        // foo()
        // throw new Error()
        var xhr = new XMLHttpRequest()
        xhr.open('GET', url)
        xhr.responseType = 'json'
        xhr.onload = function() {
            if (this.status === 200) {
                resolve(this.response)
            } else {
                reject(new Error(this.statusText))
            }
        }
        xhr.send()
    })
}

ajax('/api/users11.json')
  .then(function onFulfilled (value) {
    console.log('onFulfilled', value)
  })
  .catch(function onRejected (error) {
    console.log('onRejected', error)
}).finally(()=>{
    console.log('ok')
})
// 其实 catch(onRejected) 实际上就相等于 then(undefined, onRejected)
// finally 方法不管promise执行成功还是失败都会被执行
```

* 在浏览器中捕获 Promise 异常，类似于 window.onerror

```js
window.addEventListener('unhandledrejection', event => {
    const { reason, promise } = event

    console.log(reason, promise)
        // reason => Promise 失败原因，一般是一个错误对象
        // promise => 出现异常的 Promise 对象

    event.preventDefault()
}, false)
```

* Node.js全局捕获Promise异常

```js
process.on('unhandledRejection', (reason, promise) => {
  console.log(reason, promise)
  // reason => Promise 失败原因，一般是一个错误对象
  // promise => 出现异常的 Promise 对象
})
```

#### 2.3 Promise 静态方法

* Promise.resolve(可传递参数) : 相当于一个成功的promise回调
* Promise.reject(可传递参数) : 相当于一个失败的promise回调，不管你传入什么值都作为这个Promise失败的理由
* 调用这个两个方法都会返回一个promise对象，并且如果有传入参数，那么在返回的promise对象的`then`或`catch`方法中能获取到传入的参数

```js
// Promise.resolve()
Promise.resolve('foo')
    .then(res => {
        console.log(res)
    })

// 如果传入的是一个 Promise 对象，Promise.resolve 方法原样返回
var promise = ajax('/api/users.json')
var promise2 = Promise.resolve(promise)
console.log(promise === promise2) // true

// 如果传入的是带有一个跟 Promise 一样的 then 方法的对象，
// Promise.resolve 会将这个对象作为 Promise 执行
Promise.resolve({
        then: function(resolve, reject) {
            resolve('foo')
        }
    })
    .then(value => {
        console.log(value) // foo
    })

// Promise.reject 传入任何值，都会作为这个 Promise 失败的理由
Promise.reject('anything')
    .catch(function(error) {
        console.log(error) // anything
    })
```

#### 2.4 Promise 并行执行

有时根据业务的需求我们需要统一管理我们的发出的所有Promise对象，Promise给我们提供了两个并行执行的Promise静态方法，`Promise.all()`、`Promise.race()`，`Promise.allSettled()`

注意：它们的返回结果`res`都会根据数组的子promise位置而对应存放子promise返回的结果，并且是以数组的方式存储`[]`

* Promise.all(Promise对象构成的数组) ：Promise 数组中的所有Promise都成功了才成功，只要有一个失败则得到`reject`状态，而`all`方法返回的promise中的`res(结果)`会根据子promise位置顺序而有序的存放子promise返回的结果，是个`数组([])`
* Promise.race(Promise对象构成的数组) : `race`返回的结果只与子promise数组的第一个执行完的promise状态有关，而它的`res`也只与这个完成的promise返回的结果有关
* Promise.allSettled(promise数组)： `allSettled`方法是`ES2020`推出，它不管里面的子任务promise是否全成功还是存在失败的现象，都会放回一个成功的promise存在，然而可以根据`then(res)`的res获取到对应的promise结果，不管成功还是失败都有对应的返回对象

```js
// Promise 并行执行
function ajax (url) {
  return new Promise(function (resolve, reject) {
    // foo()
    // throw new Error()
    var xhr = new XMLHttpRequest()
    xhr.open('GET', url)
    xhr.responseType = 'json'
    xhr.onload = function () {
      if (this.status === 200) {
        resolve(this.response)
      } else {
        reject(new Error(this.statusText))
      }
    }
    xhr.send()
  })
}

// Promise.all()
var promise = Promise.all([
    ajax('/api/users.json'),
    ajax('api/posts.json')
])

promise.then(value => {
        console.log(value)
    })
    .catch(err => {
        console.log(err)
    })

// Promise.race()
const request = ajax('/api/posts.json')
const timeOut = new Promise((resolve, reject) => {
    setTimeout(() => {
        return reject(new Error('timeOut'), 500)
    })
})

Promise.race([
        request,
        timeOut
    ])
    .then(value => {
        console.log(value)
    })
    .catch(err => {
        console.log(err)
    })
// promise.allSettled()
const axios = require('axios')
const urls = [
    'https://api.github.com',
    'https://api.github.com/users'
]
const promise = Promise.allSettled(urls.map(item => axios(item)))
promise.then(res => {
    console.log(res)
})

```

#### 2.5 Promise执行时序

`new Promise(回调函数)`这里的回调函数是同步代码可视为在主线程中执行，而Promise的`then`方法中的回调函数在任务队列中作为微任务执行，**微任务执行时机可以理解为调用栈执行完所有的同步代码的时候，在其尾巴执行** ,但真实的是宏任务和微任务都有自己的执行表，它比宏任务执行在前

```js
// 微任务

console.log('global start')

// setTimeout 的回调是 宏任务，进入回调队列排队
setTimeout(() => {
  console.log('setTimeout')
}, 0)

// Promise 的回调是 微任务，本轮调用末尾直接执行
Promise.resolve()
  .then(() => {
    console.log('promise')
  })
  .then(() => {
    console.log('promise 2')
  })
  .then(() => {
    console.log('promise 3')
  })

console.log('global end')
// global start 
// global end
// promise
// promise 2
// promise 3
// setTimeout
```

#### 2.6 Promise.then的返回状态

Promise.then方法中的返回给的promise有三种状态

* then中不返回任何值，则外面接收到一个`resolved状态`的promise
* then中返还非promise对象；默认返还 resolved状态 promise对象并将返回的东西传入到一个then中，也就是说 若没有对上一个promise返回状态进行特殊指定 （是`resolved | rejected`）的话，那么返回的状态都是`resolved`并走下一个promise的`then`中的`resolved`的回调。若指定了，则为指定的状态返回。
* then中直接return promise对象，该promise对象是什么状态就是什么状态 

> 记住：当上一个promise 抛出错误而`throw  new Error()` | 返回的promise是rejected 状态的 走该promise 的then中失败的方法，其他都是走 成功的方法

```js
let p1 = new Promise((resolve, reject) => {
    reject('1')
})

p1.then(resolvedRes => {
    console.log('success', resolvedRes)
}, errorRes => {
    console.log('error1', errorRes)
}).then(resolvedRes2 => {
    console.log('success2', resolvedRes2)
}, errorRes2 => {
    console.log('error2', errorRes2)
})

// 输出结果
// error1 1
// success2 undefined
```

#### 2.7 自己封装mini版的Promise版本

已实现的功能：

* then 的 resolved 和 rejected 函数的注册
* promise 的链式调用
* 上一次返回的promise 结果可作为下一个promise 的参数传入
* 简单的catch方法的错误捕捉

MyPromise

```js
class KPromise {
    // 任务队列
    queue = {
        // resolved 状态执行的任务
        resolved: [],
        // rejected 状态执行的任务
        rejected: []
    }
constructor(handler) {
    // 执行 new Promise(回调函数) 的回调函数
    handler(this._resolve.bind(this), this._reject.bind(this))
}

//  提供外界改变Promise状态为 resolved 执行触发的函数 value 是外部调用传入的值
_resolve(value) {
    // MutationObserver() 方法是微任务的一种
    let obj = new MutationObserver((...args) => {
        let resolvedHandler
        while ((resolvedHandler = this.queue.resolved.shift()) && typeof resolvedHandler === 'function') {
            resolvedHandler(value)
        }
        obj.disconnect()
    })

    obj.observe(document.body, {
        attributes: true
    })
    document.body.setAttribute('_kkb', Math.random())
}

//  提供外界改变Promise状态为 rejected 执行触发的函数 error 是外部调用传入的值
_reject(error) {
    let obj = new MutationObserver((...args) => {
        let rejectHandler;
        while ((rejectHandler = this.queue.rejected.shift()) && typeof rejectHandler === 'function') {

            rejectHandler(error)
        }
        obj.disconnect()

    })
    obj.observe(document.body, {
        attributes: true
    })
    document.body.setAttribute('_kkb', Math.random())
}

// 提供外界可以通过 then 方法注册 成功还是失败的执行函数
then(resolvedHandler, rejectHandler) {

    // 再次返回一个Promise 用于链式调用
    return new Promise((resolve, reject) => {

        // 存储resolve 回调函数
        this.queue.resolved.push(resolvedRes => {
            if (typeof resolvedHandler === 'function') {
                let returnRes = resolvedHandler(resolvedRes)
                console.log(returnRes)
                if (returnRes instanceof KPromise) {
                    return returnRes.then(resolvedHandler)
                }
                resolve(returnRes)
            }
        })

        // 存储reject 回调函数
        this.queue.rejected.push(rejectedRes => {
            if (typeof rejectHandler === 'function') {
                let returnRes = rejectHandler(rejectedRes)
                if (returnRes instanceof KPromise) {
                    return returnRes.then(resolve)
                }
                resolve(returnRes)
            }
        })
    })
}
// 简单的错误捕获
catch (rejectedHandler) {
    console.log('catch')
    return this.then(undefined, rejectedHandler);
}
}
```

使用：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="js/MyPromise.js"></script>
</head>

<body>

    <script>
        let p1 = new KPromise((resolve, reject) => {
            let val = 100;
            resolve(val)
                // reject(val)
        });

        p1.then(resolveRes => {
                console.log('resolve1', resolveRes)
                return 22
            }, errorRes => {
                console.log('error1', errorRes)
            })
            .then(resolveRes2 => {
                console.log('resolve2', resolveRes2)

            }, errorRes2 => {
                console.log('error2', errorRes2)
            })
    </script>

    <script>
        let p2 = new KPromise((resolve, reject) => {
            let val = 100;
            // resolve(val)
            reject('1111')
        });

        p2.then(resolveRes => {
                console.log('resolve1', resolveRes)
                return 22
            }, errorRes => {
                console.log('error1', errorRes)
                return Promise.reject('抛出失败信息!!!')
            })
            .then(resolveRes2 => {
                console.log('resolve2', resolveRes2)

            }, errorRes2 => {
                console.log('error2', errorRes2)
            })
    </script>
</body>

</html>
```

面试题

```js
async function async1() {
    console.log('async1 start')
    await async2(); // Promise.resolve().then(()=>console.log("async end"))
    console.log("async1 end")
}
async function async2() {
    console.log('async2')
}
console.log('script start')
setTimeout(function() {
    console.log('setTimeout1')
}, 0)
async1()
new Promise(function(resolve) {
    console.log('promise1')
    resolve()
}).then(function() {
    console.log('promise2')
})
console.log('script end')
// 浏览器
// script start 、 async1 start、async2、promise1、script end、async1 end、promise2、setTimeout1
```

> 这里主要将await后面的代码(不包括await当前行那条，例如不包含`async2()`调用)当做一个微任务即可

### 三、Generator

上面的Promise的链式编程是可以解决回调函数嵌套过深的问题，但它的写法还不是最优的实现异步编程代码写法的扁平化，而Generator能做到这点

Generator特点

* 函数名前加 * 
* 调用生成器函数返回值是一个 generator 对象
* 只有调用`next()`方法函数体内的代码才会执行
* yield
  * 函数体内的`yield`后面跟`next()`方法执行后的返回值
  * 返回值中的 value 是值，done 是判断执行是否完毕】
  * **一次next方法会执行到一个`yield`的关键字前暂停执行**
  * yield 关键其实是暂停生成器执行，当有下一个`next`执行，则生成器函数继续往下一个`yield`执行
  * `next(参数)`方法执行的参数是会当做`yield`表达式的返回值
* generator.throw(new Error('Generator error'))  会让生成器执行的时候抛出错误，当一定要比`next()`后执行

```js
// Generator 配合 Promise 的异步方案

function ajax(url) {
    return new Promise((resolve, reject) => {
        var xhr = new XMLHttpRequest()
        xhr.open('GET', url)
        xhr.responseType = 'json'
        xhr.onload = () => {
            if (xhr.status === 200) {
                resolve(xhr.response)
            } else {
                reject(new Error(xhr.statusText))
            }
        }
        xhr.send()
    })
}

function* main() {
    try {
        const users = yield ajax('/api/users.json')
        console.log(users)

        const posts = yield ajax('/api/posts.json')
        console.log(posts)

        const urls = yield ajax('/api/urls11.json')
        console.log(urls)
    } catch (e) {
        console.log(e)
    }
}

function co(generator) {
    const g = generator()
    function handleResult(result) {
        console.log(result)
        if (result.done) return // 生成器函数结束
        result.value.then(data => {
            handleResult(g.next(data))
        }, error => {
            g.throw(error)
        })
    }

    handleResult(g.next())
}

co(main)
```

### 四、Async

真正意义上实现promise扁平化编程，他可以算是 Generator 的一个语法糖，现阶段`await`关键字只能出现在`async`函数下,否则报错，await 并不会阻塞外部代码的执行，但会阻止和它同一作用域的代码执行，可以借助`Generator中的yield关键字执行顺序理解`，它只是让我们更加扁平化获取到异步返回的结果数据,async函数也是会返回一个`Promise`对象，在外界可以用`then()`接收返回数据，用`catch()`接收返回的错误

> 注意：扁平化是指让我们异步代码编码得像同步代码，便于后期维护，并不是说让异步代码变成同步代码，我们这只是写法改变，而其本质是不变的。当一个`async`函数中有多个`await`修饰多个异步函数的时候，这时我们就可以看成异步函数依次执行，前者会阻塞后者执行，可以看成将`await`后面的代码放到这个`await`的返回的promise.then()中去，作为回调函数

```js
// Async / Await 语法糖

function ajax (url) {
  return new Promise((resolve, reject) => {
    var xhr = new XMLHttpRequest()
    xhr.open('GET', url)
    xhr.responseType = 'json'
    xhr.onload = () => {
      if (xhr.status === 200) {
        resolve(xhr.response)
      } else {
        reject(new Error(xhr.statusText))
      }
    }
    xhr.send()
  })
}

function co (generator) {
  const g = generator()

  function handleResult (result) {
    if (result.done) return // 生成器函数结束
    result.value.then(data => {
      handleResult(g.next(data))
    }, error => {
      g.throw(error)
    })
  }

  handleResult(g.next())
}

async function main () {
  try {
    const users = await ajax('/api/users.json')
    console.log(users)

    const posts = await ajax('/api/posts.json')
    console.log(posts)

    const urls = await ajax('/api/urls.json')
    console.log(urls)
  } catch (e) {
    console.log(e)
  }
}

// co(main)
const promise = main()

promise.then(() => {
  console.log('all completed')
})

```

#### await让异步代码实现依次执行

但我们在一个`async`函数内有有个`await`修饰多个异步代码，那么前一个`await`会阻塞后一个`await`执行，只有前面的异步代码状态成功后代码才能往下继续执行，若前一个异步代码执行失败，则会抛出错误。阻止代码继续向下运行。为了保证异步代码执行失败，其他代码仍然能继续往下执行，我们则需要使用`try{}catch(e){}`来捕获代码错误

```js
async function myfn() {

    try {
        // await  后面一定要跟一个  promise对象 ；
        await new Promise((resolve, reject) => {
            setTimeout(() => {
                console.log(1111);
                // resolve();
                reject("错误");
            }, 1000);
        })
        // try {
        //     await new Promise((resolve, reject) => {
        //         setTimeout(() => {
        //             console.log(1111);
        //             // resolve();
        //             reject("错误");
        //         }, 1000);
        //     })
        // } catch (err) {
        //     console.log(err)
        // }
        await new Promise((resolve, reject) => {
            setTimeout(() => {
                console.log(2222);
                resolve();
            }, 2000);
        })
        await new Promise((resolve, reject) => {
            setTimeout(() => {
                console.log(3333);
                resolve();
            }, 1000);
        })
    } catch (err) {
        console.log(err);
    }

}
myfn();
```

#### await会阻止同一作用域的代码执行，但不阻止外部外部代码执行

```js
async function main() {
   try {
    console.log(3333)
    let users = await ajax('/api/users.json')
    console.log(users)
    let posts =   await ajax('/api/posts.json')
    console.log(posts)
    let urls = await ajax('/api/urls.json')
    console.log(urls)
    console.log(4444)
       return users

     } catch (e) {
         console.log(e)
    }
    console.log('hahha')
}
main()
console.log('heheheh')
// 33
// heheheh
// users
// urls
// 4444 
```

