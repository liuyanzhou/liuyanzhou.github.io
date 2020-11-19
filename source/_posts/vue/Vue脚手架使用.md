---
title: Vue脚手架使用与单文件
date: 2020-04-10 22:22:04
img: /medias/imges/vue/vue.jpg
categories: Vue
top: true
summary:  Vue脚手架使用与单文件
tags: 
 - Vue
---

## 一、Vue脚手架

> 还在为构造`vue`项目时被命令行那丑陋的面孔所支配吗？那么你有必要阅读这篇文章，它将打开你`vue`项目构造大门😀
>
> Vue 脚手架用于快速生成 Vue 项目基础架构，其官网地址为：https://cli.vuejs.org/zh/ 

安装 3.x 版本的 Vue 脚手架： 

```ba
npm install -g @vue/cli
```

### 1.基于命令行交互式的方式，创建新版 vue 项目

* 运行

  ```bash
  vue create my-project[项目存放文件夹名]
  ```

* 配置

  ![配置解析](/medias/imges/vue/vue-cli/vue-cli-07.png )

* 运行项目

  ```bash
  cd '到你的项目创建的目录'
  # 执行
  npm run serve
  ```

### 2. 基于 图形化界面 的方式，创建 新版 vue 项目 

* 运行

  ```bash
  vue ui
  ```


![第一步](/medias/imges/vue/vue-cli/vue-cli-2-1.png )

![第二步](/medias/imges/vue/vue-cli/vue-cli-2-2.png )

![第三步](/medias/imges/vue/vue-cli/vue-cli-2-3.png )

![第四步](/medias/imges/vue/vue-cli/vue-cli-2-4.png )

### 3.Vue 脚手架生成的项目结构分析 

![项目结构](/medias/imges/vue/vue-cli/vue-cli-2-6.png )

* src ：这个是存放的我们项目源码，我们开发过程中大部分的时间就在这个目录
* mian.js : 项目入口文件
* App.vue: 首先，这是 vue 提供的一种单文件组件的文件模式，一个`.vue`文件就是一个独立的组件，这里的 App.vue 是用于的根组件
* components穆克拉：存放组件的目录
* assets目录： 存放静态资源的目录，比如：图片、css等。这里的文件与外层的`public`目录存放的静态资源的最大区别是: assets 存放的资源是通过`import`等方式作为模块导入，最后打包处理的，而 public 中的资源并不通过模块方式导入，一般都是通过`script`、`link`、`img`等方式从浏览器引入资源，比如无法通过模块处理的js文件(这样的情况并不多)
* public: 一些并非通过模块方式引入的资源文件存放的位置，一般都是通过`script`、`link`、`img`等方式从浏览器引入的资源，比如无法通过模块处理的js文件(这样的情况并不多)

### 4.Vue 脚手架的自定义配置 

#### 4.1通过 package.json 配置项目 

```json
 // 必须是符合规范的json语法
 "vue": {
 "devServer": {
 "port": "8888",
 "open" : true
	 }
 },
```

> 注意：不推荐使用这种配置方式。因为 package.json 主要用来管理包的配置信息；为了方便维护，推荐将 vue 脚 手架相关的配置，单独定义到 vue.config.js 配置文件中。

#### 4.2通过单独的配置文件配置项目 

* 在项目的跟目录创建文件 vue.config.js 

* 在该文件中进行相关配置，从而覆盖默认配置 

  ```js
   // vue.config.js
   module.exports = {
       devServer: {
           // 指定端口
           port: 8888
       }
   }
  ```

### 5.Element-UI 基于图形化界面自动安装 

* 运行 vue ui 命令，打开图形化界面 
* 通过 Vue 项目管理器，进入具体的项目配置面板 
* 点击 插件 -> 添加插件，进入插件查询面板 
* 搜索 vue-cli-plugin-element 并安装 
* 配置插件，实现按需导入，从而减少打包后项目的体积 } }

##二、单文件使用

vue的文件组是官方提供的一种用来组件代码的形式，该文件以`.vue`为后缀，该文件会被`vue-cli`内置的`webpack`解析生成对应的`javascript`、`html`、`css`

官网：https://vue-loader-v14.vuejs.org/zh-cn/start/spec.html

- 组成

```vue
<template>
<div class="example">{{ msg }}</div>
</template>

<script>
    export default {
        data () {
            return {
                msg: 'Hello world!'
            }
        }
    }
</script>

<style>
    .example {
        color: red;
    }
</style>
```

单文件组件把一个组件包含的 结构、样式、行为分别通过 `template`、`style`、`script`进行分离包含，然后统一组织在一个文件中，而且一个单文件组件最少必须包含`template`，可以不需要`script、style`

- lang属性

无聊是 template 、script 、还是 style ，都可以通过 lang 属性来指定它们所使用的语言

```vue
<template lang="jade">
div.example
p {{ msg }}
</template>

<script lang="ts">
    import Vue from 'vue'
    export default Vue.extend({
        data () {
            return {
                msg: 'Hello world'
            }
        }
    })
</script>

<style lang="stylus">
.example
  color red
</style>
```

- src 属性

我们还可以通过 src 属性把文件分离到单独的文件中

```vue
<template src="./template.html"></template>
<style src="./style.css"></style>
<script src="./script.js"></script>
```

> 这里的 src 通过遵循模块化的导入规则，`./`开头相对路径，`/`开头表示 NPM 包中的资源

- 有作用域的 css

当 `style`标签有`scoped`属性时，它的css值作用域当前组件组件中的元素，这类似于 shadow DOM 中的样式封装

```vue
<style scoped>
    .example {
        color: red;
    }
</style>

<template>
<div class="example">hi</div>
</template>
```

解析后

```vue
<style>
    .example[data-v-f3f3eg9] {
        color: red;
    }
</style>

<template>
<div class="example" data-v-f3f3eg9>hi</div>
</template>
```

-  资源路径处理

在项目开发中，我们经常会碰到要引入外部资源的需求，vue 单文件系统中，对资源引入路径有一定特殊出来，分为：绝对路径前缀、相对路径前缀、特殊前缀

1. 绝对路径前缀：就是以`/`、`http://baidu.com/`等这样的绝对路径开头的，vue-cli不做任何处理，直接被浏览器解析，下面的`/assets`必须出现在`public`目录下与之对应

```html
<img alt="Vue logo" src="/assets/logo.png">
<img alt="Vue logo" src="http://image.xx.com/logo.png">
```

2. 相对路径前缀：就是以`./`、`../`这样的相对路径开头，将会被看做相对的模块用来，并按照本地文件系统上的目录结构进行解析，如`<img src="../a.png >"`解析后`<img src="require('../a.png')">`,类似的还包括`background:url(...)`和`@import`

```html
<img alt="Vue logo" src="./assets/logo.png">
```

3. 如果URL是以`~`开头会作为一个模块请求解析，这意味着你甚至可以引用 Node 模块中的资源：

```html
<img src="~some-npm-package/foo.png">
```

4. 特殊前缀：如果路径以`@`开头，也会被看做模块依赖，如果你的 webpack配置中给 `@`配置了`alias`，则以配置的位置为主，否则vue-cli创建的项目都默认配置了将`@`指向`/src`,也就是指向src目录

```js
import Hello from '@/components/Hello.vue'
```

何时使用`public`文件夹

* 通过 `webpack`的处理并获得如下好处：
  * 脚本和样式表会被压缩且打包在一起，从而避免额外的网络请求
  * 文件丢失会直接在编译时报错，而不是到了用户端才产生 `404错误`
  * 最终生成的文件名包含了内容哈希，因此你不必担心浏览器会缓存它们的老版本
* 如下情况考虑使用`public`文件夹
  * 你需要在构建输出中指定一个固定的文件夹
  * 你有上千个图片，需要动态引用它们的路径
  * 有些库可能和`webpack`不兼容，除了将其用一个独立的`<script>`标签引入没有别的选择

使用`public`文件夹的注意事项

* 如果你的应用没有部署在域名的根部，那么你需要为你的URL配置 [`publicPath`](https://cli.vuejs.org/zh/config/#publicpath)前缀

```js
// vue.config.js
module.exports = {
    publicPath:process.env.NODE_ENV==='production' ? '/cart/' : '/'
}
```

* 在`public/index.html`等通过`html-webpack-plugin`用作模板的 HTML 文件中，你需要通过`<%= BASE_URL %>`设置链接前缀

```html
<link rel="icon" href="<%= BASE_URL %>favicon.ico">
```

* 在模板中，先向组件传入 BASE_URL：

```js
data() {
    return {
        publicPath:process.env.BASE_URL	
    }
}
```

然后：

```html
<img :src="`${publicPath}my-image.png`" >
```

### 三、css相关

使用预处理器：如果创建项目时没有选择需要的预处理器，(sass/less/stylus)，则需要手动安装相应的`loader`

```bash
# sass
npm install -D sass-loader node-sass

# less
npm install -D less-loader less

# stylus
npm install -D stylus-loader stylus
```

范例：App.vue 修改 sass

```html
<style scoped lang="scss">
	$color:#42b983;
    a {
        color:$color;
    }
</style>
```

自动化导入样式

自动化导入样式文件（用于颜色、变量、mixin等），可以使用`style-resources-loader`

```bash
npm i -D style-resources-loader
```

配置

```js
const path = require('path')

function addStyleResource(rule) {
    rule.use('style-resource')
    .loader('style-resources-loader')
    .options({
        patterns:[
            path.resolve(__dirname,'./src/styles/imports.scss')
        ]
    })
}

module.exports = {
    chainWebpack:config => {
        const types = ['vue-modules','vue','normal-modules','normal']
        types.forEach(type=>addStyleResource(config.module.rule('scss').oneOf(type)))
    }
}
```

#### scoped css

当`<style>`标签有`scoped`属性时，它的 css 值作用于当前组件的元素

```html
<style scoped>
    .red {
        color:red;
    }
</style>
```

其原理是通过使用 PostCSS 来实现以下转换：

```html
<template>
	<div class="red" data-v-f3f3eg9></div>
</template>

<style>
    .red[data-v-f3f3eg9] {
        color:red;
    }
</style>
```

混用本地和全局

```html
<style>
 /* 全局样式 */
</style>

<style scoped>
/* 本地样式 */
</style>
```

深度作用选择器，使用`>>>`操作符可以使`scoped`样式中的一个选择器能够作用得`更深`，例如影响子组件

```html
<style scoped>
    #app >>> a {
        color:red;
    }
</style>
```

注意：scss 之类的预处理器无法正确解析 `>>>`，这种情况下你可以使用`/deep/`或`::v-deep`操作符取而代之

```html
<style scoped lang="scss">
    #app {
        /deep/ a{
            color:rgb(196,50,140);
        }
        ::v-deep a {
            color:rgb(196,50,140);
        }
    }
</style>
```

#### CSS Module

css module 是一个流行的，用于模块化和组合css的系统，`vue-loader`提供了与css modules 的一流集成，可以作为模拟 scoped css 的代替方案

添加 module

```html
<style module lang="scss">
    .red {
        color:#foo;
    }
    .bold {
        font-weight:bold;
    }
</style>
```

模板中通过`$style.xxx`访问

```html
<a :class="$style.red" > awesome-vue</a>
<a :class="{[$style.red]:isRed}"></a>
<a :class="[$style.red,$style.bold]">awesome-vue</a>
```

JS中访问

```html
<script>
	export default {
        created() {
            // -> 'red_1vyoj-u2'
            // 一个机遇文件名和类目生成的标识符
            console.log(this.$style.red)
        }
    }
</script>
```

#### 四、数据访问相关

数据模拟：使用开发服务器配置`before`选项，可以编写接口，提供模拟数据

```js
devServer:{
    before(app) {
        app.get('/api/courses',(req,res)=>{
            res.json([{name:'web全栈',price:8999},{name:'web高级',price:8999}])
        })
    }
}
```

调用

```js
import axios from 'axios'
export function getCourses() {
    return axios.get('/api/courses').then(res=>res.data)
}
```

### 寄语

> 撸起袖子，加油干！！！😎