---
title: webpack常规配置篇
date: 2020-04-10 08:37:20
categories: tools
img: /medias/imges/tools/webpack/webpack.png
top: true
summary: webpack常规配置 
tags: 
 - tools
---

## webpack常规配置篇

> 本章记录的是作者在学习webpack打包的时候使用过的一些常规配置过程。😄，这里只讲`SPA`应用配置【单页面应用webpack配置】

### 1.webpack的安装

注意：请先自行安装[nodejs](https://nodejs.org)最新版的环境

- 全局安装webpack

  `npm i webpack webpack-cli -g`

- 项目中安装webpack (推荐)

  `npm i webpack webpack-cli -D`

### 2.webpack使用

> 注意：webpack4.0支持`0配置`使用,你可以直接在你的项目中运行`npx webpack`[使用npx必须是npm 5.2 以上的版本]，当这0配置的缺陷很明显，例如你不能直接规定打包文件的名字，指定入口、出口，一定要和webpack规定的名字一模一样，不然打包失败，故小编推荐`自定义配置`，那就得在你得根目录下创建`webpack.config.js`,之后webpack打包会根据这个配置来生成文件

webpack有四大核心概念:

- 入口(entry): 程序的入口js
- 输出(output): 打包后存放的位置
- loader: 用于对模块的源代码进行转换
- 插件(plugins): 插件目的在于解决 loader无法实现的**其他事**

1. 配置webpack.config.js（网站根目录下创建，在这个文件里可以写任意的nodeJS代码）
2. 运行`npx webpack`

```js
const path = require('path')

module.exports = {
  // 入口文件配置
  entry: './src/index.js',
  // 出口文件配置项
  output: {
    // 输出的路径，webpack2起就规定必须是绝对路径
    path: path.join(__dirname, 'dist'),
    // 输出文件名字
    filename: 'bundle.js'
  },
    // 设置的打包成生产环境还是开发环境
  mode: 'development' // 默认为production, 可以手动设置为development, 区别就是是否进行压缩混淆
}
```

将`npx webpack`命令配置到`package.json`的脚本中

1. 配置`package.json`
2. 运行`npm run build`

```json
{
  "name": "webpack-basic",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "build": "webpack"
  },
  "devDependencies": {
    "webpack": "^4.30.0",
    "webpack-cli": "^3.3.1"
  }
}
```
### 3.开发时自动编译工具

每次要编译代码时，手动运行 `npm run build` 就会变得很麻烦。

webpack 中有几个不同的选项，可以帮助你在代码发生变化后自动编译代码：

目前最流行得这几种：

1. webpack's Watch Mode
2. webpack-dev-server
3. webpack-dev-middleware

> 在这小编只介绍`webpack-dev-server`这款😁

* `webpack-dev-server`安装

  ```bash
  npm i webpack-dev-server webpack -D
  ```

* 配置`package.json`的scripts：`"dev": "webpack-dev-server --hot --open --port 8090"`

  webpak-dev-server后面跟着得参数

  | 参数名 |      作用      |
  | :----: | :------------: |
  |  open  | 自动打开浏览器 |
  |  port  |   指定端口号   |
  |  hot   |     热更新     |

* 运行`npm run dev`

> webpack-dev-server打包后会在内存中生成一个打包好的`bundle.js`，专供开发时使用，打包效率高，修改代码后会自动重新打包以及刷新浏览器，用户体验非常好

### 4.html-webpack-plugin【html插件】

> 说完`webpack-dev-server`开发自动编译插件，那么我们一定要来学习`html-webpack-plugin`，这个插件时和webpack-dev-server配合最好的，这是因为webpack-dev-server打包后文件会在内存中生成，而html-webpack-plugin也会在内存中生成一个打包的文件，神奇的是在这个生成好的文件中，它会把webpack-dev-server生成那个`js`文件自动引入，这就不用我们手动去引入，很神奇吧😍

* 安装

  ```bash
  npm i html-webpack-plugin -D
  ```

* 配置【这里的配置和下面的插件配置的地方都是在webpack.config.js文件下】

  ```js
  // 引入
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  // 节点下配置
  plugins: [
      new HtmlWebpackPlugin({
          filename: 'index.html', // 打包后的文件名
          template: 'template.html' // 模板文件
      })
  ]
  ```

### 5.处理css

* 安装

  ```bash
  npm i css-loader style-loader -D
  ```

* 配置`webpack.config.js`

  ```js
  // module节点是于`plugins`同级的，它是来配置loader 
  module: {
      rules: [
        // 配置的是用来解析.css文件的loader(style-loader和css-loader)
        {
          // 用正则匹配当前访问的文件的后缀名是  .css
          test: /\.css$/,
          use: ['style-loader', 'css-loader'] // webpack底层调用这些包的顺序是从右到左，链式调用
        }
      ]
    }
  ```

* loader的作用

|  loader名字  |                 作用                  |
| :----------: | :-----------------------------------: |
|  css-loader  |              解析css文件              |
| style-loader | 将解析出来的结果 放到html中, 使其生效 |

> `style-loader`为什么说是`放到html中, 使其生效`，那是因为这个loader解析后是把界面中应用的css文件都加载到头部的`style`标签里，下面会了解决这个问题，会有一个专门来抽离样式的插件

当然你可能使用的`less`或`scss`，那就要多安装一下几个loader

* less

  ```bash
  npm i less less-loader -D
  ```

  ```js
  { test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] },
  ```

* scss

  ```bash
  npm i sass-loader node-sass -D
  ```

  ```js
  { test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader'] }
  ```

### 6.处理图片和字体

> 当你的项目中引入了图片字体，那么就一定要配置这个，webpack默认是不对图片进行处理的，没配置的话打包是会错误、失败，这里使用`url-loader`来配置

* 安装

  ```bash
  npm i file-loader url-loader -D
  ```

  > 值得一说🤭：url-loader封装了file-loader, 所以使用url-loader时需要安装file-loader

* 配置

  ```js
  // url-loader
  {
      test: /\.(png|jpg|gif)/,
          use: [{
              loader: 'url-loader',
              options: {
                  // limit表示如果图片大于5KB，就以路径形式展示，小于的话就用base64格式展示
                  limit: 5 * 1024,
                  // 打包输出目录，它会在打包目录下生成
                  outputPath: 'images',
                  // 打包输出图片名称
                  name: '[name]-[hash:4].[ext]'
              }
          }]
  }
  ```

### 7.babel【js处理插件】

* 安装

  ```bash
  npm i babel-loader @babel/core @babel/preset-env webpack -D
  ```

* 配置

  ```js
  {
    test: /\.js$/,
    use: {
      loader: 'babel-loader'
    }, 
    exclude: /node_modules/, // 排除的文件
     include:path.join(__dirname,"../src") // 包括的文件
  }
  ```

* 在项目根目录下新建一个`.babelrc`的babel配置文件，将babel的预设和插件配置上 [大家都懂吧🤭]

  ```json
  {
    "presets": ["@babel/env"],
    "plugins": ["@babel/plugin-proposal-class-properties"]
  }
  ```


### 8.source map的使用[推荐安装]

> 小编强烈建议安装这个插件,它的作用是在我们`开发阶段`中可以说是巨大的，它是来解决这样的问题：当我们打包后的文件出现报错时，它会帮我们映射到我们真实代码中的那行。webpack默认情况下映射的关系是我们打包后的文件报错行数，大家都知道这是不对应我们代码真实的行数的，因为webpack会往外面的代码加很多它自己需要的代码，也有许多兼容代码，这解释起来太多了，就不说了，大家自行学习。😂

* 使用【在plugins节点下直接加上devtool节点，不需要自己安装,webpack自己提供】

  ```js
   devtool: 'cheap-module-eval-source-map'
  ```

* 它有很的模式，请去[官网](https://www.webpackjs.com/configuration/devtool/)查看

* 这里一般使用这一种就行了

  * 开发环境推荐：

  		**cheap-module-eval-source-map**

  * 生产环境推荐：

  		**none(不使用source map,上线打包时去掉devtool节点或值为none)**

### 9.clean-webpack-plugin

> 该插件在`npm run build`[生产环境打包时]自动清除`dist`目录后重新生成，非常方便

* 安装

  ```bash
  npm i clean-webpack-plugin -D
  ```

* 引入插件

  ```js
  const { CleanWebpackPlugin } = require('clean-webpack-plugin');
  ```

* 配置(使用插件, 在plugins中直接创建对象即可)

  ```js
  plugins: [
  	...,
      new CleanWebpackPlugin()
    ],
  ```

### 10.copy-webpack-plugin

>该插件时将外面一些媒体文件拷贝到我们的项目中去，并不会对媒体文件做任何处理，纯拷贝

* 安装

  ```bash
  npm i copy-webpack-plugin -D
  ```

* 引入插件

  ```js
  const CopyWebpackPlugin = require('copy-webpack-plugin')
  ```

* 使用插件, 在plugins中插件对象并配置源和目标

  from: 源, 从哪里拷贝, 可以是相对路径或绝对路径, 推荐绝对路径

  to: 目标, 拷贝到哪里去, 相对于`output`的路径, 同样可以相对路径或绝对路径, 但更推荐相对路径(直接在打包目录下生成)

  ```js
  plugins: [
      new CopyWebpackPlugin([
        {
          from: path.join(__dirname, 'assets'),
          to: 'assets'
        }
      ])
    ],
  ```

### 11.BannerPlugin

> 这是一个webpack的内置插件，用于给打包的JS文件加上版权注释信息，注释信息会出现在你每个打包文件的最上方

* 引入webpack

```js
const webpack = require('webpack')
```
* 创建插件对象

```js
plugins: [
   ......
    new webpack.BannerPlugin('你要给你代码的注释')
  ],
```
### 寄语：

> 这是webpack最常规配置篇，并未涉及到高级配置和优化配置，别担心，在未来小编会更新文章。😄