---
title: xss-crfs攻击
date: 2021-02-23 14:48:51
categories: 原理分析
top: false
summary: xss-crfs攻击
tags: 
 - 原理分析
---

### xss-csrf攻击

> 两者都是我们常见的跨站脚本攻击方式，作为前端我们要懂得它们是如果实现的攻击的才能在编码的时候尽量去避免它们

### xss

* 反射型

> 当我们的接口是根据传入路径的参数是什么就返回什么，并且没做路径的编译转码，这就让一些不法分子可以通过这个漏洞去嵌入脚本，获取到用户的敏感信息，例如`cookie`

例如我们让这个接口`welcome`接口嵌入脚本`alert(document.cookie)`得到cookie

```js
 http://localhost:3000/welcome?type=<script>alert(document.cookie)</script>
```

```js
app.get('/welcome', function(req, res) {
    res.send(`${req.query.type}`)
})
```

改进，我们可以通过`encodeURIComponent`的方法对访问路径参数进行转码

```js
app.get('/welcome', function(req, res) {
    res.send(`${encodeURIComponent(req.query.type)}`)
})
```

* DOM-Based 

> 它不基于后端，我们在前端操作dom时，若对一些路径不进行转码 会造成这种危害。但是这种攻击的手段很弱，我们业务上很难写出这种`弱智`逻辑，这里作为了解。

例如：添加图片，我们并未对`src`路径进行转码，这时若我们嵌入`<img  src="xxx" onerror="alert(1)" id="" />`这样子的代码，用户点击了就会执行我们的脚本，弹出`alert(1)`

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <input type="text" name="" id="web"> <button id="add">添加图片</button>
    <div class="box"></div>
    <script src="/node_modules/jquery/dist/jquery.js"></script>
    <script>
        $('#add').on('click', function() {
            $('.box').html(`<img  src="${$('#web').val()} "/>`)
        })
    </script>
</body>

</html>
```

解决方法：对`src`进行转码，使用`encodeURI`

```js
$('.box').html(`<img  src="${encodeURI($('#web').val())} "/>`)
```

* 存储型

> 存储型，是我们的后端存储的用户嵌入恶意的代码，并且后端未对代码进行安全转码，这就有可能导致每个返回这个站点的用户都受到攻击

例如：用户评论信息

```js
// 用户评论信息
let comments = [{ username: 'ccx', content: '存储型攻击' }, { username: 'zs', content: '如何防范' }]
app.get('/api/list', function(req, res) {
    res.json({ code: 0, comments })
})
// xss存储型 恶意的脚本存储到了服务器上，所有人访问时都会造成攻击，比反射型和DOM-Based范围更大
app.post('/api/addcomment', function(req, res) {
    let r = session[req.cookies[SEESION_ID]] || {}
    let user = r.user
    if (user) {
        comments.push({ username: user.username, content: req.body.content })
        res.json({ code: 0 })
    } else {
        res.json({ code: 1, error: '用户未登陆' })
    }
})
```

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>评论页面</title>
        <link rel="stylesheet" href="/node_modules/bootstrap/dist/css/bootstrap.css">
    </head>

    <body>
        <div class="container">
            <div class="row">
                <div class="col-md-12">
                    <div class="col-md-6 col-md-offset-3">
                        <div class="panel panel-danger">
                            <div class="panel-heading">
                                <h2>评论页面</h2>
                            </div>
                            <div class="panel-body">
                                <ul class="list-group"></ul>
                            </div>
                            <div class="panel-footer">
                                <form onsubmit="addComment(event)">

                                    <div class="form-group">
                                        <label for="content" class="control-label">内容</label>
                                        <input type="text" id="content" class="form-control">
                                    </div>
                                    <div class="form-group">
                                        <input type="submit" value="发表" class="btn btn-primary">
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <script src="/node_modules/jquery/dist/jquery.js"></script>
        <script>
            function getList() {
                $.get('/api/list').then(res => {
                    if (res.code == 0) {
                        let html = '';
                        res.comments.forEach(element => {
                            html += `<li class="list-group-item">${element.username} ${encodeHmtl(element.content)}</li>`
                        })
                        $('.list-group').html(html)
                    }
                })
            }
            getList()

            function addComment(e) {
                e.preventDefault()
                let content = $('#content').val()
                $.post('/api/addcomment', {
                    content
                }).then(res => {
                    console.log(res)
                    if (res.code === 1) {
                        location.href = '/login.html'
                    } else {
                        getList() // 评论成功获取最新列表
                    }
                })
            }
            // 对评论内容进行转码
            function encodeHmtl(str) {
                return str.replace(/&/g, '&amp;').replace(/"/g, '&qout;').replace(/'/g, '&apos').replace(/</g, '&lt;').replace(/>/g, '&gt;')
            }
        </script>
    </body>

</html>
```

> 防范手段：对评论内容进行转码处理，例如我们调用`encodeHtml`函数

**小结：**防范xss的手段可以归为对**该过滤的代码就过滤**

* 客户端传递给服务器时，需要校验先过滤一次
* 服务器在对数据过滤一次
* 最显著的就是直接在输出的时候进行过滤

### csrf

> 伪造用户身份，发送请求，关键就是让用户访问已经嵌入恶意脚本的网站。

例如：不法分子要盗取用户银行卡的金额，我们可以借助下面图来说明一些钓鱼网站的做法：

![csrf](/medias/imges/theory/csrf.png)

当用户登录xxx银行之后，在访问了钓鱼网站，而钓鱼网站已进内嵌了发送给xxx银行的转账请求，并且银行也并未对用户身份进行进一步的验证，就会导致csrf转账攻击。

例如：转账页面

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>转账页面</title>
    <link rel="stylesheet" href="/node_modules/bootstrap/dist/css/bootstrap.css">
</head>

<body>
    <div class="container">
        <div class="row">
            <div class="col-md-12">
                <div class="col-md-6 col-md-offset-3">
                    <div class="panel panel-danger">
                        <div class="panel-heading">
                            <h2>转账页面</h2>
                            <p>用户: <span id="u"></span></p>
                            <p>金额: <span id="m"></span></p>

                        </div>
                        <div class="panel-body">
                            <form onsubmit="transfer(event)">
                                <div class="form-group">
                                    <label for="target" class="control-label">收款人</label>
                                    <input type="text" id="target" class="form-control">
                                </div>
                                <div class="form-group">
                                    <label for="money" class="control-label">金额</label>
                                    <input type="text" id="money" class="form-control">
                                </div>
                                <div class="form-group">
                                    <label for="code" class="control-label" id="picture"></label>
                                    <input type="text" id="code" class="form-control">
                                </div>
                                <div class="form-group">
                                    <input type="submit" value="支付" class="btn btn-primary">
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script src="/node_modules/jquery/dist/jquery.js"></script>
    <script>
        function getUserInfo() {
            $.get('/api/userInfo').then(res => {
                console.log(res)
                if (res.code === 1) {
                    location.href = '/login.html'
                } else {
                    let {
                        username,
                        money,
                        svg
                    } = res.user
                    $('#u').html(username)
                    $('#m').html(money)
                    $('#picture').html(svg)

                }
            })
        }
        getUserInfo()

        function transfer(e) {
            e.preventDefault()
            let target = $('#target').val()
            let money = $('#money').val()
            let code = $('#code').val()
            let token = document.cookie.match(/connect.sid=([^;]*)/) || [];
            $.post('/api/transfer', {
                target,
                money,
                code,
                token: 'my_' + token[1]
            }).then(res => {
                console.log(res)
                if (res.code === 0) {
                    getUserInfo()
                } else {
                    alert(res.error)
                }
            })
        }
    </script>
</body>

</html>
```

钓鱼网站：fish

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    哈哈哈 (钱没了)
    <iframe src="/bad.html" style="display:none;" frameborder="0"></iframe>
</body>

</html>
```

bad.html

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <!-- 其实第三方网站是拿不到用户的cookie 当3000端口提交内容时自动会加cookie -->
    <form name="myform" action="http://localhost:3000/api/transfer" method="POST">
        <input type="text" name="target" value="jw">
        <input type="text" name="money" value="100">
    </form>
</body>
<script>
    console.log(document.myform)
    document.myform.submit()
</script>

</html>
```

解决方法：1.增加验证码验证 2.比对是否是同一个端口下发送的请求 3.增加token

* 在服务器增加验证码验证，当用户再一次访问时带上验证码

```js
let svgCaptcha = require('svg-captcha')
app.get('/api/userInfo', function(req, res) {
    let r = session[req.cookies[SEESION_ID]] || {}
    // data表示的是svg内容 text表示的是验证码对应的结果
    let { data, text } = svgCaptcha.create()
    r.text = text; // 下次请求时应该拿到返回的结果和上次存好的结果做对比
    let user = r.user

    if (user) {
        res.json({ code: 0, user: { username: user.username, money: user.money, svg: data } })
    } else {
        res.json({ code: 1, error: '用户未登陆' })
    }
}
        
        app.get('/api/userInfo', function(req, res) {
    let r = session[req.cookies[SEESION_ID]] || {}
        // data表示的是svg内容 text表示的是验证码对应的结果
    let { data, text } = svgCaptcha.create()
    r.text = text; // 下次请求时应该拿到返回的结果和上次存好的结果做对比
    let user = r.user

    if (user) {
        res.json({ code: 0, user: { username: user.username, money: user.money, svg: data } })
    } else {
        res.json({ code: 1, error: '用户未登陆' })
    }
})

app.post('/api/transfer', function(req, res) {
    let r = session[req.cookies[SEESION_ID]] || {}
    let user = r.user
        if (user) {
            let { target, money, code } = req.body
            money = Number(money)
            if (code && code === r.text) { // 如果有验证码 并且验证码和我给你的一致 就可以转钱
                userList.forEach(u => {
                    if (u.username === user.username) {
                        u.money -= money
                    }
                    if (u.username === target) {
                        u.money += money
                    }
                })
                res.json({ code: 0 })

            } else {
                res.json({ code: 1, error: '验证码不正确' })
            }

        } else {
            res.json({ code: 1, error: '用户未登录' })
        }
    }
})
```

* 判断referer是否来自同源域名与端口

```js
app.post('/api/transfer', function(req, res) {
    let r = session[req.cookies[SEESION_ID]] || {}
    let user = r.user
        // 不靠谱 我们可以通过node自己发请求来实现伪造
    let referer = req.headers['referer'] || ''
    if (referer.includes('http://localhost:3000')) {
        if (user) {
            let { target, money, code } = req.body
            money = Number(money)
            if (code && code === r.text) { // 如果有验证码 并且验证码和我给你的一致 就可以转钱
                userList.forEach(u => {
                    if (u.username === user.username) {
                        u.money -= money
                    }
                    if (u.username === target) {
                        u.money += money
                    }
                })
                res.json({ code: 0 })

            } else {
                res.json({ code: 1, error: '验证码不正确' })
            }

        } else {
            res.json({ code: 1, error: '用户未登录' })
        }
    } else {
        res.json({ code: 1, error: '被人攻击了' })
    }

})
```

* 增肌token验证，最为安全与简洁的方式

> 小结：xss和csrf攻击虽然现在不常见，因为我们使用的框架和编码都会对其进行防范，但我们还是要对其一定的了解

