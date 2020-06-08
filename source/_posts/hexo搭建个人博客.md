---
title: hexo搭建个人博客
date: 2020-04-07 21:04:12
categories: blog
top: true
summary: 这是一篇适合小白搭建自己的`blog`流程
tags: 
 - blog
---



## 小白hexo搭建属于自己的blog

> 这是一篇适合小白搭建自己的`blog`流程
>
> 所用的技术栈：`hexo` + `hexo-theme-matery`主题



### 1.什么是hexo

* 官方定义:Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 [Markdown](http://daringfireball.net/projects/markdown/)（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。 
* 官网(中文)路径:[hexo](https://hexo.io/zh-cn/)

### 2.hexo的使用

在官网文档中有就那个几个

* 第一步：安装（推荐直接全局安装剩去其他麻烦）

  > npm install -g hexo-cli 

* 第二步：安装 Hexo 完成后，请执行下列命令。 **在cmd（命令行）中运行**

  注意:

  + folder 是指的你要初始项目到那个文件夹下面

  ```bash
  $ hexo init <folder> 
  $ cd <folder>
  $ npm install
  ```

* 第三步: 打开初始化好的项目(观察项目目录)

  ![项目目录](/medias/imges/blog/1.jpg)

* 第四步：在hexo官网下载好自己想要的主题并把下载好的主题放到网站的根目录的**themes**(也就是上图themes)下

  > 小编下载的是`hexo-theme-matery`这个主题，小编也是根据该主题搭建博客为例子讲述搭建过程

  ![主题的文件目录介绍](/medias/imges/blog/2.jpg )

* 第五步：在根目录下的`_config.yml`文件下配置好`theme`节点

  ```yaml
  theme: <这里填写根目录下的theme文件夹下你下载下来的主题文件名>
  ```

  **注意**

  到这里你可能有点懵--->（主题下的_config.yml和根目录.__config.yml的文件）小编在这介绍一下

  * 根目录下的`._config.yml`可以说是配置网站的公共部分的文件

    > 例如：博客的名字，标题，网站的语言(中文/英文），域名，推送的仓库地，主题没有的功能配置（比如说主题下没有添加动漫人物的功能，那么我们就要从`npm` 上下载动漫人物的包 ，在把需要的配置参数在根目录下的`._config.yml`文件下配置）等

  * 主题下的`._config.yml`可以说是配置主题上有的功能文件

    > 例如：本博客的主题是有配置的文章文字数统计功能节点，如果你想要这个功能直接去主题下这个配置文件开启就行了

* 第六步：运行博客

  ```bash
  hexo clean  ----> 清除hexo之前生成的静态文件
  hexo g  --------> 根据现在的配置创建静态文件
  hexo s ---------> 创建一个服务器去运行你的静态文件(运行了这步在`cmd`中会出现一个地址，这个地址一般位`https://localhost:4000`，直接将他复制到浏览器的url地址栏上运行就ok啦)
  ```

  当浏览器出现了这个界面 ---> 恭喜你上面的配置成功 😊 辛苦了 阿里嘎多

![博客最基本步骤配置成功](/medias/imges/blog/3.jpg )



### 3.配置文件的解析

> 到此博客的脚本样子ok了，剩下的就是配置功能啦 \(@^0^@)/
>
> 如何配置功能呢？正如上面所说，那我们要去找`._config`文件了
>
> 下面都是以小编的博客配置来给大家讲解😄

* 我们先来配置博客的基本配置吧[在博客根目录下`._config.yml`文件下配置]

  ```y aml
  # Site
  title: DEV_LIU_Blog  			[`博客的名字`]
  subtitle: ''				    [`博客的副标题`]
  description: '开发小白成长博客'    [`博客的描述`]
  keywords: 					    [`为了方便浏览器的seo，此时小编还不懂seo所以没写配置`]
  author: DEV_LIU 				[`博客的作者`]
  language: zh-CN 				[`博客的语言包`]
  timezone: '' 					[`博客的建站时间`] 
  
  # URL
  url: xxxxx  [上线后的博客域名地址，后面部署会详细说明这个配置]
  root: xxxx [上线后的博客从那资源访问，后面部署会详细说明这个配置]
  
  # Extensions
  theme: hexo-theme-matery [选择啥主题]
  
  # Deployment [推送代码仓库配置，后面部署篇会讲]
  deploy: 
    type:  xxxx
    repo:  xxxx
    branch: xxxx
  ```

  > 根目录上的配置文件我们只要明白以上三个节点就ok啦😀 其他的节点配置默认就好

* 在者就是主题文件下的`.config.yml` 文件的解析了

  > 小编认为所有主题的配置文件的配置方法都是差不多的，不外乎就是更改一下主题的图片，友情链接的地址等，也没说明好讲的，要的功能你就在改功能的节点下的`enable:true`，不要的功能就`enable:false`  ,身为程序员都应该看得懂这个简单的配置文件消息，不明白在下面评论或者加我微信都可，冲鸭！！✈

### 4.博客功能配置

兄弟萌！！！我们先来配置主题给我们提供的功能啊。冲冲冲【小编只列举这几种配置噢！！！】更多配置看主题官网

github:[文档](<https://github.com/blinkfox/hexo-theme-matery> 

#### 4.1搜索

本主题中还使用到了 [hexo-generator-search](https://github.com/wzpan/hexo-generator-search) 的 Hexo 插件来做内容搜索，安装命令如下：

```bash
npm install hexo-generator-search --save
```

在 Hexo 根目录下的 `_config.yml` 文件中，新增以下的配置项：

```yaml
search:
  path: search.xml
  field: post

```

#### 4.2 文章字数统计插件（建议安装）

如果你想要在文章中显示文章字数、阅读时长信息，可以安装 [hexo-wordcount](https://github.com/willin/hexo-wordcount)插件。

安装命令如下：

```bash
npm i --save hexo-wordcount
```

然后只需在本主题下的 `_config.yml` 文件中，将各个文章字数相关的配置激活即可：

```yaml
postInfo:
  date: true
  update: false
  wordCount: false # 设置文章字数统计为 true.
  totalCount: false # 设置站点文章总字数统计为 true.
  min2read: false # 阅读时长.
  readCount: false # 阅读次数.
```
####4.3添加emoji表情支持（可选的）

本主题新增了对`emoji`表情的支持，使用到了 [hexo-filter-github-emojis](https://npm.taobao.org/package/hexo-filter-github-emojis) 的 Hexo 插件来支持 `emoji`表情的生成，把对应的`markdown emoji`语法（`::`,例如：`:smile:`）转变成会跳跃的`emoji`表情，安装命令如下：

```bash
npm install hexo-filter-github-emojis --save
```

在 Hexo 根目录下的 `_config.yml` 文件中，新增以下的配置项：

```yaml
githubEmojis:
  enable: true
  className: github-emoji
  inject: true
  styles:
  customEmojis:
```

#### 4.4添加 RSS 订阅支持（可选的）

本主题中还使用到了 [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed) 的 Hexo 插件来做 `RSS`，安装命令如下：

```bash
npm install hexo-generator-feed --save
```

在 Hexo 根目录下的 `_config.yml` 文件中，新增以下的配置项：

```yaml
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
  content_limit: 140
  content_limit_delim: ' '
  order_by: -date
```

执行 `hexo clean && hexo g` 重新生成博客文件，然后在 `public` 文件夹中即可看到 `atom.xml` 文件，说明你已经安装成功了。

#### 4.5修改页脚

页脚信息可能需要做定制化修改，而且它不便于做成配置信息，所以可能需要你自己去再修改和加工。修改的地方在题文件的 `/layout/_partial/footer.ejs` 文件中，包括站点、使用的主题、访问量等。

#### 4.6修改社交链接

在主题的 `_config.yml` 文件中，默认支持 `QQ`、`GitHub` 和邮箱等的配置，你可以在主题文件的 `/layout/_partial/social-link.ejs` 文件中，新增、修改你需要的社交链接地址，增加链接可参考如下代码：

```html
<% if (theme.socialLink.github) { %>
    <a href="<%= theme.socialLink.github %>" class="tooltipped" target="_blank" data-tooltip="访问我的GitHub" data-position="top" data-delay="50">
        <i class="fab fa-github"></i>
    </a>
<% } %>
```

其中，社交图标（如：`fa-github`）你可以在 [Font Awesome](https://fontawesome.com/icons) 中搜索找到。以下是常用社交图标的标识，供你参考：

- Facebook: `fab fa-facebook`
- Twitter: `fab fa-twitter`
- Google-plus: `fab fa-google-plus`
- Linkedin: `fab fa-linkedin`
- Tumblr: `fab fa-tumblr`
- Medium: `fab fa-medium`
- Slack: `fab fa-slack`
- Sina Weibo: `fab fa-weibo`
- Wechat: `fab fa-weixin`
- QQ: `fab fa-qq`
- Zhihu: `fab fa-zhihu`

> **注意**: 本主题中使用的 `Font Awesome` 版本为 `5.11.0`。

#### 4.7修改打赏的二维码图片

在主题文件的 `source/medias/reward` 文件中，你可以替换成你的的微信和支付宝的打赏二维码图片。

### 5.文章 Front-matter 介绍

#### 5.1Front-matter 选项详解

`Front-matter` 选项中的所有内容均为**非必填**的。但我仍然建议至少填写 `title` 和 `date` 的值。

| 配置选项      | 默认值                         | 描述                                                         |
| ------------- | ------------------------------ | ------------------------------------------------------------ |
| title         | `Markdown` 的文件标题          | 文章标题，强烈建议填写此选项                                 |
| date          | 文件创建时的日期时间           | 发布时间，强烈建议填写此选项，且最好保证全局唯一             |
| author        | 根 `_config.yml` 中的 `author` | 文章作者                                                     |
| img           | `featureImages` 中的某个值     | 文章特征图，推荐使用图床(腾讯云、七牛云、又拍云等)来做图片的路径.如: `http://xxx.com/xxx.jpg` |
| top           | `true`                         | 推荐文章（文章是否置顶），如果 `top` 值为 `true`，则会作为首页推荐文章 |
| cover         | `false`                        | `v1.0.2`版本新增，表示该文章是否需要加入到首页轮播封面中     |
| coverImg      | 无                             | `v1.0.2`版本新增，表示该文章在首页轮播封面需要显示的图片路径，如果没有，则默认使用文章的特色图片 |
| password      | 无                             | 文章阅读密码，如果要对文章设置阅读验证密码的话，就可以设置 `password` 的值，该值必须是用 `SHA256` 加密后的密码，防止被他人识破。前提是在主题的 `config.yml` 中激活了 `verifyPassword` 选项 |
| toc           | `true`                         | 是否开启 TOC，可以针对某篇文章单独关闭 TOC 的功能。前提是在主题的 `config.yml` 中激活了 `toc` 选项 |
| mathjax       | `false`                        | 是否开启数学公式支持 ，本文章是否开启 `mathjax`，且需要在主题的 `_config.yml` 文件中也需要开启才行 |
| summary       | 无                             | 文章摘要，自定义的文章摘要内容，如果这个属性有值，文章卡片摘要就显示这段文字，否则程序会自动截取文章的部分内容作为摘要 |
| categories    | 无                             | 文章分类，本主题的分类表示宏观上大的分类，只建议一篇文章一个分类 |
| tags          | 无                             | 文章标签，一篇文章可以多个标签                               |
| keywords      | 文章标题                       | 文章关键字，SEO 时需要                                       |
| reprintPolicy | cc_by                          | 文章转载规则， 可以是 cc_by, cc_by_nd, cc_by_sa, cc_by_nc, cc_by_nc_nd, cc_by_nc_sa, cc0, noreprint 或 pay 中的一个 |

> **注意**:
> 1. 如果 `img` 属性不填写的话，文章特色图会根据文章标题的 `hashcode` 的值取余，然后选取主题中对应的特色图片，从而达到让所有文章都的特色图**各有特色**。
> 2. `date` 的值尽量保证每篇文章是唯一的，因为本主题中 `Gitalk` 和 `Gitment` 识别 `id` 是通过 `date` 的值来作为唯一标识的。
> 3. 如果要对文章设置阅读验证密码的功能，不仅要在 Front-matter 中设置采用了 SHA256 加密的 password 的值，还需要在主题的 `_config.yml` 中激活了配置。有些在线的 SHA256 加密的地址，可供你使用：[开源中国在线工具](http://tool.oschina.net/encrypt?type=2)、[chahuo](http://encode.chahuo.com/)、[站长工具](http://tool.chinaz.com/tools/hash.aspx)。
> 4. 您可以在文章md文件的 front-matter 中指定 reprintPolicy 来给单个文章配置转载规则

以下为文章的 `Front-matter` 示例。

#### 5.2最简示例

```yaml
---
title: typora-vue-theme主题介绍
date: 2018-09-07 09:25:00
---
```

#### 5.3最全示例

```yaml
---
title: typora-vue-theme主题介绍
date: 2018-09-07 09:25:00
author: 赵奇
img: /source/images/xxx.jpg
top: true
cover: true
coverImg: /images/1.jpg
password: 8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: false
mathjax: false
summary: 这是你自定义的文章摘要内容，如果这个属性有值，文章卡片摘要就显示这段文字，否则程序会自动截取文章的部分内容作为摘要
categories: Markdown
tags:
  - Typora
  - Markdown
---
```
>注意：Front-matter这个知识很重要，你可能还不知道font-matter是干嘛用的，小编在这讲一下
>
>所谓的`front-matter`是指你文章类型参数的配置，它位于你文章最顶部（下面会在如何来写一篇文章讲到这个配置）。

不要小看它，它的作用大:

* 定义文章类型
* 定义文章标签
* 定义是否为推荐文章
* 等等（看上面的表格介绍）

### 6.如何来写一篇文章

> 经过上面枯燥的配置是不是跃跃欲试想写一篇文章来看看呢！！！😘
>
> 它来啦！！！😁

#### 6.1新建文章

```bash
hexo new  '文件名字'
```

运行完，新建的文章就会在根目录下的`source/_posts`文件夹下【这里可是全部文章的位置哦！！！】

打开新建好的文章名字.md,会发现已经有默认的信息了 例如: 

```yaml
---
title: typora-vue-theme主题介绍
date: 2018-09-07 09:25:00
---
```
这就是`Front-matter`的作用（选项参数配置你文章属性），例如：你要让你这篇文章为推荐文章，就要在上写上`top: true`

```yaml
---
title: typora-vue-theme主题介绍
date: 2018-09-07 09:25:00
top: true
---
```

#### 6.2新建其他页面

```bash
hexo new page '页面名字'
```

新建完成之后，新建的页面会出现到`source/`下，这里说的是新建页面，不是文章，这要区分出来，小编当时采坑了，文章可不能在这种创建出来的文章夹下书写哦，记住文章的`.md`文件只能放到`source/_posts`文件夹下，起来文件下不能放！！！这个很重要，敲黑板😫

那么这些新建的页面是干嘛用的呢?

>比如说你新建了分类界面 

```bash
hexo new page Categories
```

它在`根目录/source/categories`出现`index.md`文件，那么这个index.md文件你就要配置这几个参数（type必填）

```markdown
---
title: categories
date: 2020-04-04 12:49:56
type: "categories"  [type声明类型]
layout: "categories" [布局]
---
```

那么你点你博客的分类页面的话它就会跳转到这个`md`文件生成的静态`html`

### 7.一键部署

> 小编的博客是部署到gitee（码云），因为它在国内上运行访问速度比较快

* 建库

* 库名

> 建议库命是你的`用户名`，方便直接部署你的网站根目录资源配置，少出现一些坑，在这小编可是入了几次坑，已经没有勇气再跳进去了，推荐想小编这样固定配置，下面会出现两个root都有作用
>
> 例如我的库名： https://gitee.com/mrs_liu/mrs_liu 

* 将你的博客文件直接推送到这个库中去

* 去你的根目录的`._config.yml`文件下配置博客的`url`和`root`，想小编这样就好了

  ```yaml
  # URL 配置博客网址
   url: https://gitee.com/mrs_liu/mrs_liu 【库地址】
   root:  /mrs_liu/ 【库名，记住  /库名/  一定要这种格式不然出现样式表会访问不到的现象 】--->推到gitee上去去掉注释
   # root: /  ----> 本地开发去掉注释
   
  # Deployment 推送库配置
  deploy:
    type: 'git'
    repo: https://gitee.com/mrs_liu/mrs_liu.git 【库的地方，建议直接将库克隆地址复制过来】
    branch: master 【推送的分支】
  ```

  > 请特别注意上面两个`root`节点在什么时候使用，它会影响到你的博客样式表/资源的获取

* 之后就是常规的几步

  ```bash
  hexo clean ----> 清除之前创建的静态文件
  hexo g --------> 根据配置生成静态文件
  hexo d --------> 推送到gitee上去
  ```

  

* 部署到你的gitee上去

  在仓库上找到服务一栏点

  ![找到Gitee Page](/medias/imges/blog/4.jpg )

![部署](/medias/imges/blog/5.jpg )

* 点击更新 

> 到这你的博客就ok啦，快去写你的文章吧 ！！！不懂可以留言，也可私信