---
title: Query-Params
date: 2020-09-30 08:17:08
categories: 需求解决
top: false
summary: 在不同情况下的传参区别
tags: 
 - 业务需求解决
---

### query-params

大家都知道`query-params`是我们常见的路由参数或`Url`传参时携带过去的参数对象，它们之间有些是共性，但也有所区别，这篇我们就从`VUE的路由传参`和`Node传参`出发深入挖掘它们之间的区别。

### 一、Vue路由传参层面

假设我们现在要实现一个路由切换，点击切换的`Login`组件，并传递`username与password`

路由组件:

```js
{
    path:'/Login',
    name:'login',
    component:Login
}
```

这时我们使用`router-link`组件来进行路由跳转，分为`query`和`params`形式：

#### 1-1、query

query层面写法：**可以看出query方式，路由路径可以写成name或path**.

两者Url都映射为：`http://localhost/Login?username=root&password=123`

参数接收使用：`$route.query.username`，`$route.query.password`

```html
<router-link :to="{name:'login',query:{username:'root',password:'123'}}"></router-link>
<router-link :to="{path:'/Login',query:{username:'root',password:'123'}}"></router-link>
```

#### 1-2、params

params层面写法：**可以看出params，路由路径只能写成name，不然会无视掉params中的内容**

```html
<router-link :to="{name:'login',params:{username:'root',password:'123'}}"></router-link>
```

要使用params传参，我们路由组件则需要改动，这里的`username与password`传参参数名要与我们路由传入的名字一致，否则会接收不到参数

```js
{
    path:'/Login/:username/:password',
    name:'login',
    component:Login
}
```

params的Url映射为`http://localohost/Login/root/123`,

参数接收：`$route.params.username`或`$route.params.password`

* 小结：

可以看出`query`在路由切换与参数较为灵活，而`params`在参数较为严格，但接受的参数能够与我们定义的参数保持一致

### 二、Node层面

这一层的场景是，前端使用`axios`发送`url`请求，在Node中接收参数作为讲解例子。

发送场景还是：前端将`username与password`传递给后端，后端并接收

#### 2-1、query

```js
axios.get('/Login?username=root&password=123’)
// 或
axios.get('/Login',{params:{username:'root',password:'123'}})
```

接收方式：这里与Vue的路由参数不同，上面的这两种方式从本质是可以理解为`URL的？`传参，我们都用`req.query`来接收

```js
router.get('/Login',function(req,res){
    console.log(req.query.username)
    console.log(req.query.password)
})
```

#### 2-2、params

```js
axios.get('/Login/root/123')
```

接收方式：

```js
router.get('Login/:username/:password',function(req,res){
    console.log(req.params.username)
    console.log(req.params.password)
})
```

#### 2-3、body【扩展】

body方式接收是用来接收前端发送的`Post`请求参数的，但我们`node`本身是不支持`post`参数接收，我们要安装第三方的库，例如：在 koa中，我们安装`koa-body`，这里我们只讲接收方式

```ja
axios.post('/Login',{username:'root',password:'123'})
```

接收方式：

```js
router.post('/Login',function(req,res){
    console.log(req.body.username)
    console.log(req.body.password)
})
```

### 三、小结

在Vue路由层面的参数传递与接收：用什么传就用`this.$route.xxx`来接收

在前端与Node参数的传递与接收：只要是URL上的?形式传递（可以理解为queryString形式）都用`req.query.xxx`接收，若是`URL/username/password`形式，就用`req.params.xxx`接收，

> 要特别注意axios中url请求的`params`传参形式，它与我们Vue路由的params形式不同。本质还是URL的问号(`?`)传参。