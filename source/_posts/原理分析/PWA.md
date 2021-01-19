---
title: PWA
date: 2021-01-17 10:39:24
categories: 原理分析
top: false
summary: PWA概述
tags: 
 - 原理分析
---

### PWA（Progressive Web Apps）

webapp用户体验差（不能离线缓存），用户粘性低（无法保存入口），pwa就是为了解决这一系列问题（Progressive Web Apps），让webapp具有快速、可靠、安全等特点

PWA是一系列的技术

* Web App Manifest
* Service Worker
* Push Api & Notification Api
* App Shell & App Skeleton
* ....

### 一、Web App Manifest

> 作用：将网站添加到桌面、更类似`native`的体验

Web App Manifest 配置

```html
<link rel="manifest" href="/manifest.json">
```

manifest.json

```json
{
    "name": "PWA效果展示",  // 应用名称 
    "short_name": "PWA", // 桌面应用的名称  ✓
    "display": "standalone", // fullScreen (standalone) minimal-ui browser ✓
    "start_url": "/", // 打开时的网址  ✓
    "icons": [{ // 设置桌面图片 icon图标
        "src": "/icon.png",
        "sizes": "144x144",
        "type": "image/png"
    }],
    "background_color": "#aaa", // 启动画面颜色
    "theme_color": "#aaa" // 状态栏的颜色
}
```

> ios 不支持`manifest.json`文件，可以通过`meta/link`私有属性进行设置

```html
<!-- 图标icon -->
<link rel="apple-touch-icon" href="/icon.png"/>
<!-- 添加到主屏后的标题 和 short_name一致 -->
<meta name="apple-mobile-web-app-title" content="标题"> 
<!-- 隐藏safari地址栏 standalone模式下默认隐藏 -->
<meta name="apple-mobile-web-app-capable" content="yes" /> 
<!-- 设置状态栏颜色 -->
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" /> 
```

### 二、Service Worker

> service worker的核心就是**能够拦截请求**，配合`caches Api`能够让web App 实现`普通app`的离线缓存效果。**caches api 缓存的是整个文件**

**Service Worker 特点：**

* 不能访问 / 操作dom
* 会自动休眠，不会随浏览器关闭所失效（必须手动卸载）
* 离线缓存内容开发者可控
* 必须在`https`或者`localhost`下使用
* 所有的api都基于`promise`
* ...

**生命周期**

* 安装（installing）：这个状态发生在`Service Worker`注册之后，表示开始安装，触发`install`事件回调指定一些静态资源进行离线缓存
* 安装后（installed）：Service Worker 已经完成了安装，并且等待其他的`Service Worker`线程被关闭
* 激活（activating）：在这个状态下没有被其他的 `Service Worker`控制的客户端，允许当前的`worker`完成安装，并且清除了其他的`worker`以及关联缓存的旧缓存资源，等待新的`Service Worker`线程被激活
* 激活后（`activated`)：在这个状态会处理activate事件回调（提供了更新缓存策略的机会）。并可以处理功能性的事件 `fetch(请求)`、sync（后台同步）、push（推送）。
* 废弃状态（redundant）：这个状态表示一个 `Service Worker`的生命周期结束。

**关键方法**

* `self.skipWaiting()`：表示强制当前处在`waiting`状态的`Service Worker`进入`activate`状态
* `event.waitUntil()`：传入一个`Promise`为参数，等到该`Promise`为`resolve`状态为止
* `self.clients.claim()`：在`activate`事件回调中执行该方法表示取得页面的控制权，这样之后打开页面都会使用版本更新的缓存，旧的`Service Worker`脚本不再控制着页面，之后会被停止。

部分实例代码:

index.html

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="manifest" href="/manifest.json">

    <link rel="apple-touch-icon" href="/icon.png" />
    <!-- 添加到主屏后的标题 和 short_name一致 -->
    <meta name="apple-mobile-web-app-title" content="标题">
    <!-- 隐藏safari地址栏 standalone模式下默认隐藏 -->
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <!-- 设置状态栏颜色 -->
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
    <link rel="stylesheet" href="./index.css">
</head>

<body>
    <h1>PWA 网站</h1>
    <ul></ul>
    <script src="/main.js"></script>
    <script>
        // 给网站注册serviceWorker, 注册时机  等待资源加载完毕后 ，在开线程
        function urlBase64ToUint8Array(base64String) {
            const padding = '='.repeat((4 - base64String.length % 4) % 4);
            const base64 = (base64String + padding)
                .replace(/-/g, '+')
                .replace(/_/g, '/');

            const rawData = window.atob(base64);
            const outputArray = new Uint8Array(rawData.length);

            for (let i = 0; i < rawData.length; ++i) {
                outputArray[i] = rawData.charCodeAt(i);
            }
            return outputArray;
        }

        // 将公钥转为Buffer类型
        const convertedVapidKey = urlBase64ToUint8Array('BKn9WZWSFKaRlWfxwg32xV5M_IYr_nUFRQnS8tb_fR_1X1Ga_xP2TGfObHtKZzDeVBSJfoNasD_-N5qnYyg5enc');

        window.addEventListener('load', async() => {
            if ('serviceWorker' in navigator) {
                // 注册 service worker
                let registration = await navigator.serviceWorker.register('/sw.js');

                // 等待serviceWorker 激活成功成 订阅
                await navigator.serviceWorker.ready;
                // 与push service 服务器进行交互
                let pushSubscription = await registration.pushManager.subscribe({
                    userVisibleOnly: true,
                    applicationServerKey: convertedVapidKey
                });

                // 拿到pushSubscription 可以推送消息， 我将他转交给我们的服务器，我们的服务器可以用他来通知pushService -> 浏览器
                await fetch('/add-sub', {
                    headers: {
                        'content-type': 'application/json'
                    },
                    method: 'POST',
                    body: JSON.stringify(pushSubscription)
                })
            }
        })
    </script>

</body>

</html>
```

serviceWorker.js

```js
// 对资源进行离线缓存  seriveWorker 可以自定义缓存的内容
const CACHE_NAME = 'cache_v' + 2;
// 缓存列表
const CAHCE_LIST = [ // 列表越长 越容易出问题
    '/',
    '/index.html',
    '/main.js',
    '/index.css',
    '/api/list',
    '/manifest.json',
    '/icon.png'
];

// serviceWorker的核心就是离线拦截请求
async function fetchAndSave(request) {
    let res = await fetch(request); // 数据流
    let cloneRes = res.clone(); // 为了保证不破坏原有的响应结果
    let cache = await caches.open(CACHE_NAME);
    await cache.put(request, cloneRes); // 用响应结果更新缓存
    return res;
}
self.addEventListener('fetch', (e) => {
    // 如果是静态资源 不做拦截
    let url = new URL(e.request.url);
    if (url.origin !== self.origin) {
        return
    }

    // 缓存策略， 如果接口是不停的变化的 我们希望将数据更新到缓存中
    if (e.request.url.includes('/api')) {
        return e.respondWith(
            fetchAndSave(e.request).catch(res => {
                return caches.match(e.request);
            })
        )
    }

    // serviceWorker中不支持ajax， 但是支持fetch
    // 如果断网了, 抛出异常，并从catchs中获取请求数据
    e.respondWith(
        fetch(e.request).catch(res => {
            return caches.match(e.request);
        })
    )
});


// 当serviceWorker 安装时 需要跳过等待
async function preCache() {
    let cache = await caches.open(CACHE_NAME); // 创建一个缓存空间
    await cache.addAll(CAHCE_LIST);
    await self.skipWaiting()
}
self.addEventListener('install', (e) => {
    // e.waitUtil表示等待promise执行完成
    // 预先将缓存列表的数据缓存起来
    e.waitUntil(preCache())
})
async function clearCache() {
    let keys = await caches.keys();
    return Promise.all(keys.map(key => {
        if (key !== CACHE_NAME) {
            return caches.delete(key)
        }
    }))
}
// serviceWorker 不是立即生效，需要在下一次访问的时候才生效
self.addEventListener('activate', (e) => {
    e.waitUntil(Promise.all([clearCache(), clients.claim()])); // 激活后立刻让serviceWorker拥有控制权
})

// workbox ->workbox-webpack-plugin

self.addEventListener('push', function(e) {
    console.log(e.data)
    self.registration.showNotification(e.data.text())
})
```

### 三、push Api

[`Web Push Protocol`](https://tools.ietf.org/html/draft-ietf-webpush-protocol-12)

```
+-------+           +--------------+       +-------------+
 |  UA   |           | Push Service |       | Application |
 +-------+           +--------------+       |   Server    |
     |                      |               +-------------+
     |      Subscribe       |                      |
     |--------------------->|                      |
     |       Monitor        |                      |
     |<====================>|                      |
     |                      |                      |
     |          Distribute Push Resource           |
     |-------------------------------------------->|
     |                      |                      |
     :                      :                      :
     |                      |     Push Message     |
     |    Push Message      |<---------------------|
     |<---------------------|                      |
     |                      |                      |
```

**核心实现流程**

* `Subscribe`：向Push Service 发起订阅，获取`PushSubscription`
* `Monitor`：实现浏览器和`PushService`通信（用户离线，PushService会维护消息列表）
* `Distribute Push Resource`：将`PushSubscription`交给服务器，用于通信
* `Push Message`：服务端将消息推送给`Push Service`。`Push Service`在推送给对应的客户端

实现服务端推送

```js
function urlBase64ToUint8Array(base64String) {
    const padding = '='.repeat((4 - base64String.length % 4) % 4);
    const base64 = (base64String + padding)
        .replace(/-/g, '+')
        .replace(/_/g, '/');

    const rawData = window.atob(base64);
    const outputArray = new Uint8Array(rawData.length);

    for (let i = 0; i < rawData.length; ++i) {
        outputArray[i] = rawData.charCodeAt(i);
    }
    return outputArray;
}
const publicKey = 'BKn9WZWSFKaRlWfxwg32xV5M_IYr_nUFRQnS8tb_fR_1X1Ga_xP2TGfObHtKZzDeVBSJfoNasD_-N5qnYyg5enc';
const convertedVapidKey = urlBase64ToUint8Array(publicKey); // 通过公钥通信确保安全, 类型要求是ArrayBuffer

window.addEventListener('load', async () => {
    if ('serviceWorker' in navigator) {
        let registration = await navigator.serviceWorker.register('/sw.js');
        // 等待serviceWorker激活后
        await navigator.serviceWorker.ready;
        let pushSubsription = await registration.pushManager.subscribe({
            userVisibleOnly: true,
            applicationServerKey: convertedVapidKey
        });
        // 服务器这是我的通信对象
        fetch('/add-sub', {
            headers: {
                "Content-Type": 'application/json'
            },
            method: 'post',
            body: JSON.stringify(pushSubsription)
        })
    }
});
```

服务端代码

```js
const express = require('express');
const fs = require('fs');
const path = require('path');
const bodyParser = require('body-parser');
const webpush = require('web-push');
const app = express();
let list = require('./data.json');

const vapidKeys = {
    publicKey: 'BKn9WZWSFKaRlWfxwg32xV5M_IYr_nUFRQnS8tb_fR_1X1Ga_xP2TGfObHtKZzDeVBSJfoNasD_-N5qnYyg5enc',
    privateKey: 'bmsKpg6rE-K-LgU_DAIPynBdD8AK8hal8IMfYo3IyVc'
}
const subs = [];
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname)));
app.use(express.static(path.join(__dirname, 'public')))
app.get('/api/list', (req, res) => {
    let start = Math.floor(Math.random() * (list.length - 20));
    res.json(list.slice(start, start + 20));
});
// 添加pushMessageSubscription
app.post('/add-sub', (req, res) => {
    console.log('111232')
    subs.push(req.body);
    res.json({ data: 'ok' })
});
webpush.setVapidDetails(
    'mailto:1035465284@qq.com',
    vapidKeys.publicKey,
    vapidKeys.privateKey
)
// 服务端进行推送
app.get('/server-push', (req, res) => {
    console.log(subs)
    subs.forEach(sub => webpush.sendNotification(sub, JSON.stringify('hello zf')));
    res.end();
});
app.listen(3000, () => {
    console.log(`pwa server port:3000`)
});
```

> 服务端获取对象后，可以直接给我们的客户端推送消息了

### 四、Notification

在`ServiceWorker`中监听服务端推送的消息

```js
self.addEventListener('push', function (e) {
    var data = e.data;
    if (e.data) {
        self.registration.showNotification(data.text());        
    }  else {
        console.log('push没有任何数据');
    }
});
```

> 这样可以实现`App`中信息推送的功能，当然可以直接使用`Notification API`，但是大部分情况，我们还是会配合`push Api`进行使用