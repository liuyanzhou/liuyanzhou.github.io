---
title: BOM
date: 2020-06-28 16:34:10
categories: JS
top: false
summary: BOM
tags: 
 - JS
---

## 一、什么是BOM

BOM（Browser Object Model）即浏览器对象模型，它提供了独立于内容而与浏览器窗口进行交互的对象，其核心对象是 window。

​	BOM 由一系列相关的对象构成，并且每个对象都提供了很多方法与属性。

​	BOM 缺乏标准，JavaScript 语法的标准化组织是 ECMA，DOM 的标准化组织是 W3C，BOM 最初是Netscape 浏览器标准的一部分。

* BOM 与 DOM 对比

| DOM                                     | BOM                                            |
| --------------------------------------- | ---------------------------------------------- |
| 文档对象模型                            | 浏览器对象模型                                 |
| DOM就是吧【文档】当做一个【对象】来看待 | 把【浏览器】当做一个【对象】来看待             |
| DOM 的顶级对象 是 `document`            | BOM的顶级对象是 `window`                       |
| DOM 主要学习的是操作页面元素            | BOM学习的是浏览器窗口交互的一些对象            |
| DOM  是w3c 标准规范                     | BOM 是浏览器厂商对各自浏览器上定义的，兼容性差 |

* BOM  的构成

![BOM](/medias/imges/js/bom/img1.png)

由图可知：BOM  比 DOM 更大，它包含了DOM 

注意：window 对象是浏览器的顶级对象 ，它具有双重角色

* 它是 JS 访问浏览器窗口的一个接口
* 它是一个全局对象。定义在全局作用域中的变量、函数都会变成 window 对象的属性和方法，在调用的时候可以省略 window ，比如我们`alert()、prompt` 等都属与BOM
* window 下有一个特殊属性 window.name ，所以我们命名变量的时候不要用`name`

## 二、window 对象常用事件

### 2.1窗口加载事件

* window.onload = function() {} 或 window.addEventListener('load',function(){})

> window.load 是窗口(页面)加载事件，当文档内容完全加载完成会触发该事件（包括图像、脚本文件、CSS文件等），就调用的处理函数

触发load事件条件：

* a标签的超链接
* F5或者刷按钮（强制刷新）
* 前进后退按钮

注意：

1. 有了 window.onload 就可以把 JS 代码写到页面元素的上方，因为 onload 是等页面内容全部加载完毕，再去执行处理函数
2. window.onload 传统注册事件方式 只能写一次，如果有多个，会以最后一个 window.onload 为准 
3. 如果使用 addEventListener 则没有限制 

**扩展其他窗口加载事件:**

* document.addEventListener('DOMContentLoaded',function(){})

注意:

1. DOMContentLoaded 事件触发时，仅当DOM加载完成，不包括样式表，图片，flash等等。Ie9以上才支持 
2. 如果页面的图片很多的话, 从用户访问到onload触发可能需要较长的时间, 交互效果就不能实现，必然影响用户的体验，此时用 DOMContentLoaded 事件比较合适。

**代码演示**

```html
<script>
    window.addEventListener('load', function() {
        var btn = document.querySelector('button');
        btn.addEventListener('click', function() {
            alert('点击我');
        })
    })
    window.addEventListener('load', function() {
        alert(22);
    })
    document.addEventListener('DOMContentLoaded', function() {
        alert(33);
    })
</script>
```

> 注意：
>
> document.addEventListener('DOMContentLoaden',function(){}) 
>
> 比
>
>  window.addEventListener('load',function(){}) 先执行

* window.pageshow事件 

由于浏览器缓存能够缓存js代码，所以`load`事件也是会缓存下来，那么在此加载的时候，从缓存中拿取代码的话，load事件不会在被执行，这是一个大的项目隐患，所以我们可以使用`pageshow`事件来解决，pageshow会在load事件触发后触发；根据事件对象中的persisted来判断是否是缓存中的页面触发的pageshow事件，若为true则是

**`注意这个事件给window添加。`**

**代码演示：**

```html

<body>
    <script>
        // console.log(window.devicePixelRatio);
        window.addEventListener('pageshow', function() {
            alert(11);
        })
    </script>
    <a href="http://www.itcast.cn">链接</a>
</body>
```



### 2.2调整窗口大小事件

* window.onresize = function() {} 或 window.addEventListener("resize",function(){})

  window.inresize 是调整窗口大小加载事件，当触发时就调用处理函数

注意；

1. 只要窗口大小发生像素变化 ，就会触发这个事件
2. 我们经常利用这个事件完成响应式布局，通过window.innerWidth 得到当前屏幕宽度

```js
window.addEventListener('load', function() {
    window.addEventListener('resize', function() {
        console.log(window.innerWidth);
        console.log('变化了');
    })
})
```

### 2.3 监听窗口关闭事件

`beforeunload` 会在当前页面关闭前触发事件

```js
window.addEventListener('beforeunload', function() {
   // .... 业务逻辑
})
```

## 三、两种定时器

### 3.1设置定时器

| window.setTimeout(回调函数，[延迟的毫秒数])              | window.setInterval(回调函数，[间隔的毫秒数])           |
| -------------------------------------------------------- | ------------------------------------------------------ |
| 用于设置一个定时器，该定时器在定时器到期后执行调用函数。 | 法重复调用一个函数，每隔这个时间，就去调用一次回调函数 |

注意：

* window 可以省略
* 这个调用函数可以直接写成函数，或者函数名或者采用字符串`'函数名()'`【不推荐】三种形式
* 间隔的毫秒数省略默认是0，如果写，必须是毫秒。
* 因为定时器可能有很多，所以我们经常给 定时器赋值一个标识符，用于以后清除定时器用。
* 第一次执行也是间隔毫秒数之后执行。

**代码演示:**

```js
setTimeout(function(){
    console.log('爆炸了');
}, 3000);

setInterval(function() {
    console.log('继续输出');
}, 1000);
```

### 3.2清除定时器

| window.clearTimeout(timeoutID)                         | window.clearInterval(intervalID)           |
| ------------------------------------------------------ | ------------------------------------------ |
| 取消了通过调用 setTimeout() 建立的定时器 coordSampled) | 取消了通过调用 setInterval()建立的定时器。 |

> window可以省略，里面的参数是定时器的标识符

* window.clearTimeout(timeoutID)

```html
<button>点击停止定时器</button>
<script>
    var btn = document.querySelector('button');
    var timer = setTimeout(function() {
        console.log('爆炸了');

    }, 5000);
    btn.addEventListener('click', function() {
        clearTimeout(timer);
    })
</script>
```

* window.clearInterval(intervalID)

```html
<button class="begin">开启定时器</button>
<button class="stop">停止定时器</button>
<script>
    var begin = document.querySelector('.begin');
    var stop = document.querySelector('.stop');
    var timer = null; // 全局变量  null是一个空对象
    begin.addEventListener('click', function() {
        timer = setInterval(function() {
            console.log('ni hao ma');

        }, 1000);
    })
    stop.addEventListener('click', function() {
        clearInterval(timer);
    })
</script>
```

## 四、this指向问题

> ​	this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定this到底指向谁，一般情况下this的最终指向的是那个调用它的对象。

this指向主要有这几方面:

1. 全局作用域或者普通函数中this指向全局对象window（注意定时器里面的this指向window）
2. 方法调用中谁调用this指向谁
3. 构造函数中this指向构造函数的实例

**代码演示:**

```html
<button>点击</button>
<script>
    // this 指向问题 一般情况下this的最终指向的是那个调用它的对象
    // 1. 全局作用域或者普通函数中this指向全局对象window（ 注意定时器里面的this指向window）
    console.log(this);
    function fn() {
        console.log(this);
    }
    window.fn();
    window.setTimeout(function() {
        console.log(this);
    }, 1000);
    // 2. 方法调用中谁调用this指向谁
    var o = {
        sayHi: function() {
            console.log(this); // this指向的是 o 这个对象
        }
    }
    o.sayHi();
    var btn = document.querySelector('button');
    btn.addEventListener('click', function() {
        console.log(this); // 事件处理函数中的this指向的是btn这个按钮对象
    })
    // 3. 构造函数中this指向构造函数的实例
    function Fun() {
        console.log(this); // this 指向的是fun 实例对象
    }
    var fun = new Fun();
</script>
```

## 五、location对象

> window 对象给我们提供了一个 `location`属性用于获取或设置窗体的URL，并且可以用于解析URL，因为一个属性返回的是一个对象，所以我们将中国属性也称为`location`对象

### 5.1认识URL

> 统一资源定位符是互联网上标准资源的地址，互联网的每个文件都有一个唯一的URL，它包含的学习指出文件的位置以及浏览器应该怎么处理他

URL的一帮语法格式为

**protocol://host[:port]/path/[?query]#fragment**

例如：http://www.baidu.com/index.html?name=zs&age=12#link

| 组成     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| protocol | 通信协议，常用http，ftp,maito等                              |
| host     | 主机(域名) www.baidu.com                                     |
| port     | 端口号 [可选]，省略时使用方案的默认端口 如http的默认端口为 `80` |
| path     | 路径 有 0或多个 '/' 符号隔开的字符串，一般用来表示主机上的一个目录或文件地址 |
| query    | 参数 以键值对的形式，通过`&`符号分隔开来                     |
| fragment | 片段 #后面内容 常见于链接 锚点                               |

### 5.2 location 对象的属性

| location对象属性                 | 返回值                             |
| -------------------------------- | ---------------------------------- |
| location.href **（重点掌握）**   | 获取或设置 整个URL                 |
| location.host                    | 返回主机(域名)                     |
| location.port                    | 返回端口号，如果威胁返回 空字符串  |
| location.pathname                | 返回路径                           |
| location.search **（重点掌握）** | 返回参数                           |
| location.hash                    | 返回片段 #后面内容 常见于链接 锚点 |

**代码演示：**

```html
<body>
    <button>点击</button>
    <div></div>
    <script>
        var btn = document.querySelector('button');
        var div = document.querySelector('div');
        btn.addEventListener('click', function() {
            // console.log(location.href);
            location.href = 'http://www.baidu.com';
        })
        var timer = 5;
        setInterval(function() {
            if (timer == 0) {
                location.href = 'http://www.itcast.cn';
            } else {
                div.innerHTML = '您将在' + timer + '秒钟之后跳转到首页';
                timer--;
            }

        }, 1000);
    </script>
</body>
```

###5.3 location对象的常见方法

| location对象方法   | 返回值                                                       |
| ------------------ | ------------------------------------------------------------ |
| location.assign()  | 跟 href一样，可以跳转页面（也称为重定向页面），会产生历史记录 |
| location.replace() | 替换当前页面，因为不产生历史记录，所以半年后退页面           |
| location.reload()  | 重新加载页面，相当于刷新按钮或者 `f5`,若参数为`true`相当于强制刷新`ctrl+f5` |

**代码演示:**

```html
<body>
    <button>点击</button>
    <script>
        var btn = document.querySelector('button');
        btn.addEventListener('click', function() {
            // 记录浏览历史，所以可以实现后退功能
            location.assign('http://www.itcast.cn');
            // 不记录浏览历史，所以不可以实现后退功能
            location.replace('http://www.itcast.cn');
            location.reload(true);
        })
    </script>
</body>
```

### 5.4 navigator 对象

> navigator 对象包含有关浏览器的信息，它有很多属性，我们最常用的是 userAgent，该属性可以返回由客户机发送服务器的 user-agent 头部的值。

下面前端代码可以判断用户那个终端打开页面，实现跳转

```js
if((navigator.userAgent.match(/(phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i))) {
    window.location.href = "";     //手机
} else {
    window.location.href = "";     //电脑
}
```

### 5.5 history对象

​	window对象给我们提供了一个 history对象，**与浏览器历史记录进行交互**。该对象包含用户（在浏览器窗口中）访问过的URL。

| history对象方法 | 作用                                                         |
| --------------- | ------------------------------------------------------------ |
| back()          | 实现后退功能                                                 |
| forward()       | 实现前进功能                                                 |
| go(参数)        | 实现前进后退功能 参数若是1 前进一个页面 ，若是-1 后退一个页面(几就前/退 几个页面) |

## 六、JS执行机制

JavaScript 语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。这是因为 Javascript 这门脚本语言诞生的使命所致——JavaScript 是为处理页面中用户的交互，以及操作 DOM 而诞生的。比如我们对某个 DOM 元素进行添加和删除操作，不能同时进行。 应该先进行添加，之后再删除

> 单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务。这样所导致的问题是： 如果JS 执行的时间过长，这样就会造成页面的渲染不连贯，导致页面渲染加载阻塞的感觉

以下代码执行的结果是什么？

```js
console.log(1);
setTimeout(function () {
    console.log(3);
}, 1000);
console.log(2);
// 输出 1 2 3 
```

为什么不是`132`输出呢，那是因为我们JS中分同步和异步任务队列

### 6.1 什么是同步和异步

* 同步:

> 前一个任务结束后再执行后一个任务，程序的执行顺序与任务的排列顺序是一致的、同步的。比如做饭的同步做法：我们要烧水煮饭，等水开了（10分钟之后），再去切菜，炒菜。

* 异步:

> 你在做一件事情时，因为这件事情会花费很长时间，在做这件事的同时，你还可以去处理其他事情。比如做饭的异步做法，我们在烧水的同时，利用这10分钟，去切菜，炒菜。 

**他们的本质区别： 这条流水线上各个流程的执行顺序不同**

### 6.2 同步和异步任务

* 同步任务
  * 同步任务都在主线程上执行，形成一个执行栈。

* 异步任务
  * 可以说回调函数都是异步任务，一般而言异步任务有以下三种类型:
    * 普通时间，如 click 、resize 等
    * 资源加载，如 load、error 等
    * 定时器，包括 setInterval、setTimeout 等
    * 异步任务相关回调函数添加到任务队列中 (任务队列也称为消息队列)![同步和异步任务](/medias/imges/js/bom/img2.png)

### 6.2 JS执行过程

1. 先执行**执行栈中的同步任务**
2. 异步任务(回调函数) 放入任务队列中
3. 一旦执行栈中的所有同步任务执行完毕，系统就会按次序读取**任务队列**中的异步任务，于是被读取的异步任务结束等待状态，进入执行栈，开始执行

![JS执行过程](/medias/imges/js/bom/img3.png)

![JS执行过程](/medias/imges/js/bom/img4.png)



> 由于主线程不断的重复获得任务、执行任务、再获取任务、再执行，所以这种机制被称为事件循环（ event loop）

## 七、客户端存储

> 随着互联网的快速发展，基于网页的应用越来越普遍，同时也变的越来越复杂，为了满足各种各样的需求，会经常性在本地存储大量的数据，HTML5规范提出了相关解决方案。

### 7.1本地存储特性

* 数据存储在用户浏览器中
* 设置、读取方便、甚至页面刷新不丢失数据
* 容量较大，sessionStorage 约 5M，localStorage 约 20M
* 只能存储字符串，可以将对象`JSON.stringIfy()`编码后存储【也就序列化】

### 7.2window.sessionStorage

* 生命周期为关闭浏览器窗口
* 存储数据页面私有
* 以键值对的形式存储使用
* 受同源策略影响
* 存储的内容会转为字符串格式
* 有存储大小限制
* **不支持 storage 事件**

| 方式                              | 描述                   |
| --------------------------------- | ---------------------- |
| sessionStorage.setItem(key,value) | 存储数据               |
| sessionStorage.getItem(key)       | 获取数据               |
| sessionStorage.removeItem(key)    | 删除数据               |
| sessionStorage.clear()            | 清空数据(所有都清除掉) |

**代码演示：**

```html
<body>
    <input type="text">
    <button class="set">存储数据</button>
    <button class="get">获取数据</button>
    <button class="remove">删除数据</button>
    <button class="del">清空所有数据</button>
    <script>
        console.log(localStorage.getItem('username'));

        var ipt = document.querySelector('input');
        var set = document.querySelector('.set');
        var get = document.querySelector('.get');
        var remove = document.querySelector('.remove');
        var del = document.querySelector('.del');
        set.addEventListener('click', function() {
            // 当我们点击了之后，就可以把表单里面的值存储起来
            var val = ipt.value;
            sessionStorage.setItem('uname', val);
            sessionStorage.setItem('pwd', val);
        });
        get.addEventListener('click', function() {
            // 当我们点击了之后，就可以把表单里面的值获取过来
            console.log(sessionStorage.getItem('uname'));
        });
        remove.addEventListener('click', function() {
            // 
            sessionStorage.removeItem('uname');
        });
        del.addEventListener('click', function() {
            // 当我们点击了之后，清除所有的
            sessionStorage.clear();
        });
    </script>
</body>

```

### 7.3window.localStorage

* 声明周期永久生效，除非手动删除 否则关闭页面也会存在
* 可以多窗口(页面)共享(同一浏览器可以共享)【 也就是同域下页面共享】
* 以键值对的形式存储使用
* 受同源策略影响
* 存储的内容都会转为字符串格式
* 有存储大小限制
* **支持 storage 事件**

| 方式                            | 描述                   |
| ------------------------------- | ---------------------- |
| localstorage.setItem(key,value) | 存储数据               |
| localstorage.getItem(ley)       | 获取数据               |
| localstorage.removeItem(key)    | 删除数据               |
| localstorage.clear()            | 清空数据(所有都清除掉) |

**代码演示:**

```html
<body>
    <input type="text">
    <button class="set">存储数据</button>
    <button class="get">获取数据</button>
    <button class="remove">删除数据</button>
    <button class="del">清空所有数据</button>
    <script>
        var ipt = document.querySelector('input');
        var set = document.querySelector('.set');
        var get = document.querySelector('.get');
        var remove = document.querySelector('.remove');
        var del = document.querySelector('.del');
        set.addEventListener('click', function() {
            var val = ipt.value;
            localStorage.setItem('username', val);
        })
        get.addEventListener('click', function() {
            console.log(localStorage.getItem('username'));

        })
        remove.addEventListener('click', function() {
            localStorage.removeItem('username');

        })
        del.addEventListener('click', function() {
            localStorage.clear();

        })
    </script>
</body>
```

###7.4 storage 事件 

storage事件能够监听到`localStorage`存储的数据变化

```js
window.addEventListener("storage", function(e) {
  // ....业务逻辑
})
```

在事件对象`e`中我们可以获取到常用的值

* oldValue：更新前的值。如果该键为新增加，则这个属性为null。   
*  newValue：更新后的值。如果该键被删除，则这个属性为null。 
*  url：原始触发storage事件的那个网页的网址路径。  
*  key：发生变化的值

##八、cookie

cookie是http协议下，服务端和客户端都能操作的一种维护用户信息的一种方式

特点：

* 浏览器会在每次请求的时候主动住址私有域下的cookie到请求头 cookie字段中，发给服务器端
* 浏览器会主动存储接收到的 `set-cookie`头信息的值
* 服务器端可以设置`http-only`属性为true（默认就是true）来禁止客户端(js)操作服务器端下发的cookie值
* 可以设置有效期(默认浏览器关闭自动销毁，不同浏览器有所不同)
* 同域下个数有限制，最好不要超过50个（不同浏览器有所不同)
* 单个cookie内容大小有限制，最好不要操作`4000`字节（不同浏览器有所不同）
* 受同源策略影响
* 存储的内容会转为字符串格式

### 8.1 koa中使用cookie

* 存储cookie

```js
ctx.cookies.set(name, value, [options])
// 例如：
ctx.cookies.set("isLogin", loginStatus, {
    maxAge: 1 * 1000 * 3600 * 24 * 7,
    path: '/',
    httpOnly: false
})
```

* 获取cookie的值

```js
ctx.cookies.get(name, [options])
// 例如：
ctx.cookies.get('isLogin')
```

options常用的设置

| 属性值    | 描述                                                         |
| --------- | ------------------------------------------------------------ |
| maxAge    | 一个数字表示从 Date.now() 得到的毫秒数                       |
| expires   | cookie 过期的 `Date`                                         |
| path      | cookie 路径, 默认是`'/'`                                     |
| secure    | 安全 cookie  设置后只能通过https来传递cookie                 |
| httpOnly  | 浏览器是否能够获取到服务器设置的cookie, 默认是 **true**（即只有服务端可以获取到自己设置的cookie，浏览器不能），浏览器想要获取则设置`false`，它是有效阻止暴露用户cookie信息手段 |
| overwrite | 一个布尔值，表示是否覆盖以前设置的同名的 cookie (默认是 **false**). 如果是 true, 在同一个请求中设置相同名称的所有 Cookie |

### 8.2 客户端使用cookie

* 设置

```js
document.cookie="key=value"
```

> key和value是包含在一个字符串中
>
> key包含字段
>
> * [name] 这个name为自己取的cookie名称，同名的值会覆盖
>
> * domain 所属域名
>
> * path 所属路径
>
> * Expires/Max-Age 到期时间/持续时间 (单位是秒)
>
> * http-only 是否只作为http时使用，如果为true，那么客户端能够在http请求和响应中进行传输，但时客户端浏览器不能使用js去读取或修改
>
>多个key=value使用 ; （分号）分隔

* 获取

```js
document.cookie
```

> 返回值是当前域名下的所有cookie，并按照某种格式组织的字符串 ：key=value;key1=value1;......keyn=valuen

由于客户端获取cookie比较麻烦则我们可以封装设置/获取cookie的方法

设置cookie

```js
function setCookie(name, value, options = {}) {
    let cookieData = `${name}=${value};`;
    for (let key in options) {
        let str = `${key}=${options[key]};`;
        cookieData += str;
    }
    document.cookie = cookieData;
}

// 调用
setCookie("key", '123', { "Max-Age": 3600 * 24 })
```

获取cookie

```js
function getCookie(name) {
    let arr = document.cookie.split('; ')
    for (let i = 0; i < arr.length; i++) {
        let arr2 = arr[i].split('=')
        if (arr2[0] == name) {
            return arr2[1]
        }
    }
    return ''
}
// 调用
getCookie('key')
```

