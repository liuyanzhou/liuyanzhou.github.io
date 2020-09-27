---
title: webpack
date: 2020-08-27 16:53:25
categories: tools
top: false
summary: webpack常规配置 
tags: 
 - tools
---

### webpack与模块化

### 一、模块化

模块化已经是现代前端开发中不可或缺的一部分了，它把复杂的问题分解程相对独立的模块，这样的设计可以降低程序复杂性，提高代码的重用，也有利于团队协作开发与后期维护和扩展，从`ESMAScript2015`开始引入了模块的概念，我们称为：`ECMAScript Module` ,简称`ESM`

模块化的核心

* 模块拥有独立的作用域
* 如何导出模块内部数据
* 如何导入外部模块数据

#### 1.1 ESM

从`ESMAScript2015/ECMAScript6`开始，`JavaScript`原生引入了模块的概念，而且现在主流浏览器也都有了很好的支持

* 独立模块作用域

一个文件就是模块，拥有独立的作用域，且导出的模块都自动处于`严格模式`，即`'use strict'`

* 导出模块内部数据

使用`export`语句导出模块内部数据

```js
// 导出单个特性
export let name1,name2,....,nameN;
export let name1=...,name2=...,...,namN;
export function FunctionName() {...};
export class ClassName {...};

// 导出列表
export {name1,name2,...,name3};

// 重命名导出
export {variable as name1,variable2 as name2,....,nameN};

// 默认导出
export default expression;
export default function(...){...};
export default function name1(...){...};
export {name1 as default,....};

// 模块重定向导出
export * from ...;
export {name1,name2,...,nameN} from ...;
export {import as name1,import2 as name2,...,nameN} from ...;
export {default} from ....;                       
```

* 导入外部模块数据：导入分为两种模式

  * 静态导入：在浏览器中，`import`语句只能在声明了`type="module"`的script 标签中使用

  ```js
  import defaultExport from 'module-name';
  import * as name from 'module-name';
  import {export} from 'module-name';
  import {export as alias} from 'module-name';
  import {export1,export2} from 'module-name';
  import { foo , bar } from "module-name/path/to/specific/un-exported/file";
  import { export1 , export2 as alias2 , [...] } from "module-name";
  import defaultExport, { export [ , [...] ] } from "module-name";
  import defaultExport, * as name from "module-name";
  import "module-name";
  ```

  > 静态导入方式不支持延迟加载，`import`必须在模块的最顶层书写，下路这种代码格式是不被允许的

  ```js
  document.onclick = function () {
  
      // import 必须放置在当前模块最开始加载
      // import m1 from './m1.js'
  
      // console.log(m1);
  
  }
  ```

  * 动态导入：通过`import()`函数进行动态导入，它不需要依赖`type="module"`的script标签，关键字`import`可以像调用函数一样来动态的导入模块，以这种方式调用，将返回一个`promise`对象

  ```js
  import('./m.js')
      .then(m => {
          //...
      });
  // 也支持 await
  let m = await import('./m.js');
  ```

  > 通过`import()`方法导入返回的数据会被包装在一个promise对象中，即使是`default`导出也是如此

#### 1.2 commonJS

在早些时候前端对于模块化并没有什么需求，反而是偏向服务端的应用有更强烈的需求，commonJS规范就是一套偏向服务端的模块化规范，NodeJS就采用了这个规范

* 独立模块作用域

一个文件就是一个模块，拥有独立的作用域

* 导出模块作用域

通过`module.exports`或`exports`对象导出模块内部的数据

```js
// a.js
let a = 1;
let b = 2;

module.exports = {
    x:a,
    y:b
}
// or
exports.x =x ;
exports.y = y
```

* 导入外部模块数据

通过`require`函数导入外部模块数据

```js
// b.js
let a = require('./a')
a.x;
a.y;
```

#### 1.3 AMD

以为 CommonJS 规范一些特性（基于文件系统，同步加载），它并不适用与浏览器端，所以另外定义了适用于浏览器端的规范

 `AMD(Asynchronous Module Definition)`

https://github.com/amdjs/amdjs-api/wiki/AMD

浏览器并没有具体实现该规范的代码，我们可以通过一些第三方库来解决

**requireJS**:https://requirejs.org/

注意：script 标签上的 data-main 属性 是要进行模块化的文件地址，src是 require.js

```html
// 1.html
<script data-main="scripts/main" src="https://cdn.bootcss.com/require.js/2.3.6/require.min.js"></script>
```

* 独立模块作用域：通过一个 `define`方法来定义一个模块，并通过该方法的回调函数来产生独立作用域（其实就是利用闭包）

```js
// script/Cart.js
define(function(){
    // 模块内部代码
})
```

* 导出模块内部数据：通过`return`导出模块内部数据

```js
// script//Cart.js
define(function(){
    return class Cart{
        add(item) {
            console.log(`添加商品:${item}`)
        }
    }
})
```

* 导入外部模块数据：通过前置依赖列表导入外部模块数据，第二个参数（回调函数）中的参数对应着前置导入模块导出的数据

```js
// script/main.js
// 定义一个模块，并导入 ./Cart 模块
define(['./Cart'],function(Cart){
    let cart = new Cart()
    Cart.add({name:"zs"})
})
```

* requireJS 的 CommonJS 风格：require.js 也支持 CommonJS风格

  * 导出模块内部数据

  ```js
  // scripts/Cart.js
  define(['require', 'exports', 'module'], function(require, exports, module) {
      class Cart {
          add(item) {
              console.log(`添加商品：${item}`)
          }
      }
      exports.Cart = Cart;
  })
  // 忽略不需要的依赖导入
  define(['exports'], function(exports) {
      class Cart {
          add(item) {
              console.log(`添加商品：${item}`)
          }
      }
      exports.Cart = Cart;
  })
  // 如果是依赖的导入为：require, exports, module，也可以省略依赖导入声明
  define(function(require, exports, module) {
      class Cart {
          add(item) {
              console.log(`添加商品：${item}`)
          }
      }
      exports.Cart = Cart;
  })
  ```

  * 导入外部模块数据

  ```js
  // scripts/main.js
  define(['./Cart'], function(Cart) {
      let cart = new Cart()
      cart.add({name: 'iphoneXX', price: 1000000})
  })
  ```

#### 1.4 UMD

严格来说,`UMD`并不属于一套模块规范，它主要用来处理`CommonJS`、`AMD`、`CMD`的差异兼容，让模块代码能在前面不同的模块环境下都能正常运行

```js
(function(factory) {
    // Node环境下兼容
    if (typeof module === 'object' && typeof module.exports === 'object') {
        module.exports = factory
    } else if (typeof define === 'function' && define.amd) {
        // AMD 环境下兼容
        console.log('1111')
        define(function() {
            return factory
        })
    } else {
         //  普通JS环境下兼容
        window.rmb = factory
    }
})(function(val) {
    return '$ ' + (val / 100).toFixed(2)
})
```

### 二、webpack

![webpack](/medias/imges/tools/webpack/webpack_description_2.jpg)

本质上，`webpack`是一个现代`JavaScript`应用程序的静态模块打包器(module bundler)。当webpack 处理应用程序时，它会递归地构建一个用来关系图（dependency graph）,其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。前面我们已经说了模块化开发，我们在开发时可以使用模块化来进行应用开发，当我们开发完，这些模块化会出现相互依赖，互相调用的情况，这时我们的webpack 能够分析它们之间的依赖关系并打包成一个文件，减少网络请求。而且webpack也能够做低版本浏览器兼容的问题。例如： ES6 转 ES5 。总之，webpack的作用在于我们项目即将上线的时候对我们的项目代码进行优化，使我们的代码更加高效

- 官⽅方⽹网站:https://webpack.js.org/
- 中⽂文⽹网站:https://www.webpackjs.com/

#### 2.1 webpack使用

* 安装

webpack 是一个使用 Node.js 实现的一个模块化代码打包工具。所以，我们需要先搭建好 Node.js环境，在去安装webpack，**不推荐全局安装**

```bash
npm install webpack webpack-cli --D
```

webpack--cli ： 提供 webpack 命令、工具，类似 `vue-cli,create-react-app`

webpack：实现打包的代码，类似 `vue,react`

* 使用

```bash
./node_modules/.bin/webpack

// 查看版本
./node_modules/.bin/webpack -v
```

也可以编辑`package.json`的 script 来简化输入

```js
// package.json
{
    ...,
        "scripts":{
            "start":"webpack 指定的入口文件" // scripts 中可以定位到 ./node_modules/.bin/ 目录下
        }
}
```

> script 中使用 test 、start、restart、stop命令的时候，可以在调用的时候省略   run ，即直接 `npm xxx` ，例如`npm start`，其他自定义的我们得`npm run 自定义名称`

通过 npx 也可以帮助我们定位到`./node_modules/.bin/`目录下

> 注：npm5.2+ 增加，如果没有，可以使用 `npm i -g npx` 来安装

#### 2.2 开始打包

打包之前，我们需要了解一个概念，入口文件

* 入口文件

入口文件就是我们项目中加载的第一个文件，比如上面的`main.js`文件，其他文件都是通过 import等方式引入的，`webpack`会从我们指定的入口文件开始分析所有需要依赖的文件，然后把它们打包成一个完整的文件

* 打包命令

```bash
webpack ./js/index.js 
```

配置了package.json文件，例如上面，命令改为

```bash
npm start 
```

上面的命令会使用`webpack`默认的一些配置对模块文件进行打包，并把打包后的文件输出到默认创建的`./dist`目录下，打包后的文件名称默认为`main.js`，模块文件打包以后，就可以在不支持 es6 模块语法的浏览器环境下引入使用

* 打包文件分析

1. 把分散的模块文件打包到一个文件中，不需要外部引入
2. 内置一个小型模块加载器（类似requireJS） ，实现了打包后的代码隔离与引用

以上就是 webpack 最基础的使用与基本原理，当然强大的webpack 远远不止这些功能

* 打包配置

虽然，我们可以直接通过命令来打包，但是推荐创建一个`webpack.config.js`的配置文件来实现更方便和强大的功能

webpack命令在运行的时候，默认会读取运行命令所在的目录下的`webpack.config.js`文件，通常我们会在项目的根目录下运行命令和创建配置文件，我们也可以通过 `--config`选项在`package.json文件中`来指定配置文件路径

```bash
webpack --config ./config/my_webpack.config.js
```

通过情况下，我们的项目目录大致如下:

```txt
/
-- /dist - 项目打包后存放目录
-- /node_modules - 第三方模块
-- /src
------ css/
------ images/
------ js/
------ index.js
-- webpack.config.js
-- package.json
```

webpack.config.js

```js
module.exports = {
    ... // 配置项
}
```

### 三、webpack核心配置

#### 3.1 mode 

mode 即 模式，可选`"production" | "development" | "none"`

```js
module.exports = {
    mode:"production"
}
```

#### 3.2 entry

entry 即 指定打包入口文件，有三种不同的形式：`string | object | array `

* 一对一：一个入口，输出一个打包文件

```js
module.exports = {
    entry:'./src/index.js'
}
```

* 多对一：多个入口，输出一个打包文件

```js
module.exports ={
    entry:[
        './src/index1.js',
        './src/index2.js'
    ]
}
```

* 多对多：多个入口，输出多个打包文件

```js
module.exports = {
    entry:{
        'index1':'./src/index1.js',
        'index2':'./src/index2.js'
    }
}
```

#### 3.3 output

output 即 打包后的文件输出位置

```js
module.exports = {
    ....,
    output:{
        path:path.resolve(__dirname,'dist'),
        filename:'bundle.js', // or filename:"[name].js"
        
    }
}
```

* 可以指定一个固定的文件名称，如果是多个入口多出口（entry为对象），则不能使用单文件出口，需要使用通过 webpack 内置的变量占位符：`[name]`，作为文件输出名

#### 3.4 webpack 引入外包的执行流程

在webpck  中，有一个很重要的特性：模块不仅仅只是`js`文件，webpack 可以把任意文件数据作为模块进行处理，包括：非js文本、css、图片等等

```js
import txt from './a.txt'
console.log(txt)
```

但是webpack 默认情况下只能处理`js`文件模块，如果需要处理其他类型的模块，则需要使用它提供的一些其它功能

执行流程:

![执行流程](/medias/imges/tools/webpack/webpack-zxlc.png)

* loaders : webpack 中非常核心的内容之一，我们说的非js类型的模块处理就靠它了，不同类型的模块的解析就是用来不同的`loader`来实现的
* plugins : webpack 中另外一个核心的内容，它主要是扩展webpack 本身的一些功能，它们会运行在各种模块解析完以后的打包编辑阶段，比如对解析后的模块文件进行压缩等等

### 四、loader

https://webpack.js.org/loaders/

```js
module.exports = {
  ...,
  module: {
  	rules:[
  		{
  			test:/\.xxx$/,
       	use:{
        	loader: 'xxx-load'
      	}
			}
  	]
	}
}
```

当`webpack`碰到不识别的模块的时候，webpakc 会在配置 的 `module`中进行该文件解析规则的查找

* rules ： 就是我们为不同类型的文件定义的解析规则对应的 loader ，它是一个数组
* test：每一种类型规则通过 test 选项来定义，通过正则进行匹配，通常我们会通过正则的方式来匹配文件后缀类型
* use：针对匹配到文件类型，调用对应的 loader 进行处理

从一个简单的案例来了解 loader

<!--src/datas/data.txt-->

```
我是 txt 的内容
```

<!--src/datas/data.md-->

```
# 我是 md 的内容
```

<!--src/raw-loader.js-->

```js
import txtData from './datas/data.txt';
import mdData from './datas/data.md';

console.log('txtData: ', txtData);
console.log('mdData: ', mdData);
```

默认情况下，webpack 会报错，因为 webpack 处理不了 txt 和 md 这样的非 js的模块，当时我们可以同专门来处理纯文本内容（不同的 loader）有不同的作用

#### 4.1 raw-loader

在webpack 中通过 import 方式导入文件内容，loader 并不是 webpack 内置的， 所以首先要安装

```bash
npm i raw-loader -D
```

然后在 webpack.config.js 中进行配置

```js
module.exports = {
  ...,
  module: {
      rules: [
      {
        test: /\.(txt|md)$/,
        use: 'raw-loader'
    	}
    ]
	}
}
```

处理md文件的loader 也可以使用 markdown-loader ,html-loader 是处理 markdown-loader 将md文件转为为 html 的结构

```bash
npm i html-loader markdown-loader  -D
```

```js
module.exports = {
  ...,
  module: {
      rules: [
      {
        test: /\.md$/,
      	use: ['html-loader', 'markdown-loader']
    	}
    ]
	}
}
```



#### 4.2 file-loader

把识别出来的资源模块，移动到指定的输出目录下，并且返回这个资源在输出目录的地址(字符串)

```bash
npm install file-loader -D
```

```js
rules: [
  ...,
	{
		test: /\.(png|jpe?g|gif)$/,
    use: {
      loader: "file-loader",
      options: {
        // placeholder 占位符 [name] 源资源模块的名称
        // [ext] 源资源模块的后缀
        name: "[name]_[hash].[ext]",
        //打包后的存放位置
        outputPath: "./images"
        // 打包后文件的 url
        publicPath: './images',
      }
    }
	}
]
```

> 占位符：https://webpack.js.org/loaders/file-loader#placeholders

#### 4.3 url-loader

可以处理 `file-loader`所有的事情，但是遇到图片格式的模块，可以选择性把图片转成`base64`格式的字符串，并打包到`js`中，对小体积的图片比较适合，大图片不适合，**注意：url-loader 内置依赖了  file-loader ，所以安装的时候也要装上 file-loader**

```bash
npm install --save-dev url-loader file-loader
```

```js
rules: [
  ...,
	{
		test: /\.(png|jpe?g|gif)$/,
    use: {
      loader: "url-loader",
      options: {
        // placeholder 占位符 [name] 源资源模块的名称
        // [ext] 源资源模块的后缀
        name: "[name]_[hash].[ext]",
        //打包后的存放位置
        outputPath: "./images"
        // 打包后文件的 url
        publicPath: './images',
        // 小于 100 字节转成 base64 格式
        limit: 100
      }
    }
	}
]
```

> outputPath 打包路径时相对于 全局输出配置（output）路径来算的。

#### 4.4 css-loader

分享`css`模块之间的关系，并合成一个`css`

```bash
npm install --save-dev css-loader
```

```js
rules: [
  ...,
	{
		test: /\.css$/,
    use: {
      loader: "css-loader",
      options: {
  			// 启用/禁用 url() 处理
  			url: true,
  			// 启用/禁用 @import 处理
  			import: true,
        // 启用/禁用 Sourcemap
        sourceMap: false
      }
    }
	}
]
```

#### 4.5style-loader

把`css-loader`生成的内容，用 `style`标签挂载到页面的 `head`中

```js
npm install --save-dev style-loader
```

```js
rules: [
  ...,
	{
		test: /\.css$/,
    use: ["style-loader", "css-loader"]
	}
]
```

同一个任务的 `loader`可以同时挂载多个，处理顺序为：从右到左，也就是先通过`css-loader`处理，然后在把处理后的`css`字符串交给`style-loader`进行处理

```js
rules: [
  ...,
	{
		test: /\.css$/,
    use: [
  		{
  			loader: 'style-loader',
  			options: {}
  		},
      'css-loader'
		]
	}
]
```

#### 4.5 sass-loader

把`sass`语法转换成`css`，依赖`node-sass`模块

```bash
npm install --save-dev sass-loader node-sass
```

### 五、Plugins

扩展 `webpack`本身的一些功能，它们会运行在各种模块解析完成以后的打包编译阶段（也就是在output输出之前对代码进行处理），比如对解析后的模块文件进行压缩等

#### 5.1 HtmlWebpackPlugin

在打包结束后，自动生成一个`html`文件，并把打包生成的 js 与 css 模块引入到改`html`中

```bash
npm install -D html-webpack-plugin
```

```js
// webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    ...,
    plugins:[
    new HtmlWebpackPlugin({
    	title:'My App',
    	filename:'app.html',
    	template:'./src/html/index.html'
	 })
    ]
}
```

```html
<!--./src/html/index.html-->
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title><%=htmlWebpackPlugin.options.title%></title>
            </head>
        <body>
            <h1>html-webpack-plugin</h1>
       </body>
</html>
```

在`html`模板中，可以通过`<%=htmlWebpackPlugin.options.XXX%>`的方式获取到配置的值，xxx代表配置的值，例如`<%=htmlWebpackPlugin.options.title%>`

更多配置：

| 属性名         | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| title          | 用来生成页面的`title`元素                                    |
| filename       | 输出的HTML文件名，默认是`index.html`，也可以直接配置子目录   |
| template       | 模板文件路径，支持加载器`loader`,比如`html!./index.html`     |
| inject         | inject: true \| 'head' \| 'body' \| false , 注入所有的资源到特定的 `template`或者`templateContent`中，如果设置为`true`或者`body`,所有的`javascript`资源将被放置到`body`元素的底部，`'head'`将放置到`head`元素中 |
| favicon        | 添加特定的`favicon`路径到输出的`HTML`文件中                  |
| minify         | {} \| false, 传递`html-minifier`选项给`minify`输出           |
| hash           | hash:true \| false ，如果为`true`,将添加`webpack`编译生成的`hash`到所有包含的脚本和`css`文件，对于解决浏览器`cache`很有用 |
| cache          | cache:true \| false，如果为 true , (默认值)，仅在文件修改之后才会发布文件 |
| showErrors     | showErrors:true \| false，如果为 true (默认值)，错误信息会写入到`HTML`页面中 |
| chunks         | 允许只添加某些块(比如，仅在 unit test 块)                    |
| chunksSortMode | 允许控制块在添加到页面之前的排序方式，支持的值  none'\| 'default' \| {function}-default:'auto' |
| excludeChunks  | 允许跳过某些模块，（比如，跳过单元检测的块）                 |

#### 5.2 clean-webpack-plugin

删除（清理）构建的目录

```bash
npm install --save-dev clean-webpack-plugin
```

```js
const {CleanWebpackPlugin} = require('clean-webpack-plugin');
module.exports = {
    ...,
    plugins:[
    	...,
    	new CleanWebpackPlugin(),
    	...
    ]
}
```

#### 5.3 mini-css-extract-plugin

提取`css`到一个单独的文件中

```bash
npm install --save-dev mini-css-extract-plugin
```

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
module.exports = {
	...,
  module: {
  	rules: [
  		{
  			test: /\.s[ac]ss$/,
  			use: [
  				{
  					loader: MiniCssExtractPlugin.loader
					},
  				/*不能使用style-loader*/
          'css-loader',
          'sass-loader'
        ]
			}
  	]
	},
  plugins: [
    ...,
    new MiniCssExtractPlugin({
    	filename: '[name].css'
    }),
    ...
  ]
}
```

#### 5.4 sourceMap

我们实际运行在浏览器的代码是通过`webpack`打包合并甚至是压缩混淆过的代码，所生产的代码并不利于我们的调试和错误定位，（这是因为打包后的文件会包含一些webpck工具加入的一些注释和执行代码）。我们可以通过`sourceMap`来解决这个问题，`sourceMap`本质是一个记录了编译后代码与源代码的映射关系的文件。我们可以通过`webpack`的`devtool`选项来开启`sourceMap`

```js
module.exports  = {
    mode:'production',
    devtool:'source-map'
}
```

首先，编译后会为每一个编译文件生成一个对月的`.map`文件，同时在编译文件中添加一段对月的`map`文件引入代码

```js
...
//# sourceMappingURL= xx.js.map
```

```js
...
/*# sourceMapingURL = xx.css.map */
```

同时，现代浏览器都能够识别`sourceMap`文件，如`chrome`，会在`sources`面板中显示根据编译文件与对应的`map`文件定位到源文件中，有利于我们的调试和错误定位

![sourceMap](/medias/imges/tools/webpack/source_map.png)

#### 5.5 WebpackDevServer

每次的代码修改都需要重新编译打包，刷新浏览器，特别麻烦，我们可以通过安装`webpackDevServer`来改善这方面的体验

```bash
npm install --save-dev webpack-dev-server
```

启动命令

```js
npx webpack-dev-server
```

或者，`package.json`中添加`script`，当输入`npm run server`后我们的webpack-dev-server就会构建出一个虚拟本地服务器，并且调用webpack进行对项目打包

```js
...,
"scripts": {
    "server":"webpack-dev-server"
}
```

修改`webpack.config.js`

```js
module.exports = {
    ...,
    devServer: {
    // 生成的虚拟目录路径
    contentBase: "./dist",
    // 自动开启浏览器
    open: true,
    // 端口
    port: 8081
}
}
```

启动服务以后，`webpack`不在会把打包后的文件生成到硬盘真实目录中了，而是直存在于内存中（同时引入虚拟的打包文件的路径），由于存在于内存中，后期的更新编译打包和访问的速度会大大提升

#### 5.6 Proxy

当下前端的开发都是前后端分离开发的，前端开发过程中代码会运行在一个服务器环境下，(如当前的`webpackDevServer`)，那么在处理一些后端请求的时候通常会出现跨域的问题。`webpackDevServer`内置了一个代理服务，通过内置代理就可以把我们的跨域请求转发到目标服务器上（webpackDevServer 内置的代理发送的请求属于后端 - `node`，不收同源策略限制）,具体如下

<!--后端代码，以 node 为例-->

```js
const Koa = require('koa');
const KoaRouter = require('koa-router');

const app = new Koa();
const router = new KoaRouter();

router.get('/api/info', async ctx => {
    ctx.body = {
        username: 'zMouse',
        gender: 'male'
    }
})

app.use( router.routes() );
app.listen(8787);
```

<!--前端代码-->

```js
axios({
    url: 'http://localhost:8787/api/info'
}).then(res => {
    console.log('res',res.data);
})
```

默认情况下，该代码运行以后会出现跨域请求错误，修改 `webpack` 配置

```js
module.exports = {
  ...,
  devServer: {
  	// 生成的虚拟目录路径
  	contentBase: "./dist",
  	// 自动开启浏览器
  	open: true,
  	// 端口
  	port: 8081,
  	proxy: {
      '/api': {
      	target: 'http://localhost:8787'
    	}
    }
	}
}
```

通过`proxy`设置，当我们在当前`WebpackDevServer`环境下发送以`/api`开头的请求都会被转发到`http://localhost:8787`目标服务器下

<!--修改前端代码-->

```js
axios({
    //url: 'http://locahost:8081/api/info',
    url: '/api/info'
}).then(res => {
    console.log('res',res.data);
})
```

注意`url`地址要填写`webpackDevServer`域，比如当前`webpackDevServer`开启的是`http://localhost:8081`,也就是我们当前前端代码运行的环境，那么请求的`url`也必须发送到这来，当我们的请求满足了`proxy`中设置的`/api`开头，那么就会把请求转发到`target`,所以最后的实际请求是`http://localhost:8787/api/info`

#### 5.7 Hot Module Replacement

在之前当代码有变化时，我们使用的`live reload`，也就是刷新整个页面(要保证浏览器中的 NetWork- Disable cache选项不被勾选上 )。虽然这样为我们省掉了很多手动刷新页面的麻烦，但是这样即使只是修改了很小的内容，也会刷新整个页面，无法保持页面操作状态，故`HMR`随之就出现了，它的核心是 局部（模块）更新，也就是不刷新整个页面，只更新变化的部分

```js
module.exports = {
  ...,
  devServer: {
  	// 生成的虚拟目录路径
  	contentBase: "./dist",
  	// 自动开启浏览器
  	open: true,
  	// 端口
  	port: 8081,
  	// 开启热更新
  	hot:true,
  	// 即使 HMR 不生效，也不去刷新整个页面(选择开启)
    hotOnly:true,
  	proxy: {
      '/api': {
      	target: 'http://localhost:8787'
    	}
    }
	}
}
```

开启`HMR`之后（也就是 hot ，hotOnly选项配置好了），当代码发生变化，`webpack`即会进行编译，并通过`websocket`通知客户端（浏览器），我们需要监听处理来自`webpack`的通知，然后通过`HMR`提供的`API`来完成我们的局部更新

<!--./fn1.js-->

```js
export default function() {
    console.log('start1!');
}
```

<!--index.js-->

```js
import fn1 from './fn1.js';
box1.onclick = fn1;

if (module.hot) {//如果开启 HMR
    // 监听 fn1.js文件是否变化
    module.hot.accept('./fn1.js', function() {
        // 更新逻辑
        box1.onclick = fn1;
    })
}
```

上面代码就是 当 ./fn.js 模块代码发生变化的时候，把最新的 fn1函数绑定到 box1.onclick 上

从上面就可以看到，`HMR`其实就是以模块为单位，当模块代发生修改的时候，通知客户端进行对应的更新，而客户端则根据具体的模块来更新我们的页面逻辑，（这些逻辑需要自己去实现），好在一些常用的更新逻辑都有了现成的插件

* css热更新

样式热更新比较简单，`style-loader`中就 已经集成实现了，我们只需要在`use`中使用就可以了

* react 热更新
  - https://github.com/gaearon/react-hot-loader
  - react 脚手架中也有集成
* vue 热更新
  - https://github.com/vuejs/vue-loader
  - vue 脚手架中也有集成