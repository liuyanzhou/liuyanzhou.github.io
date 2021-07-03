---
title: promise原理分析
date: 2020-11-22 09:17:41
categories: 原理分析
top: false
summary: promise原理分析
tags: 
 - 原理分析
---

### Promise

promise三种状态：`pending`、`fulfilled`、`rejected`

promise走then的错误处理函数二种方式：

* 取决于上一个promise是否为`rejected`状态
* 上一个promise在执行函数的时候抛错，`throw new Error`

promise解析流程：

> 我们`new Promise(execute)`，execute函数立刻执行，这个函数如果抛错了或者调用了`reject`函数，则会将reject函数返回值或抛错信息往下一个promise传递。

```js
let p1 = new Promise((resolve,reject)=>{
    resolve('success')
})
p1.then(res=>{
    console.log(res) // success
})

let p2 = new Promise((resolve,reject)=>{
    reject('fail')
})
p2.then(res=>{
    console.log(res) 
},err=>{
    console.log(err) // fail
})
```

> 如何在`new Promise(resolve,reject)`的resolve方法内返回另一个promise（p2），则then返回的promise的状态是取决p2的，如果p2还内嵌promise，则promise会一直递归解析，直到解析出普通值，若在解析的过程中，如果遇到了一个promise的状态为reject，直接就走下一个then的错误处理函数，不管`reject`是否还返回promise或值，全作为下一个then的错误处理函数的参数传入

```js
let p = new Promise((resolve,reject)=>{
    resolve(new Promise(rs,rj)=>{
        rj(new Promise((rs2,rj2)=>{
            rs2('123')
        }))
    })
})

p.then(()=>{},err=>{
    console.log('err',err) // err promise {xxxx}
})
```

> 在`then`中返回的是普通值，也会都下一个then的成功处理函数

注意：`new Promise(execute)`execute函数中一定要执行`resolve`和`reject`其中之一，否则会阻塞