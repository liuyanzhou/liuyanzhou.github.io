---
title: node爬虫
date: 2020-05-15 11:17:18
categories: node
img:  /medias/imges/node/node-title.jfif
top: true
summary: nodeJS爬虫篇
tags:
 - nodeJS
---

### node爬虫篇

#### 一、什么是爬虫

可以把互联网比做成一张“大网”，爬虫就是在这张大网上不断爬取信息的程序

所以一句话总结：**爬虫是请求网站并提取数据的自动化程序**

爬虫的基本工作流程如下：

1. 向指定的URL发送http请求
2. 获取响应（HTML、XML、JSON、二进制等数据）
3. 处理数据（解析DOM、解析JSON等）
4. 将处理好的数据进行存储

![爬虫工作流程](/medias/imges/node/spider/sprider.jpg)

#### 二、爬虫的意义

爬虫就是一个探测程序，它的基本功能就是模拟人的行为去各个网站转悠，点点按钮，找找数据，或者把看到的信息背回来。就像一只虫子在一幢楼里不知疲倦地爬来爬去。

你可以简单地想象：**每个爬虫都是你的“分身”。就像孙悟空拔了一撮汗毛，吹出一堆猴子一样。**

你每天使用的百度和Google，其实就是利用了这种爬虫技术：每天放出无数爬虫到各个网站，把他们的信息抓回来，存到数据库中等你来检索。

#### 三、爬虫基础

##### 案例:下载传值博客老师图片

> 地址`http://web.itheima.com/teacher.html`

##### 3.1发送请求

```js
// 引入http模块
const http = require('http')
// 创建请求对象
let req = http.request('http://web.itheima.com/teacher.html', res => {
  // 准备chunks
  let chunks = []
  res.on('data', chunk => {
    // 监听到数据就存储
    chunks.push(chunk)
  })
  res.on('end', () => {
    // 结束数据监听时讲所有内容拼接
    // Buffer.concat(chunks).toString('utf-8') 是将获取到的二进制数据转化成我们识别的字符串
    console.log(Buffer.concat(chunks).toString('utf-8'))
  })
})
// 发送请求
req.end()
```

> 此时输出的是`html`文件的字符串

##### 3.2引用`cheerio`库来解析上一步的字符串为HTML，方便我们操作DOM

> `cheerio`的说明请去npm的官网查看

官方dom

```js
const cheerio = require('cheerio')
const $ = cheerio.load('<h2 class="title">Hello world</h2>')
 
$('h2.title').text('Hello there!')
$('h2').addClass('welcome')
 
$.html()
//=> <html><head></head><body><h2 class="title welcome">Hello there!</h2></body></html>
```

* 安装

  ```bash
  npm i cheerio
  ```

* 使用

  ```js
  // 引入http模块
  const http = require('http')
  const cheerio = require('cheerio')
  const download = require('download');
  
  // 创建请求对象 (此时未发送http请求)
  const HOST = 'http://web.itheima.com/'
  const req = http.request(HOST + 'teacher.html', res => {
    let chucks = []
    res.on('data', c => chucks.push(c))
  
    res.on('end', () => {
      let htmlStr = Buffer.concat(chucks).toString('utf-8')
      let $ = cheerio.load(htmlStr)
      // 获取老师的照片url路径并解析为base64编码的字符串存储为一个数组并返回
      let imgs = Array.prototype.map.call( $('.tea_main .tea_con .li_img > img'),item => HOST+encodeURI($(item).attr('src')))
  	// 输出老师照片的url路径
      console.log(imgs)
    })
  
  })
  
  // 发送请求
  req.end()
  ```

  > 此时获取到的是老师照片的url的数组

##### 3.3使用`download`库批量下载图片

> download介绍请求npm官网查看

官方dom：

```js
const fs = require('fs');
const download = require('download');
 
(async () => {
    await download('http://unicorn.com/foo.jpg', 'dist');
 
    fs.writeFileSync('dist/foo.jpg', await download('http://unicorn.com/foo.jpg'));
 
    download('unicorn.com/foo.jpg').pipe(fs.createWriteStream('dist/foo.jpg'));
 
    await Promise.all([
        'unicorn.com/foo.jpg',
        'cats.com/dancing.gif'
    ].map(url => download(url, 'dist')));
})();
```

* 安装

  ```bash
  npm install download
  ```

* 使用

  ```js
  // 引入http模块
  const http = require('http')
  const cheerio = require('cheerio')
  const download = require('download');
  
  // 创建请求对象 (此时未发送http请求)
  const HOST = 'http://web.itheima.com/'
  const req = http.request(HOST + 'teacher.html', res => {
    let chucks = []
    res.on('data', c => chucks.push(c))
  
    res.on('end', () => {
      let htmlStr = Buffer.concat(chucks).toString('utf-8')
      let $ = cheerio.load(htmlStr)
  	// 获取老师的照片url路径并解析为base64编码的字符串存储为一个数组并返回
      let imgs = Array.prototype.map.call( $('.tea_main .tea_con .li_img > img'),item => HOST+encodeURI($(item).attr('src')))
      // 输出老师照片的url路径
      // console.log(imges)
      
      // 下载图片
      // 将imgs老师的照片url数组传入进去，下载好的图片会放在目录下的`dist`文件夹下
      Promise.all(imgs.map(url => download(url, 'dist'))).then(()=>{
         // 下载成功的回调
        console.log('files downloaded!')
      });
    })
  })
   
  // 发送请求
  req.end()
  ```

  > 下载好的图片会放在目录下的`dist`文件夹下

* 代码运行成功

  ![爬虫小dom运行成功](/medias/imges/node/spider/node_spider_photo.jpg)

  > 此案例方法适合服务端渲染页面

#### 四、爬虫高级

> 使用`selenium`框架进行爬虫

##### 4.1、Selenium介绍

Selenium是一个Web应用的自动化测试框架，可以创建回归测试来检验软件功能和用户需求，通过框架可以编写代码来启动浏览器进行自动化测试，换言之，用于做爬虫就可以使用代码启动浏览器，让真正的浏览器去打开网页，然后去网页获取想要的信息，从而实现真正意义上无惧**反爬虫技术**

##### 4,2、Selenium的基本使用

1.根据平台下载需要的**webdirver**

> webdirver是帮助**Selenium**打开浏览器的程序,请下载好你的浏览器对应的版本，并把`.exe`文件放到根目录上

2.项目中安装**selenium-webdirver**包

> npm i selenium-webdirver

官方dom：

```js
const {Builder, By, Key, until} = require('selenium-webdriver');
 
(async function example() {
  let driver = await new Builder().forBrowser('chrome').build();
  try {
    // 自动打开百度
    await driver.get('https://www.baidu.com/');
    // 找到元素，向里面发送有一个关键字并按回车
    await driver.findElement(By.id('kw')).sendKeys('黑马程序员', Key.RETURN);
    // 验证是否通过
    await driver.wait(until.titleIs('webdriver - Google Search'), 1000);
  } finally {
    // 退出
     await driver.quit();
  }
})();
```

3.**官方api文档**

> https://www.selenium.dev/selenium/docs/api/javascript/module/selenium-webdriver/

##### 4.3案例：拉勾网前端岗位数据获取并实现翻页数据获取

> 爬虫抓取数据地址:<https://www.lagou.com/> 

数据源：

![拉勾网网页数据源](/medias/imges/node/spider/node_spider_info.jpg)

```js
const { Builder, By, Key, until } = require('selenium-webdriver');
let currentPage = 1;
let maxPage;

let driver = new Builder().forBrowser('chrome').build();


(async function example() {
  // 自动打开百度，并搜索拉勾网
  await driver.get('https://www.lagou.com/');
  // 点击选择城市
  await driver.findElement(By.css('#changeCityBox .checkTips .tab.focus')).click()
  // 找到元素，向里面发送有一个关键字并按回车
  await driver.findElement(By.id('search_input')).sendKeys('前端', Key.RETURN);
  // 获取岗位总共页数
  maxPage = await driver.findElement(By.css('.totalNum')).getText()
  // 获取数据
  getData()

})();

  // 获取到岗位信息
async function getData() {
  console.log(`-----------正在获取第${currentPage}页数据,总共${maxPage}页------------`)
  while (true) {
    // notError说明该js文本框下面
    let notError = true
    try {
        // 获取到岗位列表的所有li盒子
      let infoList = await driver.findElements(By.css('#s_position_list .item_con_list .con_list_item'))
      // 存放数据数组
      let arr = []
      for (var i = 0; i < infoList.length; i++) {
        let item = infoList[i]
        let title = await item.findElement(By.css('.position_link >  H3')).getText()
        let address = await item.findElement(By.css('.position .p_top .add > em')).getText()
        let timer = await item.findElement(By.css('.position .p_top .format-time')).getText()
        let company = await item.findElement(By.css('.company .company_name > a')).getText()
        let companyLink = await item.findElement(By.css('.company .company_name > a')).getAttribute('href')
        let moneny = await item.findElement(By.css('.position .p_bot .money')).getText()
        let background = await item.findElement(By.css('.position .p_bot .li_b_l')).getText()
        background.replace(moneny, '')
        let industry = await item.findElement(By.css('.company .industry')).getText()
        let tges = await item.findElement(By.css('.list_item_bot .li_b_l')).getText()
        let welfare = await item.findElement(By.css('.list_item_bot .li_b_r')).getText()

        let obj = {
          item,
          title,
          address,
          timer,
          company,
          companyLink,
          moneny,
          background,
          industry,
          tges,
          welfare
        }
        arr.push(obj)
      }
      currentPage++;
      if (currentPage <= maxPage) {
        // 点击下一页
        await driver.findElement(By.css('.pager_next')).click()
        // 递归重新获取数据
        getData()
      }

    } catch (e) {
	// 设置notError
      notError = false
    } finally {
     // 判断是否终止循环
      if(notError) break
    }
  }
}
```

> 说明：
>
> **notError**是为了解决当爬虫点击下一页的时候，网页加载时还未插入文档时爬虫去获取数据报错，而且我们上的**while**循环这个保险也是为了解决这个问题，每当爬取数据的时候就先将`notError`设置为`true`，
>
> 如果在获取该页数据时报错会在`catch`中将`notError`设置为`false`,之后会通过`finally`判断中继续循环爬取该页数据。
>
> 如果获取该页数据正常，`notError`一直都是`true`，那么就会在`finally`判断中跳出该循环机制，并会通过递归在获取下一页数据。😁

成功的截图：

![数据获取成功](/medias/imges/node/spider/node_spider_success.jpg)



> 这一篇是小编刚接触用node写爬虫之后总结出来的，可能很多地方没说清楚，望指出，但亲测这样写爬虫是完全可以爬取数据的！！！😄