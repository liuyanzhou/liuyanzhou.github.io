---
title: wepack-基础篇
date: 2021-09-27 15:05:57
categories: 原理分析
top: false
summary:  wepack-基础篇
tags: 
 - 原理分析
---

### Webpack

### webpack介绍

本质上，webpack是一个用于现代`javaScript`应用程序的静态打包工具。当 webpack 处理应用程序时，它会在内部构建一个依赖图（dependency graph），此依赖图对应映射到项目所需的每个模块，并生成一个或多个`bundle`

#### 安装：

```cmd
npm install webpack webpack-cli --save-dev 
```

#### 入口

* 入口起点（entry point）指示 webpack 应该使用哪个模块，来作为构建其内部依赖图（dependency graph）的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接或间接）依赖的
* 默认值是`./src/index.js`，但你可以通过`webpack.config.js`中的`entry`属性，来指定一个或多个不同的入口起点

`src/index.js`

```js
import './index.css' 
```

`index.css`

```css
body {
    background-color:green;
}
```

webpack.config.js

```js
const path = require('path');
module.exports = {
    entry: './src/index.js'
}
```

#### 输出（output）

* `output`属性告诉webpack 在哪里输出它所创建的`bundle`，以及如何命名这些文件
* 主要输出文件的默认值是`./dist/main.js`，其他生成文件默认放置`./dist`文件夹中。

webpack.config.js

```js
const path = require('path');
module.exports = {
    entry:'./src/index.js',
    output:{
        path:path.resolve(__dirname,'dist'),
        filename:'main.js'
    }
}
```

#### loader 

* webpack 只能理解处理`javascript`和`json`文件
* loader的作用就是让`webpack`能够去处理其他类型的文件，并将它们转换为有效模块，以供应用程序使用，以及被添加到依赖图中

webpack.config.js

```js
const path = require('path');
module.exports = {
  mode: 'development',
  devtool:false,
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'main.js'
  },
+  module: {
+    rules: [
+      { test: /\.css$/, use: ['style-loader','css-loader']}
+    ]
+  }
};
```

#### 插件（plugin）

loader  用于转换某些类型的模块，而插件的执行范围更广，包括：打包优化，资源管理，注入环境变量等。

`src/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>webpack5</title>
</head>
<body>
</body>
</html>
```

webpack.config.js

```js
const path = require('path');
+const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  mode: 'development',
  devtool:false,
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'main.js'
  },
  module: {
    rules: [
      { test: /\.css$/, use: ['style-loader','css-loader']}
    ]
  },
+  plugins: [
+    new HtmlWebpackPlugin({template: './src/index.html'})
+  ]
};
```

#### 模式（mode）

日常的前端开发工作中，一般都会有两套构建环境

* 一套开发时使用，构建结果用于本地开发调试，不进行代码压缩，打印`debug`信息，包含`sourcemap`文件
* 一套构建后的结果是直接应用于线上的，即代码都是压缩后，运行时打印`debug`信息，静态文件不包括`sourcemap`

webpack4.x 版本引入了  **mode**的概念

当你指定使用`mode:production`时，默认会启动各种性能的功能，包括构建结果优化以及 webpack运行性能优化，而如果是`mode:development`的话，则会开启`debug`根据，运行时打印详细的错误信息，以及更加快速的增量编译构建

| 选项        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| development | 会将`process.env.NODE_ENV`的值设为 `development`，启用`NamedChucksPlugin`和`NamedModulesPlugin` |
| production  | 会将`process.env.NODE_ENV`的值设为`production`。启用FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 UglifyJsPlugin |

**环境差异**

开发环境

* 需要生成`sourcemap`文件
* 需要打印`debug`信息
* 需要`live reload`或者`hot reload`的功能

生成环境

* 可能需要分离`css`成单独文件，以便多个页面共享同一个`CSS`文件
* 需要压缩`HTML/CSS/JS`代码
* 需要压缩图片

其默认值为`production`

**区分环境**

* `--mode `用来设置模块内的`process.env.NODE_ENV`
* `--env ` 用来设置webpack配置文件的函数参数
* `cross-env`用来设置 node 环境的`process.env.NODE_ENV`
* `DefinePlugin`用来设置模块内的全局变量

**命令行配置1**

1. webpack 的 mode 默认为`production`
2. `webpack serve`的mode 默认为`development`
3. 可以在模块内部通过`process.env.NODE_ENV`获取当前的环境变量，无法在webpack配置文件中获取此变量

```json
"script":{
    "build":"webpack",
    "start":"webpack serve"
}
// 或者
"scripts": {
  "build": "webpack --mode=production",
  "start": "webpack --mode=development serve"
},

// 和在webpack.config.js设置mode一样
module.exports = {
    mode:'production'
}
```

index.js --> 打包模块内

```js
console.log(process.env.NODE_ENV); // development | production
```

webpack.config.js 

```js
console.log(process.env.NODE_ENV); // undefined
```

**命令行配置2**

1. 无法在模块内通过`process.env.NODE_ENV`访问
2. 可以通过`webpack.config.js`（配置文件）中通过函数获取当前环境变量

```json
"scripts": {
   "dev": "webpack serve --env=development",
   "build": "webpack --env=production",
}
```

index.js

```js
 console.log(process.env.NODE_ENV);// undefined
```

webpack.config.js

```js
// 在函数外访问不行
console.log('NODE_ENV',process.env.NODE_ENV);// undefined

// 在函数内可以访问
module.exports = (env,argv) => {
  console.log('env',env);// development | production
};
```

**DefinePlugin**

* 可以设置全局变量（不是`window`）所有模块都能读取到该变量的值
* 可以在任意模块内通过`process.env.NODE_ENV`获取当前的环境变量
* 但无法在`node环境`(webpack配置文件中)下获取当前环境变量

其实这里相当于将模块内名为`process.env.NODE_ENV`的值代替为我们设置的值

```js
plugins:[
   new webpack.DefinePlugin({
      'process.env.NODE_ENV':JSON.stringify(process.env.NODE_ENV)
   })
]   
```

index.js

```js
console.log(NODE_ENV);//  production
```

webpack.config.js

```js
console.log('process.env.NODE_ENV',process.env.NODE_ENV);// undefined
console.log('NODE_ENV',NODE_ENV);// error ！！！
```

**cross-env**

只能设置`node`环境下的变量`NODE_ENV`

package.json

```json
"scripts": {
  "build": "cross-env NODE_ENV=development webpack"
}
```

webpack.config.js，但在配置函数中也是获取不到的，只能在这个文件中不是导出函数之外才能获取到值                                                                                                                                                                                                                                                                                                                                                                                                         

```js
console.log('process.env.NODE_ENV',process.env.NODE_ENV);// development
```

### 开发环境配置

#### 安装服务器

```cmd
npm install webpack-dev-server --save-dev 
```

webpack.config.js

| 类别      | 配置名称   | 描述                                                         |
| --------- | ---------- | ------------------------------------------------------------ |
| output    | path       | 指定输出硬盘上的目录                                         |
| output    | publicPath | 表示的是打包生成的index.html文件里面引用资源的前缀           |
| devServer | publicPath | 表示的是打包生成的静态文件所在的位置（若是`devServer`里面的publicPath没有设置，则会认为是`output`里面设置的`publicPath`的值） |
| devServer | static     | 用于配置提供额外静态内容的目录                               |

`webpack.config.js`

```js
module.exports = {
    devServer:{
        static:path.resolve(__dirname,'public'),
        port:8080,
        open:true 
    }
}
```

package.json

```json
"scripts": {
    "build": "webpack",
+   "start": "webpack serve"
}
```

#### 支持css

* [css-loader](https://www.npmjs.com/package/css-loader)用来翻译处理@import和url()
* [style-loader](https://www.npmjs.com/package/style-loader)可以把CSS插入DOM中

安装模块

```cmd
cnpm i style-loader css-loader -D
```

webpack.config.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  mode: 'development',
  devtool:false,
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  },
  module: {
    rules: [
     { test: /\.css$/, use: ['style-loader','css-loader'] }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};
```

注意：css-loader有个`importLoaders:参数`的参数来设置，其起作用时可以将这个chunk在向前交给前几个loader处理，但是呢，`less-loader/sass-loader`都将这个功能内置了，所以我们可以不设置。

#### 支持less和sass

安装

```cmd
npm i less less-loader -D
npm i node-sass sass-loader -D
```

webpack.config.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  mode: 'development',
  devtool: false,
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  },
  module: {
    rules: [
      { test: /\.css$/, use: ['style-loader', 'css-loader'] },
+     { test: /\.less$/, use: ['style-loader','css-loader', 'less-loader'] },
+     { test: /\.scss$/, use: ['style-loader','css-loader', 'sass-loader'] }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({ template: './src/index.html' })
  ]
};
```

#### px转换为rem

```cmd 
npm i px2rem-loader --save-dev 
```

```js
{
    test: /\.css$/,
        //最后一个loader,就上面最左边的loader一定要返回一个JS脚本
        //1读取CSS内容给css-loader
        use: ['style-loader',
              {
                  loader: 'css-loader',
                  options: {//如果不配的话默认值是0
                      importLoaders: 1
                  }//对于包含的CSS文件而言，要使用前面的几个loader来处理
              }, 'postcss-loader',{
                  loader:'px2rem-loader',
                  options:{
 +                    remUnit:150,// 一个REM是多少个像素
 +                    remPrecision:8//计算REM的单位，保留几位小数 设置精度
                  }
              }]
},
```

#### css兼容性

为了浏览器的兼容性，有时候我们必须加入`-webkit`，`-ms`，`-o`，`-moz`这些前缀

* Trident内核：主要代表IE浏览器，前缀为`-ms`
* Gecko内核：主要代表Firefox，前缀为`-moz`
* Presto内核：主要代表Opera，前缀为`-o`
* Webkit内核：主要为Chrome和Safari，前缀为`-webkit`

我们可以通过插件`postcss-loader`来解决

* postcss-loader 可以使用`PostCss`处理css
* postcss-preset-env 把现代的css转换成大多数浏览器能理解的
* Postcss-prevset-env已经包含了`autoprefixer`和`browsers`选项

```cmd
npm i postcss-loader postcss-preset-env -D
```

postcss.config.js

```js
let postcssPresetEnv = require('postcss-preset-env');
module.exports={
    plugins:[postcssPresetEnv({
        browsers: 'last 5 version'
    })]
}
```

webpack.config.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  mode: 'development',
  devtool: false,
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  },
  module: {
    rules: [
+     { test: /\.css$/, use: ['style-loader', 'css-loader','postcss-loader'] },
+     { test: /\.less$/, use: ['style-loader','css-loader','postcss-loader','less-loader'] },
+     { test: /\.scss$/, use: ['style-loader','css-loader','postcss-loader','sass-loader'] }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({ template: './src/index.html' })
  ]
};
```

当然**browsers**可以在`.browserslistrc`文件配置

```js
{
+  "browserslist": {
+    "development": [
+      "last 1 chrome version",
+      "last 1 firefox version",
+      "last 1 safari version"
+    ],
+    "production": [
+      ">0.2%"
+    ]
+  }
+}
```

#### 支持图片

安装

```cmd
npm i file-loader url-loader asset-modules 
```

webpack.config.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  mode: 'development',
  devtool: false,
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' },
      { test: /\.css$/, use: ['style-loader', 'css-loader'] },
      { test: /\.less$/, use: ['style-loader','css-loader', 'less-loader'] },
      { test: /\.scss$/, use: ['style-loader','css-loader', 'sass-loader'] },
+     { 
+        test: /\.(jpg|png|bmp|gif|svg)$/, 
+        type:'asset/resource',
+        generator:{
+         filename:'images/[hash][ext]'
+        }
+     }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({ template: './src/index.html' })
  ]
};
```

#### 压缩JS、CSS和HTML

- [optimize-css-assets-webpack-plugin](https://www.npmjs.com/package/optimize-css-assets-webpack-plugin)是一个优化和压缩CSS资源的插件
- [terser-webpack-plugin](https://www.npmjs.com/package/terser-webpack-plugin)是一个优化和压缩JS资源的插件

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
+const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
+const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
+  mode: 'none',
  devtool: false,
  entry: './src/index.js',
+  optimization: {
+    minimize: true,
+    minimizer: [
+      new TerserPlugin(),
+    ],
+  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js',
    publicPath: '/',
  },
  devServer: {
    contentBase: path.resolve(__dirname, 'dist'),
    compress: true,
    port: 8080,
    open: true,
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        loader: 'eslint-loader',
        enforce: 'pre',
        options: { fix: true },
        exclude: /node_modules/,
      },
      {
        test: /\.jsx?$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [[
              '@babel/preset-env',
              {
                useBuiltIns: 'usage'
                corejs: {
                  version: 3
                },
                targets: {
                  chrome: '60',
                  firefox: '60',
                  ie: '9',
                  safari: '10',
                  edge: '17',
                },
              },
            ], '@babel/preset-react'],
            plugins: [
              ['@babel/plugin-proposal-decorators', { legacy: true }],
              ['@babel/plugin-proposal-class-properties', { loose: true }],
            ],
          },
        },
        include: path.join(__dirname, 'src'),
        exclude: /node_modules/,
      },
      { test: /\.txt$/, use: 'raw-loader' },
      { test: /\.css$/, use: [MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader'] },
      { test: /\.less$/, use: [MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader', 'less-loader'] },
      { test: /\.scss$/, use: [MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader', 'sass-loader'] },
      {
        test: /\.(jpg|png|gif|bmp|svg)$/,
        type:'asset/resource',
        generator:{
          filename:'images/[hash][ext]'
        }
      },
      {
        test: /\.html$/,
        loader: 'html-loader',
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
    template: './src/index.html',
+     minify: {  
+        collapseWhitespace: true,
+        removeComments: true
      }
    }),
    new MiniCssExtractPlugin({
      filename: 'css/[name].css',
    }),
+    new OptimizeCssAssetsWebpackPlugin(),
  ],
};
```

### 文件指纹（hash）

webpack的文件构建顺序：entry入口模块出发，解析所有依赖的模块 => 形成一个代码块（chunk）=> chunk在生成不同的文件（asset）

* 打包后输出的文件名和后缀
* **hash**一般是结合CDN缓存来使用，通过webpack构建之后，生成对应文件名自动带上对应的MD5值。如果文件内容改变的话，那么对应文件哈希值也会改变，对应的`HTML`引用的URL地址也会改变，触发CDN服务器从源服务器上拉取对应数据，进而更新本地缓存

指纹占位符

| 占位符      | 含义                                                   |      |
| ----------- | ------------------------------------------------------ | ---- |
| ext         | 资源后缀名                                             |      |
| name        | 文件名称                                               |      |
| path        | 文件的相对路径                                         |      |
| folder      | 文件所在的文件夹                                       |      |
| hash        | 每次webpack构建时生成一个唯一的hash值                  |      |
| chunkhash   | 根据chunk生成hash值，来源于同一个chunk，则hash值就一样 |      |
| contenthash | 根据内容生成hash，文件内容相同hash值就相同             |      |

#### hash

Hash是整个项目的hash值，其更加每次编译内容计算得到，每次编译之后都会生成`hash`，当修改任何文件都会导致hash发生改变

```js
const path = require("path");
const glob = require("glob");
const PurgecssPlugin = require("purgecss-webpack-plugin");
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const PATHS = {
  src: path.join(__dirname, 'src')
}
module.exports = {
  mode: "production",
+  entry: {
+    main: './src/index.js',
+    vender:['lodash']
+  },
  output:{
    path:path.resolve(__dirname,'dist'),
+    filename:'[name].[hash].js'
  },
  devServer:{
    hot:false
  },
  module: {
    rules: [
      {
        test: /\.js/,
        include: path.resolve(__dirname, "src"),
        use: [
          {
            loader:'thread-loader',
            options:{
              workers:3
            }
          },
          {
            loader: "babel-loader",
            options: {
              presets: ["@babel/preset-env", "@babel/preset-react"],
            },
          },
        ],
      },
      {
        test: /\.css$/,
        include: path.resolve(__dirname, "src"),
        exclude: /node_modules/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
          },
          "css-loader",
        ],
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
+      filename: "[name].[hash].css"
    }),
    new PurgecssPlugin({
      paths: glob.sync(`${PATHS.src}/**/*`,  { nodir: true }),
    }),
  ],
};
```

#### chunkhash

文件采用hash计算的话，当项目中任何一个文件被修改了构建后都会生成新的哈希值，即使其他文件内容压根没改变。这样子是没办法实现缓存效果，我们需要换另一种哈希值计算方式，即`chunkhash`，其和`hash`不一样，它根据不同的入口文件（Entry）进行依赖文件解析，同一个chunk生成的hash是一样的。chunk之间相互独立，修改互不影响。

```js
const path = require("path");
const glob = require("glob");
const PurgecssPlugin = require("purgecss-webpack-plugin");
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const PATHS = {
  src: path.join(__dirname, 'src')
}
module.exports = {
  mode: "production",
  entry: {
    main: './src/index.js',
    vender:['lodash']
  },
  output:{
    path:path.resolve(__dirname,'dist'),
+    filename:'[name].[chunkhash].js'
  },
  devServer:{
    hot:false
  },
  module: {
    rules: [
      {
        test: /\.js/,
        include: path.resolve(__dirname, "src"),
        use: [
          {
            loader:'thread-loader',
            options:{
              workers:3
            }
          },
          {
            loader: "babel-loader",
            options: {
              presets: ["@babel/preset-env", "@babel/preset-react"],
            },
          },
        ],
      },
      {
        test: /\.css$/,
        include: path.resolve(__dirname, "src"),
        exclude: /node_modules/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
          },
          "css-loader",
        ],
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
+      filename: "[name].[chunkhash].css"
    }),
    new PurgecssPlugin({
      paths: glob.sync(`${PATHS.src}/**/*`,  { nodir: true }),
    }),
  ],
};
```

#### contenthash

使用`chunkhash`存在一个问题，就是当在一个`JS`文件中引入`css`文件，由于是属于同一个chunk编译后它们的hash是相同的，而且只要js文件发生改变，关联的css文件hash也会改变，这个时候可以使用`mini-css-extract-plugin`里的`contenthash`值，保证即使css文件所处的模块里就算其他文件内容改变，只要css文件内容不变，那么就不会重复构建。

```js
const path = require("path");
const glob = require("glob");
const PurgecssPlugin = require("purgecss-webpack-plugin");
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const PATHS = {
  src: path.join(__dirname, 'src')
}
module.exports = {
  mode: "production",
  entry: {
    main: './src/index.js',
    vender:['lodash']
  },
  output:{
    path:path.resolve(__dirname,'dist'),
    filename:'[name].[chunkhash].js'
  },
  devServer:{
    hot:false
  },
  module: {
    rules: [
      {
        test: /\.js/,
        include: path.resolve(__dirname, "src"),
        use: [
          {
            loader:'thread-loader',
            options:{
              workers:3
            }
          },
          {
            loader: "babel-loader",
            options: {
              presets: ["@babel/preset-env", "@babel/preset-react"],
            },
          },
        ],
      },
      {
        test: /\.css$/,
        include: path.resolve(__dirname, "src"),
        exclude: /node_modules/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
          },
          "css-loader",
        ],
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
+      filename: "[name].[contenthash].css"
    }),
    new PurgecssPlugin({
      paths: glob.sync(`${PATHS.src}/**/*`,  { nodir: true }),
    }),
  ],
};
```

![hash](/medias/imges/theory/webpack/hash.jpgs)

### polyfill

* `@babel/preset-env`会根据预设的浏览器兼容列表从`stage-4`选取必须的`plugin`，也就是说，不引入别的`stage-x`，@babel-preset-env将只支持`stage-4`
* 只要兼容的是三个方面：
  * 最新的ES语法，比如：箭头函数
  * 最新的ESAPI，比如：Promise
  * 最新的ES实例方法，比如：String.prototype.includes

#### babel-polyfill

* Babel默认只能转换新的`javascript`语法，而不转化新的API，比如`Iterator,Generator,Set,Maps,Proxy,Reflect,Symbol,Promise`等全局对象。以及一些在全局对象上的方法（比如：Object.assgin）都会转码。比如说，ES6在`Array`对象上新增`Array.form`方法，Babel就不会转码这个方法，如果想要让这个方法运行，必须使用`babel-polyfill`来转换等。
* `babel-polyfill`它是通过向全局对象和内置对象的`prototype`上添加方法来实现，比如运行环境中不支持`Array.prototype.find`方法，引入`polyfill`我们就可以使用`es6`方法来编写了，但是缺点就是会造成全局空间污染
* [@babel/@babel/preset-env](https://www.npmjs.com/package/@babel/preset-env)为每一个环境的预设
* @babel/preset-env默认支持语法转化，需要开启`useBuiltIns`配置才能转化API和实例方法
* `useBuiltIns`可选值包括："usage" | "entry" | false, 默认为 false，表示不对 polyfills 处理，这个配置是引入 polyfills 的关键

安装

```cmd
npm i @babel/polyfill
```

**useBuiltIns:false**

* `useBuiltIns:false`不对`polyfill`做操作，如果引入`@babel/polyfill`，则无视配置的浏览器兼容，引入所有的`polyfill`

```js
import '@babel/polyfill';
```

```js
 {
                test: /\.jsx?$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: [["@babel/preset-env", {
+                           useBuiltIns: false,
                        }], "@babel/preset-react"],
                        plugins: [
                            ["@babel/plugin-proposal-decorators", { legacy: true }],
                            ["@babel/plugin-proposal-class-properties", { loose: true }]
                        ]
                    }

                }
},
```

`useBuiltIns:entry`

在项目入口引入一次（多次引入会报错），其会根据配置的浏览器兼容，引入浏览器不兼容的`polyfill`，需要在入口文件手动添加`import '@babel/polyfill`，会自动根据`browserslist`替换成浏览器不兼容的所有`polyfill`。

* 这里需要制定`core-js`的版本，如果`corejs:3`则`import @babel/polyfill`需要改成`import 'core-js/stable';import 'regenerator-runtime/runtime`
* corejs默认是2，配置3需要单独安装`core-js@3`

```js
import '@babel/polyfill';
```

```cmd
npm i core-js@3
```

```js
import 'core-js/stable';
import 'regenerator-runtime/runtime';
```

```js
 {
                test: /\.jsx?$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: [["@babel/preset-env", {
+                           useBuiltIns: 'entry',
+                           corejs: { version: 2 }
                        }], "@babel/preset-react"],
                        plugins: [
                            ["@babel/plugin-proposal-decorators", { legacy: true }],
                            ["@babel/plugin-proposal-class-properties", { loose: true }]
                        ]
                    }

                }
},
```

```js
{
    "browserslist": {
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ],
    "production": [
      ">1%"
    ]
  },
}
```

**useBuiltIns:usage**

其i根据配置的浏览器兼容，以及你代码中用到的API来进行`polyfill`，实现按需添加

当设置为`usage`时，`polyfill`会自动按需添加，不需要手动引入`@babel/polyfill`

useage的行为类型`babel-transform-runtime`，不会造成全局污染，其是局部引入

```js
import '@babel/polyfill';
console.log(Array.from([]));
```

```js
 {
    test: /\.jsx?$/,
    exclude: /node_modules/,
    use: {
        loader: 'babel-loader',
        options: {
            presets: [["@babel/preset-env", { 
+               useBuiltIns: 'usage',
+               corejs: { version: 3 }
            }], "@babel/preset-react"],
            plugins: [
                ["@babel/plugin-proposal-decorators", { legacy: true }],
                ["@babel/plugin-proposal-class-properties", { loose: true }]
            ]
        }
    }
},
```

#### babel-runtime

* Babel为了解决全局空间污染问题，提供单独的包`babel-runtime`用以提供编译模块的工具函数，简单说`babel-runtime`更像是一种按需加载的实现，比如你那里需要使用`Promise`，只要在这个文件头部`import Promise from 'babel-runtime/core-js/promise'`就行了

```cmd
npm i babel-runtime -D
```

```js
import Promise from 'babel-runtime/core-js/promise';
const p = new Promise(()=> {

});
console.log(p);
```

#### babel-plugin-transform-runtime 

* `@babel/plugin-transform-runtime`插件是为了解决
  * 多个文件重复引用相同的`helpers`（帮助函数） -> 提供运行时
  * 新API方法全局污染 -> 局部引入
* 启用插件`babel-plugin-transform-runtime`后，Babel就会使用`babel-runtime`下的根据函数
* `babel-plugin-transform-runtime`插件能够将这些工具函数代码转换成`require`语句，指向为对`babel-runtime`的引用
* `babel-plugin-transform-runtime`就是可以在我们使用新API时自动`import babel-runtime`里面的`polyfill`
  * 当我们使用 `async/await` 时，自动引入 `babel-runtime/regenerator`
  * 当我们使用 ES6 的静态事件或内置对象时，自动引入 `babel-runtime/core-js`
  * 移除内联`babel helpers`并替换使用`babel-runtime/helpers` 来替换
* `corejs`默认是3,配置2的话需要单独安装`@babel/runtime-corejs2`

```cmd
npm i @babel/runtime-corejs2 -D
```

```js
  {
        test: /\.jsx?$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ["@babel/preset-env",'@babel/preset-react'],
            plugins: [
+              [
+                "@babel/plugin-transform-runtime",
+                {
+                  corejs: 2,//当我们使用 ES6 的静态事件或内置对象时自动引入 babel-runtime/core-js
+                  helpers: true,//移除内联babel helpers并替换使用babel-runtime/helpers 来替换
+                  regenerator: true,//是否开启generator函数转换成使用regenerator runtime来避免污染全局域
+                },
+              ],
              ['@babel/plugin-proposal-decorators', { legacy: true }],
              ['@babel/plugin-proposal-class-properties', { loose: true }],
            ],
          },
        },
      },
```

**如何选择**

`babel-runtime`适合在组件和类库项目中使用，而`babel-polyfill`适合在业务项目中使用

#### polyfill-service

- [polyfill.io](https://polyfill.io/v3/)自动化的 JavaScript Polyfill 服务
- [polyfill.io](https://polyfill.io/v3/)通过分析请求头信息中的 UserAgent 实现自动加载浏览器所需的 polyfills

```html
<script src="https://polyfill.io/v3/polyfill.min.js"></script>
```

