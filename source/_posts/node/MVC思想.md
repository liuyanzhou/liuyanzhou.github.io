---
title: MVC思想
date: 2020-08-13 11:10:13
categories: node
top: true
summary: nodeJS插件篇
tags:
 - nodeJS
---

### MVC思想

mvc思想是我们后端语言构建中大型项目常用的构建思想，它能将项目大致分为三个模块，即`model(数据处理层)`、`controller(控制层)`、`view(视图展示层)`,使用MVC分层使得系统更加灵活、扩展性更强、更加直观、可维护性更强。但其构建使用在小型项目上体现非常繁琐，所以小编建议小型项目就不推荐使用MVC思想架构。

* model : 负责数据访问
* controller ：负责消息处理
* view ：负责视图呈现

controller 可以了理解为是沟通`model`和`view`的桥梁，它既要把model层处理好的数据传给view层，也要把view层的数据改变(如数据添加，删除等指令)传给modal去处理，就当于一个快递员，两边传入指令或数据。这让其他两个模块功能就变得单一，可维护性变高。

构建项目开始：

* 先建立主入口`index.js`，它只负责将项目中所依赖的包导入，不做其他数据处理
* 在分别建立`model`、`controller`、`view`三个文件夹
* 在建立`router.js`用于构建项目路由模块，当然也可以当做所有的路由的主入口，并将其他路由导入

![MVC](/medias/imges/node/mvc/mvc.png)

index.js

```js
const Koa = require('koa')
const static = require('koa-static')
const nunjucks = require('koa-nunjucks-2')
const koaBoy = require('koa-body')
const { router } = require('./router')

const app = new Koa()
app.use(static(__dirname + '/assets'))
app.use(koaBoy({
    multipart: true // 允许文件上传
}))
app.use(nunjucks({
    ext: "html", //.njk
    path: __dirname + "/views",
    nunjucksConfig: {
        trimBlocks: true //防止Xss漏铜；
    }
}))
router(app)
app.listen(3000, () => {
    console.log('服务器开启...')
})
```

router.js

```js
const newRouter = require('./router/news')
const adminRouter = require('./router/admin')

module.exports = {
    router: function(app) {
        app.use(newRouter.routes())
        app.use(adminRouter.routes())
    }
}
```

router/admin/index.js

```js
const Router = require('koa-router')
const router = new Router({
    prefix: '/admin'
})
const adminController = require('../../controller/admin')

router.get('/', adminController.showIndex)
router.get('/addNewsPage', adminController.showAddNewsPage)
router.get('/message', adminController.showMessage)
router.get('/newsListPage', adminController.showNewsListPage)
router.post('/addNews', adminController.addNews)

module.exports = router
```

router/news/index.js

```js
const Router = require('koa-router')
const newController = require('../../controller/news')
const router = new Router({
    prefix: '/news'
})

router.get('/', newController.getData)
module.exports = router
```

controller/admin/index.js

```js
const adminModal = require('../../model/admin')

const showIndex = async(ctx) => {
    await ctx.render('admin/index')
}

const showAddNewsPage = async ctx => {
    await ctx.render('admin/addNewsPage')
}

const showMessage = async ctx => {
    await ctx.render('admin/message')
}
const showNewsListPage = async ctx => {
    await ctx.render('admin/newsListPage')
}

const addNews = async ctx => {
    let res = await adminModal.addNews(ctx.request)
    let info = {}
    if (res.affectedRows > 0) {
        info.message = '上传成功'
        info.status = 1
    } else {
        info.message = '上传失败'
        info.statuc = 0
    }
    console.log(info)
}

module.exports = {
    showIndex,
    showAddNewsPage,
    showMessage,
    showNewsListPage,
    addNews
}
```

controller/news/index.js

```js
let newModal = require('../../model/news')

async function getData(ctx) {
    let showData = await newModal.getData()
    await ctx.render('news/index', {
        data: showData
    })
}

module.exports = {
    getData
}
```

model/admin/index.js

```js
const connection = require('../conn')
const fs = require('fs')
const path = require('path')
const addNews = async(query) => {
    let body = query.body
    let files = query.files
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

model/news/index.js

```js
const connection = require('../conn')

async function getData() {
    let sql = "select * from news"
    let [row] = await connection.promise().query(sql)
    return row
}

module.exports = {
    getData
}
```

model/conn.js

```js
const mysql = require('mysql2')

const connection = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: '123456',
    database: 'nodeMysqlTest',
    charset: 'utf8mb4'
})

module.exports = connection
```

小结：由此可见，我们数据数据库操作的代码在`model`目录下，并将方法模块化导出，视图呈现放在`view`目录下，两者通过`controller`目录下的方法进行交互，controller 上的方法可以负责展示页面，接受参数，传递数据等操作，并导出到router目录对应的路由模块进行使用，并件`router.js`路由总导出口导入到项目主入口`index.js`中去使用

