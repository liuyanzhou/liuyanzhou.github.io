---
title: node基础篇
date: 2020-08-11 10:02:25
top: false
categories: node
summary: node基础篇
tags:
 - nodeJS
---

### Node基础篇

node.js 诞生于2009年，Node.js采用C++ 语言编写而成，Node不是一门语言，而让js运行在后端的运行时（环境），并且不包括`javascript`全集，因为在服务器中不包含DOM和BOM，Node也提供了一些新的模块，例如：`http`、`fs`模块等。Node.js 使用了事件驱动、非阻塞式 I/O 的模型，使其轻量又高效并且Node.jsd的包管理`npm`，是全球最大的开源生态系统。并且`webpack`是基于`Node`来编写的。所以，我们前端有必要学习。[安装官网](https://nodejs.org/en/)

#### Node解决了那些问题

node在处理高并发、I/O密集型场景有明显的性能优势

* 高并发：在同一时间并发访问服务器的请求量
* I/O 密集：是文件操作、网络操作、数据库操作

**什么场合下应该考虑使用node框架**
当应用程序需要处理大量并发的输入和输出，而在向客户端响应之前，应用程序并不需要进行非常复杂的处理：`聊天服务器`、`电子商务网站`

> I/O密集相对的是CPU密集：CPU密集是指 逻辑处理运算、压缩、解压、加密、解密

掌握：Web主要场景就是接受客户端的请求读取静态资源和渲染界面，所以Node非常适合Web应用的开发。

#### JS单线程

`javascript`在最初设计时设计成了单线程，为什么不是多线程呢？如果多线程同时查找DOM那岂不是会混乱？这里所谓的单线程是指的是主线程是单线程的。所以在node中主线程依旧是单线程的

* 单线程热点是节约了内存，并且不需要在切换执行上下文
* 而且单线程不要管锁的问题

#### 同步阻塞和异步非阻塞

![同步阻塞异步非阻塞](/medias/imges/node/同步异步和阻塞非阻塞.png)

> 同步和异步是由被调用者决定(这个任务是同步还是异步)，阻塞和非阻塞是由调用者决定（它等就是阻塞，它不等就是非阻塞），我们node就是异步非阻塞

同步异步|阻塞非阻塞的组合：

* 同步阻塞
* 异步阻塞
* 同步非阻塞
* 异步非阻塞

更为专业的说法：（注：这得的调用者和非调用者不与上面的相同）

**同步与异步**：关注的是消息通知机制

* 同步就是发出调用后，没有得到结果之前，该调用不返回，一旦调用返回，就得到返回值。简而言之就是调用者主动等待这个调用的结果。
* 而异步则相反，调用者在发出调用后这个调用就直接返回了，所以没有返回结果。换句话说当一个异步过程调用发出后，调用者不会立即得到结果，而是调用发出后，被调用者通过状态、通知或回调函数处理这个调用并返回结果

**阻塞与非阻塞**：阻塞和非阻塞关注的是程序在等待调用结果（消息、返回值）时的状态

* 阻塞调用的是指调用结果返回之前，当前线程会被挂起。调用线程只有在得到结果之后才会返回该线程。
* 非阻塞调用指在不同立刻得到结果之前，该调用不会阻塞当前线程。

#### node与java对比

由上面可知：node的主线程是单线程的，那么它就不涉及锁、线程池的改变，在解决高并发的 i/o密集型的程序是有天然优势的。而java是多线程语言，那么它就要涉及到线程池、锁的概念，对于高并发的处理并没有天然的优势，但在处理`cpu`密集型的程序却有着天然的优势。

> 小编本想在这写node的事件循环（eventloop）机制的解析，但它要与浏览器事件循环进行对比，那么要了解这部分的知识，请移架到另一篇文章`浏览器事件环与node事件环`

### 一、使用node原生搭建服务器

```js
// 引入http内置模块
let http  = require('http')
// 创建服务器
let server = http.createServer((req,res)=>{
    console.log('hello')
    res.end('hello world'); // 等同于 res.write('hello world');res.end();
})
// 设置端口
server.listen(3000)
```

### 二、node模块化

一、为什么会有模块化

* 在JavaScript 发展初期就是为了实现简单的页面交互逻辑，不会写太多的逻辑代码，而如今随着前端代码日益膨胀，这时候`JS`作为嵌入式脚本语言的定位动摇了，它的代码组织规范不足以驾驭大型的代码规范。这会导致我们常说的`变量污染`，`文件依赖冲突`等常见的JS不规范现象，由此node的`commonjs`规范由此应运而生。

二、commonjs规范

* Node.js规定一个JavaScript文件就是一个模块，模块内部定义的变量和函数默认情况下在外部无法得到 

* 模块内部可以使用exports（module.exports.xxx）对象进行成员导出， 使用require方法导入其他模块。

  * 模块成员导出 (**模块导出有两种方式，推荐选其一种导出即可***）

  ```js
  // a.js
  // 在模块内部定义变量
  let version = 1.0;
  // 在模块内部定义方法
  const sayHi = name => `您好, ${name}`;
  // 向模块外部导出数据 
  exports.version = version;
  exports.sayHi = sayHi;
  // 导出的另一种方式
  module.exports.version = version;
  module.exports.sayHi = sayHi;
  ```

  * 模块成员导入 （导入模块时后缀可以省略）

  ```js
  // b.js
  // 在b.js模块中导入模块a
  let a = require('./b.js');
  // 输出b模块中的version变量
  console.log(a.version);
  // 调用b模块中的sayHi方法 并输出其返回值
  console.log(a.sayHi('小明'));
  ```

  注意：exports是module.exports的别名(地址引用关系)，导出对象最终以module.exports为准

* require的查找原则

  * 加载文件 , 注意：要带上`./`

  ```js
  require('./a.js')
  require('./a')
  ```

  解析：

  1. require方法根据模块路径查找模块，如果是完整路径，直接引入模块

  2. 如果模块后缀名省略，先找同名的JS文件，再找同名JS文件夹(例如上面的第二行代码)

  3. 如果找到了同名文件夹，找文件夹中的`index.js`

  4. 如果文件夹没有`index.js`就会找当前文件夹中的`package.json`文件中查找`main`选项中的入口文件

  5. 如果找到指定的入口文件不存在或没有指定入口文件则模块没有被找到，在向上一级的node_modules文件夹查找

     

  * 加载没有路径且没有后缀的模块

  ```js
  require('a')
  ```

  解析：

     	1. Node.js会假设它是内置模块，找到则引用
     	2. 如果不是内置模块，会去`node_modules`文件夹中找
     	3. 首先看是否有该名字的`.js`文件，有则引用，无接下查询
     	4. 再看是否有该名字的文件夹
     	5. 如果文件夹下有`index.js`则引用，无则接下查询
     	6. 如果文件夹没有`index.js`，则查看文件夹是否有`package.json`文件，有则在查看是否存在`main`入口
     	7. 若没有mian入口则找不到，在向上一级的node_modules文件夹查找

注意：require查找有向上搜索功能，直到node安装依赖的node_modules文件夹(可用 `npm root -g`查看)下没有该模块则报错

### 三、node的内置模块

nodejs内置模块有：Buffer，C/C++Addons，Child Processes，Cluster，Console，Cr

ypto，Debugger，DNS，Domain，Errors，Events，File System，

Globals，HTTP，HTTPS，Modules，Net，OS，Path，Process，P unycode，Query Strings，Readline，REPL，Stream，String De coder，Timers，TLS/SSL，TTY，UDP/Datagram，URL， Utilities，V8，VM，ZLIB；内置模块不需要安装，外置模块需要安装；

#### 3.1 fs模块

fs是文件操作模块，所有文件操作都是有同步和异步之分，特点是同步会加上`Sync`，如：异步读取文件`readFile`，同步读取文件`readFileSync`,fs操作可分为**文件操作和文件夹操作**

* 文件操作：

  * 文件读取： 读取出来的文件数据形式是`buffer`类型(十进制表示的二进制数据) ,而`str.toString()`方法可以`utf8`转为字符串形式

    * 异步读取

    ```js
    let fs = require('fs')
    fs.readFile('1.txt',(err,data)=>{
        if(err){
            return console.log(err)
        }
        console.log(data.toString())
    })
    ```
    * 同步读取

    ```js
    let fs = require('fs')
    let res = fs.readFileSync('1.txt')
    console.log(res.toString())
    ```
  * 文件写入： 文件写入有参数配置: flag  ---> "a"：追加写入 ，"w":重新写入 , "r"：读取

  ```js 
  fs.writeFile("1.txt", "追加的文字", { flag: "w" }, function(err) {
      if (err) {
          return console.log(err)
      }
      console.log('追加成功')
  })
  ```
  * 复制文件

    自制写法：

  ```js
  // src 复制源
  // dist 输出源
  function mycopy(src,dist) {
      fs.writeFileSync(dist,fs.readFileSync(src))
  }
  mycopy("1.txt","4.txt")
  ```

  ​	内置写法

  ```js
  fs.coypFile("1.txt","3.txt",err=>{
      if(err){
          return console.log(err)
      }
      console.log("复制成功")
  })
  ```
  *  文件删除

  ```js
  fs.unlink("2.txt",err=>{
      if(err){
          return console.log(err)
      }
      console.log("删除成功")
  })
  ```

  * 修改名称

  ```js
  fs.rename("1.txt","2.txt",err=>{
      if(err){
          return console.log(err)
      }
      console.log("修改成功")
  })
  ```

* 操作文件夹(同步不演示，就只是加上`Sync`并且不是回调函数接受)

  * 读取文件夹

  ```js
  fs.readdir('22',(err,data)=>{
      if(err){
          return console.log(err)
      }
      console.log(data) // data 是将22文件夹的文件或文件夹名称组成一个数组形式返回
  })
  ```

  * 创建文件夹

  ```js
  fs.mkdir('11',err=>{
      if(err){
          return console.log(err)
      }
  })
  ```

  * 删除空文件夹

  ```js
  fs.rmdir("33",err=>{
      if(err){
          return console.log(err)
      }
      console.log("删除成功")
  })
  ```

  * 判断文件或目录是否存在，存在则返回`true`,不存在则`fasle`

  ```js
  // 文件
  fs.exists("ma.js",exists=>{
      console.log(exists)
  })
  // 文件夹
  fs.exits("22",exists=>{
      console.log(exists)
  })
  ```

  * 获取文件或目录详细信息

  ```js
  fs.stat("ma.js",(err,stat)=>{
      if(err) {
          return console.log(err)
      }
      // 判断文件是否是文件
      let res = stat.isFile() // true
      let res2 = stat.isDirectory() // fasle
  })
  ```

  * 清空文件夹

  ```js
  function removeDir(path) {
      let data = fs.readdirSync(path)
      for(let i=0;i<data.length;i++) {
          let url = path + '/' + data[i]
          let stat = fs.statSync(url)
          if(stat.isDirectory) {
              removeDir(url)
          }else {
              fs.unlinkSync(url)
          }
      }
      fs.rmdirSync(path) // 清空空文件夹
  }
  removeDir('./222')
  ```

* stream 流

stream 流主要的用于数据与视图加载层面，因为普通的读取数据，例如`fs.readFiles()`方法读取文件，它并不会因为我们的内存过小而将文件进行划分为小文件传输，这很可能会造成我们机器死机，内存使用溢出现象。而且stream(流)会将大文件进行划分为小文件传输

```js
let fs = require('fs')
// 创建65kb字节
let buffer = Buffer.alloc(65 * 1024)
fs.writeFile("65", buffer, err => {
    if (err) {
        return console.log(err)
    }
    console.log('写入成功')
})

// 创建流读取文件
let res65 = fs.createReadStream('65')
let num = 0
let str = ''
// 监控每次流每次发送
res65.on('data', chunk => {
    num++
    str += chunk
    console.log(num) 
})
// 监控流传送数据完毕
res65.on('end', () => {
    console.log(str)
})

// 使用流复制 相当于1.txt 的内容复制给 88.txt
let res65 = fs.createReadStream('1.txt')
let ws = fs.createWriteStream('88.txt')
res65.pipe(ws)
```



### 3.2 path路径模块

这个模块主要帮我们解决node相对路径和绝对路径的问题：

大多数数情况下使用绝对路径，因为相对路径有时候相对于是命令行工具的当前工作目录，而我们在读取文件或设置文件路径时都会选择使用`绝对路径`,这也是因为node的读取文件和写入文件的`api`都是基于命令行窗口的路径，当`require()`写的相对基于文件目录的。

注意：**我们可以使用(__dirname)获取当前文件所在的绝对路径**

* 路径拼接 `path.join('路径','路径',....)`

```js
// 导入path模块
const path = require('path')
// 路径拼接
let finialPath = path.join('itcast','a','b','c.css')
console.log(finialPath) // window: itcast/a/b/c.css linux: itcast\a\b\c.css
```

### 3.3 buffer 模块

```js
// 创建10字节
let buffer = Buffer.alloc(10)
// 将文字转化为buffer 
let buffer2 = Buffer.from('大家好')
let buffer3 = Buffer.from([0xe5, 0xa4, 0xa7, 0xe5, 0xae, 0xb6, 0xe5, 0xa5, 0xbd])
console.log(buffer3.toString()) // 大家好
// ------------------------------------------
let buffer1 = Buffer.from([0xe5, 0xa4, 0xa7, 0xe5, 0xae])
let buffer2 = Buffer.from([0xb6, 0xe5, 0xa5, 0xbd])
let newBuffer = Buffer.concat([buffer1, buffer2])
console.log(newBuffer.toString()) // 大家好
// -------------------------------------------
let { StringDecoder } = require('string_decoder')
let decoder = new StringDecoder()
let res1 = decoder.write(buffer1)
let res2 = decoder.write(buffer2)
console.log(res1, res2) // 大家好
```

### 四、node的包管理工具

#### 4.1 npm包管理器

NPM(node package manager)官网的地址是[npm官网](https://www.npmjs.com)	

常用指令：

| 指令                       | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| npm init                   | 引导创建一个`package.json`文件，若`npm init -y`可以跳过询问环节，默认创建好文件 |
| npm help(npm -h)           | 查看npm帮助信息                                              |
| npm version                | 查看npm版本信息(npm -v)                                      |
| npm search                 | 查找                                                         |
| npm install(npm i )        | 安装，默认在当前目录，如果没有`node_modules`会创建文件夹；`npm install module_name -S`或者--save,这会将包直接写入`dependencies(运行依赖)`，值得又说的是，现在npm安装包的时候，没注明后缀默认是`--save`，而当`npm install module_name -D`或者`--save-dev`会写入`devDependencies（开发依赖）`，npm install module_name -g 全局安装(命令行使用)，指定版本安装模块:  npm i module_name@1.0 通过`@`符号指定 |
| npm update(npm -up)        | 更新                                                         |
| npm remove / npm uninstall | 删除                                                         |
| npm root                   | 查看当前包安装的路径，或者通过`npm root -g` 查看全局安装路径; |

* 查看包下载源地址

```bash
npm config list
```

* 修改包下载源地址

官方地址: `https://registry.npmjs.org/ `

淘宝源地址: `https://registry.npm.taobao.org/ `

推荐使用：

```bash
npm config set registry 'https://registry.npm.taobao.org/'
```

#### 4.2 yarn

* 安装

```bash
npm install -g yarn
```

* 语法

| Yarn                     | Npm                             |
| ------------------------ | ------------------------------- |
| yarn init                | npm init                        |
| yarn                     | npm install                     |
| yarn global add xx@x.x.x | npm install xxx@x.x.x           |
| yarn add xxx@x.x.x --dev | npm install xx@x.x.x --dev-save |
| yarn remove xxx          | npm uninstall xxx               |
| yarn run xxx             | npm run xxx                     |

