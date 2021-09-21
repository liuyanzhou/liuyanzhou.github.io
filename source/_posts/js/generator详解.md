---
title: generator详解
date: 2021-07-29 00:40:37
categories: JS
top: false
summary: generator详解
tags: 
 - JS
---

### 详解Generator

### Generator基础语法与概念

语法：`function *函数名(){}`创建一个生成器函数

```js
function *generator() {}
const itor = generator()
```

注意几点：

* `generator`执行不能通过`new`，并且这个执行并不是把函数体中的代码进行执行，而是返回一个结果，这个结果是`generator`函数把自己当做一个类，创建自己的实例对象返回
* 这个实例对象链上有`next,return,throw...`等方法和`Symbol.iterator`接口规范，我们将其称为迭代器对象
  * next：指向下一个`yield`的指令
  * return：返回值并且不再接下来的`next`指令将会无效
  * throw：抛错

实例说明：

```js
function* generator() {
    console.log('OK');
}
generator.prototype.test = 'zhufeng';
let itor = generator();
console.log(itor); // 迭代器对象
console.log(itor instanceof generator); //证明itor确实是generator类的实例，true
console.log(({}).toString.call(itor)); //"[object Generator]" 
```

`yield`的处理机制

```js
function* generator() {
    console.log('A');
    yield 1;
    console.log('B');
    yield 2;
    console.log('C');
    yield 3;
    console.log('D');
    return 4;
}
let itor = generator();
// 执行`next`方法，可以让GENERATOR函数中的代码执行；每一次执行NEXT，遇到YIELD则结束，返回结果是具备done && value的对象，并且value的值就是yield后面的值
console.log(itor.next()); //输出’A‘  {value: 1, done: false}
console.log(itor.next()); //输出’B‘  {value: 2, done: false}
console.log(itor.next()); //输出’C‘  {value: 3, done: false}
console.log(itor.next()); //输出’D‘  {value: 4, done: true}
console.log(itor.next()); //{value: undefined, done: true} 
```

结合`generator在next方法中传递值`

```js
function* generator() {
    console.log('A');
    let A = yield 1;
    console.log('B', A); // 输出A为100
    let B = yield 2;
    console.log('C', B); // 输出B为200
}
let itor = generator();
console.log(itor.next()); //{value: 1, done: false}
// 每一次NEXT传递的值「第一次传递的值没用」：都是作为上一次YIELD的返回结果
console.log(itor.next(100)); //{value: 2, done: false}
console.log(itor.next(200)); //{value: undefined, done: true} 
```

当`yield`返回的值是一个`generator`函数执行返回迭代对象时，如果我们想要进入其内部则需要在这个`generator`函数前面多加`*`，例如

```js
function* generator1() {
    yield 1;
    yield 2;
}
function* generator2() {
    yield 3;
    yield generator1();
    yield 4;
}
let itor = generator2();
console.log(itor.next()); //{value: 3, done: false} 
console.log(itor.next()); //{value: generator1执行创建的迭代器对象, done: false} 
console.log(itor.next()); //{value: 4, done: false} 
console.log(itor.next()); //{value: undefined, done: true}
```

改正：

```js
function* generator1() {
    yield 1;
    yield 2;
}
function* generator2() {
    yield 3;
    // yield*：可以进入到其他生成器中进行迭代
    yield* generator1();
    yield 4;
}
let itor = generator2();
console.log(itor.next()); //{value: 3, done: false} 
console.log(itor.next()); //{value: 1, done: false} 
console.log(itor.next()); //{value: 2, done: false} 
console.log(itor.next()); //{value: 4, done: false} 
console.log(itor.next()); //{value: undefined, done: true}
```

### generator在实际中运用

> 需求：第一个请求「1000MS」；第一个请求成功，才能发送第二个请求「2000MS」；第二个请求成功了，我们才可以发送第三个请求「3000MS」；当第三个请求也成功了，才算任务完成...
>
> 扩展：何为ajax的串行请求与并行请求
>
> * 串行：下一个请求的发送需要依赖上一个请求的结果
> * 并行：多个请求同时发送「相互之间没有任何依赖」，一般指所有请求都成功，我们再去做啥事情 例如我们使用的：Promise.all
>
> 真实项目中，并行请求是最多的，偶尔有一些需求需要串行处理；并行请求过多也不是什么好事，所以我们需要”并发管理、并行管控“；

数据模拟：

```js
const query = interval => {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve(interval);
        }, interval);
    });
};
```

方案一：基于promise中的`then`链机制实现串行即可

```js
query(1000).then(result => {
    console.log(`第一个请求成功：${result}`);
    return query(2000);
}).then(result => {
    console.log(`第二个请求成功：${result}`);
    return query(3000);
}).then(result => {
    console.log(`第三个请求成功：${result}`);
});
```

> 弊端：请求代码书写可读性太差，then链太长。

方案二：基于generator处理

```js
function* generator() {
    let result = yield query(1000);
    console.log(`第一个请求成功：${result}`);

    result = yield query(2000);
    console.log(`第二个请求成功：${result}`);

    result = yield query(3000);
    console.log(`第三个请求成功：${result}`);
}
let itor = generator();
itor.next().value.then(result => {
    itor.next(result).value.then(result => {
        itor.next(result).value.then(result => {
            itor.next(result);
        });
    });
});
```

> 弊端：请求代码书写较为可观，但迭代器执行出现了嵌套，可读性也差

方案三：实现一个`AsyncFunction`函数，函数中将我们迭代器一点点执行

```js
const isPromise = function isPromise(x) {
    if (x !== null && /^(object|function)$/i.test(typeof x)) {
        if (typeof x.then === "function") {
            return true;
        }
    }
    return false;
};
const AsyncFunction = function AsyncFunction(generator, ...params) {
    return new Promise(function (resolve, reject) {
        let itor = generator(...params);
        const next = x => {
            let {
                done,
                value
            } = itor.next(x);
            if (done) {
                resolve(value);
                return;
            }
            if (!isPromise(value)) value = Promise.resolve(value);
            value.then(
                result => next(result),
                reason => reject(reason)
            );
        };
        next();
    });
};
AsyncFunction(function* generator() {
    let result = yield query(1000);
    console.log(`第一个请求成功：${result}`);

    result = yield query(2000);
    console.log(`第二个请求成功：${result}`);

    result = yield query(3000);
    console.log(`第三个请求成功：${result}`);
}).then(() => {
    // GENERATOR函数中的内容全部正常执行完「例如：所有请求都成功」
    console.log('都成功了!!');
}).catch(reason => {
    // GENERATOR函数执行中出现问题「例如某个请求失败」，则直接结束即可
    console.log('请求失败', reason);
});
```

> 可见：这样子书写的代码可读性强，维护性也高，**其实这就是`async+await`底层原理的实现机制**

方案四：async+await：就是GENERATOR+PROMISE的语法糖

```js
(async function() {
    let result = await query(1000);
    console.log(`第一个请求成功：${result}`);

    result = await query(2000);
    console.log(`第二个请求成功：${result}`);

    result = await query(3000);
    console.log(`第三个请求成功：${result}`);
})();
```

