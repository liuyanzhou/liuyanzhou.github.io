---
title: wepy
date: 2020-07-06 19:05:42
categories: 小程序
top: false
summary: wepy
tags: 
 - 小程序
---

## wepy

> `Wepy`是腾讯官方出品的一个小程序快速开发框架，对原生小程序的开发模式进行了再次封装，wepy相比于原生小程序开发，拥有众多的开发特性和优化方案

* 开发风格接近于 Vue.js ，支持很多 vue 中的语法特性
* 通过 polyfill 让小程序完美支持 Promise
* 可以使用ES6等诸多高级语法特性，简化代码，提高开发效率
* 对小程序本身的性能做出了进一步优化
* 支持第三方的 npm 资源
* 支持多种插件处理和编译器
* [开发文档](https://wepyjs.github.io/wepy-docs/)

## 一、wepy的安装与运行

* wepy 的安装或更新都通过`npm`进行全局安装

```bash
npm i wepy-cli -g
```

* 初始化 wepy 项目
  * `wepy init` 固定写法，代表要初始化`wepy`项目
  * `standard` 代表模板类型为标准模板，可以运行`wepy list`命令查看所有可用的项目模板
  * `myproject` 为自定义的项目名称

```bash
wepy init standard myproject
```

* 运行编译`wepy`项目
  * 运行`cd myproject` 切换至 `wepy-cli`构建出来的项目根目录
  * 运行`npm i` 安装`wepy`项目依赖项
  * 运行`wepy build --watch` 开启实时编译，该条命令会循环监听`wepy`项目中的源代码的变化，自动编译生成小程序项目

**注意：生成的小程序项目默认被存放于 dist 目录中 **

* 认识`wepy`项目目录

![wepy目录](/medias/imges/xcx/wepy/img1.png)

* 将`wepy`项目导入到开发者工具中
  * 1.7.0 版本之后的`wepy-cli`工具生成的项目根目录下，包含`project.config.json`文件，记录项目的基本配置详细，例如：项目名称，appId，生成的小程序项目根路径等
  * 如果项目中存在`project.config.json`文件，使用 微信开发者工具-> 导入项目，项目目录请选择 wepy 项目根目录，开发者工具会根据 `project.config.json`文件的配置，把`wepy`编译生成的小程序项目加载到微信开发者工具中
* wepy 构建出来的项目 和 小程序项目的关系

> 通过`wepy init ` 命令初始化的`wepy`项目，实际是一个模板项目，不能直接当做小程序运行。需要运行相关操作(也就是运行`wepy build --watch`命令生成小程序项目 ），把模板项目编译为小程序项目，才可以运行

![wepy项目与小程序项目比较](/medias/imges/xcx/wepy/img2.png)

## 二、wepy文件介绍

### 2.1 介绍`.wpy`文件的组成部分

* 一个`.wpy`文件可分为三大部分,各自对应一个标签：
  * 脚本部分，即`<script></script>`标签中的内容，又可分为两个部分：
    * 逻辑部分，除了`config`对象之外的部分，对应元素的`.js`文件
    * 配置部分,即`config`对象，对应原生的`.json`文件
  * 结构部分，即`<template></template>`模板部分，对应于原生的`.wxml`文件
  * 样式部分，即`<style></style>`样式部分，对应于原生的`.wxss`文件
* 其中，小程序入口文件`app.wpy`不需要`<template></template>`,所以编译时会被忽略

### 2.2 `.wpy`文件的使用说明

* `.wpy`文件中的`script`、`template`、`style`这三个标签都支持`lang`和`src`属性
  * `lang`决定了其代码编译过程
  * `src`决定是否外联代码，存在 src 属性且有效时，会忽略内联代码
* 语法属性

| 标签     | lang默认值 | lang支持值                       |
| -------- | ---------- | -------------------------------- |
| style    | css        | css、less、scss、stylus、postcss |
| template | wxml       | wxml、xml、pug(原jade)           |
| script   | bable      | babel、TypeScript                |

* 案例代码

```html
<style lang="less" src="page1.less"></style>
<template lang="wxml" src="page1.wxml"></template>
<script>
    // some code
</script>
```

### 2.3 小程序入口`app.wpy`

> 入口文件`app.wpy`中所有声明的小程序实例继承自`wepy.app`类，包含一个`config`属性和其它全局属性、方法、事件	

- `config` 属性会被编译为小程序的 `app.json` 全局配置文件；
- `config` 属性之外的其它节点，会被编译为小程序的 `app.js` 文件；
- style 标签会被编译为小程序的 `app.wxss` 全局样式；

例如全局配置小程序的外观:

在小程序的入口文件中找到 window 节点：`app.wpy` -> `script标签` -> `config` -> `window` 即可全局配置小程序的外观

```js
<script>
    import wepy from 'wepy';
export default class extends wepy.app {
    config = { // 对应 app.json 文件
        "window": { // 全局配置小程序外观
            "backgroundTextStyle": "light",
            "navigationBarBackgroundColor": "#fff",
            "navigationBarTitleText": "WeChat",
            "navigationBarTextStyle": "black"
        }
    } }
        </script>
```

### 2.4 页面`.wpy`文件中 `script`标签组成结构

> 页面文件`page.wpy`中所声明的页面实例继承自`wepy.page`类

| 属性        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| **config**  | 页面配置对象，对应于原生的`page.json`文件，类似于`app.wpy`中的config |
| components  | 页面组件列表对象，声明页面所引入的组件列表                   |
| **data**    | 页面渲染数据对象，存放可用于页面模板绑定的渲染数据           |
| **methods** | wxml 事件处理函数对象，存放响应 `wxml`中所捕获到的事件，如`bindtap`、`bindchange` |
| events      | wepy组件事件处理函数对象，存放响应组件之间`$broadcast`、`$emit`、`$invoke`所传递的事件的函数 |
| **其它**    | 小程序页面生命周期函数,如`onLoad`、`onReady`等，以及其它自定义的方法与属性 |

## 三、wepy开发规范与使用

### 3.1 如何设置默认首页

* 打开`src`->`app.wpy`入口文件，将新创建的页面路径，注册到`config` -> `pages`数组，并调整为数组的第一项即可

```html
<script>
    import wepy from 'wepy'
    import 'wepy-async-function'
    export default class extends wepy.app {
        config = {
            pages: ['pages/home', 'pages/index’]
                    } }
</script>
```

### 3.2 创建页面的主要事项

* 每个页面的`script`标签中，必须导入`wepy`模块，并创建继承自`wepy.page`的页面类；否则会报错
* 每个页面的路径，必须记录到`app.wpy`的`config`->`pages`数组中
* 页面路径记录完毕之后，必须再回到页面文件中，摁下`Ctrl+S`快捷键重新编译生成页面，否则会报错

### 3.3 修改data 中的数据

在`wepy`框架中，修改data 中的值可以直接`this.要修改data中的键名 = xxx`,如果是异步获取到数据，那么还要多要不`this.$apply()`强制页面重新渲染，下面有讲到

### 3.4 页面绑定事件以及传参

####3.4.1 wepy绑定事件

> 原生小程序使用 bindtap、bindinput 等绑定事件处理函数，在 wepy 框架中，优化了事件绑定机制，支持类似于 Vue.js 的事件绑定语法，今后绑定事件可以采用如下方式
>
> 原 bindtap="clickHandler" 替换为 @tap="clickHandler" 
>
> 原 bindinput="inputHandler" 替换为 @input="inputHandler" 

在`wepy`中,统一使用`@`绑定事件，传递参数直接采用`@tap="handle({{oarams}})"`传递

```html
<button type='warn' @tap='handle({{age}})'>WePY 绑定事件</button>
```

```js
data = {
    age: 18
}

methods = {
    handle: function (params) {
        console.log('触发函数')
        console.log(params)
    }
}
```

**页面绑定事件的注意事项**

* 通过`@`符号绑定的事件处理函数，必须定义在页面的`methods`节点下
* `wepy`中的`methods`属性只能声明页面`wxml`标签的事件处理函数，不能声明自定义方法
* 自定义方法需要声明到和`methods`平级的节点位置，这与`vue`中的用法是不一致的

```js
data = {
    age: 18
}

methods = {
    handle: function (params) {
        console.log('触发函数')
        console.log(params)

        this.add()
    }
}

add () {
    console.log('自定义事件')
}
```

####3.4.2 将文本框与 data 做双向数据绑定 

* 为 input 输入框绑定数据和事件处理函数，代码为：`<input value="{{msg}}" @input="inputHandler" />`
* 在 methods 中定义事件处理函数，函数名称为 inputHandler 
* 在事件处理函数中，通过事件参数 e.detail.value 获取到最新的文本框内容 
* 通过 this.msg = e.detail.value 为 data 中的 msg 重新赋值 

演示代码：

```html
<view>{{ age }}</view>
<input type="text" @input='inputHandle' value="{{ val }}" />
```

```js
data = {
    age: 18,
    val: ''
}

methods = {
    inputHandle: function (e) {
        console.log(e.detail.value)
        this.val = e.detail.value
    }
}
```

####3.4.3 小程序数据基本都是在`onLoad `中请求

### 3.5 wxs 脚本的使用

在wepy 中使用 wxs 脚本的方式如下：

* 将 wxs 脚本定义为外联文件，并且后缀名为`.wxs`
* 在`<script></script>`标签内，通过`import`导入相对路径的 `wxs`模块
* 在当前页面的 class 类中，通过 wxs = {} 注册刚才导入的 wxs 模块

**注意：被注册的 wxs 模块，只能在当前页面的 template 中使用，不能在 script 中使用**

```js
// wxs
var a = 10

module.exports = {
  a: a
}
```

```html
<view>{{ homeData.a }}</view>
```

```js
import homeWxs from '../wxs/home.wxs'

export default class Home extends wepy.page {
    wxs = {
        homeData: homeWxs
    }
}
```

### 3.6 配置 `promisify` 启用`async` 和 `await`

* 默认使用`wepy-cli`创建的项目，不支持使用`ES7`的`async`和`await`来简化`Promise API`的调用
* 需要手动开启此功能，打开`src->app.wpy`，找到`constructor()`构造函数，在构造函数中代码的最后一行，添加`this.use('promisify')`

```js
constructor () {
    super()
    this.use('requestfix')
    this.use('promisify')  // 添加此行代码，即可启用 async 和 await
}
```

### 3.7 wepy 发送 Get 和 Post 请求

> wepy  框架对原生小程序做了封装，之前通过`wx`调用的`API`,都可以直接使用`wepy`进行调用

* GET 请求

```js
// wepy 发送 Get 请求

async getInfo () {
    const res = await wepy.request('接口地址')

    console.log(res)
}
```

* POST 请求

```js
// wepy 发送 Post 请求

async getInfo () {
    const res = await wepy.request({
        url: '接口地址',
        method: 'post',
        data: {
            name: 'loong'
        }
    })

    console.log(res)
}
```

* 异步更新数据

在异步函数中更新数据的时候，页面检测不到数据的变化，**必须手动调用`this.$apply()方法`**，作用是 强制刷新页面重新渲染

```js
// 被 async 修饰的函数叫做异步函数

async getInfo() {
    const res = await wepy.request('接口地址’)
       this.getMsg = res.data
       this.$apply()
    }

```

## 四、wepy 开发需要注意路径问题

* 在`app.wpy`的 `pages`节点下的页面路径，必须是`page/tabs/home`形式开头，这是因为我们小程序项目要根据`wepy build --watch`实时编译的到`dist`目录下的结构来查找到页面文件
* 在`app.wpy`的`tabBar`节点下的图标路径(`iconPath、selectedIconPath`)，是以`dist`目录为根目录，`/`开头，例如`/assets/icons/cates.png`,而它的`pagePath` 和pages`数组节点一致
* 在使用导航的时候，不管是 声明式导航还是 编程式导航中的`url`都是以`/`开头之后接上`app.wpy`中的`pages`数组下的元素，例如`<navigator url="/pages/tabs/cates">跳转</navigator>`
* 在`.wpy`文件中，也可以使用`@`来定位到`src`目录下所要的文件，例如`@/mixins/tabs/cart.js`

## 五、把页面的业务逻辑抽离到单独的`mixin`文件中

为了精简每个小程序页面的代码，可以将`script`中的业务逻辑，抽离到对应的`mixin`文件中，具体步骤为:

1. 在`src->mixins`文件夹下，新建与页面路径对应的`.js`文件,并初始化基本的代码结构如下

```js
import wepy from 'wepy'

// 注意，必须继承自 wepy.mixin
export default class extends wepy.mixin {}
```

2. 在对应的页面中，可以导入并使用对应的`mixin`，具体代码如下：

```js
<script>
    import wepy from 'wepy'
// 1. 导入外界的 mixin 文件，并接受
// @ 就代表 src 这一层路径
import mix from '@/mixins/tabs/home.js'

export default class extends wepy.page {
    // 2. 把导入的 mix 对象，挂载到 mixins 这个数据中就行
    mixins = [mix]
}
    </script>
```

**注意：必须继承自 wepy.mixin这个类**

## 六、 封装小程序中全局公用的函数

1. 为了提高项目的维护性，可用性、扩展性、可以将常用的`js`逻辑，封装到`src->baseAPI.js`文件中，当然baseAPI.js文件要自己在`src`文件目录下新建，例如：

* 封装全局的`baseToast`函数用来提示错误信息

```js
import wepy from 'wepy'

/**
 * 弹框提示一个无图标的 Toast 消息
 * @str 要提示的消息内容
 */
wepy.baseToast = function(str = '获取数据失败！') {
    wepy.showToast({
        title: str,
        // 弹框期间不会携带任何图标
        icon: 'none',
        duration: 1500
    })
}
```

2. 在`app.wpy`中导入执行`baseAPI.js`文件中的代码

```html
<script>
    import wepy from 'wepy'
    import 'wepy-async-function'
    // 导入并执行 baseAPI.js 中的所有代码
    import '@/baseAPI.js'
</script>
```

* 封装 GET 和 POST 请求

在小程序项目中，需要经常发起数据请求，因此，可以将 `wepy.request()` 函数封装，在全局下挂载`wepy.get()` 和 `wepy.post()`函数，从而发送网络请求，代码如下

```js
// src/baseAPI.js

import wepy from 'wepy'

// 请求根路径
const baseURL = 'https://www.zhengzhicheng.cn/api/public/v1'

/**
 * 发起 get 请求的 API
 * @url 请求的地址，为相对路径，必须以 / 开头
 * @data 请求的参数对象
 */
wepy.get = function(url, data = {}) {
    return wepy.request({
        url: baseURL + url,
        method: 'GET',
        data
    })
}


/**
 * 发起 post 请求的 API
 * @url 请求的地址，为相对路径，必须以 / 开头
 * @data 请求的参数对象
 */
wepy.post = function (url, data = {}) {  
    return wepy.request({
        url: baseURL + url,
        method: 'POST',
        data
    })
}
```

## 七、 `vant`小程序UI组件库

### 7.1 下载 vant 组件库

* 访问`vant-weapp`的 Github 主页 https://github.com/youzan/vant-weapp
* 点击 `Clone or Download`按钮
* 选择`Download ZIP`
* 解压下载的`vant-weapp-dev.zip`
* 进入解压后的目录，将`lib`目录更改为`vant`
* 把重命名的`vant`的目录，复制到`src->assets`目录中

### 7.2 将 vant 中的组件注册为全局组件

例如：徽章组件注册为全局组件

1. 打开`app.wpy`文件
2. 在`config`节点中，新增`usingComponents`节点，具体代码如下:

```js
config = {
    // 引用并注册全局组件
    usingComponents: {
        // 徽章组件
        'van-badge': './assets/vant/badge/index',
        'van-badge-group': './assets/vant/badge-group/index'
    }
}	
```

## 八、interceptor  拦截器

可以使用 Wepy 提供的全局拦截器对原生API 的请求进行拦截，具体方法是配置API的`config`、`fail`、`success`、`complete`回调函数，例如官方例子:

```js
import wepy from 'wepy';

export default class extends wepy.app {
    constructor () {
        // this is not allowed before super()
        super();
        // 拦截request请求
        this.intercept('request', {
            // 发出请求时的回调函数
            config (p) {
                // 对所有request请求中的OBJECT参数对象统一附加时间戳属性
                p.timestamp = +new Date();
                console.log('config request: ', p);
                // 必须返回OBJECT参数对象，否则无法发送请求到服务端
                return p;
            },

            // 请求成功后的回调函数
            success (p) {
                // 可以在这里对收到的响应数据对象进行加工处理
                console.log('request success: ', p);
                // 必须返回响应数据对象，否则后续无法对响应数据进行处理
                return p;
            },

            //请求失败后的回调函数
            fail (p) {
                console.log('request fail: ', p);
                // 必须返回响应数据对象，否则后续无法对响应数据进行处理
                return p;
            },

            // 请求完成时的回调函数(请求成功或失败都会被执行)
            complete (p) {
                console.log('request complete: ', p);
            }
        });
    }
}
```

* 实现数据加载期间的 loading 效果

打开`app.wpy`，在`constructor()`构造函数中，通过拦截器实现`loading`效果，注意， `wepy.showLoading()` 开启之后   `wepy.hideLoading()` 要手动关闭 具体代码如下：

```js
constructor() {
    super()
    this.use('requestfix')
    // 通过这一行代码，可以为异步的API，开启Promise功能，这样，异步API调用的结果，返回值是Promise对象
    this.use('promisify')

    // 拦截器
    this.intercept('request', {
        // 发出请求时的回调函数
        config(p) {
            // 显示loading效果
            wepy.showLoading({
                title: '数据加载中...'
            })
            // 必须返回OBJECT参数对象，否则无法发送请求到服务端
            return p
        },

        // 请求成功后的回调函数
        success(p) {
            // 必须返回响应数据对象，否则后续无法对响应数据进行处理
            return p
        },

        // 请求失败后的回调函数
        fail(p) {
            // 必须返回响应数据对象，否则后续无法对响应数据进行处理
            return p
        },

        // 请求完成时的回调函数(请求成功或失败都会被执行)
        complete(p) {
            // 隐藏loading效果
            wepy.hideLoading()
        }
    })
}
```

## 九、定义全局共享数据和方法

使用`app.wpy`中的`globalData`节点来实现全局共享数据，我们以`购物车`为例子

1. 在`globalData`中，定义全局的购物车李彪

```js
// 专门存储全局共享的数据
// 只需要通过 this.$parent.globalData 就可以拿到这个全局共享的数据对象
globalData = {
    // 全局的购物车列表
    cart: []
}
```

2. 和`globalData`平级，定义全局可调用的函数

```js
test() {
    console.log('ok')
}
```

3. 在每个小程序页面中，可通过`this.$parent`来访问全局的数据或函数：

```js
// 点击按钮，把商品添加到购物车列表中
addToCart() {
    // 获取到当前商品的所有信息
    // console.log(this.goodsInfo)
    console.log(this.$parent.globalData)
    console.log(this.$parent)
    // 提示用户加入购物车成功
    wepy.showToast({
        title: '已加入购物车',
        icon: 'success'
    })
}
```

