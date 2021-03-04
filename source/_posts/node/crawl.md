---
title: crawl
date: 2021-03-02 16:09:20
top: false
categories: node
summary: crwl
tags:
 - nodeJS
---

### 爬取--基础

#### 一、爬取内容

1.http/axios 等爬取API接口

> 只适合提供了API接口的网站

```js
let axios = require('axios');
axios.get('https://follow-api-ms.juejin.im/v1/getUserFollowInfo?uid=551d6923e4b0cd5b623f54da&src=web')
    .then(res => console.log(res.data))
```

2.superagent/request/crawl爬取HTML页面

> 只适合是服务端渲染的文章

```js
let request = require('request');
request('https://juejin.im/tag/%E5%89%8D%E7%AB%AF', (err, response, body) => {
    let regexp = /class="title" data-v-\w+>(.+?)<\/a>/g;
    let titles = [];
    body.replace(regexp, (matched, title) => {
        titles.push(title);
    });
    console.log(titles);
});
```

3.使用puppeteer控制chromium

* puppeteer 是Chrome 团队开发的一个node库
* 可以通过api来控制浏览器的行为，比如点击，跳转，刷新，在控制台执行js脚本等等
* 通过这个工具可以用来写爬虫，自动签到，网页截图，生成pdf，自动化测试等

> 适合任何网站，尤其是`SPA`网站的抓取

```js
(async () => {
     const browser = await puppeteer.launch();//打开浏览器
     const page = await browser.newPage();//打开一个空白页
     await page.goto('https://www.baidu.com');//在地址栏输入网址并等待加载
     await page.screenshot({ path: 'baidu.png' });//截个图
     await browser.close();//关掉浏览器
})();
```

```js
const puppeteer=require('puppeteer');
const fs=require('fs');
(async function () {
        const browser=await puppeteer.launch({headless:false});
        const page=await browser.newPage();
        await page.goto('https://juejin.im/tag/%E5%89%8D%E7%AB%AF', {
waitUntil: 'networkidle2'
        });
        await page.waitFor(500);
        let comments = await page.$$eval('a.title', els => {
return els.map(item => item.innerText);
        });
        fs.writeFileSync('comments.txt',comments.join('\r\n'),'utf8');
        await browser.close();
})();
```

```js
const puppeteer=require('puppeteer');
(async function () {
    const browser=await puppeteer.launch({headless:false});//启动浏览器
    let page = await browser.newPage();//创建一个 Page 实例
    await page.setJavaScriptEnabled(true);//启用javascript
    await page.goto("https://www.jd.com/");
    const searchInput = await page.$("#key");//获取元素
    await searchInput.focus(); //定位到搜索框
    await page.keyboard.type("手机");//输入手机
    const searchBtn = await page.$(".button");
    await searchBtn.click();
    await page.waitForSelector('.gl-item'); //等待元素加载之后，否则获取不了异步加载的元素
    const links = await page.$$eval('.gl-item > .gl-i-wrap > .p-img > a', links => {
        return links.map(a => {
            return {
                href: a.href.trim(),
                title: a.title
            }
        });
    });
    page.close();
    const aTags = links.splice(0, 1);
    for (var i = 0; i < aTags.length; i++) {
        page=await browser.newPage();
        page.setJavaScriptEnabled(true);
        await page.setViewport({//修改浏览器视窗大小
            width: 1920,
            height: 1080
        });
        var a = aTags[i];
        await page.goto(a.href, {timeout: 0});
        let filename = "items-" + i + ".png";
        await page.screenshot({
            path: filename,
            fullPage: true
        });
        page.close();
    }
    browser.close();
})();
```

#### 二、数据持久化

* 根据爬取的规则和策略，把爬取到的数据存储到数据库中
* 如果要兼容不同的来源，需要对数据格式化
* 为不同的数据建立索引方便检索

#### 三、数据订阅

用户可以按照自己的需求和需要进行订制内容

#### 四、分发

有新的数据的时候

* 可以使用邮件推送到订阅者
* 可以使用极光推送等推送服务
* 可以使用及时通信服务向客户端推送

### 爬取--核心库

#### 一、request

```bash
npm install --save request
```

发送GET请求

```js
var request = require('request');
request('http://www.baidu.com', function (error, response, body) {
  if (!error && response.statusCode == 200) {
    console.log(body);
  }
```

发送POST application/json

```js
const request=require('request');
const options={
    url: 'http://localhost:8080/post',
    method:'POST',
    json: true,
    headers: {
        "Content-Type":"application/json"
    },
    body: {name:"zfpx",age:8}
}
request(options,function (error,response,body) {
    if (!error && response.statusCode == 200) {
        console.log(body);
    } else {
        console.error(error);
    }
});
```

发送POST application/x-www-form-urlencoded 

```js
const request=require('request');
const options={
    url: 'http://localhost:8080/form',
    method:'POST',
    json: true,
    form:{name:'zfpx',age:10}
}
request(options,function (error,response,body) {
    if (!error && response.statusCode == 200) {
        console.log(body);
    } else {
        console.error(error);
    }
});
```

发送POST multipart/form-data

```js
const request=require('request');
const fs=require('fs');
var formData = {
    name: 'zfpx',
    avatar:{
      value:  fs.createReadStream('avatar.jpg'),
      options: {
        filename: 'avatar.jpg',
        contentType: 'image/jpeg'
      }
  }
};
request.post({url:'http://localhost:8080/upload', formData}, function (error, response, body) {  
    if (!error&&response.statusCode==200) {
        console.log(body);
    } else {
        console.log(error);
    }
})
```

#### 二、cheerio

cheerio是一个node的库，可以理解为一个node.js版本的jquery，使用方式和jquery基本相同

```bash
npm install cheerio
```

load装载DOM

```js
let str=`
<h2 class="title">Hello world</h2>
`;
const cheerio=require('cheerio');
const $=cheerio.load(str);
$('h2.title').text('hello there!');
$('h2').addClass('welcome');
console.log($.html());
```

**选择器：**

* 选择器在 Context 范围内搜索，Context又在Root范围内搜索。
* root在右，context在左
* selector 和 context 可以是一个字符串表达式，DOM元素，和DOM元素的数组或者cheerio对象
* root 是通常是HTML 文档字符串

````
$(selectior,[context],[root])
```

```js
let html=`
<ul id="fruits">
  <li class="apple">Apple</li>
  <li class="orange">Orange</li>
  <li class="pear">Pear</li>
</ul>
`;

let cheerio=require('cheerio');
let $=cheerio.load(html);
console.log($('.apple','#fruits').text());
```

**attr修改属性：**

* 获取和修改属性
* 在匹配的元素找只能获得第一元素的属性
* 如果设置一个属性的值为`null`，则移除这个属性
* 你也可以传递一对键值，或者一个函数

```js
attr(name,value)
```

```js
attr(name,value)
console.log($('ul').attr('id'));
$('.apple').attr('id','favorite').attr('class','favorite');
$('.apple').attr({id:'favorite',class:'favorite'});
console.log($('.favorite').html());
console.log($('ul').html());
```

**removeAttr(name)**

通过name删除属性

```js
$('.favorite').removeAttr('id');
```

**prop()修改状态值**

```js
$('input[type="checkbox"]').prop('checked')
//=> false

$('input[type="checkbox"]').prop('checked', true).val()
//=> ok
```

**data()自定义属性**

```js
$('<div data-apple-color="red"></div>').data()
//=> { appleColor: 'red' }

$('<div data-apple-color="red"></div>').data('apple-color')
//=> 'red'

var apple = $('.apple').data('kind', 'mac')
apple.data('kind')
//=> 'mac'
```

**val()赋值**

```js
$('input[type="text"]').val()
//=> input_text

$('input[type="text"]').val('test').html()
//=> <input type="text" value="test"/>
```

**hasClass(className)**

检查匹配到的元素是否有给出的类名

```js
$('.pear').hasClass('pear')//=> true
$('apple').hasClass('fruit')//=> false
$('li').hasClass('pear')//=> true
```

**addClass(name)**

增加class(es)给所有匹配的`elements`，也可以传函数

```js
$('.pear').addClass('fruit').html()//=> <li class="pear fruit">Pear</li>
$('.apple').addClass('fruit red').html()//=> <li class="apple fruit red">Apple</li>
```

**removeClass([className])**

从选择的elements里去除一个或多个有空格分开的class，如果className没有定义，所有的classes将会被去除，也可以传函数

```js
$('.pear').removeClass('pear').html()//=>Pear
$('.apple').addClass('red').removeClass().html()//=>Apple
```

**find(selector)**

获取一个在匹配的元素中由选择器过滤的后代

```js
$('#fruits').find('li').length//=> 3
```

**parent([selector])**

获得通过选择器筛选匹配的元素的parent集合

```js
$('.orange').parents().length// => 2
$('.orange').parents('#fruits').length// => 1
```

**next()获得第一个本元素之后的同级元素**

```js
$('.apple').next().hasClass('orange')//=> true
$('.pear').next().html()//=> null
```

**nextAll()**

获取本元素之后的所有同级元素

```js
$('.apple').nextAll()//=> [<li class="orange">Orange</li>, <li class="pear">Pear</li>]
$('.apple').nextAll().length//=>2
```

**prev()**

获取本元素之前的第一个同级元素

```js
$('.orange').prev().hasClass('apple')//=> true
```

**prevAll()**

获取本元素前的所有同级元素

```js
$('.pear').prevAll()//=> [<li class="orange">Orange</li>, <li class="apple">Apple</li>]
```

**slice(start,[end])**

获取选定范围内的元素数组

```js
$('li').slice(1).eq(0).text()//=> 'Orange'
$('li').slice(1, 2).length//=> 1
```

**siblings(selector)**

获取被选择的同级元素（除去自己）

```js
$('.pear').siblings().length//=> 2
$('.pear').siblings('.orange').length//=> 1
$('.pear').siblings('.pear').length//=> 0
```

**first()**

会选择cheerio对象的一个元素

```js
('#fruits').children().first().text()//=> Apple
```

**last()**

会选择cherrio对象的最后一个元素

```js
$('#fruits').children().last().text()//=> Pear
```

**eq(i)**

**通过索引筛选匹配的元素，使用eq(-i)就从最后一个元素向前数**

```js
$('li').eq(0).text()//=> Apple
$('li').eq(-1).text()//=> Pear
```

**children(selector)**

获取被选择元素的子元素

```js
$('#fruits').children().length//=> 3
$('#fruits').children('.pear').text()//=> Pear
```

**each(function(index,element))**

迭代一个cheerio对象，为每个匹配元素执行一个函数，要提早跳出循环，返回false

```js
var fruits = [];

$('li').each(function(i, elem) {
  fruits[i] = $(this).text();
});

fruits.join(', ');
//=> Apple, Orange, Pear
```

**map(function(index,element))**

迭代一个cheerio对象，为每个匹配元素执行一个函数，Map会返回一个迭代结果的数组

```js
$('li').map(function(i, el) { 
  return $(this).attr('class');
}).join(', ');
//=> apple, orange, pear
```

**filter(selector)**

* 迭代一个cheerio对象，滤出匹配选择器或者是传进去的函数的元素
* 如果使用函数方法，这个函数在被选择的元素中执行，所以this指向的是当前元素

```js
$('li').filter('.orange').attr('class');//=> orange

$('li').filter(function(i, el) { 
    // this === el 
    return $(this).attr('class') === 'orange';
}).attr('class')；


//=> orange
```

**append(content,[content....])**

在每个元素最前插入一个子元素

```js
$('ul').prepend('<li class="plum">Plum</li>')
$.html()
//=>
// <li class="plum">Plum</li>
// <li class="apple">Apple</li>
// <li class="orange">Orange</li>
// <li class="pear">Pear</li>
```

**after(content,[content,....])**

在每个匹配元素之后插入一个元素

```js
$('.apple').after('<li class="plum">Plum</li>')
$.html()
//=>
// <li class="apple">Apple</li>
// <li class="plum">Plum</li>
// <li class="orange">Orange</li>
// <li class="pear">Pear</li>
```

**before(content,[content,....])**

在每个匹配的元素之前插入一个元素

```js
$('.apple').before('<li class="plum">Plum</li>')
$.html()
//=>
// <li class="plum">Plum</li>
// <li class="apple">Apple</li>
// <li class="orange">Orange</li>
// <li class="pear">Pear</li>
```

**remove([selector])**

从DOM中去除匹配的元素和它们的子元素，选择器用来筛选要删除的元素

```js
$('.pear').remove()
$.html()//=>

// <li class="apple">Apple</li>
// <li class="orange">Orange</li>
```

**replaceWith(content)**

替换匹配的元素

```js
var plum = $('<li class="plum">Plum</li>')
$('.pear').replaceWith(plum)
$.html()
//=>
// <li class="apple">Apple</li>
// <li class="orange">Orange</li>
// <li class="plum">Plum</li>//
```

**empty()**

清空一个元素，移除所有的子元素

```js
$('ul').empty()$.html()
```

**html([htmlString])**

获得元素的HTML字符串，如果htmlString有内容的话，将会替代原来的HTML

```js
$('.orange').html()
//=> Orange
$('#fruits').html('<li class="mango">Mango</li>').html()
//=> <li class="mango">Mango</li>
```

**text(textString)**

获得元素的text内容，包括子元素。如果textString被指定的话，每个元素的text内容都会被替换

```js
$('.orange').text()//=> Orange
$('ul').text()//=> Apple// Orange// Pear
```

#### 三、debug

* 在编写程序的时候，有时候需要输出一些调试信息，以便排查问题。
* 但是在程序运行过程中又不需要这些信息，为了方便切换而且不需要改代码，可以使用debug模块

```js
let debug = require('debug')('app:main');
debug('现在的时间是%s',new Date());
```

* Window系统在命令行中执行`SET DEBUG=app:*`，查看`echo %DEBUG%`
* Mac系统在命令行中执行`export DEBUG=app:*`

源码实现：

```js
let chalk = require('chalk')
let colors = ['red', 'green', 'yellow', 'blue', 'cyan'];

function getRandomColor() {
    let index = Math.floor(Math.random() * colors.length)
    return chalk[colors[index]]
}

function debug(name) {
    return function() {
        let start = Date.now();
        let args = Array.from(arguments)
        let DEBUG = process.env.DEBUG
        if (DEBUG.indexOf('*') === -1) {
            if (DEBUG == name) {
                args = [getRandomColor()(name), ...args, getRandomColor()(`+${Date.now() - start}ms`)]
                console.log.apply(console, args)
            }
        } else {
            DEBUG = DEBUG.replace('*', '\W*')
            let reg = new RegExp(DEBUG)
            if (reg.test(name)) {
                args = [getRandomColor()(name), ...args, getRandomColor()(`+${Date.now() - start}ms`)]
                console.log.apply(console, args)
            }
        }
    }
}
module.exports = debug
```



#### 四、cron

[cron](https://www.npmjs.com/package/cron)用来周期性的执行某种任务或等待处理某些事情的一个守护进程

| 符号        | 含义                                                         |
| ----------- | ------------------------------------------------------------ |
| 星号（*）   | 代表所有可能的值                                             |
| 逗号（,）   | 可以用逗号隔开的值指定一个列表范围，例如："1,2,5,7,8,9"      |
| 中杠（-）   | 可以用整数之间的中杠表示一个整数范围，例如：”2-6“表示”2,3,4,5,6“ |
| 正斜线（/） | 可以用正斜线指定时间的间隔频率，*/10，如果用在minute字段，表示每十分钟执行一次 |

| 单位        | 范围 |
| ----------- | ---- |
| Seconds     | 0-59 |
| Minutes     | 0-59 |
| Hours       | 0-23 |
| Day         | 1-31 |
| Months      | 0-11 |
| Day of Week | 0-6  |

```js
var cronJob = require('cron').CronJob;
var job1 = new cronJob("* * * * * *",function(){
  console.log('每秒');
});
job1.start();
```

#### 五、监听未知错误

* 在大部分情况下，异步的IO操作发生的错误无法被`try catch`捕获，如果没有捕获会导致程序退出
* 在Node.js 中，如果一个抛出的异常没有被`try catch`捕获，会尝试将错误交给`uncaughtExeption`事件处理函数来进行处理，仅当没有注册该事件处理函数时才会导致进程直接退出

```js
// 监听 uncaughtException 可以捕获那些没有try catch的异步捕获之后则系统将不会退出
process.on('uncaughtException', function(err) {
    console.log(err.stack)
})

function go() {
    try {
        setTimeout(function() {
            console.log(a);
        }, 1000);
        //throw new Error('Wrong');
    } catch (e) {
        console.log(e);
    }
}
go();
setTimeout(function() {
    console.log('after go');
}, 3000);
```

#### 六、pm2

pm2是一个功能强大的进程管理器，通过`pm2 start`来启动程序，当该进程异常退出时，pm2会自动尝试重启进程

```bash
npm install pm2 -g
npm2 start 
pm2 stop 
```

#### 七、编码（iconv-lite）

```js
var request = require('request');
var iconv=require('iconv-lite');
let cheerio=require('cheerio');
// 将encoding设置为null，node就不会将其编码进行专业
request({url: 'http://top.baidu.com/buzz?b=26&c=1&fr=topcategory_c1'
, encoding: null},function(err,response,body){
    if(err)
        console.error(err);
    // 将gbk转码为utf8
    body = iconv.decode(body, 'gbk').toString();
    let $=cheerio.load(body);
    let movies=[];
    $('.keyword .list-title').each((index,item) => {
        let movie=$(item);
        movies.push({
            name:movie.text()
        });
    });
    console.log(movies);
})
```

#### 八、发送邮件

[nodemailer](https://nodemailer.com/about/)是一个简单易用的Node.js邮件发送模块

```js
const nodemailer = require('nodemailer');
let transporter = nodemailer.createTransport({
    // host: 'smtp.ethereal.email',
    service: 'qq', // 使用了内置传输发送邮件 查看支持列表：https://nodemailer.com/smtp/well-known/
    port: 465, // SMTP 端口
    secureConnection: true, // 使用了 SSL
    auth: {
        user: '1281026802@qq.com',
        // 这里密码不是qq密码，是你设置的smtp授权码
        pass: 'gfndwuvvfpbebjdi',
    }
});

let mailOptions = {
    from: '"1281026802" <1281026802@qq.com>', // sender address <内容>内容要与沙面的auth中user一致
    to: '1281026802@qq.com', // list of receivers
    subject: 'hello', // Subject line
    // 发送text或者html格式
    // text: 'Hello world?', // plain text body
    html: '<h1>Hello world</h1>' // html body
};

// send mail with defined transport object
transporter.sendMail(mailOptions, (error, info) => {
    if (error) {
        return console.log(error);
    }
    console.log('Message sent: %s', info.messageId);
    // Message sent: <04ec7731-cc68-1ef6-303c-61b0f796b78f@qq.com>
});
```

