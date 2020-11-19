---
title: vue项目的优化
date: 2020-11-14 16:17:51
categories: 需求解决
top: false
summary: vue项目的优化
tags: 
 - vue项目的优化
---

### Vue项目的优化

###一、动态添加路由

我们可以通过用户的类型不同，例如普通用户有个人文章有编写，删除权限，但没有发表审核功能，发表审核功能是属于管理员的权限，那么我们在可以在针对文章这个模块来做不同用户的路由区分，之后在路由注册或路由切换时的钩子函数上动态进行路由注册，使用到的方法有：

* router.addRoutes([新路由的对象])  -----> 它会去将新路由注册上到router上去，如果已经存在该路由的父路由时，这个方法只会将新的子路由注册到router上去
* router.beforeEach() -----> 路由注册时机

### 二、vuex优化

场景：当我们在页面第一次加载时，我们要将数据存到vuex上，比如轮播图数据，由于这类数据是不会经常变化的，那么我们在使用系统时在此切换到home页就没必要在去请求数据库，可以判断vuex上的轮播图数据是否存在，存在便不再重新发送请求。否则，反之。

### 三、axios优化

场景：当我们的网很慢时，我们在加载第一个页面后又去切换了另一个页面，那么我们上一个请求就没必要在进行进行了，我们可以在每一个请求axios上添加一个特殊的请求标识，在页面切换后的钩子函数中手动去取消这些不必要的数据请求。

```js
let Cancel = axios.CancelToken
// 给这个axios创建一个唯一token 为了给取消请求
config.cancelToken = new Cancel(function(c) {
    store.commit(types.SET_REQUEST_TOKEN, c);
});
```

### 四、给请求上添加loading效果

我们没必要给每一个请求都加一个loading，我们完全可以在发送第一个请求开启loading，之后在发送请求就不必要在去另开loading。这要怎么实现呢，我们可以将axios封装一个类，之后在这个类上定义`this.queue={}`属性。在发送请求的钩子内每一次发送请求就往这个属性添加好`请求路径:true`，在完成请求的钩子上`delete 请求路径`删除这个属性上的路径对应的属性。那么我们可以直接在请求的钩子上判断这个`this.queue={}`是否有属性，当没有属性那么就是第一个请求发送，**开启loading**，在结束的响应钩子内判断`this.queue`数据是否已被删除全，若没有属性了，就`关闭loading`

```js
setInterceptor(instance, url) {
    instance.interceptors.request.use((config) => {
        // 所有的请求都会走这轮
        if (Object.keys(this.queue).length == 0) {
            // 当前是所有请求中的第一个
            loadingInstance = Loading.service({ fullscreen: true });
        }
        this.queue[url] = true;
        return config;
    });
    instance.interceptors.response.use(res => {
        delete this.queue[url]; // 删除
        if (Object.keys(this.queue).length == 0) {
            // 当前是所有请求中的第一个
            loadingInstance.close();
        } 
        return res.data
    }, err => { // 失败直接返回失败的promoise
        delete this.queue[url]; // 删除
        if (Object.keys(this.queue).length == 0) {
            // 当前是所有请求中的第一个
            loadingInstance.close();
        }
        return Promise.reject(err);
    })
}
```

