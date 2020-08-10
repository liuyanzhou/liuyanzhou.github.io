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

* Promise.resolve() : 相当于一个成功的promise回调
* Promise.reject() : 相当于一个失败的promise回调，不管你传入什么值都作为这个Promise失败的理由

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

Promise在任务队列中作为微任务执行，**微任务执行时机可以理解为调用栈执行完所有的同步代码的时候，在其尾巴执行** ,但真实的是宏任务和微任务都有自己的执行表，它比宏任务执行在前

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
* then中返还非promise对象；默认返还 resolved状态 promise对象并将返回的东西传入到一个then中
* then中直接return promise对象，该promise对象是什么状态就是什么状态 

```js
let p = new Promise((resolve, reject) => {
    resolve()
}); 
let p2 =  p.then(()=>{}) 
console.log(p2) // resolved状态
let p3 = p.then(()=>{
    return '123'
}) 
console.log(p3) // resolved
let p4 = p.then(()=>{
    return Promise.resolve('123')
})
console.log(p4) // resolved
```

### 三、Generator

上面的Promise的链式编程是可以解决回调函数嵌套过深的问题，但它的写法还不是最优的实现异步编程代码写法的扁平化，而Generator能做到这点

Generator特点

* 函数名前加 * 
* 调用生成器函数返回值是一个 generator 对象
* 只有调用`next()`方法函数体内的代码才会执行
* yield
  * 函数体内的`yield`后面跟`next()`方法执行后的返回值
  * 返回值中的 value 是值，done 是判断执行是否完毕
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

真正意义上实现promise扁平化编程，他可以算是 Generator 的一个语法糖，现阶段`await`关键字只能出现在`async`函数下,否则报错，await 并不会阻塞代码的执行，它只是让我们更加扁平化获取到异步返回的结果数据,async函数也是会返回一个`Promise`对象，在外界可以用`then()`接收返回数据，用`catch()`接收返回的错误

> 注意：扁平化是指让我们异步代码编码得像同步代码，便于后期维护，并不是说让异步代码变成同步代码，我们这只是写法改变，而其本质是不变的。当一个`async`函数中有多个`await`修饰多个异步函数的时候，这时我们就可以看成异步函数依次执行，前者会阻塞后者执行

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

