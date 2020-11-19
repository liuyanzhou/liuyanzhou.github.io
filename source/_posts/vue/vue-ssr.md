---
title: vue-ssr
date: 2020-11-05 22:54:07
categories: Vue
top: false
summary:  Vue-SSR
tags: 
 - Vue
---

## VUE-SSR

### 背景

我们之前传统使用`vue`开发的项目被称为单页面程序`(spa)`，你可以打开它的源代码，会发现里面就只有个一个div标签`<div id="app"></div>`。从这就会衍生出一些问题，例如你要开发一个新闻网站，采用的`spa`程序架构来开发的话，那么它是非常不利于我们网站的`SEO`优化的，所以对于用户强依赖搜索的网站传统的`SPA`开发是极其不合适的。那有人就会说，那我们可以采用传统的服务端渲染来写。当然这也是一种解决方案，但传统服务端渲染方案无异于是加重了服务器的压力，它既要处理请求，也要渲染页面。普通服务器几百人同时访问都有可能挂到。除了加重服务器压力，而且在传统的服务端渲染它还不能处理一些我们浏览器特有的事件。那我们也没有一种折中的办法呢？至此我们的**ssr**就衍生出来了。它是两者的折中办法。

**SSR的优点**

* 利于客户端SEO
* 直接将HTML字符串传递给浏览器，大大加快了首屏的加载时间

**SSR的缺点**

* SSR对一些常用的浏览器API可能无法正常使用（但对于浏览器的js我们有解决策略）
* 在VUE中只支持`beforeCreate`和`created`两个生命周期

### 一、vue的SSR

![通信类型](/medias/imges/vue/ssr/ssr.png)

从图上可分析：在vue的ssr中，我们要对项目设置两个打包入口

* app.js 是项目打包的公共入口
* server.entry 是后端的打包入口 ----> server.Bundle 打包后的文件(是一个自执行函数)
* client.entry 是浏览器的打包入口 -----> client.Boundle 打包的后的文件(是一个自执行函数)
* Bundle.Renderer 是用于将打包好的`server.Bundle.js`中的vue实例转化为字符串，可配置将其放入模板中去

最终我们将服务端的vue实例 出来并转化为一个`html`结构之后，之后我们需要将客户端打包好的`client.Bundle.js`加入到这个html文件上(这个过程就叫`客户端激活`)。

为什么要客户端激活：这是因为我们后端转化好vue实例的html结构它并不具备一些交互行为和一些写在`style`标签上的样式。它仅仅呈现着是一个我们写的html结构。而客户端激活时，我们会在一次`new Vue()`的实例，并挂载，触发`diff`算法，将元素本该有的样式和交互行为添加上去。

需要注意的是:

我们实现vue的ssr中 ，后端只对首屏访问的路径页面进行渲染生成字符串返回，而我们在浏览器中切换组件导致的页面更新，它还是我们利用vue的组件切换实现的，走的是前端渲染逻辑。也就是说我们首屏的代码才会出现在源代码上，而我们在浏览器上切换组件后显示的内容代码是不会出现在我们源代码上

### 二、简单的VUE-SSR的实现

需要安装的依赖包:

* vue 、vue-server-renderer  koa koa-router 

```
- 目录 src
 - index.html
 - server.js
```

index.html 

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <!--vue-ssr-outlet-->
</body>

</html>
```

server.js

```js
// vue vue-server-renderer 
// koa @koa/router

const Vue = require('vue');
const VueServerRenderer = require('vue-server-renderer');
const fs = require('fs');
const path = require('path');
const vm = new Vue({
    data() {
        return {
            name: 'zf',
            age: 10
        }
    },
    template: `<div>{{name}} 今年 {{age}} 岁了</div>`
});

const Koa = require('koa');
const Router = require('@koa/router');
const template = fs.readFileSync(path.resolve(__dirname, 'index.html'), 'utf-8');
const render = VueServerRenderer.createRenderer({
    template // 采用哪个模板去渲染
})

let app = new Koa(); // 产生一个app实例
let router = new Router(); // 产生一个路由实例

router.get('/', async(ctx) => {
    ctx.body = await render.renderToString(vm);
})


app.use(router.routes());
app.listen(3000, () => {
    console.log('服务器启动完毕....')
})
```

打开源码之后就可看到后端返回的渲染结果

## 三、vue中SSR实现

这里我们手动配置一个webpack，以及配置一些依赖包来实现ssr

```
 webpack webpack-cli webpack-dev-server (webpack核心模块)
 vue-loader(解析.vue文件 => template js style) vue-style-loader css-loader vue-template-compiler 
 @babel/core @babel/preset-env babel-loader(将babel与wabpack相关联)
 html-webpack-plugin
 vue-server-renderer vue vuex vue-router
 koa koa-router koa-static 
```

目录

```
├── config
│   ├── webpack.base.js
│   ├── webpack.client.js
│   └── webpack.server.js
├── dist
│   ├── client.bundle.js
│   ├── index.html
│   ├── index.ssr.html
│   ├── server.bundle.js
│   ├── vue-ssr-client-manifest.json
│   └── vue-ssr-server-bundle.json
├── package.json
├── public
│   ├── index.html
│   └── index.ssr.html
├── server.js
├── src
│   ├── App.vue
│   ├── components
│   │   ├── Bar.vue
│   │   └── Foo.vue
│   ├── entry-client.js
│   ├── entry-server.js
│   ├── app.js
│   ├── router.js
│   └── store.js
├── webpack.config.js
```

#### 2.1 改造app.js

将`app.js`的创建实例包装成一个函数，这用于让不用的用户访问时，都返回一个新的实例，避免数据污染

```js
import Vue from 'vue';
import App from './App.vue'
import createRouter from './create-router'
import createStore from './create-store'


export default () => {
    const router = createRouter();
    const store = createStore();
    const app = new Vue({
        router,
        store,
        render: h => h(App)
    });
    return { app, router, store } // 后续会导出路由 vuex相关的
}

// 1.以前代码在前端跑的时候 每个客户端都拥有一个独立的实例
// 2.每次客户端访问都要产生一个新的实例 这里导出一个函数
```

前端的打包入口 `client-entry.js`

```js
import createApp from './app'

const { app } = createApp();
app.$mount('#app'); // 客户端直接挂载即可
```

后端的打包入口`server-entry.js`

```js
import createApp from './app'

export default (context) => { // context.url 这里包含着当前服务端的路径
    return new Promise((resolve, reject) => {
        const { app, router, store } = createApp();
        router.push(context.url); // 默认跳转到路径里，有异步组件
        // onReady 监听路由
        router.onReady(() => {

            const matchComponents = router.getMatchedComponents(); // 获取匹配到的组件
            if (matchComponents.length > 0) { // 匹配到路由了
                // 调用组件对应的asyncData
                Promise.all(matchComponents.map(component => {
                    // 需要所有的asyncdata方法执行完毕后 才会响应结果
                    if (component.asyncData) {
                        // 返回的是promise
                        return component.asyncData(store);
                    }
                })).then(() => {
                    // 自动会将结果放到window上 vue-server-renderer 帮你实现
                    context.state = store.state; // 将状态放到上下文中

                    resolve(app) // 每次都是一个新的  只是产生一个实例 服务端根据实例 创建字符串 
                }, reject)

            } else {
                reject({ code: 404 }); // 没有匹配到路由
            }


        }, reject)
    })

}
```

打包的配置文件

* webpack.base.js

```js
// 默认运行webpack命令会调用此文件 webpack配置文件

// webpack webpack-cli webpack-dev-server (webpack核心模块)
// vue-loader(解析.vue文件 => template js style) vue-style-loader css-loader vue-template-compiler 
// @babel/core @babel/preset-env babel-loader(将babel与wabpack相关联)
// html-webpack-plugin

const path = require('path');

const VueLoaderPlugin = require('vue-loader/lib/plugin');
const resolve = (dir) => {
    return path.resolve(__dirname, dir); // 传入路径 通过当前文件所在的位置找到这个文件
}


module.exports = {
    output: { // 出口 通过打包的文件产生结果
        // 配置多个出口
        filename: '[name].bundle.js', // 结果的名字
        path: resolve('../dist') // 产生的路径
    },
    resolve: { //解析文件 按照以下顺序匹配
        extensions: ['.js', '.vue', '.css', '.jsx']
    },
    module: { // 针对不同的模块做处理

        rules: [{
                test: /\.vue$/,
                use: 'vue-loader'
            },
            {
                test: /\.css$/, // loader 的执行顺序 是从下到上 从右到左
                use: ['vue-style-loader', 'css-loader']
            },
            {
                test: /\.js$/,
                use: {
                    options: { // 告诉js 文件需要将es6->es5的插件
                        presets: ['@babel/preset-env']
                    },
                    loader: 'babel-loader' // 默认使用babel-loader => babel-core => preset
                },
                exclude: /node_modules/
            }
        ]

    },
    plugins: [
        new VueLoaderPlugin()
    ]
}
```

* webpack.client.js

```js
const base = require('./webpack.base');
const { merge } = require('webpack-merge');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const VueSSRClientPlugin = require('vue-server-renderer/client-plugin');
const path = require('path');
const resolve = (dir) => {
    return path.resolve(__dirname, dir); // 传入路径 通过当前文件所在的位置找到这个文件
};
// mergeoptions webpack merge

module.exports = merge(base, {
    mode: 'development',
    entry: {
        client: resolve('../src/client-entry.js')
    },
    plugins: [
        new VueSSRClientPlugin()
        // 前端打包出来的结果 只是用于挂载到服务端生成的字符串
        // new HtmlWebpackPlugin({
        //     template: resolve('../public/index.html')
        // })
    ]
});
```

* webpack.server.js

```js
// 通过入口 打包出一份代码来 代码给node使用

const base = require('./webpack.base');
const { merge } = require('webpack-merge');
const VueSSRServerPlugin = require('vue-server-renderer/server-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const path = require('path');
const resolve = (dir) => {
    return path.resolve(__dirname, dir); // 传入路径 通过当前文件所在的位置找到这个文件
};
// mergeoptions webpack merge

// webpack 打包服务端代码 是不需要引入打包后的js的 只是引入前端的打包后的结果
module.exports = merge(base, {
    mode: 'development',
    entry: {
        server: resolve('../src/server-entry.js')
    },
    target: 'node', // 给node使用
    output: {
        libraryTarget: 'commonjs2', // 指定导出方式
    },
    plugins: [
        new VueSSRServerPlugin(),
        new HtmlWebpackPlugin({
            filename: 'index.ssr.html', // html的名字
            template: resolve('../public/index.ssr.html'),
            minify: false, // 不压缩
            excludeChunks: ['server'] // 排除引入文件
        })
    ]
});
```

关联vue-router： create-router.js

```js
import Vue from 'vue';
import VueRouter from 'vue-router';

Vue.use(VueRouter);

const Foo = () =>
    import ('./components/Foo');
const Bar = () =>
    import ('./components/Bar');

export default () => {
    const router = new VueRouter({
        mode: 'history',
        routes: [
            { path: '/', component: Foo },
            { path: '/bar', component: Bar }
        ]
    });
    return router; // 每次调用此方法都可以返回一个路由实例
}
```

关联vuex : create-store.js

```js
import Vue from 'vue';
import Vuex from 'vuex'

Vue.use(Vuex);

export default () => {
    let store = new Vuex.Store({
        state: {
            name: 'jw'
        },
        mutations: {
            changeName(state, payload) {
                state.name = payload
            }
        },
        actions: {
            changeName({ commit }, payload) {
                return new Promise((resolve, reject) => {
                    setTimeout(() => {
                        commit('changeName', payload)
                        resolve()
                    }, 1000);
                })
            }
        }
    });
    // 前端允许时会执行此方法，用服务端的状态替换前端的状态
    if (typeof window !== 'undefined' && window.__INITIAL_STATE__) {
        store.replaceState(window.__INITIAL_STATE__)
    }
    return store
}
```

后端服务器代码:server.js

```js
const Vue = require('vue');
const VueServerRenderer = require('vue-server-renderer');
const fs = require('fs');
const path = require('path');
const Koa = require('koa');
const Router = require('@koa/router');
const KoaStatic = require('koa-static');

const app = new Koa();
const router = new Router();
// const serverBundle = fs.readFileSync(path.resolve(__dirname, './dist/server.bundle.js'), 'utf8');
// const template = fs.readFileSync(path.resolve(__dirname, './dist/index.ssr.html'), 'utf8');
// const render = VueServerRenderer.createBundleRenderer(serverBundle, {
//     template
// });

const serverBundle = require('./dist/vue-ssr-server-bundle.json');
const template = fs.readFileSync(path.resolve(__dirname, './dist/index.ssr.html'), 'utf8');
const clientManifest = require('./dist/vue-ssr-client-manifest.json')
const render = VueServerRenderer.createBundleRenderer(serverBundle, {
    template,
    clientManifest // 通过后端注入前端的js脚本
});
// 先匹配静态资源，资源找不到在找对应的api
app.use(KoaStatic(path.resolve(__dirname, 'dist')));

router.get('/(.*)', async(ctx) => {
    try {
        ctx.body = await render.renderToString({ url: ctx.url });
    } catch (e) {
        if (e.code === 404) {
            ctx.body = "page not found"
        }
    }

});


app.use(router.routes());


app.listen(4000, () => {
    console.log('服务器开启.....')
})
```

打包执行命令: package.json

```bash
"scripts": {
        "client:dev": "webpack-dev-server --config ./build/webpack.client.js",
        "client:build": "webpack --config ./build/webpack.client.js --watch",
        "server:build": "webpack --config ./build/webpack.server.js --watch",
        "run-all": "concurrently \"npm run client:build\"  \"npm run server:build\"  "
    }
```

