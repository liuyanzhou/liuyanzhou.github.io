---
title: node插件篇
date: 2020-04-09 10:56:46
categories: node
img:  /medias/imges/node/node-title.jfif
top: true
summary: nodeJS插件篇
tags:
 - nodeJS
---



### nodeJS插件篇

> 这篇文章只介绍node开发中，最基础配置一些插件(提高开发效率)的过程，想要读懂这篇文章需要一些nodeJS基础，自行学习啦，小编也是在此记录一下过程，方便以后自己开发时好`复制`😁,这里使用的数据可`mongodb`

### 1.body-parser[获取post参数] 

> 对post请求参数做处理成在req.body中获取到参数 

* 安装

  ```bash
  npm i bady-parser
  ```

* 配置[**在项目的app.js项目入口文件下(下面的配置一样)**]

  ```javascript
  // 配置body-parser的中间件 处理所有的请求在req下绑定body属性
  app.use(bodyPaser.urlencoded({ extended: false }))
  ```

### 2.express-session[实现session功能]

> 在node.js中需要借助express-session实现session功能。

* 安装

  ```bash
  npm i express-session
  ```

* 配置

  ```js
  // 配置session
  app.use(session({
      // 固定属性 值可以任意
      secret: 'secret key',
      // 未登陆是否保留 true为保留 false 为不保留
      saveUninitialized: false,
      // 设置cookie过期时间(按毫秒存储)
      cookie: {
          maxAge: 24 * 60 * 60 * 1000
      }
  }))
  ```

### 3.cookie-parser [cookie的获取]

> express框架是没有对cookie操作的能力,所以要借助插件来完成

* 安装

  ```bash
  npm i cookie-parser
  ```

* 配置

  ```js
  // 配置解析cookie中间件 
  app.use(cookieParser())
  ```

* 使用

  ```js
  // 设置cookie,值要字符串
  res.cookie('user',JSON.stringify(userInfo), {
      maxAge: 1000 * 60 * 60 * 24 // 设置过期时间(单位毫秒)
  })
  
   //  清除 Cookie 中的登录信息
   res.clearCookie('user')
  ```

  



### 4. formidable[解析表单] 

> 解析表单，支持get请求参数，post请求参数、文件上传。

* 安装

  ```bash
  npm i formidable
  ```

* 使用

  ```js
   // 引入formidable模块
   const formidable = require('formidable');
   // 创建表单解析对象
   const form = new formidable.IncomingForm();
   // 设置文件上传路径
   form.uploadDir = path.join(__dirname, '../', '../', 'public','uploadFile')
   // 是否保留表单上传文件的扩展名
   form.keepExtensions = false;
   // 对表单进行解析
   form.parse(req, (err, fields, files) => {
       //1. err错误对象 如果表单解析失败,err里面存储错误信息,如果表单解析成功, err将会是null
       //2.fields 对象类型 保存普通表单数据
       //3. files 对象类型 保存了和上传文件相关的数据
       // Article.create 这里时小编的一个接口，列出来时为了看清楚怎么用
       await Article.create({
           title:fields.title,
           author:fields.author,
           publishDate: fields.publishDate,
           cover:files.cover.path.split('public')[1], // files.cover.path是你图片上传到那个位置，也就是面的设置文件的上传路径，在以`public`切割就得到 `\uploadFile\upload_2673c23d04dc6fd2992746e397052f00.png`
           content:fields.content
       })
  
       res.redirect('/admin/article')
   });
  
  ```

* 上面是小编做的上传图片功能，若你也是做这功能并且需要在线预览功能的话可以接下面的思路

  ```js
  // 得到上传文件按钮DOM对象
  var file = document.querySelector('#fileInput');
  // 得到你想要显示图片的盒子DOM对象
  var imgBox = document.querySelector('#imgBox')
  
  file.onchange = function() {
      // 1.创建文件读取对象
      var reader = new FileReader()
      // 2.读取文件 this.files=Fielslist是存放文件信息的数组，一般只有一个对象，直接[0]得到该文件对象
      reader.readAsDataURL(this.files[0])
      // // 3.监听onload事件
      reader.onload = function() {
          // 给照片预览盒子赋值
          imgBox.src = reader.result
      }
  }
  ```

  

### 5.mongoose-sex-page[数据分页]

> 数据分页

* 安装

  ```bash
  npm i mongoose-sex-page
  ```

* 使用

  ```js
  // 导入mongoose-sex-page
  const pagination = require('mongoose-sex-page')
  // 这个.exec()一定要加不然不会获取数据 
  // page 指定当前页
  // size 指定每页显示的数据条数
  // display 指定客户端要显示的页码数量
  // exec 向数据库中发送查询请求
   let articles = await pagination(数据集合).find().page(page).size(2).display(3).populate('author').exec()
  ```

### 6.config[配置敏感信息]

> 允许开发人员将不同运行环境下的应用配置信息抽离到单独的文件中，模块内部自动判断当前应用的运行环境，并读取对应的配置信息，极大提供应用配置信息的维护成本，避免了当运行环境重复的多次切换时，手动到项目代码中修改配置信息，说白了就是把链接数据库的信息放到一个文件下，这样在后期上线或开发阶段更好维护

* 安装

  ```bash
  npm i config
  ```

* 在项目的根目录下新建config文件夹

* 在config文件夹下面新建custom-environment-variables.json，default.json、development.json、production.json

  ![文件](/medias/imges/node/config.jpg )

* 设置系统的环境变量来区分是开发阶段还是生产阶段

  ![设置/获取环境变量](/medias/imges/node/env_config.jpg )

* custom-environment-variables.json文件可以存放系统的环境变量[小白可以忽略这项]

* 在项目中通过require方法，将模块进行导入

* 使用模块内部提供的get方法获取配置信息,get()方法会自动得去你系统环境变量中NODE_ENV值来判断使用那套配置

  ![小编使用](/medias/imges/node/config-use.jpg )

### 7.bcrypt[密码加密]

> 密码加密模块，使用`哈希`算法加密，哈希加密是单程加密方式：1234 => abcd，在加密的密码中加入随机字符串可以增加密码被破解的难度。

* 安装

  ```bash
  npm i bcrypt
  ```

* 使用

  ```js
  // 导入bcrypt模块
  const bcrypt = require('bcrypt');
  // 生成随机字符串 gen => generate 生成 salt 盐
  // genSalit接收的是一个数值,数值越大,生成的随机字符串复杂度越高,数值越小,反之,默认值为10
  let salt = await bcrypt.genSalt(10);
  // 使用随机字符串对密码进行加密
  let pass = await bcrypt.hash('明文密码', salt);
  // 例如： const pass = await bcrypt.hash('123456',salt)
  
  // 解密比对
  let isEqual = await bcrypt.compare('明文密码', '加密密码');
  // 例如   var isEqual = await bcrypt.compare('123456',user.password)     
  ```

### 8.art-template[模板引擎]

> 让程序员更方便对html和数据的拼接,这里是基于node中的`express`框架来配置的

* 安装

  ```bash
  npm install art-template express-art-template
  ```

* 配置

  ```js
  
  // 当渲染后缀为art的模板时 使用express-art-template
   app.engine('art', require('express-art-template'));
    // 设置模板存放目录[根目录文件夹]
   app.set('views', path.join(__dirname, 'views'));
    // 渲染模板时不写后缀 默认拼接art后缀
   app.set('view engine', 'art');
  ```

* 使用

  ```js
   app.get('/', (req, res) => {
       // 渲染模板
       res.render('[根目录view文件夹下的art文件]',{要传入的数据});
       // 例如
       res.render('index',{username:'zs'});
   }); 
  ```

### 9.nunjucks[模板引擎] 

>也是一款模板引擎,作用和art-template一样

* 安装

  ```bash
  npm i nunjucks
  ```

* 配置

  ```js
  // 配置模板引擎
  nunjucks.configure('view', {
    autoescape: true,
    express: app,
    watch: true // 禁止模板文件缓存，实时监听模板变化
  });
  ```

* 使用

  ```js
  exports.showNew = (req, res, next) => {
      res.render('topic-new.html')
  }
  ```

### 10.svg-captcha [随机生成svg验证码]

> 随机生成svg验证码,用于客户端注册/登录中使用验证码

* 安装

  ```bash
  npm i svg-captcha
  ```

* 使用

  * 1.创建

    ```JS
    // 导入svg验证码
    const svgCaptcha = require('svg-captcha');
    
    router.get('/captcha', (req, res, next) => {
      const captcha = svgCaptcha.create() // 创建验证码
      req.session.captcha = {
        text: captcha.text, // 验证码文本内容
        expires: +new Date() + 1000 * 60 * 10 // 验证码过期时间
      } // 把验证码存储到会话 Session 中
      res.type('svg') // 设置响应内容类型
      res.status(200).send(captcha.data) // 发送响应结果
    })
    ```

  * 2.找到使用验证码的html中使用

    ```html
    <!-- 直接在img标签中的src属性赋值,以为src一旦结果路由就能得到插件生成的svg --> 
    <img id="svg_code" src="/captcha" alt="captcha">
    ```

### 11.jquery-validation [客户端校验]

> jquery通过的客户端校验规则

* 安装

  ```bash
  npm i jquery-validation
  ```

* 使用

  * 引入文件

    ```html
    <script src="/node_modules/jquery-validation/dist/jquery.validate.js"></script>
    ```

  * 设置规则,更多规则请去官网查看,推荐去[菜鸟教程](https://www.runoob.com/jquery/jquery-plugin-validate.html)

    ```js
      // signinFrom 你要控制的那个表单
      const signinFrom = $('#signinFrom')
      $('#signinFrom').validate({
          // 规则这里要和你想要的表单控件的id对上
        rules: {
          "sgPassword": {
            required: true
          },
          "sgPassword": {
            required: true
          }
        },
         // 当规则不匹配的时候方式的错误信息
        messages: {
          username: {
            required: "请输入用户名"
          },
          password: {
            required: "请输入密码"
          }
        },
          // 当点击提交的按钮触发的事件,这里是值表单中的<button />的 type为submit
          // 参数from是固定写法
        submitHandler: function (form) {
            // 处理事件
            $.ajax({
                url: '/signin',
                method: 'POST',
                data: signinFrom.serialize(),
                success: function (data) {
                    console.log('okaaa')
                    const { code, message } = data
                    if (code === 1 || code === 2) {
                        $('#siginError').show()
                    } else if (code === 0) {
                        $('#siginError').hide()
                        window.location = '/'
                    }
                },
                error:function(err) {
                    console.log(err)
                }
            })
        }
      })
    ```

### 12.express-validator [服务端数据校验]

* 安装

  ```bash
  npm i express-validator
  ```

* 使用

  ```js
  // 导入模块
  const { body, check, validationResult } = require('express-validator');
  
  // 在路由中使用[post的第二参数(规则),第三参数(验证失败返回客户端数据)]
  router.post('/signup', [
      body('username').not().isEmpty(),
      body('nickname').not().isEmpty(),
      body('password').not().isEmpty(),
      body('captcha').not().isEmpty()
  
  ], (req, res,next) => {
      const errors = validationResult(req);
      if (!errors.isEmpty()) {
          return res.status(422).json({ errors: errors.array() });
      }
      next()
  }, indexCtrl.signin)
  ```

### 13.node的静态文件开启配置

直接在app.js配置

```js
const path = require('path')
const express = require('express')
//  第一个参数是你请求发出去的时候的路径[例如你的<img src="/public/***.jpg" />标签中的src属性] ,第二个参数 你要开饭的静态文件路径 , 
app.use('/public/', express.static(path.join(__dirname, '/public/')))
app.use('/node_modules/', express.static(path.join(__dirname, './node_modules/')))
```





### 寄语

> 小编在node开发中使用过的插件就这些,这些插件每个都有官网,小编这里只是最基本的配置,但基本够用于普通开发,深层开发还未接触😁~~~~



