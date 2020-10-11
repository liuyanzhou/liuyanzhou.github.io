---
ktitle: koa
date: 2020-08-11 21:05:33
top: false
categories: node
summary: koa
tags:
 - nodeJS
---

### Koa

koa是express原班原马打造的轻量、健壮、富有表现力的`nodeJS`框架，目前koa有`koa1`和`koa2`两个版本，koa2用来`node.js7.6.0`或者更高版本；koa不在内核方法中绑定任何中间件，它仅仅是一个轻量级的函数库，几乎所有功能都必须通过第三方插件来实现

koa1 与 koa2 、express之间的区别只要在于获取异步任务的结果的方式不同

| 框架名  | 作用    | 异步处理          |
| ------- | ------- | ----------------- |
| Express | web框架 | 回调函数          |
| Koa     | web框架 | Generator + yield |
| Koa2    | web框架 | async / await     |

[官网](https://koa.bootcss.com/)

### 一、koa使用

* 安装

```bash
npm i koa
```

* 搭建一个简单的koa服务器

```js
const Koa = require('koa')
const app = new Koa()

app.use(async ctx=>{
    ctx.body = 'hello world'
})

app.listen(3000,()=>{
    console.log('服务器开启')
})
```

### 二、中间件概念

node框架都是靠中间件来扩展自己的能力，不管是`express`还是`koa`都是要靠中间来扩展自己的业务处理能力，也同样是给我们context添加相应的处理方法

中间件可以看做一条河流，从上游开始往下游传递`next()方法`，而在中间处理好了请求，往浏览器响应请求的时候，是从后一个中间传递到上一个中间的`next()`函数位置，并继续往下执行next函数后的代码。注册中间件的方法`app.use(相应的中间件配置)`，可以将中间件的配置理解为一个函数

中间件的特点：

* 一个中间件就是一个函数
* 中间件的执行顺序符合洋葱模型
* 内层中间件能否执行取决于外层中间件的`next`函数
* 调用next函数得到的是`Promise`对象，它是用下一个中间件挂载的函数中`return`的值

```js
//这里的middleWare函数就是一个中间件
let middleWare = async (ctx,next)=>{
    console.log("first middleWare");
    ctx.body = "hello world";
}
app.use(middleWare);

app.use(async(ctx,next)=>{
    // 刚进入中间件想做的事情
    await next()
    // 内层所有中间件结束之后想做的事情
})
```

中间件是通过`next()`方法来往下一个中间件传递信息，执行顺序可以通过`洋葱模型`来理解

![洋葱模型]( /medias/imges/node/koa/洋葱模型.png )

```js
const Koa = require('koa')
const app = new Koa()

let middleWare1 = async function(ctx, next) {
    console.log("m1 start ...")
    ctx.state = {
        name: 'zs'
    }
    next()
    console.log('m1 end...')
}
let middleWare2 = async function(ctx, next) {
    console.log('m2 start ....')
    console.log(ctx.state.name)
    next()
    console.log('m2 end...')
}
app.use(middleWare1)
app.use(middleWare2)
app.listen(3000, () => {
    console.log('服务器开启')
})
// 请求服务器输出顺序
// m1 start...
// m2 start...
// m2 end...
// m1 end...
```

### 三、koa中该注意的对象

#### 3.1Application对象

* application是koa创建的实例，通常简写为`app`
* app.use(中间件配置) 将给定的中间件方法添加到此应用程序
* app.listen(端口后,回调函数) 设置端口口
* app.on 错误处理

#### 3.2 上下文context对象常用属性及方法

- ctx.app:应用程序实例引用,等同于app;
- ctx.req:Node 的 `request` 对象.
- ctx.res:Node 的 `response` 对象.
- ctx.request:koa中的Request对象；
- ctx.response:koa中的response对象；
- ctx.state：对象命名空间，通过中间件传递信息；
- ctx.throw:抛出错误；

#### 3.3 常见http状态码

| http状态码 | 描述                                                   |
| ---------- | ------------------------------------------------------ |
| 100        | 继续。继续响应剩余部分，进行提交请求                   |
| 200        | 成功                                                   |
| 301        | 永久移动。请求资源永久移动到新位置                     |
| 302        | 临时移动。请求资源临时移动到新位置                     |
| 304        | 未修改。请求支援对比上次未被修改，响应中不包含资源内容 |
| 401        | 未授权，需要身份验证                                   |
| 403        | 禁止。请求被拒绝                                       |
| 404        | 未找到，服务器未找到需要资源                           |
| 500        | 服务器内部错误。服务器遇到错误。无法完成请求           |
| 503        | 服务器不可用。临时服务过载。无法处理请求               |

### 四、koa常见中间件介绍

#### 4.1 koa-router

路由是引导匹配之意，是匹配url到相对应处理程序的活动

* 安装

```bash
npm i koa-router -S
```

* 配置使用

```js
const Koa = require('koa')
const Router = require('koa-router')
const app = new Koa()
const router = new Router()
// 挂载中间件
app.use(router.routes())
router.get('/', async ctx => {
    ctx.redirect('/index')
})
router.get('/index', async ctx => {
    ctx.body = '主页'
})
router.get('/detail', async ctx => {
    ctx.body = '详细页'
})
router.get('/getData', async ctx => {
    ctx.body = {
        name: 'zs',
        age: 20
    }
})

// 匹配全局路由
router.all('/(.*)',async ctx=>{
    ctx.body = '没有该路由'
})

app.listen(3000, () => {
    console.log('服务器开启')
})
```

* koa-router 也能够设置路由头, 配置`prefix`参数即可

```js
const Router = require('koa-router')
const router = new Router({
    prefix: '/admin'
})
```



#### 4.2 koa-views

koa-views 用于加载html模板文件,它会在ctx上挂载一个`render`方法，一般配置模板引擎使用，例如：pug引擎，nunjucks引擎，模板引擎用法请去相应的官网查看，个人推荐`nunjucks`

* 安装

```bash
npm i koa-views -S
npm i pug 
# 或
npm i koa-nunjucks-2 
```

* 配置使用(配合pug模板引擎)

```js
const Koa = require('koa')
const Router = require('koa-router')
const views = require('koa-views')
const app = new Koa()
const router = new Router()
app.use(views(__dirname + '/views', {
    map: {
        html: "pug"
    }
}))
app.use(router.routes())
router.get('/', async ctx => {
    ctx.body = "首页"
})

router.get('/home', async ctx => {
    // ctx.body = 'home'
    await ctx.render('home.pug')

})

app.listen(3000, () => {
    console.log('服务器开启')
})
```

* 配置使用(配合nunjucks模板引擎)

```js
const Koa = require('koa')
const Router = require('koa-router')
const nunjucks = require('koa-nunjucks-2')
const router = new Router()
const app = new Koa()
app.use(nunjucks({
    ext: "html", //.njk
    path: __dirname + "/views",
    nunjucksConfig: {
        trimBlocks: true //防止Xss漏铜；
    }
}))

app.use(router.routes())

router.get('/', async ctx => {
    await ctx.render("index", {
        username: 'zs',
        num: 3,
        arr: [
            { name: 'zs', age: 20 },
            { name: 'ls', age: 11 }
        ],
        str: 'hello world'
    })
})

router.get('/son1', async ctx => {
    await ctx.render('son1')
})

router.get('/import', async ctx => {
    await ctx.render('import')
})

app.listen(3000, () => {
    console.log('服务器启动')
})
```

#### 4.3 koa-static

koa-static 是用于加载静态资源的中间件，通过它可以加载css、js等静态资源

* 安装

```bash
npm i koa-static -S
```

* 配置使用

```js
const static = require("koa-static");
app.use(static(__dirname+"/static")) //加载静态文件的目录
```

#### 4.4 koa-body

我们大部分请求分为get 和 post ，get请求的参数获取koa已经帮我们封装好了，从`ctx.query.xxx`获取到，而`post`的参数并没有封装获取的方法，但我们可以借助`koa-body` 这个模块来配置获取，**而且它允许文件、照片等上传并能够接收到上传的文件信息**

* 安装

```bash
npm i koa-body -S
```

* 配置使用

```js
const koaBody = require('koa-body')
app.use(koaBoy({
    multipart: true // 允许文件上传
}))
```

* 参数信息获取

```js
const connection = require('../conn')
const fs = require('fs')
const path = require('path')
// query是我们的 ctx.request ，这是作为一个函数参数传入
const addNews = async(query) => {
    // 普通的post参数获取
    let body = query.body // ctx.request.body
    // 文件类型信息获取 
    let files = query.files // ctx.request.files
    const { title, content, type } = body
    let pathSrc = path.join(__dirname, '../', '../', 'assets/uploads/')
    if (!fs.existsSync(pathSrc)) {
        fs.mkdirSync(pathSrc)
    }
    fs.writeFileSync(pathSrc + files.imgUrl.name, fs.readFileSync(files.imgUrl.path))
    let imgUrl = 'assets/uploads/' + files.imgUrl.name
    console.log(imgUrl)
    // fs.readFileSync(query.files.img)
    let sql = "insert into news (title,content,type,imgUrl) values (?,?,?,?)"
    let [row] = await connection.promise().query(sql, [title, content, type, imgUrl])
    return row
}

module.exports = {
    addNews
}
```



####4.5 mysql2

mysql2是node连接mysql的模块

* 安装

```bash
npm i mysql2 -S
```

* 配置使用

```js
const mysql = require('mysql2')
// 连接数据库
const connection = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: '123456',
    database: 'nodeMysqlTest',
    charset: "utf8mb4"
})

// 使用 例如：
app.use(async ()=>{
    // 调用promise()方法返回promise对象 rows 是查询返回的数据数组
    const [rows] =  await connection.promise().query(sql)
})
```

#### 4.6 koa2-cors

koa2-cors是给后端响应添加让浏览器允许接收跨域请求信息的包，官网:https://www.npmjs.com/package/koa2-cors

```js
const Koa = require('koa')
const cors = require('koa2-cors')
const app = new Koa()
// 跨域基础配置
app.use(cors({
    // 接收的请求源
    origin: ['http://localhost:9528'],
    // 全局证书，设为 true 即可
    credentials: true
}))
app.listen(3000,()=>{
    console.log('服务器开启....')
})
```

### 五、koa实现长连接

长连接是说服务端能够下发数据给客户端，客户端也能将数据发给服务器，并且也能使用服务端广播的方式传给其他客户端，实现方式有几种，客户端轮询，sse，websoket， socket.io 

#### 5.1 客户端轮询

所谓的客户端轮询是指在客户端下使用定时器`setInterval()`不断先服务器发送请求，获取最新数据渲染，这是一种非常不好并消耗内存的方法，现在的开发中不推荐使用

index.js

```js
const Koa = require('koa')
const Router = require('koa-router')
const static = require('koa-static')
const koaBoy = require('koa-body')
const mysql = require('mysql2')
const app = new Koa()
const router = new Router()

const connection = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: '123456',
    database: 'nodeMysqlTest',
    charset: 'utf8mb4'
})

app.use(static(__dirname + '/static'))
app.use(koaBoy({
    multipart: true
}))


router.get('/', async ctx => {
    ctx.body = 'hello world'
})

router.get('/getData', async ctx => {
    let sql = "select * from chat"
    let [rows] = await connection.promise().query(sql)
    ctx.body = rows
})

router.post('/addChat', async ctx => {
    let { content } = ctx.request.body
    let sql = "insert into chat (content) values (?)"
    let [rows] = await connection.promise().query(sql, [content])
    console.log(rows)
})

app.use(router.routes())
app.listen(3000, () => {
    console.log('服务器开启....')
})
```

页面请求：

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <script src="jquery.min.js"></script>
        <title>文章信息展示</title>
        <style>
            body {
                margin: 0;
            }

            ul {
                margin: 0;
                padding: 0;
                list-style: none;
            }

            /* 聊天框样式 */

            .message-box {
                width: 320px;
                height: 480px;
                border: 1px solid;
                position: fixed;
                bottom: 20px;
                right: 10px;
                background: white;
                border-radius: 10px;
                overflow: hidden;
            }

            .message-header {
                width: 100%;
                height: 40px;
                background: rgb(0, 175, 242);
                text-align: center;
                line-height: 40px;
                color: white;
            }

            .input-text {
                position: absolute;
                bottom: 0px;
            }

            .input-text textarea {
                width: 300px;
                margin-left: 10px;
                margin-bottom: 20px;
                height: 100px;
            }

            .input-text button {
                float: right;
                margin-right: 10px;
                margin-bottom: 20px;
            }

            .chat-show {
                height: 200px;
                margin-top: 20px;
            }

            .chat-show li {
                margin: 10px;
            }

            .chat-left {
                /* float: left; */
                text-align: left;
            }

            .chat-right {
                text-align: right;
            }

            ul {
                overflow: scroll;
            }
        </style>
    </head>

    <body>
        <!-- 聊天框模板 -->
        <div class="message-box">
            <div class="message-header">聊天框</div>
            <ul class="chat-show">
                <!-- <li class="chat-left">A:大家好</li>
<li class="chat-right">B:你好啊</li>
<li class="chat-left">A:你好啊</li>
<li class="chat-right">B:很高兴见到大家</li>
<li class="chat-left">B:哈哈</li>
<li class="chat-left">B:哈哈</li>
<li class="chat-left">B:哈哈</li>
<li class="chat-left">B:哈哈</li>
<li class="chat-left">B:哈哈</li> -->
            </ul>
            <form class="input-text" onsubmit="return false;">
                <textarea class="input-frame"></textarea>
                <button type="submit">发送</button>
            </form>
        </div>


    </body>
    <script>
        function render() {
            $.ajax({
                url: 'getData',
                methods: 'get',
                success(res) {
                    console.log(res)
                    let str = ''
                    for (let i = 0; i < res.length; i++) {
                        str += `<li class="chat-left">A:${res[i]['content']}</li>`
                    }
                    document.querySelector("ul").scrollTop = 10000000
                    $('.chat-show').html(str)
                }
            })
        }
        render()
        // 这里不断向服务器发送数据请求并重新渲染，便为客户单轮询
         setInterval(() => {
                 render()
             }, 1000)
        // document.querySelector("ul").scrollTop = 10000000;
        $('.input-text button').on('click', function() {
            let content = $(".input-frame").val();
            $.ajax({
                url: '/addChat',
                method: "post",
                data: {
                    content
                },
                success(res) {
                    console.log(res)
                }
            })
        })
    </script>
</html>
```

#### 5.2 sse

sse是服务端任何时刻向我们的web⻚⾯推送数据和信息，是单工形式，基于http协议，会存在跨域问题，不能断开发送，一旦断开，则服务器不能在发送给相应的客户端。也就是不能存在`res.end()`发送完成代码，它的实现要客户端和服务器相互配合

* 服务端设置
  *  响应头头部

  ```js
  res.writeHead(200, { "content-type": "text/event-stream" })
  ```

  *  返回数据格式

  ```js
  //  data: 声明数据开始
  // \r\n\r\n 标志数据结尾
  res.write('data:' + JSON.stringify(obj) + '\r\n\r\n')
  ```

* 客户端接收

```js
let source = new EventSource('/sse')
// 建立连接会调用的回调函数
source.onopen = function() {
    console.log('建立连接...')
    console.log(source.readyState)
}
// 接收数据
source.onmessage = function(e) {
    console.log('onmessage')
    console.log(e.data)
}
// 发生错误会调用的回调函数
source.onerror = function(err) {
    return console.log(err)
}
```

index.js

```js
const http = require('http')
const fs = require('fs')
const url = require('url')

const app = http.createServer((req, res) => {
    const { pathname } = url.parse(req.url, true)
    if (pathname == '/') {
        let showIndex = fs.readFileSync(__dirname + '/static/index.html')

        res.write(showIndex)
        res.end()
    } else if (pathname == '/sse') {
        res.writeHead(200, { "content-type": "text/event-stream" })
        let obj = {
            name: 'zs',
            age: 20
        }
        setInterval(() => {
            res.write('data:' + JSON.stringify(obj) + '\r\n\r\n')
        }, 500)
    }
})

app.listen(3000, () => {
    console.log('服务器开启')
})
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
        <h1>主页</h1>
    </body>
    <script>
        let source = new EventSource('/sse')
        source.onopen = function() {
            console.log('建立连接...')
            console.log(source.readyState)
        }
        source.onmessage = function(e) {
            console.log('onmessage')
            console.log(e.data)
        }
        source.onerror = function(err) {
            return console.log(err)
        }
    </script>
</html>
```

#### 5.3 websocket

WebSocket 是 HTML5 开始提供的⼀种在单个 TCP 连接上进⾏全双⼯通讯的协议 ，全双工形式，不存在跨域问题，但要在安装`ws`模块

```bash
npm i ws -S
```

* 搭建websocket服务器

```js
const WebSocketServer = require('ws').Server

let wss = new WebSocketServer({ port: 8181 })
// 监听链接
wss.on('connection', ws => {
    console.log('有连接进来')
    let obj = {
        name: 'zs',
        age: 20
    }
// 监听socket发送过来的数据
    ws.on("message", data => {
        console.log(data)
    })
 // 发送给客户端数据
    setInterval(() => {
        ws.send(JSON.stringify(obj))
    }, 1000)
 // 错误时调用
    ws.onerror = err => {
        return console.log(err)
    }
// ws.close() // 关闭连接
})
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
        <h1>主页</h1>
        <button class="btn">点击我推送数据到服务端</button>
    </body>
    <script>
        // 连接服务器；建立握手
        let ws = new WebSocket("ws://localhost:8181");
        ws.onopen = function(){
            console.log("连接成功...");
        }
        // 监听服务端发送过来的数据
        ws.addEventListener("message",function(e){
            console.log(e.data);
        })

        document.querySelector(".btn").onclick = function(){
            // 向服务端发送数据
            ws.send("数据");
        }
        ws.onerror = function(err){
            return console.log(err);
        }
        // 关闭连接；
        // ws.close();
    </script>
</html>
```

websockt的用法在开发中用处很多。例如demo的`电商数据可视化`的项目中，我们要实现多个客户端的界面进行同步的业务需求，那么就可以通过`websocket`作为多个客户端的中间信息传输层，让多端操作同步于一端。它的实现方式是先在展示的组件上`注册要触发的事件`，在从`websocket`响应中提取所需要触发的图表信息和函数进行触发。具体看demo中项目写法。

#### 5.4 socket.io 

socket.io 是node一个外部模块，它能够兼容上面的几种方式，所以我们开发一般都是采用它来实现长连接,当然它也不能存在`res.end()`方法，使用`socket.emit()`方法发送数据，` socket.broadcast.emit()`广播方式发送数据,它的实现要客户端和服务端共同配合，在客户端要引入`socket.io.js`文件获取`io`对象，服务端要引入`socket.io`模块

```bash
npm i socket.io -S
```

index.js

```js
    const Koa = require('koa')
    const Router = require('koa-router')
    let static = require('koa-static')

    const app = new Koa()
    const router = new Router()
    app.use(static(__dirname + '/static'))

    const server = require('http').createServer(app.callback())
    const io = require('socket.io')(server)
    io.on('connection', (socket) => {
        console.log('有连接进来')
        let obj = {
                name: 'zs',
                age: 20
            }
        // 发送数据给客户端
        socket.emit("getData", JSON.stringify(obj))
            // setInterval(() => {
            //     socket.emit("getData", JSON.stringify(obj))
            // }, 500)
        // 监听客户端触发addData事件
        socket.on('addData', function(data) {
            console.log(data)
            socket.broadcast.emit("getData", data)
        })
    })
    router.get('/', async ctx => {
        ctx.body = 'hello world'
    })

    app.use(router.routes())

    server.listen(3000, () => {
        console.log('服务器开启')
    })
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
        <script src="socket.io.js"></script>
    </head>

    <body>
        <h1>主页</h1>
        <button class="btn">点击我发送数据</button>
    </body>
    <script>
        // 添加连接地址，这里是localhost并未写路由，可以空着也可以'/'会拼接为`localhost:3000/`
        let socket = io.connect('/')
        // 监听服务器的getData(发送数据) 事件
        socket.on('getData', function(data) {
            console.log(data)
        })
		// 触发服务器的addData事件，发送数据给服务器
        document.querySelector('.btn').onclick = function() {
            socket.emit("addData", "数据")
        }
    </script>
</html>
```









