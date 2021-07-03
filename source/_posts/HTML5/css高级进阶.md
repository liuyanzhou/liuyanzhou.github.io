---
title: css高级进阶
date: 2021-05-03 17:15:39
categories: HTML5
top: false
summary:  css高级进阶
tags: 
 - CSS
---

### HTML

超文本标记语言（英语：HyperText Markup Language，简称:HTML）是一种用于创建网页标准标记语言

常用的html标签汇总

**HTML4**

* 基本文档：html、head、body...
* 基本标签：h1-h6、p、br、hr
* 文本格式化：strong、b、em、i、small、del、sub、sup....
* 链接：a、link
* 图片：img、map、area
* 区块：div、span
* 三大列表：ul li、ol li、dl、dt、dd
* 表格：table、caption、th、tr、td、thead、tbody、tfoot
* 框架：iframe
* 表单：form、input、select option、textarea、button、label
* 实体：&lt、&gt、&copy、&nbsp....

**HTML5**

* 图形新元素：canvas
* 新多媒体元素：audio、video、source、embed、track
* 新表单元素：deatalist、keygen、output
* 新的语义和结构元素：article、aside、bdi、command、details、dialog、summary、figure、figcaption、footer、header、mark、meter、nav、progress、ruby、rt、rp、section、time、wbr

**语义化含义和好处**

含义：指对文本内容的结构化（内容语义化），选择合乎语义的标签（代码语义化），便于开发者阅读，维护和写出更优雅的代码的同时，让浏览器的爬虫和辅助技术更好的解析（通俗理解：**用正确的标签做正确的事**）

通过使用恰当语义化的HTML标签，让页面具有良好的结构与含义，可以有效提高：

* 可访问性：帮助辅助技术更好的阅读和转译你的网页，利于无障碍阅读
* 可检索性：有了良好的结构和语义，可以提高搜索引擎的高效爬取，提高网站流量
* 国际化：全球只有13%的人口是英语母语使用者，因此通用的语义化标签可以让各国开发者更容易弄懂你网页的结构；
* 互用性：减少网页间的差异性，帮助其他开发者了解你网页的结构，方便后期开发和维护；

好处：

* 利于SEO优化（也就是搜索引擎的抓取，搜索引擎的爬虫也依赖于标记来确定上下文和各个关键词的权重）
* 在样式丢失的时候，还是可以比较好的呈现结构
* 更好的支持各种终端，例如无障碍阅读和有声小说等
* 利于团队开发和维护，W3C给我们定了一个标准，那么团队中都遵循这个标准，代码的差异就会缩小，在开发和维护的时候可以提高效率

#### **如何写出更优的HTML**

##### 从结构搭建上

* 采用HTML5标准时，开头应该加上`<!DOCTYPE html>`
* 应在`head`标签中引入CSS文件，这样浏览器就可以在输出HTML之前获取CSS信息
* 在`body`标签的末尾引入`js`文件，这样可以在页面显示之后在编译`js`文件，以加快页面读取速度，同时有助于`js`对页面中的元素进行操作
* 对元素的操作应添加在js代码中，而不是在html中添加，后期难以维护

##### 从代码校验

虽然现在浏览器的容错力越来越高，但这不能成为代码粗制滥造的借口。不管什么时候，正确的HTML代码都更易于debug、体积更小，运行更快、渲染时消耗资源更少，而错误的HTML代码只会使得页面的最终设计难以实现想要的效果。特别是在使用模板来开发时，正确有效的HTML就更显得尤为重要 ，也许一个正常运行的模板会在其他环境中出现可怕的异常。

* 在项目中加入校验的过程：可以在代码编辑器中使用`HTMLHint`、`SublimeLinter`这类代码校验插件，也可以在`build`的时候使用`HTMLHint with Grunt`这类校验工具，还可以通过`W3C HTML validator`等网站来在线检测代码
* 尽量采用HTML5标准
* 确保正确的HTML层级：嵌套元素时确保元素首尾完整，在一个有大量内容的元素的结束标签后应添加注释，这样有助于后期debug，特别是在使用模板的时候
* 在所有不能自动结束的元素末尾添加结束标签
* `li`结束标签不收必须的，所以有些人认为可以不写`li`，这可以接受，但`ul`和`ol`标签一定要加
* video和autdio元素必须要有结束标签

##### 从良好的代码排版

* 在项目中保持统一的HTML代码风格
* 使用代码编辑器来帮助你自动排版，比如在`Sublime Text`中可以使用自带的`Reindent`，也可以找一些自动排版的插件来帮助你。同样也可以使用一些在线工具比如：`CSS Beautifier`和`JS Beautifier`
* 在HTML中用缩进来分层更易于阅读，如果代码编辑器没有自动缩进功能的话可以自行修改相应的设置。当你发现你的HTML层级过深的时候，那就表示你的需要重构一下你的HTML了
* 用更直接易读的方式写HTML代码，注意标签的相互嵌套关系
* 元素要按常规放置，比如`footer`元素最好是放在`HTML`页面的底部，虽然理论上把它放在任何地方都可以正常运行
* 统一所有引号的使用规则，不要这里用双引号，那里用单引号
* 使用小写字母来写标签，大写字母很不易阅读

##### 从语义化设计

* 标题使用`h1(h2、h3、....)`，列表使用`ul`或`ol`
* 在适当的地方使用HTML5的新元素，比如`header、footer、nav、aside`
* 正文中的文本内容要用`p`标签，内容的结构化可以使用`HTML5`的新元素（或者div）
* 修改文字样式时，em和strong要比`i`和`b`更好些，因为前者语义化更加明显
* form中要包含`label`元素，`input`要有`type、placeholder`以及其它必要的属性，即使值为空都可以
* 尽量减少使用无意义的标签，例如`span和div`
* 尽量不使用标签本身的css属性，例如：b、font、s等标签，如果需要这些样式，那么是用css样式来进行添加
* 在需要强调的部分，使用`strong、em`但是样式尽量使用css样式来描述
* 使用表格时，标题用`caption`，表头用`thead`，主体部分用`tbody`包围，尾部`tfoot`包围。表头和一般单元格要区分开，表头用`th`，单元格用`td`
* 列表搭建时，使用`<ul>,<ol>,<dl>`定义
* 表单域要用`fieldset`标签包裹起来，并用`legend`标签说明用途（可理解为表单标题）
* 每个`input`标签对应的说明文本都需要使用`label`标签，并且通过为`input`设置`id`属性，在`label`标签中设置`for=id`来让说明文本和相对应的`input`关联起来，或者直接在`label`中内嵌控件。

##### 从布局规范

* p元素用来放文字，而不是用来布局。在浏览器自身的样式中默认p有`margin`和其他样式
* 想实现换行可以使用`block`元素或者`css的display`属性，尽量避免使用`br`来换行。文字内容的换行可以用`br`，但是通常也很少这样用
* 不要滥用`div`，w3c对div描述是这样的：当没有其他元素可用时才能使用div，如果想让`link`和`img`这类元素能够在结尾换行，可以在样式中添加`display:block`，这样要比把他们放进div或者使用br来换行要好很多
* 必须知道哪些是块级元素，这样就可以避免把块级元素放到div里面，比如列表就不需要放入div里面
* table是用来放表格数据的，不适合用来布局
* Flex box现在越来越流行，学一学没坏处
* 盒模型一定要掌握，必须知道什么时候该用`padding`，什么时候用`margin`，top和left
* 使用`margin`的规则：通常情况下，margin都是添加在元素的`bottom`和`right`，有时也可以是`top`或者`left`。无论如何，尽量避免同时在`bottom,top`或者`right,left`添加margin，可以用`last-of-type`选择器来去掉最后一个子元素的`margin`

##### 从标签嵌套规范

* 块元素可以包含内联元素或某些块元素，但内联元素却不能包含块元素，它只能包含其它的内联元素
* 有几个特殊的块级元素只能包含内嵌元素，不能再包含块级元素： h1、h2、h3、h4、h5、h6、p、dt 
* ul,li/ol,li/dl,dt,dd拥有父子级关系的标签；ul、ol下都只能跟li，dl下只能跟dt.dd
* a标签不能嵌套a标签

#### 网站代码优化

要多为用户考虑，不同的设备条件、使用环境以及输入法等都会给你的HTML带来不同的体验：

* 网页的title、keywords、description一定要写，要精简全面
* 网页使用语义化代码
* a标签要设置`title`属性；外部链接还要设置`rel`属性，`--rel="nofollow"`，nofollow值会使得网页爬虫不顺着链接爬出
* 所有的标题使用`h1`标签，样式可以使用css设置
* br标签只能用于文本内换行
* table一定要使用`caption`设置表格标题
* strong用来设置加粗，em设置斜体
* img标签一定要设置其`alt`属性，增加用户体验

```html
// 表单域
<form>
  <fieldset>
    <legend>Personalia:</legend>    <!-- legend 可理解为表单标题 -->
    Name: <input type="text"><br>
    Email: <input type="text"><br>
    Date of birth: <input type="text">
  </fieldset>
</form>

// label文本
<label for="username">请输入用户名: </label> 
<input type="text" id="username" name="username">

<label>请输入用户名<input type="text" id="username" name="username"></label>
```

#### HTML及扩展

Doctype、Meta> 、Entity> 、DTD、XML、namespace、SVG、MathML> <br />

DOCTYPE文档说明：

* 声明帮助浏览器正确地显示网页
* 定义这个文档的类型，浏览器会先设别这句话，会按照这个类型去解析这个文档
* HTML也有多个不同的版本，只有完全明白页面中使用的确切HTML版本，浏览器才能完全正确地显示出HTML页面
* 一般高版本兼容低版本，所以在工作中我们默认写高版本就可以了（html5文档声明）
* 文档声明不区分大小写
* 文档声明必须放在第一行（因为浏览器从上往下解析的）
* 必须写文档说明，如果不写就会出怪异事件
* 文档声明不是一个标签，它只是一个声明

```html
HTML5
<!DOCTYPE html>

HTML 4.01
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">

XHTML 1.0
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"> 
```

> meta元信息
> meta标签提供关于HTML文档的元数据。元数据不会显示在页面上，但是对于机器是可读的。它可用于浏览器（如何显示内容或重新加载页面），搜索引擎（关键词），或其他 web 服务。

```html
// 申明编码
<meta charset="UTF-8" />

// 页面关键词
<meta name="keywords" content="" />

//页面描述
<meta name="description" content=" " />

// 视口-移动设备
<meta name="viewport" content="width=device-width, initial-scale=1">

// 搜索引擎索引方式
all：文件将被检索，且页面上的链接可以被查询；
none：文件将不被检索，且页面上的链接不可以被查询；
index：文件将被检索；
follow：页面上的链接可以被查询；
noindex：文件将不被检索；
nofollow：页面上的链接不可以被查询。
<meta name="robots" content="index,follow" />

// 定义网页作者
<meta name="author" content="author name" /> 

// 优先使用IE最新版本和 Chrome
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
// 关于X-UA-Compatible
<meta http-equiv="X-UA-Compatible" content="IE=6" ><!-- 使用IE6 -->
<meta http-equiv="X-UA-Compatible" content="IE=7" ><!-- 使用IE7 -->
<meta http-equiv="X-UA-Compatible" content="IE=8" ><!-- 使用IE8 -->
// 页面重定向和刷新
<meta http-equiv="refresh" content="0;url=" />
// 禁止浏览器从本地计算机的缓存中访问页面内容：这样设定，访问者将无法脱机浏览
<meta http-equiv="Pragma" content="no-cache">
// 转码申明:用百度打开网页可能会对其进行转码（比如贴广告），避免转码可添加如下meta。
<meta http-equiv="Cache-Control" content="no-siteapp" />

// 启用 WebApp 全屏模式
<meta name="apple-mobile-web-app-capable" content="yes" />
// 隐藏状态栏/设置状态栏颜色：只有在开启WebApp全屏模式时才生效。
// content的值为default | black | black-translucent 
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
// 添加到主屏后的标题
<meta name="apple-mobile-web-app-title" content="标题">
// 忽略数字自动识别为电话号码
<meta content="telephone=no" name="format-detection" />
// 忽略识别邮箱
<meta content="email=no" name="format-detection" />
// 添加智能 App 广告条 Smart App Banner
<meta name="apple-itunes-app" content="app-id=myAppStoreID, 
  affiliate-data=myAffiliateData, app-argument=myURL">
<!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->
<meta name="HandheldFriendly" content="true">
<!-- 微软的老式浏览器 -->
<meta name="MobileOptimized"  content="320">
<!-- uc强制竖屏 -->
<meta name="screen-orientation" content="portrait">
<!-- QQ强制竖屏 -->
<meta name="x5-orientation" content="portrait">
<!-- UC强制全屏 -->
<meta name="full-screen" content="yes">
<!-- QQ强制全屏 -->
<meta name="x5-fullscreen" content="true">
<!-- UC应用模式 -->
<meta name="browsermode" content="application">
<!-- QQ应用模式 -->
<meta name="x5-page-mode" content="app">
<!-- windows phone 点击无高光 -->
<meta name="msapplication-tap-highlight" content="no">
// 浏览器内核控制 
<meta name="renderer" content="webkit|ie-comp|ie-stand">
```

> HTML Entity
> [https://developer.mozilla.org/en-US/docs/Glossary/Entity](https://developer.mozilla.org/en-US/docs/Glossary/Entity)
> [https://www.cnblogs.com/gopark/p/11342753.html](https://www.cnblogs.com/gopark/p/11342753.html)

* 在编写HTML页面时，需要用到"<"、">"、"空格"等符号，直接输入这些符号时，会错误的把它们与标记混在一起，非常不利于编码。
  那么就需要把这些字符进行转义，以另一种方式抒写，以相同的形式展现。
  在HTML中，这些字符可称为HTML Entity，即HTML字符实体。
  一个HTML Entity都含有2种转义格式：Entity Name 和 Entity Number。

* Entity Name
  格式： &entityName; 
  说明："&"开头，";"结尾，以语义的形式描述字符。如字符"<"，英文名称为"less than"，Entity Name为"&lt;"，取自"less than"2个单词的首字母。

  Entity Number
  格式： &#entityNumber; 
  说明："&#"开头，";"结尾，以编号的形式描述字符。此编号可以为十进制或十六进制(以"&#x"开头)等数字格式。

  作用: 
  显示 HTML 保留字符，如 <、>、&、 " 等（联想到防御 XSS 攻击）；
  表示难以用常规输入设备输入的字符，如 ©、®、± 等；
  表示给定的字符编码可能无法表达文档字符集的其他字符，如ASCII 编码想显示中文，使用`&#x6C34;`表示 “水”;

> SVG矢量图形，https://www.w3school.com.cn/svg/svg_examples.asp
>
> 含义：
>
> - SVG 指可伸缩矢量图形 (Scalable Vector Graphics)
> - SVG 用来定义用于网络的基于矢量的图形
> - SVG 使用 XML 格式定义图形
> - SVG 图像在放大或改变尺寸的情况下其图形质量不会有所损失
> - SVG 是万维网联盟的标准
> - SVG 与诸如 DOM 和 XSL 之类的 W3C 标准是一个整体；
>
> 优势：
> 与其他图像格式相比（比如 JPEG 和 GIF），使用 SVG 的优势在于：
>
> - SVG 图像可通过文本编辑器来创建和修改
> - SVG 图像可被搜索、索引、脚本化或压缩
> - SVG 是可伸缩的
> - SVG 图像可在任何的分辨率下被高质量地打印
> - SVG 可在图像质量不下降的情况下被放大
>
> 浏览器支持:
>
> - Internet Explorer 9+, Firefox, Opera, Chrome, 和 Safari 支持内联SVG。

```html
// 把 SVG 直接嵌入 HTML 页面
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" height="190">
  <polygon points="100,10 40,180 190,60 10,60 160,180"
  style="fill:lime;stroke:purple;stroke-width:5;fill-rule:evenodd;">
</svg>  
  
  
// 包含了 XML 声明。请注意 standalone 属性！该属性规定此 SVG 文件是否是“独立的”，
// 或含有对外部文件的引用。standalone="no" 意味着 SVG 文档会引用一个外部文件 - 在这里，是 DTD 文件
<?xml version="1.0" standalone="no"?>

// 引用了这个外部的 SVG DTD。该 DTD位于 “http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd”。
// 该DTD位于W3C，含有所有允许的 SVG 元素
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" 
"http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

// SVG 代码以 <svg> 元素开始，包括开启标签 <svg> 和关闭标签 </svg> 。这是根元素。
// width 和 height 属性可设置此 SVG 文档的宽度和高度。
// version 属性可定义所使用的 SVG 版本，xmlns 属性可定义 SVG 命名空间
<svg width="100%" height="100%" version="1.1"
xmlns="http://www.w3.org/2000/svg">

// SVG 的 <circle> 用来创建一个圆。cx 和 cy 属性定义圆中心的 x 和 y 坐标。如果忽略这两个属性，
// 那么圆点会被设置为 (0, 0)。r 属性定义圆的半径
// stroke 和 stroke-width 属性控制如何显示形状的轮廓。我们把圆的轮廓设置为 2px 宽，黑边框
// fill 属性设置形状内的颜色。我们把填充颜色设置为红色
<circle cx="100" cy="50" r="40" stroke="black"
stroke-width="2" fill="red"/>

</svg>
```

### CSS3高级属性绘制技巧

* linear-gradient
* radial-gradient
* background
* border-radius
* box-shadow
* text-shadow
* Filter
* clip-path
* counter

#### liner-gradient【线性渐变】

[官网](https://developer.mozilla.org/zh-CN/docs/Web/CSS/linear-gradient())

> 语法：可以使用角度或者关键字
>
> * background：liner-gradient( color-stop1, color2-stop2)
> * background：liner-gradient( direction, color-stop1, color-stop2 )
> * background: liner-gradient(angle, color-stop1, color-stop2);

![liner-gradient](/medias/imges/theory/css3/liner-gradient.jpeg)

参数说明：

* 第一个参数：指定渐变方向，可以用`角度`的关键词或`英文`来表示：to/top、bottom、left、right
* 第一个参数省略时，默认为`180deg`，等同于 `to bottom`
* 第二个和第三个参数，表示颜色的起始点和结束点，可以拥有多个颜色值

| 角度   | 英文表示     | 作用             |
| ------ | ------------ | ---------------- |
| 0deg   | to top       | 从下向上         |
| 90deg  | to right     | 从左向右         |
| 180deg | to bottom    | 从上向下         |
| 270deg | to left      | 从右向左         |
|        | to top left  | 从右下角到左上角 |
|        | to top right | 从左下角到右上角 |

代码块演示

```css
// 1.线性渐变默认是从上到下
.box {
    background:linear-gradient(red,green);
    background:--webkit-linear-gradient(red,green);
}
// 2.从一个方向到另一个方向(to/left、right、bottom、top)
.box {
    background:linear-gradient(to left,red,green);
    background: -webkit-linear-gradient(to left,red,green);
}
// 3.对角的、两个方向可以进行组合，比如下面的就是从左上角到右上角
.box {
    background: linear-gradient(to left top, red,green);
    background: -webkit-linear-gradient(to left top, red,green);
}
// 4.还可以设置角度
.box {
    background: linear-gradient(90deg,red,green);
    background: -webkie-linear-gradient(90deg,red,green);
}
```

#### radial-gradient【径向渐变】

[官网](https://developer.mozilla.org/zh-CN/docs/Web/CSS/radial-gradient())

径向渐变：默认的形状是椭圆、至少得有两个颜色值

形状：`ellipse 椭圆(默认)`、`circle(圆)`

```css
.box {
    background: radial-gradient(#e66465, #9198e5);
    background: radial-gradient(closest-side, #3f87a6, #ebf8e1, #f69d3 c);
    background: radial-gradient(circle at 100%, #333, #333 50%, #eee 7 5%, #333 75%);
    background: radial-gradient(ellipse at top, #e66465, transparent), radial-gradient(ellipse at bottom, #4d9f0c, transparen t);
}
```

关键字用于描述边缘轮廓的具体位置。以下为关键字常量：

| 常量            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| closest-side    | 渐变的边缘形状与容器距离渐变中心点最近的一边相切（圆形）或者至少与距离渐变中心点最近的垂直和水平边相切（椭圆）。 |
| closest-corner  | 渐变的边缘形状与容器距离渐变中心点最近的一个角相交。         |
| farthest-side   | 与closest-side相反，边缘形状与容器距离渐变中心点最远的一边相切（或最远的垂直和水平边）。 |
| farthest-corner | 渐变的边缘形状与容器距离渐变中心点最远的一个角相交。         |

#### background【背景】

* background-size ：设置背景图大小

[官网](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-size)

1. 检索或设置对象的背景图像的尺寸大小
2. 该属性提供2个参数值（特性值 cover 和 contain 除外 ）
3. 如果提供两个，第一个用于定义背景图像的宽度，第二个用于定义背景图像的高度
4. 如果只提供一个，该该值将用于定义背景图像的宽度，第2个值默认为auto，即高度为auto，此时背景图以提供的宽度作为参照来进行等比缩放

取值情况：

1. length：用长度值指定背景图像大小。不允许负值。
2. percentage：用百分比指定背景图像带下。不允许负值。
3. auto：背景图像的真实大小
4. cover：将背景图像等比缩放宽度**和**高度到完全覆盖容器，背景图有可能超出容器
5. contain：将背景图像等比缩放到宽度**或**高度与容器的宽度或高度相等，背景图像始终被包含在容器内

```css
.box1 {
    background: #000 url(images/icon-wechat.png) no-repeat center;
    background-size: cover;
}
.box2 {
    background: #000 url(images/icon-wechat.png) no-repeat center;
    background-size: contain;
}
```

* background-origin ： 设置背景图的原点位置

[官网](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-origin)

1. 设置或检索对象的背景图像计算 `background-position`时的参考原点(位置)
2. padding-box：从padding区域（含padding）开始显示背景图像
3. border-box：从border区域（含border）开始显示背景图像
4. content-box：从content区域开始显示背景图像

```css
.box1 {
    /* 包含边框线 */
    background-origin: border-box;
}

.box2 {
    /* content 内容 */
    background-origin: content-box;
}

.box3 {
    /* 包含padding */
    background-origin: padding-box;
}
```

* background-clip

[官网](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-clip)

1. 指定对象的背景图像向外裁剪的区域
2. padding-box：从padding区域（不含padding）开始向外裁剪背景，显示padding以内的背景
3. border-box：从border区域（不含border）开始向外裁剪背景，显示border以内的背景图片
4. content-box：从content区域开始向外裁剪背景
5. text：从前景内容的形状（比如文字）作为裁剪区域向外裁剪，如此即可实现使用背景作为填充色之类的遮罩效果

```html
<style>
    div {
        float: left;
        width: 500px;
        height: 300px;
        margin: 20px;
        border: 20px dashed red;
        padding: 30px;
        background: url(images/1.jpg) no-repeat;
        background-size: cover;
    }

    .box1 {
        /* 边框往外 - 没有任何的效果 */
        background-clip: border-box;
    }

    .box2 {
        /* 内容往外 - padding + border */
        background-clip: content-box;
    }

    .box3 {
        /* padding往外 - border */
        background-clip: padding-box;
    }
    .box4{
        font-size:100px;
        font-weight:bold;
        -webkit-background-clip: text;
        background-clip: text;
        color:transparent
    }
</style>

<body>
    <div class="box1"></div>

    <div class="box2"></div>

    <div class="box3"></div>

    <div class="box4">珠峰培训珠峰</div>
</body>
```

#### border-radius

[官网](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-radius)

作用：圆角矩形、圆形、半圆/扇形、一些奇怪的角角

含义：设置或检索对象使用圆角边框

* 提供2个参数，2个参数以`/`分隔，每个参数允许设置`1~4`个参数值，第一个参数表示水平半径，第二个参数表示垂直半径，如果2个参数省略，则默认等于第一个参数
* 水平半径：如果提供全部四个参数值，将按上左（top-left）、上右（top-right）、下右（bottom-right）、下左（bottom-left）的顺序作用于四个角，垂直半径也遵循以上四点

取值情况：

* 四个值：第一个值为左上角、第二值为右上角、第三个值为右下角，第四个值为左下角
* 三个值：第一个值为左上角，第二值为右上角和左下角、第三个值为右下角
* 两个值：第一个值为左上角和右下角，第二值为右上角和左下角
* 一个值：四个角值相同

#### border-shadow

[官网](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-shadow)

作用：营造层次，充当没有宽度的边框，特殊效果

含义：设置或检索对象阴影

* none：无阴影
* length1：第1个长度值用来设置对象的阴影水平偏移量，可以为负值
* length2：第2个长度值用来设置对象的阴影垂直偏移量，可以为负值
* length3：如果提供了第3个长度值则用来设置对象的阴影模糊度，不允许为负值
* length4：如果提供了第4个长度值则用来设置对象的阴影外延值，可以为负值
* color：设置对象的阴影的颜色
* inset：设置对象的阴影类型为内阴影，该值为空时，则对象的阴影类型为外阴影

语法：

> box-shadow：X偏移量 Y偏移量 【阴影模糊半径】【阴影扩展半径】【阴影颜色】【投射方式】

| 值           | 描述                                                        |      |
| ------------ | ----------------------------------------------------------- | ---- |
| X轴偏移量    | 必需，水平阴影的位置，允许负值                              |      |
| Y轴偏移量    | 必需，垂直阴影的位置，允许负值                              |      |
| 阴影模糊半径 | 可选，模糊距离                                              |      |
| 阴影扩展半径 | 可选，阴影的尺寸                                            |      |
| 阴影颜色     | 可选，阴影的颜色，省略默认为黑色                            |      |
| 投影方式     | 可选，（设置inset时为内部阴影方式，如果省略则为外阴影方式） |      |

```html
<style>
    /* border + box-shadow */
    div {
        width: 300px;
        height: 300px;
        background-color: burlywood;
        /* border-radius:50px/50px; */
        /* border-radius:50%; */

        /* 1.水平偏移 正- 右  负 - 左
        2.垂直偏移  正 - 下   负 - 上
        3.阴影的模糊度  只能是正值
        4.阴影的扩展大小 - 
        5.颜色  - rgba() alpha [0-1]
        */
        box-shadow:10px 10px 10px rgba(0,0,0,.4) inset,
            -20px -20px 10px rgba(0,0,0,.4);
    }
</style>

<body>
    <div class="box1 active"></div>

</body>
```

#### text-shadow

[官网](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-shadow)

作用：立体感，印刷品质感

含义：设置或检索对象中文的文字是否有阴影及模糊效果

* none：无阴影
* length1：第1个长度值用来设置对象的阴影水平偏移量，可以为负值
* length2：第2个长度值用来设置对象的阴影垂直偏移量，可以为负值
* length3：如果提供了第3个长度值则用来设置对象的阴影模糊值，不允许为负值
* color：设置对象的阴影的颜色

语法：

> box-shadow：X轴偏移量 Y轴偏移量 【阴影模糊半径】【阴影扩展半径】【阴影颜色】【投影方式】

```html
<style>
    .box1 {
        /* 
        没有扩展大小 + inset
        */
        font-size: 40px;
        font-weight: bold;
        text-shadow:10px 10px 10px red;
    }
</style>

<body>
    <div class="box1">text-shadow</div>
</body>
```

#### Filter

[官网](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter)

* filter：none | blur() | brightness() | contrast() | drop-shadow() | grayscale() | hue-rotate() | invert() |opacity() | saturate() | sepia() | url() 

1. none：默认值，没有效果
2. blur(px)【*】：给图像设置高斯模糊，`redius`一值设定高斯函数的标准差，或者是屏幕以多少像素融在一起，所以值越大越模糊；如果没有设定值，则默认是0；这个参数可设置css长度值，但不接受百分比。
3. brightness(%)：给图片应用一种线性乘法，使其看起来更亮或更暗。如果值是`0%`，图像会全黑。值是`100%`，则图像无变化。其他的值对应线性乘数效果。值超过`100%`也是可以的，图像会比原来更亮，如果没有设定值，默认是1
4. contrast(%)：调整图像的对比度，值是`0%`的话，图像会全黑。值是`100%`，图像不变。值可以超过`100%`，如果没有设定值，默认是`1`
5. drop-shadow(h-shadow v-shadow blur spread color)【*】：给图像设置一个阴影效果
6. grayscale(%)：将图像转换为灰度图像，值定义转化的比例。值为`100%`则完全转化为灰度图像，值为`0%`图像无变化。若值未设置，默认值是`0deg`。该值虽然没有最大值，超过`360deg`的值相当于又绕一圈
7. hue-rotate(deg)：给图像应用色相旋转。`angle`一值设定图像会被调整的色环角度值。值为`0deg`，则图像无变化。若值未设置，默认是`0deg`。该值虽然没有最大值，超过`360deg`的值相当于又绕一圈。
8. invert(%)：反转输入图像。值定义转换的比例。`100%`的价值是完全反转。值为`0%`则图像无变化。值在`0%~100%`之间，则是效果的线性乘数。若值未设置，值默认是0
9. opacity(%)【*】：转化图像的透明程度。值定义转化比例。值为`0%`则是完全透明，值为`100%`则是图像无变化，值在`0%~100%`之间，则是效果的线性乘数，也相当于图像样本乘以数量。若值未设置，值默认是`1`。该函数与已有的`opacity`属性很相似，不同之处在于通过`filter`,一些浏览器为了提升性能会提供硬件加速
10. staurate(%)：转换图像饱和度。值定义转化的比例。值为`0%`则是完全不饱和。值为`100%`则图像无变化。其他值，则是效果的线性乘法。超过`100%`的值是允许的，则有更高的饱和度。若值未设置，值默认是`1`
11. sepia(%)：将图像转化为深褐色。值定义转化的比例。值为`100%`则完全是深褐色的，值为`0%`图像无变化。值在`0%~100%`之间，则是效果的线性乘数。若为设置，值默认是`0`;
12. url()：URL函数接受一个XML文件，该文件设置了一个SVG滤镜，且可以包含一个锚点来指定一个具体的滤镜元素，filter：url(svg-url#element-id)

```html
<style>
    /* blur + opacity + drop-shadow 
    */

    .blur {
        /* 高斯模糊 */
        -webkit-filter: blur(4px);
        filter: blur(4px);
    }

    .brightness {
        -webkit-filter: brightness(0.30);
        filter: brightness(0.30);
    }

    .contrast {
        -webkit-filter: contrast(180%);
        filter: contrast(180%);
    }

    .grayscale {
        -webkit-filter: grayscale(100%);
        filter: grayscale(100%);
    }

    .huerotate {
        -webkit-filter: hue-rotate(180deg);
        filter: hue-rotate(180deg);
    }

    .invert {
        -webkit-filter: invert(100%);
        filter: invert(100%);
    }

    .opacity {
        /* 透明度 */
        -webkit-filter: opacity(50%);
        filter: opacity(50%);
    }

    .saturate {
        -webkit-filter: saturate(7);
        filter: saturate(7);
    }

    .sepia {
        -webkit-filter: sepia(100%);
        filter: sepia(100%);
    }

    .shadow {
        /* 阴影  */
        -webkit-filter: drop-shadow(8px 8px 10px green);
        filter: drop-shadow(8px 8px 10px green);
    }
</style>

<body>
    <div class="blur"><img src="images/1.jpg" alt=""></div>

    <div class="brightness"><img src="images/1.jpg" alt=""></div>

    <div class="contrast"><img src="images/1.jpg" alt=""></div>

    <div class="grayscale"><img src="images/1.jpg" alt=""></div>

    <div class="huerotate"><img src="images/1.jpg" alt=""></div>

    <div class="invert"><img src="images/1.jpg" alt=""></div>

    <div class="opacity"><img src="images/1.jpg" alt=""></div>

    <div class="saturate"><img src="images/1.jpg" alt=""></div>

    <div class="sepia"><img src="images/1.jpg" alt=""></div>

    <div class="shadow"><img src="images/1.jpg" alt=""></div>
</body>
```

#### clip-path

[官网](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Attribute/clip-path)

[clip-path生成器](https://www.html.cn/tool/css-clip-path/)

含义：使用属性裁剪方式为元素创建可显示区域。区域内部分显示，区域外的隐藏

作用：对容器进行裁剪、常见几何图形、自定义途径

```css
/* 圆形circle（半径at圆心坐标） polygon() */
.circle {
    width: 100px;
    height: 100px;
    background: #0cc;
    clip-path: circle(50% at 50% 50%);
    -webkit-clip-path: circle(50% at 50% 50%);
}

/* 椭圆形ellipse（长、短轴半径at圆心坐标） */
.ellipse {
    width: 100px;
    height: 100px;
    background: #aaa;
    clip-path: ellipse(25% 50% at 50% 50%);
    -webkit-clip-path: ellipse(25% 50% at 50% 50%);
}

/* 内置矩形inset (上右下左的边距round上右下左圆角) */
.inset {
    width: 100px;
    height: 100px;
    background: #99f;
    clip-path: inset(10px 20px 30px 10px round 20px 5px 50px 0);
    -webkit-clip-path: inset(10px 20px 30px 10px round 20px 5px 50px 0);
}

/* 正三角形 */
.triangle {
    width: 100px;
    height: 87px;
    background: #c00;
    clip-path: polygon(0% 100%, 50% 0%, 100% 100%);
    -webkit-clip-path: polygon(0% 100%, 50% 0%, 100% 100%);
}

/* 正方形 */
.square {
    width: 100px;
    height: 100px;
    background: #069;
    clip-path: polygon(0% 0%, 0% 100%, 100% 100%, 100% 0%);
    -webkit-clip-path: polygon(0% 0%, 0% 100%, 100% 100%, 100% 0%);
}
```

### css机制At-rule

[官网1](https://css-tricks.com/the-at-rules-of-css/)

[官网2](https://developer.mozilla.org/en-US/docs/Web/CSS/At-rule)

大家可能在CSS中见到过字符`@`然后加一些关键字的用法，这种用法就称为`AT`规则，在CSS中，种类还是很多的，总结如下

> @charset、@import、@namespace、@document、@font-face、@keyframs、@media、@page、@supports

将其分为常规规则、嵌套规则

* 常规规则：语法类型下面规则：`@[KEYWORD](RULE)`

> @charset、@import、@namespace

* 嵌套规则：就是带有花括号`{}`，语法类型下面规则：`@[KEYWORD]{嵌套语句}`

> @document、@font-face、@keyframes、@media、@page、@supports

#### @charset

定义字符集。用于提示CSS文件使用的字符编码方式，它如果被使用，**必须出现在最前面。**这个规则只在给出语法解析阶段前使用，并不影响页面上的展示效果；某些软件、例如`Dreamweaver`新建CSS文件的时候，自动会带有下面所示代码，但实际开发时候，作用不大，因为`meta`中已经有所设置`<meta charset="utf-8">`，会覆盖，所以我们可以直接删除掉

```css
@charset "utf8-8";
```

#### @import

导入其他CSS样式文件。除了`@charset`规则不被引入，`@import`可以引入另一个文件的全部内容。手机上线时候，不建议使用，多请求，阻塞加载之类。但本地开发可以使用，用做CSS模块化开发，然后使用一些（如grunt）工具进行压缩并合并。但是呢，相比less，sass等还是有不足，就是`@import`语句只能在CSS文件顶部，使得文件的前后关系控制就不那么灵活

```css
@import 'index.css';
@import url('index.css');
```

#### @namespace

此规则应用到`XML HTML(XHTML)`上特别有用，因为这样的话XHTML元素可以作为选择器在CSS中使用

```css
/* XHTML命名空间 */
@namespace url(http://www.w3.org/1999/xhtml);

/* 内嵌在XHTML的SVG的命名空间 */
@namespace svg url(http://www.w3.org/2000/svg);
```

#### @document

CSS4.0规范有相关说明。如果文档满足给定的一些条件，就可以应用我们指定的一些样式。比如说，这个CSS文件被子站A调用，和被子站C调用。我们可以通过域名匹配来执行不同的CSS样式。这样，我们可以有效避免冲突，或者防止外链之类。

```css
@document 
  /* 页面URL需要是 */
  url(http://www.zhangxinxu.com/),
  
  /* 页面URL的开头必须是... */
  url-prefix(www.zhangxinxu.com/wordpress/),
  
  /* 该域上的所有页面 */
  domain(zhangxinxu.com),

  /* 所有https协议页面 */
  regexp("https:.*")
{
  
  /* 开始样式 */
  body { font-family: Comic Sans; }

}
```

#### @font-face

自定义字体用的，IE6也支持

```css
@font-face {
    font-family: 'MyWebFont';
    src:  url('myfont.woff2') format('woff2'),
        url('myfont.woff') format('woff');
}
```

#### @keyframs

用来声明CSS3 animation 动画关键帧

```css
@keyframes fadeIn {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
```

#### @media

媒体查询

```css
@media all and (min-width: 1280px) {
    /* 宽度大于1280干嘛干嘛嘞... */ 
}
@media (-webkit-min-device-pixel-ratio: 1.5), (min-resolution: 2dppx) { 
    /* Retina屏幕干嘛干嘛嘞... */ 
}
@media print {
    /* 闪开闪开，我要打印啦！ */ 
}
@media \0screen\,screen\9 {
    /* IE7,IE8干嘛干嘛嘞... */ 
}
@media screen\9 {
    /* IE7干嘛干嘛嘞... */ 
}
```

#### @page

page用于分页媒体访问页面时的表现设置，页面是一种特殊的盒模型结构，除了页面本身，还可以设置它周围的盒子，这个规则用在打印文档时候修改一些CSS属性，使用`@page`我们只能改变部分CSS属性，例如间距属性`margin`，打印相关的`orphans`，`windows`，以及`page-break-*`，其他css属性会被忽略

```css
@page {
  margin: 1cm;
}

@page :first {
  margin: 2cm;
}
```

#### @supports

是否支持某些CSS属性声明的AT规则，浏览器对其支持越来越好了

```css
/* 检查是否支持CSS声明 */ 
@supports (display: flex) {
  .module { display: flex; }
}

/* 检查多个条件 */ 
@supports (display: flex) and (-webkit-appearance: checkbox) {
  .module { display: flex; }
}
```



### CSS3选择器

> css选择器的匹配规则是：**从右向左**，这是为了性能考虑

css3选择器图：

![css3选择器](/medias/imges/theory/css3/selected.png)

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Getting_Started/Selectors http://css.cuishifeng.cn/)

#### 基本选择器

| 选择器              | 类型         | 功能描述                                             |
| ------------------- | ------------ | ---------------------------------------------------- |
| *                   | 通配符选择器 | 选择文档中所有HTML元素                               |
| E                   | 元素选择器   | 选择指定类型的HTML元素                               |
| #id                 | ID选择器     | 选择指定ID属性值为`id`的任意类型元素                 |
| .class              | 类选择器     | 选择指定class属性值为`class`的任意类型的任意多个元素 |
| selector1,selectorN | 群组选择器   | 将每一个选择器匹配的元素集合并                       |

#### 层次(关系)选择器

| 选择器 | 类型                     | 功能描述                                                     |
| ------ | ------------------------ | ------------------------------------------------------------ |
| E F    | 后代选择器（包含选择器） | 选择匹配的 F 元素，且匹配的 F 元素被包含在匹配的 E 元素内    |
| E>F    | 子选择器                 | 选择匹配的 F 元素，且匹配的 F 元素是匹配的 E 元素的子元素    |
| E+F    | 相邻兄弟选择器           | 选择匹配的 F 元素，且匹配的 F 元素紧位于匹配的 E 元素的后面，也就是后面第一个相邻兄弟F元素 |
| E~F    | 通用选择器               | 选择匹配的 F 元素，且位于匹配的 E 元素后的所有匹配的 F 元素  |

#### 伪类选择器

* 动态伪类选择器

| 选择器    | 类型           | 功能描述                                                     |
| --------- | -------------- | ------------------------------------------------------------ |
| E:link    | 链接伪类选择器 | 选择匹配的 E 元素，而且匹配元素被定义了超链接并未被访问过，常用于链接锚点 |
| E:visited | 链接伪类选择器 | 选择匹配的 E 元素，而且匹配元素被定义了超链接并已被访问过。常用于链接锚点上。 |
| E:active  | 用户行为选择器 | 选择匹配的 E 元素，且匹配元素被激活，常用于链接锚点和按钮上  |
| E:hover   | 用户行为选择器 | 选择匹配的 E 元素，且用户鼠标停留在元素E上。IE6及以下浏览器仅支持`a:hover` |
| E:focus   | 用户行为选择器 | 选择匹配的 E 元素，而且匹配元素获取焦点时给其添加对应的样式  |

* 目标伪类选择器

| 选择器   | 功能描述                                   |
| -------- | ------------------------------------------ |
| E:target | 选择匹配 E 的元素，且匹配元素被相关URL指向 |

控制一个元素：

```html
<style>
    .menuSection {
        display: none;
    }

    :target {
        /*这里的:target就是指id="brand"的div对象*/
        display: block;
    }
</style>
<body>
    <!-- id => #  -->
    <!-- :target选择器称为目标选择器，用来匹配文档(页面)的url的某个标志符的目标元素  -->
    <h2><a href="#brand">Brand</a></h2>
    <div class="menuSection" id="brand">
        content for Brand
    </div>

</body>
```

控制多个

```html
<style>
    /* 伪类选择器  :  */
    #brand:target {
        display: none;
        background: orange;
        color: #fff;
    }

    #jake:target {
        background: blue;
        color: #fff;
    }

    #aron:target {
        background: red;
        color: #fff;
    }
</style>
<body>
    <h2><a href="#brand">Brand</a></h2>
    <div class="menuSection" id="brand">
        content for Brand
    </div>
    <h2><a href="#jake">Brand</a></h2>
    <div class="menuSection" id="jake">
        content for jake
    </div>
    <h2><a href="#aron">Brand</a></h2>
    <div class="menuSection" id="aron">
        content for aron
    </div>
</body>
```

* UI元素状态伪类选择器

| 选择器     | 类型                 | 功能描述                             |
| ---------- | -------------------- | ------------------------------------ |
| E:checked  | 选中状态伪类选择器   | 匹配选中复选按钮或者单选按钮表单元素 |
| E:enabled  | 启用状态伪类选择器   | 匹配所有启用的表单元素               |
| E:disabled | 不可用状态伪类选择器 | 匹配所有禁用的表单元素               |

* 结构伪类选择器

**nth-child系列：**匹配的元素是将E内的全部元素作为匹配对象

| 选择器                | 功能描述                                                     |
| --------------------- | ------------------------------------------------------------ |
| E:first-child         | 作为父元素的第一个子元素的元素E。与`E:nth-child(1)`等同      |
| E:last-child          | 作为父元素的最后一个子元素的元素E。与`E:nth-last-child(1)`等同 |
| E F:nth-child(n)      | 选择父元素 E 的第 n 个子元素F。其中 n 可以是整数 (1,2,3)、关键字 (even, odd)、可以是公式 (2n+1)，而且 n 值起始值为1，而不是0 |
| E F:nth-last-child(n) | 选择父元素 E 的倒数第 n 个子元素 F 。此选择器与 E:nth-child(n) 选择器计算顺序恰好相反，但使用方法是一样的，其中：nth-last-child(1)始终匹配最后一个，与last-child等同 |
| E:only-child          | 选择父元素只包含一个子元素，且该子元素匹配E元素              |

**nth-of-type系列：**先从父元素过滤出所需要的子元素，在将过滤出来的子元素所谓匹配对象

| 选择器                | 功能描述                                                     |
| --------------------- | ------------------------------------------------------------ |
| E:first-of-type       | 选择父元素内具有指定类型的第一个 E 元素。与`E:nth-of-type(1)`等同 |
| E:last-of-type        | 选择父元素内就有指定类型的最后一个E 元素。与 `E:nth-last-of-type(1)`等同 |
| E:nth-of-type(n)      | 选择父元素内具有指定类型的第n个E元素                         |
| E:nth-last-of-type(n) | 选择父元素内具有指定类型的倒数第n个E元素                     |
| E:only-of-type        | 选择父元素只包含一个同类型子元素，且该子元素匹配E元素        |

> 上面两大系列的选择器看起来功能很类似， 你这种感觉是对的，他们的区分的是在于我们要去匹配的元素在父元素内的表现形式。例如：
>
> * 当父元素的子元素都是E类型，那么`nth-child`与`nth-of-type`类型的选择的功能就完全一致
> * 当父元素的子元素类型不只是E类型，还是G类型的话，他们的区别在于：`nth-child`类型的匹配对象是父元素的所有子元素，而`nth-of-type`类型匹配是先将父元素的所有`E`元素过滤出来，之后在从**过滤**出来的`E`元素集合中去匹配。
> * 总的来说：nth-of-type多了**过滤操作**

例子：

```css
// nth-child是将box内的所有子元素作为匹配对象，之后在判断第2个子元素是否为span类型，不是则不给其运用样式
.box span:nth-child(2) {
    color:red;
}

// nth-of-type是先将box内的所有span元素过滤出来，在从过滤出来span元素集合判断第2个元素是否存在，存在则给其增加属性
.box span:nth-of-type(2) {
    color:red;
}
```

其他结构伪类

| 选择器  | 作用描述                                                     |
| ------- | ------------------------------------------------------------ |
| E:root  | 选择匹配元素 E 所在文档的根元素。在HTML文档中，根元素始终是`html`，此时该选择器与`html`类型选择器匹配的内容相同 |
| E:empty | 选择没有子元素的元素，而且该元素也不包含任何文本节点         |

```html
// :root
<style>
    * {
        margin: 0
    }
    /* html 根元素 */
    :root {
        background: orange;
    }
</style>
<body>
    <div>:root选择器的演示</div>
</body>

// E:empty
<style>
    /* :empty选择器表示的就是空。用来选择没有任何内容的元素，这里没有内容指的是一点内容都没有，哪怕是一个空格 */
    p:empty {
        /* display: none; */
        width: 100px;
        height: 20px;
        background: #ff0000;
    }
</style>
<body>
    <h1>这是标题</h1>
    <p>第一个段落。</p>
    <p></p>
    <p></p>
    <p>第三个段落。</p>
    <p>第四个段落。</p>
    <p>第五个段落。</p>
</body>
```

* 否定伪类选择器

| 选择器   | 功能描述                     |
| -------- | ---------------------------- |
| E:not(F) | 匹配所有除元素 F 外的 E 元素 |

```html
<style>
    form {
        width: 200px;
        margin: 20px auto;
    }

    div {
        margin-bottom: 20px;
    }
    /* submit的类型排除  input= "text" */

    input:not([type="submit"]) {
        border: 1px solid red;
        background: #ccc;
    }

    input[type="submit"] {
        border: 0;
        background-color: grey;
        cursor: pointer;
    }
    /* div:not(.box){background-color: green;} */
</style>

<body>
    <div>1</div>
    <div class="box">2</div>

    <form action="#">
        <div>
            <label for="name">Text Input:</label>
            <input type="text" class="txt" name="name" id="name" placeholder="John
                                                                              Smith" />
        </div>
        <div>
            <label for="name">Password Input:</label>
            <input type="text" class="txt" name="name" id="name" placeholder="John
                                                                              Smith" />
        </div>
        <div>
            <input type="submit" value="Submit" />
        </div>
    </form>
</body>
```

#### 伪元素选择器

| 选择器         | 功能描述                                                     |
| -------------- | ------------------------------------------------------------ |
| ::first-letter | 设置对象内的第一个字符的样式                                 |
| ::first-line   | 设置对象内的第一行的样式                                     |
| ::before       | 设置在对象前的内容，用来和`content`属性一起使用，并且必须定义`content`属性，默认是添加一个**行内元素** |
| ::after        | 设置在对象后的内容，用来和`content`属性一起使用，并且必须定义`content`属性，默认是添加一个**行内元素** |
| ::placeholder  | 设置对象文字占位符的样式                                     |
| ::selection    | 设置对象被选择时的样式，`::selection`只能定义被选择时的`background-color及color` |

```html
<!--::placeholder-->
<style>
    /* postcss- 插件 - 自动添加前缀  */
    input::-webkit-input-placeholder {
        color: green;
    }

    /*  IE10+ */
    input:-ms-input-placeholder {

        color: green;
    }

    /* Firefox4-18 */
    input:-moz-placeholder {

        color: green;
    }

    /* Firefox19+ */
    input::-moz-placeholder {
        color: green;
    }

    input{
        color:red;
    }
</style>

<body>
    <input id="test" placeholder="Placeholder text!">
</body>

<!--::selection-->
<style>
    /*  ::selection 选中 */

    ::selection {
        color: pink;
    }

    ::-moz-selection {
        color: pink;
    }
</style>

<body>
    <div>将选定的文本变为粉色</div>

    <p>将选定的文本变为粉色</p>
</body>
```

#### 属性选择器

属性选择器也被叫做**选择器中的正则表达式**

[博客](https://www.zhangxinxu.com/wordpress/2016/08/regular-expression-in-css-selector/)

正则表达式字符含义

| 字符 | 含义                         |
| ---- | ---------------------------- |
| `^`  | 表示匹配以字符串在开始位置   |
| `$`  | 表示匹配字符串在结束位置     |
| `*`  | 表示匹配字符在任意位置       |
| `i`  | 表示字匹配不区分字符串大小写 |
| `g`  | 表示全局匹配字符串           |

css属性选择器版本

| 版本                             | 内容                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| css2.1属性选择器【**单词匹配**】 | 直接匹配：[attr]、[attr="val"]、[attr ~="val"]、[attr \|="val"] |
| css3 属性选择器【**字母匹配**】  | 正则匹配：[attr^="val"]、[attr$="val"]、[attr *= "val"]      |
| css4 属性选择器 【**字母匹配**】 | 忽略大小写匹配：[attr="val" i]                               |

#### 属性选择器

| 选择器         | 功能描述                                                     |
| -------------- | ------------------------------------------------------------ |
| [attr]         | 用于选取带有指定属性的元素                                   |
| [attr=value]   | 用于选取代用指定属性和值的元素                               |
| [attr~=value]  | 用于选取属性值中包含指定词汇的元素                           |
| [attr\|=value] | 用于选取带有以指定值**开头**的属性值的元素，或者开始是`value-xx`也行，该值必须是**整个单词** |
| E[attr^=value] | 选择匹配元素E，且元素E定义了属性`attr`，其属性值以`value`**开头**的任何字符串 |
| E[attr$=value] | 选取匹配元素E，且元素E定义了 属性`attr`，其属性值以`value`结束的任何字符串 |
| E[attr*=value] | 选取匹配元素E，且E元素定义了属性`attr`，其属性任意位置包含了`value` |

```css
[foo~=abc] {
    background-color: red;
}

[foo*=abc] {
    background-color: red;
}

/* 以f开头的字符(忽略大小写) - FOUr five */
li[data-index^='f'i]:after {
    content: '支持i正则';
    color: green;
}
```

#### 全面解读伪类和伪元素

[官网](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Selectors/Pseudo-classes_and_pseudo-elements)

伪类：

* 用于当已有元素处于的某个状态时，为其添加对应的样式，这个状态是根据用户行为而动态变化的。比如说，当用户悬停在指定的元素时，我们可以通过`:hover`来描述这个元素的状态。虽然它和普通的css类相似，可以为已有的元素添加样式，但是它只有处于dom树无法描述的状态下才能为元素添加样式，所以将其称为**伪类**
* 伪类存在的意义是为了通过选择器，格式化DOM树以外的信息以及不能被常规css选择器获取到的信息

伪元素：

* 用于创建一些不在文档树中的元素，并为其添加样式。比如说，我们可以通过`:before`来在一个元素前增加一些文本，并为这些文本添加样式。虽然用户可以看到这些文本，但是这些文本实际上不在文档树中。
* 伪元素可以创建一些文档语言无法创建的虚拟元素。比如：文档语言没有一种机制可以描述元素内容的第一个字母或第一行，但伪元素可以做到`(::first-letter、first-line)`。同时，伪元素还可以创建源文档不存在的内容，比如使用`::before`或`::after`

区别：

伪类的操作对象是文档树中已有的元素，而伪元素则创建了一个文档树外的元素，因此，伪类和委员的区别在于：**有没有创建一个文档之外的元素**

* 伪类本质上是为了弥补常规css选择器的不足，以便获取更多的信息；
* 伪元素本质上是创建了一个有内容的虚拟容器【行内元素】
* css3中伪类和伪元素的语法不同
* 在css3中，已经明确规定了伪类用一个冒号来表示，而伪元素则用两个冒号来表示
* 可以同时使用多个伪类，而只能同时使用一个伪元素

![伪类](/medias/imges/theory/css3/fakeClass.jpeg)

![css3选择器](/medias/imges/theory/css3/fakeElement.jpeg)

#### 选择器的权重和优先级

> CSS三大特性：继承性、优先级、层叠性

优先级的等级：

| 等级       | 描述                                                 |
| ---------- | ---------------------------------------------------- |
| 0级(0)     | 通配符选择器（*）、层次(关系)选择器（+、>、~，空格） |
| 1级(1)     | 元素、伪元素                                         |
| 2级(10)    | 类选择器、属性选择器、伪类                           |
| 3级(100)   | ID选择器                                             |
| 4级(1000)  | style内联选择器                                      |
| 5级(10000) | !important                                           |

优先级的计算规则

> `!important` > 行内 > ID选择器 > 类选择器 > 标签选择器 > 通配符 > 继承 > 浏览器默认样式

#### 选择器的命名

* 骆驼命名法

> 第一个字母要小写，后面的词的第一个字母既要用大写，例如：studentInfo、navMenuRedButton

* 帕斯卡命名法

> 这种命名法同样也是大小写字母混编而成，和骆驼命名法很像，但是骆驼命名法有一点区别，就是所有单词的首字母都要大写，当然也包括第一个单词，例如： StudentInfo、NavMenuRedButton

* 匈牙利命名法

> 在名称前面加上一个或多个小写字母作为前缀，来让名称更加好认，更容易理解，例如：head_navigation、red_navMenuButton

页面模板的常用命名

| 头：header            | 导航：nav        | 菜单：menu      | 友情链接：friendlink |
| --------------------- | ---------------- | --------------- | -------------------- |
| 页面外围包裹：wrapper | 子导航：subnav   | 子菜单：submenu | 下载：download       |
| 页面主体：main        | 广告：banner     | 侧栏：sidebar   | 小技巧：tips         |
| 内容：content         | 标志：logo       | 栏目：column    | 滚动：scroll         |
| 页脚：footer          | 搜索：search     | 热点：hot       | 上一个：prev         |
| 版权：copyright       | 登录条：loginbar | 新闻：news      | 下一个：next         |

* BEM命名法

> BEM的意思就是块（block）、元素（element）、修饰符（modifier），是由Yandex团队提出的一种前端命名方法论。这种巧妙的命名方法让你的css类对其他开发者来说更加透明而且更有意义。BEM命名约定更加严格，而且包含更多的信息，它们常用于团队开发一个耗时的大项目
>
> * BEM：块（block）、元素（element）、修饰符（modifier）
> * block代表了更高级别的抽象或组件
> * block__element 代表`.block`的后代，用于形成一个完整的`.block`的整体
> * block--modifier 代表`.block`的不同状态或不同版本，用于修饰

```vue
air-table{} /* 块 */  
air-table__footer{} /* 元素 */  
air-table--full{} /* 修饰符 */  

// vue组件下使用
<template>
    // wrapper主要用于sass嵌套，以免父（子）组件里的css冲突
    <div class="air-table(组件名)-wrapper">  
        <el-table class="air-table"></el-table>
        <div class="air-table__footer air-table__footer--full">
	          <button class="air-table__footer--prev">上</button>
            <button class="air-table__footer--next">下</button>
        </div>
    </div>
</template>
<style lang="scss" scoped>
.air-table(组件名)-wrapper {
    .air-table {}
    .air-table__footer {
        .air-table__footer—prev {}

        .air-table__footer—bext {}

        &.air-table__footer--full {}
    }
}
</style>
```

### CSS预处理器

#### css变量var的使用

>[https://www.zhangxinxu.com/wordpress/2016/11/css-css3-variables-var/](https://www.zhangxinxu.com/wordpress/2016/11/css-css3-variables-var/)
>[http://www.ruanyifeng.com/blog/2017/05/css-variables.html](http://www.ruanyifeng.com/blog/2017/05/css-variables.html)
>[https://blog.csdn.net/muzidigbig/article/details/89917424](https://blog.csdn.net/muzidigbig/article/details/89917424)
>[https://www.w3cplus.com/css/everything-you-need-to-know-about-css-variables.html](https://www.w3cplus.com/css/everything-you-need-to-know-about-css-variables.html)
>[https://developer.mozilla.org/zh-CN/docs/Web/CSS/Using_CSS_custom_properties](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Using_CSS_custom_properties)

在任何语言中，变量的共同作用都是一样的，那急速可以降低维护成本，附带还有更高性能，文件更高压缩率的好处，随着CSS预编译工具`Sass/Less/stylus`的关注和逐渐流行，CSS工作组迅速跟进[CSS变量的规范](https://drafts.csswg.org/css-variables/)制定，并且，很多浏览器已经跟进，目前，在部分项目中已经可以直接使用了。

CSS 变量（CSS variable）又叫做"CSS 自定义属性"（CSS custom properties）。因为变量与自定义的 CSS 属性其实是一回事。

你可能会问，为什么选择两根连词线（`--`）表示变量？因为`$foo`被 Sass 用掉了，`@foo`被 Less 用掉了。为了不产生冲突，官方的 CSS 变量就改用两根连词线了。

**css变量的语法和用法**

* CSS中原生的变量定义语法是：变量名前面要加两根连词线`--*`
* 变量使用语法是：`var()`函数用于读取变量`var(--*)`
* 其中`*`表示我们的变量名称
* `var()`函数还可以使用第2个参数，表示变量的默认值。如果该变量不存在，就会使用这个默认值；第2个参数不处理内部的逗号或空格，被视为参数的一部分，例如：`var(--bg,'1','2')`，1、2会被合并为一个参数，`'1','2'`
* 变量名大小写敏感，`--header-color`和`--Header-Color`是两个不同变量
* 变量后面没有加`!important`的用法

CSS变量的命名：关于命名这个东西，各种语言都有些显示，例如CSS选择器不能是数字开头，JS的变量是不能直接数值的，但是，在CSS变量中，这些限制通通没有，例如

```css
:root {
  --1: #369;
}
body {
  background-color: var(--1);
}
```

> 但是，不能包含`$,[,^,(,%`等字符，普通字符局限在只要是**数字[0-9]，字母[a-zA-Z]，下划线(_)，短横线（-）**这些组合，但是可以是中文，日文，韩文，例如：

```css
body {
  --深蓝: #369;
  background-color: var(--深蓝);
}
```

> 无论是变量的定义和使用只能在声明块`{}`里面，例如，下面这样是无效的

```css
--深蓝: #369;
body {
  background-color: var(--深蓝);
}
```

> 变量值只能用作属性值，不能用作属性名；下面代码中，变量`--side`用作属性名，这是无效的

```css
.foo {
  --side: margin-top;
  /* 无效 */
  var(--side): 20px;
}
```

> 全局/局部变量
>
> * `:root`伪类可以看做是一个全局作用域，在里面声明的变量，它下面的所有选择器都可以拿到
> * 局部变量

```css
// 全局变量
:root { --color: blue; }
.box{color: var(--color)}

// 局部变量
.box{
    --color: red;
    color: var(--color);
}
```

> css变量的默认参数
>
> * var()函数还可以使用第2个参数，表示变量的默认值。如果该变量不存在，就会使用这个默认值，如果变量存在，但值与属性是相违背的，这会走浏览器的默认值，而不是使用第2个参数值
> * 第2个参数不处理内部的逗号或空格，都视作参数的一部分

```css
color: var(--foo, #7F583F);
var(--font-stack, "Roboto", "Helvetica");   var(--pad, 10px 15px 20px);
```

> css变量的拼接
>
> * 如果变量值是一个字符串，可以与其他字符拼接
> * 如果变量值是数值，不能与数值单位直接连用，因为`var()`函数调用之后的输出默认后面带一个空格，我们要使用`cacl()`函数来调用
> * 如果变量值带有单位，就不能写成字符串

```css
// 字符串
--bar: 'hello';   
--foo: var(--bar)' world';

// 变量值是数值
foo { 
	--gap: 20; 
  margin-top: var(--gap)px;  /* 无效 */  
}

// 上面代码中，数值与单位直接写在一起，这是无效的。必须使用calc() 函数，将它们连接，
foo { 
	--gap: 20; 
  margin-top: calc(var(--gap) * 1px); 
}

// 变量值带有单位
.foo {
	--foo: '20px'; 
  font-size: var(--foo);  /* 无效 */ 
} 

.foo {
	--foo: 20px;
  font-size: var(--foo);  /* 有效 */ 
}
```

> css变量的作用域，同一个css变量，可以在多个选择器内审明。读取的时候，优先级最高的声明生效。这与css的**层叠(cascade)**规则是一致的

```html
<style>
  :root { --color: blue; }
  div { --color: green; }
  #alert { --color: red; }
  * { color: var(--color); }
</style>

<p>蓝色</p>
<div>绿色</div>
<div id="alert">红色</div>
```

上面代码中，三个选择器都声明了`--color`变量。不同元素读取这个变量的时候，会采用优先最高的规则，因此三段文字的颜色是不一样的。这就是说，变量的作用域就是它所在的选择器的有效范围。

```css
body {
  --foo: #7F583F;
}

.content {
  --bar: #F7EFD2;
}

:root {
  --main-color: #06c;
}
```

上面代码中，变量--foo的作用域是body选择器的生效范围，--bar的作用域是.content选择器的生效范围。
由于这个原因，全局的变量通常放在根元素:root里面，确保任何选择器都可以读取它们。

> css变量的注意问题：

* 当存在多个同样名称的变量的时候，变量的覆盖规则由CSS选择器的权重去决定，但并无`!important`这种用法
* 变量的取值采用就近原则
* 如果变量值是数值，不能与数值单位直接连用。必须使用`calc()`函数，将它们连接出来，因为`var()`函数默认调用尾部是有空格的
* 如果变量值是带有单位的，就不能写成字符串
* css属性名不可以使用变量
* css变量是存在缺省值，只要定义正确，里面的值不对，结果以缺省值显示

CSS变量的响应式布局：CSS 是动态的，页面的任何变化，都会导致采用的规则变化。
利用这个特点，可以在响应式布局的media命令里面声明变量，使得不同的屏幕宽度有不同的变量值

```css
body {
  --primary: #7F583F;
  --secondary: #F7EFD2;
}

a {
  color: var(--primary);
  text-decoration-color: var(--secondary);
}

@media screen and (min-width: 768px) {
  body {
    --primary:  #F7EFD2;
    --secondary: #7F583F;
  }
}
```

css变量的兼容性处理：对于不支持CSS变量的浏览器，可以采用下面的写法

```html
<style>
    // 用属性值得无效声明
    a {
        color: #7F583F;
        color: var(--primary);
    }

    // 也可以使用@support命令进行检测
        @supports ( (--a: 0)) {
            /* supported */
    }
    @supports ( not (--a: 0)) {
        /* not supported */
    }
</style>

<script>
    // JavaScript 检测浏览器是否支持 CSS变量
    const isSupported =
          window.CSS &&
          window.CSS.supports &&
          window.CSS.supports('--a', 0);

    if (isSupported) {
        /* supported */
    } else {
        /* not supported */
    }

    // JavaScript 操作 CSS 变量的写法
    window.onload = function() {
        // 设置值
        document.body.style.setProperty("--primary","pink");
        // 读取值
        let primary = document.body.style.getPropertyValue("--primary");
        console.log(primary);
        // 删除变量;返回删除的变量值
        let delPrimary = document.body.style.removeProperty("--primary");
        console.log(delPrimary);
    }
</script>
```

#### css预处理器

什么是CSS预处理器：

* CSS预处理器定义了一种新的语言，其基本思想是，用一种专门的编程语言，为CSS增加一些编程的特性，将CSS作为目标生成文件，然后开发者就只需要使用这种语言进行编码工作。
* 通俗的说，CSS预处理器用一种专门的编程语言，进行Web页面样式设计，然后在编译成正常的`CSS`文件，以供项目使用。CSS预处理器为CSS增加一些编程的特性，无需考虑浏览器的兼容性问题，例如你可以在CSS 中使用变量、简单的逻辑程序，函数以及在编程语言中的一些基本特性，可以让你的 css 更加简洁、适应性更强，可读性更佳，更易于代码的维护等诸多好处

CSS预处理器的特点：

* 基于CSS的另外一种语言
* 通过工具编译成css
* 添加了很多css不具备的特性
* 能提升CSS文件的组织

CSS预处理器的作用：

* 帮助更好的组织CSS代码
* 提高代码复用率
* 提升可维护性

CSS预处理器的优缺点：

* 优点：提高代码复用率和可维护性
* 缺点：需要引入编译过程 有学习成本

CSS预处理器的能力：

* 变量和计算 能有效减少重复代码
* 能使用嵌套，反映代码层级和约束

* Extend 和 Mixin 复用代码片段
* 循环适用于复杂有规律的样式
* import CSS文件模块化

现阶段最火CSS预处理器语言：less，scss，stylus

#### Less

less官方文档  [http://lesscss.org/](http://lesscss.org/)
less中文文档  [https://less.bootcss.com/](https://less.bootcss.com/)
less文件后缀为.less,例如: index.less

#### sass

[Sass官方文档](https://sass-lang.com/documentation) [https://sass-lang.com/guide](https://sass-lang.com/guide)
[Sass中文网](https://www.sass.hk/)    [https://www.sass.hk/](https://www.sass.hk/)   [https://www.sasscss.com/](https://www.sasscss.com/)
练习小测试    [http://sassmeister.com/](http://sassmeister.com/)
sass文件后缀为.scss,例如: index.scss

注意点：

|          | 混合宏                                                       | 继承                                                         | 占位符                                                       |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 声明方式 | @mixin                                                       | .class                                                       | %placeholder                                                 |
| 调用方式 | @include                                                     | @extend                                                      | @extend                                                      |
| 使用环境 | 如果相同的代码块需要在不同的环境传递不同的值时，可通过混合宏来定义重复使用的代码<br />不足：就是编译出来的css代码会多次出现调用的混合宏对应的代码，使得文件变得臃肿，代码冗余 | 如果相同代码块不需要传递不同值，并且此代码块已在Sass文件中定义，那么可以通过 Sass继承来调用已存在的基类，使用继承会将调用相同基类的代码合并在一起<br />不足：如果基类，并不存在于HTML结构时，不管调用与不调用，子啊编译出来的 css 中都将产生基类对应的样式代码 | 占位和继承基本类似，唯一不同的是，相同的代码块并没有在基类中存在，而是额外声明。如果不调用已声明的占位符，将不会产生任何样式代码，如果在不同选择器调用占位符，那么编译出来的 css 代码将会把相同的代码合并在一起，占位符是不产生基类代码的 |

### 定位和层叠上下文

##### 定位

> [技术博客](http://www.ruanyifeng.com/blog/2019/11/css-position.html)
>
> position 属性用来指定一个元素在网页上的位置，一共有5种取值，即`position`属性主要有五个值

* static（默认值）：
  1. `staic`是`position`属性的默认值。如果省略`position`属性，浏览器就认为该元素就是`staic`定位
  2. 这时浏览器会按照源码的顺序，决定每个元素的位置。这称为**正常的页面流（normal flow）**。每个块级元素占据自己的区块（block），元素与元素之间不产生重叠，这个位置就是元素的默认位置。
  3. 该关键字指定元素使用正常的布局行为，即元素在文档常规流中当前的布局位置。此时`top/right/bottom/left`和`z-index`属性都无效

![position-static](/medias/imges/theory/css3/position-static.jpg)

* relative（相对定位）
  1. 表示相对默认位置（即static时的位置）进行偏移
  2. 即定位基点是元素的默认位置
  3. 它必须搭配`top/bottom/left/right`这四个属性一起使用，用来指定偏移的方向和距离
  4. **不脱离文档流**

![position-relative](/medias/imges/theory/css3/position-relative.jpg)

* absoulte（绝对定位）
  1. 表示，相对于上级元素（一般是父元素）进行偏移，即定位基点是父元素。
  2. 它有一个重要的限制条件：定位基点（一般是父元素）要有`position`属性并其属性值不能是`static`定位，否则定位基点就会一直往上找，直到找到根元素`html`为止。
  3. 另外，`absoulte`定位也必须搭配`top/bottom/left/right` 这四个属性一起使用
  4. 注意，`absoulte`定位的元素会被`正常的页面流`忽略，即在正常的页面流中，该元素所占空间为零，周边元素不受影响，也就是其**脱离文档流**。

![position-absolute](/medias/imges/theory/css3/position-absolute.jpg)

* fixed（固定定位）
  1. 表示，相当于视口（viewport，浏览器窗口）进行偏移，即定位基点是浏览器窗口。这会导致元素的位置不随着页面滚动而变化，好像固定在页面上
  2. 它如果搭配`top/bottom/left/right`这四个属性一起使用，表示元素的初始化位置是基于视口计算的，否则初始位置就是元素的默认位置
  3. 其是**脱离文档流**的

![position-fixed](/medias/imges/theory/css3/position-fixed.jpg)

* sticky（粘连定位）

  [技术博客](https://www.zhangxinxu.com/wordpress/2020/03/position-sticky-rules/)

  1. 跟前面四个属性值都不一样，它会产生动态效果，很像`relative`和`fixed` 的结合；一些时候是`relative`定位（定位基点是自身默认位置），另一些时候自动变成`fixed`定位（定位基点是视口）
  2. 因此，它能够形成**动态固定**的效果。比如页面的搜索工具栏，初始加载时在自己的默认位置（relative定位）
  3. `sticky`生效的前提是，必须搭配`top、bottom、left、right`这四个属性一起使用，不能省略，否则等同于`relative`定位，不产生**动态固定**的效果。原因是这四个属性用来定义**偏移距离**，浏览器把它当作`sticky`的生效门槛。它的具体规则是，当页面滚动，父元素开始脱离视口时（即部分不可见），只要与`sticky`元素的距离达到生效门槛，`relative`定位自动切换到`fixed`定位；等到父元素会到视口时，并且在`sticky`的生效门槛之内，`fixed`定位又会切换到`relative`定位
  4. 除了已被淘汰的`IE`以外，其他浏览器目前都支持`sticky`。但是`Safari`连起来需要加上浏览器前缀`webkie-`

  符合的需求：页面向下滚动时，工具栏变成固定位置，始终停留在页面头部（fixed定位），等到页面重新向上滚动到原位，工具栏也会会到默认位置。

对比：

* 是否脱离文档流
  * 不脱离：`static/relative/sticky`
  * 脱离：`absoulte/fixed/sticky`
* 从参照物
  * `relatve`：是自己原本的位置(`static`)
  * `absoult`：先看自己的父元素有没有`position`属性(除`static`属性值)，有的话参照物就是父元素。如果没有就一直往上查找，直到`html`为止，参照浏览器窗口
  * `fixed`：参照物直接是浏览器窗口（视口）
* `relative/absoult/fixed`都可以和方位属性（`left/right/bottom/top`使用），也都可以和`z-index`一起使用

技巧：

* 只是为了让 方位/z-index 起作用，这个时候我们就可以选用相对定位（relative）
* 当只是让模板固定在浏览器窗口的某个位置（top/bottom/right/left）可以选中固定定位
* 为了定位一些图标，参照物还是父容器，选用`absoult`，并且给父容器设置`relative`

**对比`fixed`与`sticky`**

`position:sticky`粘粘定位与`position:fixed`固定定位；否可以在拖动滚动条的时候，将元素固定于指定位置，但是两者的区别是非常巨大的。

`position:fixed`：元素可以被固定于页面指定的位置，始终固定于此位置

* 定位位置可以通过`top、bottom、left与right`属性设置
* 如果属性同时设置，那么`top`的优先级高于`bottom`，`left`的优先级高于`right`
* `top、bottom、left与right`无需显示设置，`fixed`定位也会生效
* 元素的定位参考对象是当前窗口
* 元素是会脱离文档流，当前所有主流浏览器都支持此定位方式（不在考虑IE6浏览器）

`position:sticky`

* 元素可以被固定于页面指定位置，但并不一定始终固定此位置
* 定位位置可以通过`top、bottom、leftr、right`属性设置，但是此位置是一个临界值，也就是说元素只有达到设置的这个临界值才会固定，其他值位置并不会固定。
* 如果属性同时设置，那么top的优先级高于`bottom`，`left`的优先级高于`right`
* 必须至少显示设置`top/bottom/left/right`其中某一个属性值，`sticky`定位才会生效
* 元素的定位参考对象距离其最近的`overflow`属性值为`visible`的具有滚动条的祖先元素，如果是以`body`或者`body`的父辈元素为参考，那么定位对象是窗口。
* 元素根据情况判断其是否脱离文档流，浏览器兼容当前还是差

##### 层叠上下文

[技术博客](https://www.zhangxinxu.com/wordpress/2016/01/understand-css-stacking-context-order-z-index/)

层叠上下文含义：

* 层叠上下文（stacking context），是HTML中一个三维的概念。在`CSS2.1`规范中，每个盒模型的位置是三维的，分别是平面画布上的`X`轴，`Y`轴以及表示层叠的`Z`轴。一般情况下，元素在页面上沿着`X`轴`Y`轴平铺，我们察觉不到它们在`Z`轴上的层叠关系。而一旦元素发生堆叠，这时就能发现某个元素可能覆盖了另一个元素或者被另一个元素覆盖。
* 如果一个元素含有层叠上下文，（也就是说它是层叠上下文元素），我们可以理解为这个元素在`Z`轴上就**高人一等**，最终表现就是它离屏幕观察者更近。

层叠上下文具象理解：

> 层叠上下文是一个概念，跟【块状格式化上下文（BFC）】类型。然而，概念这个定向是比较虚，比较抽象的。
>
> 可以把【层叠上下文】理解为当官；网页中有很多的元素，我们可以看成是真实的世界的芸芸众生。真实世界中，我们大多数人是普通的老百姓，还有一部分人是 做官的官员。OK，这里的**官员**就可以理解为网页的层叠上下文元素。
>
> 换句话说，页面中的元素有了层叠上下文，就好比我们普通老百姓当了官，一旦当了官，相比普通老百姓而言，离皇帝更近了，对不对，就等同于网页中元素级别更高，离我们用户更近了。

![层叠上下文](/medias/imges/theory/css3/stacking-context.jpg)

##### 层叠水平

层叠水平决定了同一个层叠上下文中元素在`z`轴上的显示顺序。每个元素都有自己的层叠水平

* 层叠上下文元素的层叠水平可以理解为官员的职级，1品2品，县长省长之类
* 于普通元素的层叠水平，我们的探讨仅仅局限在当前层叠上下文元素中

需要注意的是，千万不要把层叠水平和`CSS`的`z-index`属性混为一谈。没错，某些情况下`z-index`确实可以影响层叠水平，但是，只限于定位源以及`flex`盒子的孩子元素；而层叠水平所有的元素都存在。

##### 层叠顺序

> **层叠顺序（stacking  order）** 表示元素发生层叠时候有着特定的垂直显示顺序，注意，这里跟上面两个不一样，上面的**层叠上下文和层叠水平是概念**，而这里的**层叠顺序是规则**。

![层叠顺序](/medias/imges/theory/css3/stacking-order.png)

> 大家是否想过为什么内联元素的层叠顺序要比浮动元素和块级元素都高？为什么呢？明明感觉浮动元素和块状元素要更屌一些啊。

由上图可知，诸如`border/background`一般为装饰属性，而浮动和块状元素一般用作布局，而内联元素都是内容，网页中最重要的是什么？当然是**内容**，对不对！

因此，一定要让内容的层叠顺序相当高，当发生层叠更好的去呈现内容，重要的文字啊图片内容可以优先暴露在屏幕上。例如：文字和浮动图片层叠的时候

当然上面的层叠顺序是`css2`的，如果把`css3`也牵引进来，那顺序就不一样了。下面会有说明

**层叠准则**

下面这两个是层叠领域的黄金准则。当元素发生层叠的时候，其层叠关系遵循下面2个准则：

* 谁大谁上：当具有明显的层叠水平标示的时候，如识别的`z-index`值，在同一个层叠上下文领域，层叠水平值大的那一个覆盖小的那一个。通俗讲就是官大的压死官小的。
* 后来居上：当元素的层叠水平一致，层叠顺序相同的时候，在DOM流中处于后面的元素会覆盖前面的元素

层叠上下文特性：

* 层叠上下文的层叠水平要比普通元素高
* 层叠上下文可以阻断元素的混合模式
* 层叠上下文可以嵌套，内部层叠上下文及其所有子元素均受制于外部的层叠上下文
* 每个层叠上下文和兄弟元素独立，也就是当进行层叠变化或渲染的时候，只需要考虑后代元素。
* 每个层叠上下文是自成体系的，当元素发生层叠的时候，整个元素被认为是在父层叠上下文的层叠顺序中

翻译成通俗语言就是：

* 当官的比老百姓更有机会面见圣上；
* 领导下去考察，会被当地官员阻隔只看到繁荣看不到真实民情；
* 一个家里，爸爸可以当官，孩子也是可以同时当官的。但是，孩子这个官要受爸爸控制。
* 自己当官，兄弟不占光。有什么福利或者变故只会影响自己的孩子们。
* 每个当官的都有属于自己的小团体，当家眷管家发生摩擦磕碰的时候（包括和其他官员的家眷管家），都是要优先看当官的也就是主子的脸色。

##### 固定定位（position fixed）的特殊性

* 单纯是**绝对/相对定位**元素是无法创建一个层叠上下文的，需要同时满足`z-index`的值不为`auto`的条件。然而，固定定位元素就不需要满足这个条件，显得尤为特殊。
* 固定定位元素无需满足`z-index`值不为`auto`的条件就可以创建层叠上下文；同样地，设置`z-index:auto`并不能撤销固定定位元素所创建的层叠上下文
* 正常情况下，固定定位时相对于浏览器视窗（`viewport`）进行定位的，但是当其祖先元素中存在符合以下任意一个条件的元素时，固定定位元素会相对于该元素进行定位：
  * `transform`属性不为`none`
  * `transform-style:preserve-3d`
  * `perspective`属性不为`none`
  * `will-change`属性指定了上面 3 个 CSS属性中的任意一个

> 然而，这种表现在不同的浏览器中有差异，譬如在`Safari`中只有上述第1点会对固定定位元素产生影响

建议：通过对层叠上下文和层叠顺序的了解，我们知道，要控制元素间的层叠关系除了使用`z-index`属性外还有很多途径，而且比使用`z-index`要优雅得多。滥用`z-index`往往只会把层叠关系复杂化，造成代码难以维护。

##### 层叠上下文的创建

[技术博客](https://developer.cdn.mozilla.net/zh-CN/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context)

> 层叠上下文（stacking context），它是一块区域，这块区域由某个元素创建，它规定了该区域中的内容在Z轴上排列的先后顺序。凡是拥有大的层叠水平的元素，将离用户最近，也就是越靠在`Z`轴前面。默认情况下只有根元素`html`会产生一个层叠上下文，并且元素一旦使用了一些属性也将会产生一个层叠上下文，如我们常用的定位属性。如两个层叠上下文相遇，总是后一个层叠前一个，**除非使用`z-index`来改变其层叠上下文的水平**

如同块状格式化上下文，层叠上下文也基本是有一些特定CSS属性创建的。我们将其总结分为3个流派，也就是做官的3种途径：

* **皇亲国戚**派：页面根元素天生具有层叠上下文，称之为“根层叠上下文”。
* **科考入选**派：z-index值为数值的定位元素的传统层叠上下文。
* **其他当官途径**：其他CSS3属性。

**根层叠上下文**

> 指的是页面根元素，也就是滚动条的默认的始作俑者`<html>`元素。这就是为什么，绝对定位元素在`left`/`top`等值定位的时候，如果没有其他定位元素限制，会相对浏览器窗口定位的原因。

* 文档根元素（`html`）

**定位元素与传统层叠上下文**

> 对于包含有`position:relative`/`position:absolute`的定位元素，以及FireFox/IE浏览器（不包括Chrome等webkit内核浏览器）（目前，也就是2016年初是这样）下含有`position:fixed`声明的定位元素，当其`z-index`值不是`auto`的时候，会创建层叠上下文。

* `position`值为`absoult`（绝对定位）或`relative`（相对定位）且`z-index`值不为`auto`的元素
* `position`值为`fixed`（固定定位）或`sticky`（粘滞定位）的元素（沾滞定位适配所有移动设备上的浏览器，但老的桌面浏览器不支持）

**CSS3与新时代的层叠上下文**

> CSS3的出现除了带来了新属性，同时还对过去的很多规则发出了挑战。例如，CSS3 `transform`[对overflow隐藏对position:fixed定位的影响](http://www.zhangxinxu.com/wordpress/2015/05/css3-transform-affect/)等。而这里，层叠上下文这一块的影响要更加广泛与显著。

* `flex`（flexbox）容器的子元素，且`z-index`值不为`auto`
* `grid`（grid）容器的子元素，且`z-index`值不为`auto`
* `opacity`属性值小于 1 的源
* `mix-blend-mode`属性值不为`normal`的元素
* 以下任意属性值不为`none`的元素
  * `transform`
  * `filter`
  * `perspective`
  * `clip-path`
  * `mask/mask-image/mask-border`
* `isolation`属性值为`isolate`的元素
* `-webkit-overflow-scrolling`属性值为`touch`的元素·
* `will-change `值设定了任一属性而该属性在 non-initial 值时会创建层叠上下文的元素；
* contain 属性值为 layout、paint 或包含它们其中之一的合成值（比如 contain: strict、contain: content）的元素。

##### 层叠上下文与层叠顺序

一旦普通元素具有了层叠上下文，其层叠顺序就会变高。那它的层叠顺序究竟在哪个位置呢？

这里需要分两种情况讨论：

1. 如果层叠上下文元素不依赖`z-index`数值，则其层叠顺序是`z-index:auto`可看成`z:index:0`级别；
2. 如果层叠上下文元素依赖`z-index`数值，则其层叠顺序由`z-index`值决定。

于是乎，我们上面提供的层叠顺序表，实际上还是缺少其他重要信息

![普通元素拥有了层叠上下文](/medias/imges/theory/css3/stacking-orderUp.png)

当普通的元素创建了层级上下文，那么它的层叠顺序就会发生质的变化，与`position:relative/absoult`的`z-index`不为`auto`，或者`position:fixed`的时候的等级

##### z-index与层叠顺序

> 如果元素支持z-index值，则层叠顺序就要好理解些了，比较数值大小嘛，小盆友都会，本质上是应用的“谁大谁上”的准则。在以前，我们只需要关心定位元素的z-index就好，但是，在CSS3时代，flex子项也支持`z-index`，使得我们面对的情况比以前要负复杂。然而，好的是，规则都是一样的，对于`z-index`的使用和表现也是如此，套用上面的7阶层叠顺序表就可以了。

![层叠上下文对z-index影响](/medias/imges/theory/css3/stacking-level.png)

##### 总结：

> 其实就是在同一个层叠上下文中，若子元素并无新的层叠上下文产生时，发生覆盖按层叠顺序来判断谁覆盖谁，当有新的层叠上下文产生，该元素的层叠水平就会被提升，按照新的层叠顺序该判断。本质就是根据那**两个层叠的黄金规则**

### icon全解析

* `img`及`HTML<area>`技术
* `background-icon`
* `icon-Sprites`
* `iconfont-HTML`
* `SVG-icon`
* `CSS绘制icon`

#### `img`及`HTML<area>`技术

图片格式的分类：

* 位图图片：`bmp、jpg、gif、png`

* 矢量图图片：以svg格式为代表，可缩放矢量图形（Scalable Vector Graphics），svg是一种使用XML格式定义的图像

传统方式的图标：

图标采用图片格式，有`bmp、jgp、gif、png`等文件格式，使用时用`<img>`标签引入，其最大的缺点就是：大小改变后，可能出现锯齿效果，影响美观；

```html
<div>
    <img src="xxx" class="icon" />
</div>
```

HTML`<area>`标签：

* `<area>`标签定义图像映射中的区域（注：图像映射指得是带有可点击区域的图像）

* `<area>`元素总是嵌套在`<map>`标签中

* `<img>`标签中的`usemap`属性与`map`元素`name`属性相关联，创建图像与映射之间的联系。

* 所有主流浏览器都支持`<area>`标签

* 一款在线工具`Image-Maps`:http://www.image-maps.com/ 

  我们只需在`Image-Maps`上注册一个账号，就能够通过可视化工具对图片进行裁剪，获取裁剪的图片坐标

```html
<img src="planets.jpg" border="0" usemap="#planetmap" alt="Planets" />

<map name="planetmap" id="planetmap">
    <area shape="circle" coords="180,139,14" href ="venus.html" alt="Venus" />
    <area shape="circle" coords="129,161,10" href ="mercur.html" alt="Mercury" />
    <area shape="rect" coords="0,0,110,260" href ="sun.html" alt="Sun" />
</map>
```

#### `background-icon`

将图标的显示方式从`<img>`插入的方式，演变成用背景图片去插入，其实个人看法与`<img>`插入并未本质区别，但我们要熟悉知道`background`的八大属性



| 属性名                  | 描述                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `background-color`      | 背景颜色：`red,十六进制,rgba()`                              |
| `background-image`      | 背景图片：`url()函数导入`                                    |
| `background-repeat`     | 平铺方式：`no-repeat(不平铺)、repeat-x、repeat-y`            |
| `background-position`   | 背景图在容器的显示位置                                       |
| `background-attachment` | 背景是否随着滚动条滚动，滚动`scroll`，固定不动`fixed`        |
| `background-size`       | 设置背景尺寸，`px,%,cover(宽高等比例缩放自适应),contain(宽或高进行缩放自适应)` |
| `background-origin`     | 设置背景显示的元素位置`border-box,padding-box,center-box`    |
| `background-clip`       | 背景的裁剪方式，`border-box,padding-box,center-box,          |

> 前五个是`css2`的书写，后三个是`css3`增加的，前五个可以缩写为`background:颜色 图片路径 是否平铺 背景图片在容器的显示方位 背景图是否随着滚动条滚动`

```html
<style>
    li{
        list-style: none;
    }
    .icon{
        width: 50px;
        height: 50px;
        background-repeat: no-repeat;

        background-color: green;
        background-position: center center;
        background-size: contain;
        background-origin: ;
    }
</style>

<ul>
    <li>
        <div class="icon" style="background-image:url(images/icon-wechat.png)"></div>
        <div class="txt"></div>
    </li>
    <li>
        <div class="icon" style="background-image:url(images/icon-rp.png)"></div>
        <div class="txt"></div>
    </li>
    <li>
        <div class="icon" style="background-image:url(images/icon-credit.png)"></div>
        <div class="txt"></div>
    </li>
</ul>
```

#### icon-sprites

> 该技术的全称是：`CSS Sprite`，又称为CSS雪碧图（精灵图），是网页中图片的一种处理方式，即将网页中所应用的比较小的图片合并为一个大图片
>
> * 优点：减少`http`请求次数，使页面的加载速度更快，提高了网页的性能
> * 缺点：先设置标签背景图片，在通过精灵图中小图标的宽高和位置，设置对应的`div`宽高和背景定位的坐标，要进行图片图标的测量，操作相对繁琐
>
> 生成工具：CSS sprites Generator,直接生成雪碧图，http://css.spritegen.com/

步骤：

* 测量需要的图标在雪碧图中的**宽高以及其左上角的在图标的X,Y轴的方位信息**
* 将雪碧图导入到容器中去，根据上一步的图标`X,Y`的方位信息，通过背景定位`background-position`进行定位到需要的图标身上
* 这里的定位置值一定要是**负值**，因为图标一定是在原来的圆点的左侧或下侧

```html
<style>
    body{
        background-color: #000;
    }
    .search{
        width: 35px;
        height: 47px;
        background: url(images/index_z_0348131.png) no-repeat;
        /* 背景坐标点: x  y */
        background-position: -380px -174px;
    }

    .search1{
        width:48px;
        height: 48px;
        background: url(images/sprites-baidu.png) no-repeat;
        background-position: 0 -204px;
    }
</style>
<body>
    <div class="search"></div>
    <div class="search1"></div>
</body>
```

#### @font-face

目前很多页面中的图标都是使用字体图标`@font-face`完成的，https://www.zhangxinxu.com/wordpress/2017/03/css3-font-face-src-local/ 

优势：

* 矢量化：字体是矢量格式，因此能够轻松的适配不同的设备，而不必为不同分辨率的屏幕准备不同的图片资源

* 轻量化：一个图标字体比一系列的图像（特别是在`Retina`屏中使用双倍图像）要小。一旦图标字体加载了，图标就会马上渲染出来，不需要下载一个图像。可以减少HTTP请求，还可以配合`HTML5`离线存储做性能优化

* 可扩展性：图标字体可以使用`font-size`属性设置其任意大小，这使您能够随时输出不同大小的图标。

* 灵活性：文字效果可以很容易地应用到你的图标上，包括颜色、阴影和翻转等效果。他们还可以在任意背景下显示

* 兼容性：页面字体支持现代所有浏览器，包括`IE低版本`【 

  http://caniuse.com/#feat=fontface】

弊端：

* 图标字体只能被渲染成单色或者CSS3的渐变色，由于此限制使得它不能广泛使用。
* 使用版权上有限制，有好多字体是收费的。当然也有很多免费开源的精美字体图标供下载使用。
* 创作自己的字体图标很耗时间，重构人员后期维护成本偏高。

 常用的字体图标库

* 阿里字体图标库：http://iconfont.cn/
* Font Awesome：http://fontawesome.dashgame.com/ 
* icomoon：http://icomoon.io/app/ 
* fontello：http://fontello.com/ 

```css
font-face{
    font-family: 'YourWebFontName';
    src: url('YourWebFontName.eot'); /* IE9 Compat Modes /
    src: url('YourWebFontName.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
    url('YourWebFontName.woff') format('woff'), /* Modern Browsers */
    url('YourWebFontName.ttf')  format('truetype'), /* Safari, Android, iOS */
    url('YourWebFontName.svg#YourWebFontName') format('svg'); /* Legacy iOS */
}
.iconfont {
    display: block;
    font-family: "iconfont" !important;

    font-size: 20px;

    font-style: normal;

    -webkit-font-smoothing: antialiased;

    -moz-osx-font-smoothing: grayscale;
}
```

自定义字体格式

* `.ttf`：（TrueType格式）是`Windows`和`Mac`的常见字体，是一种`RAW`格式，因此他不为网站优化，支持这种字体的浏览器有`IE9+,Firefox3.5+,Chrome4+,Safari3+,Opera10+,iOS Mobile Safari4.2+`
* `.woff`：（Web Open Font格式）：针对网页进行特殊优化，Webz字体中最佳格式，他是一个开放的`TrueType/OpenType`的压缩版本，同时也支持元数据包的分离，支持这种字体的浏览器有`IE9+,Firefox3.5+,Chrome6+,Safari3.6+,Opera11.1+`
* `.eot`：（Embedded Open Type格式）：字体是`IE`专用字体，可以从`TrueType`创建此格式字体，支持这种字体的浏览器有`IE4+`
* `.svg`：（SVG格式）：是基于SVG字体渲染的一种格式，支持这种字体的浏览器有`Chrome4+,Safari3.1+,Opera10.0+,iOS Mobile Safari3.2+`

字体图标工具

* FontCreator 通过这款工具可以制作新的字体图标库或查看现有字体图标库的信息

#### 图标的显示

##### iconfont-HTML（unicode）

> 利用HTML中实体名称/实体编号显示图标的原理，添加自定义字体`icon`

优点：轻量性、灵活性、兼容性好（支持IE6+），是目前被广泛使用的技术

缺点：大多数的图标字体只能被渲染成单色；需要在HTML中使用编号，在不同的设备浏览器或系统中字体的渲染会略有差别，其显示的位置和大小可能受到`font-size、line-height、word-spacing`等`css`属性的影响，而且这种影响调整起来较为困难。

**Unicode 是字体在网页端最原始的应用方式，特点是**

* 兼容性最好，支持`IE6+`，及所有现代浏览器
* 支持按字体的方式去动态调整图标大小，颜色等等，但是因为是字体，所有不支持多色。只能使用平台里单色的图标，就算项目里有多色图标也会自动去色。
* 注意：新版 `iconfont`支持多色图标，这些多色图标在`Unicode`模式下将不能使用，如果有需求建议使用`symbol`的引用方式

Unicode使用步骤如下：

* 第一步：拷贝羡慕下面生成的`@font-face`

```css
@font-face {
  font-family: 'iconfont';
  src: url('iconfont.eot');
  src: url('iconfont.eot?#iefix') format('embedded-opentype'),
      url('iconfont.woff2') format('woff2'),
      url('iconfont.woff') format('woff'),
      url('iconfont.ttf') format('truetype'),
      url('iconfont.svg#iconfont') format('svg');
}
```

* 第二步：定义使用`iconfont`的样式，共有样式

```css
.iconfont {
    font-family: "iconfont" !important;
    font-size: 16px;
    font-style: normal;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}
```

* 第三步：挑选相应图标并获取字体编码，应用页面

```html
<span class="iconfont">&#x33;</span>
```

> 一定要加上公用类名`iconfont`，否则`unicode`编码不起作用

##### iconfont-css（font-class）

> 原理和`unicode`其实是一样中，值不过是在`CSS`通过伪类的方式将字体显示出来，只是`HTML-Unicode`编码中`&#X`在CSS要换成`\`，与`unicode`使用方式相比，具有如下的特点
>
> * 兼容性好，支持`ie8+`
> * 相比于`unicode`语义明确，书写更直观，可以很容易分辨直观`icon`是什么图标，因为使用`class`来定义图标，类名就明确了该类名是指定哪个图标显示，并且当要替换图标时，只需要修改`class`里面的`Unicode`引用。包裹因为本质上还是使用字体，使用多色图标还是不支持的
>
> 所以：`font-class`是`unicode`使用方式的一种变种，主要是解决 `Unicode`书写不直观，语言不明确的问题

使用步骤如下：

* 第一步：引入项目下面生成的`fontclass`代码

```css
@font-face {font-family: "iconfont";
    src: url('iconfont.eot?t=1595234030574'); /* IE9 */
    src: url('iconfont.eot?t=1595234030574#iefix') format('embedded-opentype'), /* IE6-IE8 */
        url(iconfont.woff2?t=1595234030574) format('woff2'),
        url('iconfont.woff?t=1595234030574') format('woff'),
        url('iconfont.ttf?t=1595234030574') format('truetype'), /* chrome, firefox, opera, Safari, Android, iOS 4.2+ */
        url('iconfont.svg?t=1595234030574#iconfont') format('svg'); /* iOS 4.1- */
}

.iconfont {
    font-family: "iconfont" !important;
    font-size: 16px;
    font-style: normal;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}

.icon-youjian:before {
    content: "\ec01";
}

.icon-sousuo:before {
    content: "\e632";
}

.icon-ai66:before {
    content: "\e6b8";
}

.icon-yuyue:before {
    content: "\e606";
}

.icon-help:before {
    content: "\e619";
}
```

* 第二步：跳转相对应图标并获取类名，应用于页面

```html
<span class="iconfont icon-xxx"></span>
```

> `iconfont`是公有类名一定要写，否则会出现编码错误，icon-xxx是你对应需要的图标类名

##### SVG-icon

SVG的扩展：

* 它的英文全称为`Scalable Vector Graphics`，意思为可缩放的矢量图形；
* 用来定义用于网络的矢量的图形，用于定义`XML`格式定义图形
* 图像在放大或改变尺寸的情况下其图形质量不会有所损失，

* 其也是万维网联盟的标准，与诸如`DOM`和`XSL`之类的`W3C`标准是一个整体

SVG的优势：

* SVG可被非常多的工具读取和修改（比起记事本），文件是纯粹的`XML`
* SVG与`JPEG`和`GIF`图像比起来，尺寸更小，且可压缩性更强
* SVG是可伸缩的
* SVG图像可在任何的分辨率下被高质量地打印，并且可在图像质量不下降的情况下被放大
* SVG图像中的文本是可选的，同时也是可搜索的（很适合制作地图）

> SVG矢量官网：
>
> - 阿里巴巴矢量图标库官网  [https://www.iconfont.cn/](https://www.iconfont.cn/)
> - iconmoon图标库官网      [https://icomoon.io/#home](https://icomoon.io/#home)

SVG-icon实现方法：

* `lnline SVG`

> 这种方法就是直接把`SVG`标签写入到`HTML`中去，直接通过修改`fill`和`stroke`属性来控制填充颜色和边框颜色，但是缺点就是维护性不好，如果一个页面`Icon`特别多，可能要写好几十个SVG页面，复用性差，后期扩展性也不佳。

```html
<svg width="74"  height="74" viewBox="0 0 74 74">
	<path fill="#444444" d="xxxx" ></path>
</svg>
```

* `img/Object`标签

> 这种方法直接将`SVG ICON`保存成一个一个单独文件，通过`img或object`标签引用，它的缺点就请求数增加，每个图标都要去单独加载，对服务器负载和页面优化不好

```html
<img src="svg/icon.svg" alt="" />
```

* `Background And Data URLs`

> 采用`background`去调用SVG文件，如果单独使用`background`引用`SVG`也会和上一种方案引用造成请求数增加，所以有不少人采用`base64`编码来减少`HTTP`请求。

```css
.icon {
    background:url(icon.svg);
}
```

* SVG Sprites

> 目前市面上有很多提供`ICON FONT`制作的网站，如`icomoom`不止开源，而且功能实在强大，可以提供输出`SVG Sprites`的功能，`SVG Sprites`它的使用方法其实就跟`Png Sprites`一样的，把多个`SVG ICON`合并到一个`SVG`文件里面去，然后通过`background-position`进行定位，这种方法可以解决请求数量增多的问题。

```css
.icon {
    width:16px;
    height:16px;
    display:inline-block;
    background-repeat:no repeat;
    background-image:url(sprite.svg);
    background-position:0 0 ;
}
```

* SVG Defs/Symbols

原理

> 通过`JS`在页面中绘制出这个矢量图像（js中存在如何绘制的方法），这是一种全新的使用方式，应该说这才是未来的主流，也是各大平台目前推荐的用法。相关介绍可以参考这篇[文章]() 这种用法其实是做了一个 SVG 的集合，与另外两种相比具有如下特点：
>
> * 支持多色图标了，不再受单色限制
> * 通过一些css技巧，支持像字体那样，通过css来调整样式
> * 兼容性较差，支持`IE9+`及现代浏览器
> * 浏览器渲染`svg`的性能一般，还不如`png`

使用步骤：

* 第一步：引入项目生成的`symbol`代码，里面就公用样式

```html
<script src="./iconfont.js"></script>
```

* 第二步：加入通用的`css`代码（引入一次即可），来整体调整svg代码样式

```html
<style>
    .icon {
        width: 1em;
        height: 1em;
        vertical-align: -0.15em;
        fill: currentColor;
        overflow: hidden;
    }
</style>
```

* 第三步：挑选相应图标并获取类名，应用于页面

```html
<svg class="icon" aria-hidden="true">
    <use xlink:href="#icon-xxx"></use>
</svg>
```

> 这里的`#icon-xxx`是指的要显示的图标symbol对应的名字

### 动画

对动画细节和原理深入解析，`transform`全解，`transition`过渡，`animation`动画与交互，关键帧动画`steps`功能符深入解析

动画的原理：视觉暂留作用，画面逐渐变化

动画的作用：愉悦感，引起注意，掩饰

动画的类型：

* `transition`补间动画
  * 位置 -- 平移（left/right/margin/transform:translate）
  * 方位 -- 旋转（transform:rotate）
  * 大小 -- 缩放（transform:scale）
  * 透明度（opacity）
  * 其他 -- 线性变化（transform）
* `keyframes`关键帧动画
  * 相当于多个补间动画
  * 与元素状态的变化无关
  * 定义更加灵活
* 逐帧动画
  * 适用于无法补间计算的动画
  * 资源较大
  * 使用`steps()`函数

#### transform全解

| 属性             | 内容                                   |
| ---------------- | -------------------------------------- |
| `translate(x,y)` | 定义`2D`转换，沿着`X`和`Y`轴移动元素   |
| `translateX(n)`  | 定义`2D`转换，沿着`X`轴移动元素        |
| `translateY(n)`  | 定义`2D`转换，沿着`Y`轴移动元素        |
| `scale(x,y)`     | 定义`2D`缩放转换，改变元素的宽度和高度 |
| `scaleX(n)`      | 定义`2D`缩放转换，改变元素的宽度       |
| `scaleY(n)`      | 定义`2D`缩放转换，改变元素的高度       |
| `rotate(angle)`  | 定义`2D`旋转，在参数中规定角度         |

**translate位移**

* `translate()`：指定对象的`2D translation`(2D平移)。第一个参数对应X轴，第二个参数对应Y轴，如果第二个参数未提供，则默认值为`0`
* `translateX()`：定义对象`X`轴（水平方向）的平移
* `translateY()`：定义对象`Z`轴（垂直方向）的平移

```css
.item:hover {
    transform: translate(0, -10px);
}
```

#### rotate旋转

* `rotate(angle)`：指定对象的`2D` rotation（2D旋转）在二维平面上顺时针或者逆时针旋转，可以与`transform-origin`属性的配合使用定义旋转原点；角度为正，顺时针，角度为负，逆时针，默认旋转的中心点是元素的中心点
* `rotateX(angle)`：对X轴进行`angle`度旋转
* `rotateY(angle)`：对Y轴进行`angle`度旋转
* `rotateZ(angle)`：对Z轴进行`angle`度旋转

```css
.item2:hover {
    transform: rotate(45deg);
}
```

#### scale缩放

* `scale(x,y)`：指定对象的`2D scale`（2D缩放）。第一个参数对应`X`洲，第二个参数对应`Y`轴。如果第二个参数未提供，则默认取第一个参数的值，取值大于1，那就是元素放大，小于1就是缩小
* `scaleX()`指定对象`X`轴的（水平方向）缩放
* `scaleY()`指定对象`Y`轴的（垂直方向）缩放

```css
.item1:hover img {
    transform: scale(1.5);
}
```

#### form-origin元素变换基点

* 设置或检索对象以某个原点进行旋转，所以其常与`transform:rotate()`属性一起使用

* 该属性提供2个参数值

  * 如果提供了两个，第一个用于横坐标，第二个用于纵坐标
  * 如果只提供了一个，该值将用于横坐标；纵坐标将默认为`50%`
  * 默认值为：`50% 50%`，效果等同于`center center`
  
* 语法：`transform-origin(x,y,z)`

  | 值   | 描述                                                         |
  | ---- | ------------------------------------------------------------ |
  | x    | 定义视图被置于`X`轴的何处，可能值为`left/center/right/length/%` |
  | y    | 定义视图被置于`Y`轴的何处，可能值为`top/center/bottom/length/%` |
  | z    | 定义视图被置于`Z`轴的何处，可能值为`length`                  |

```html
<style>
    .item2{
        width: 200px;
        height: 200px;
        background-color: hotpink;
        transform-origin:left bottom;
        transition: all 1.5s ease-in-out;
    }
    .item2:hover{
        transform: rotate(45deg);
    }
</style>
<div class="item2"></div>
```

#### tansition过渡【补间动画】

> 上面的`transform`属性只能是让元素进行**僵硬**的变换，没有过渡效果，所以一般都是将其配合`transition`过渡属性来产生动画效果，`transiton`的是在定义动画的元素上写，任何css属性变化都能用`transition`来做过渡效果

| 属性                        | 描述                                       | css  |
| --------------------------- | ------------------------------------------ | ---- |
| `transition`                | 简写属性，用于自一个属性中设置四个过渡属性 | 3    |
| `transition-property`       | 规定应用过渡的`css`属性的名称              | 3    |
| `transition-duration`       | 定义过渡效果花费的时间，默认是`0`          | 3    |
| `transition-timing-funtion` | 规定过渡效果的时间曲线，默认是`ease`       | 3    |
| `transition-delay`          | 规定过渡效果何时开始，默认是`0`            | 3    |

* `transition`：复合属性，检索或设置对象变换时的过渡。

注意：

1. 如果只提供了一个参数，则为`transition-duration`的值定义；
2. 如果提供两个参数，则第一个为`transition-duration`的值定义，第二个为`transition-delay`的值含义
3. 可以为同一元素的多个属性定义过渡效果，如果需要定义多个过渡属性且不想指定具体是哪些属性过渡，可以用`all`来代替

```css
.item {
    transition: all 1s ease 1s;  
    transition: all 1s cubic-bezier(.17, .67, 1, .24) 1s;    
}
```

* `transtion-property`：检索或设置对象中参与过渡的属性
  1. 默认值为`all`，默认代表着所有可以进行过渡的`css`属性
  2. 如果提供多个属性值，以**逗号**进行分隔，可以取值如下
     * `none`：不指定过渡的`css`属性
     * `all`：所有可以进行过渡的`css`属性
     * `css property`：指定要进行过渡的`css`属性

```css
.item {
    transition-property: opacity, width;
    transition-duration: 1s;
    transition-timing-function: ease;
    /*可以简写如下*/
    transition: opacity, width 1s ease;
}
```

* `transition-duration`：检索或设置对象过渡的持续时间，默认值为`0`，意味着没有动画效果，可以设置多值，但`transition`这时是不能采用`all`来代表所有属性的方式，`transition-duration`的值与`transition-property`的值一一对应

```css
.item {
    transition-duration:1s;
}
```

* `transition-timing-function`：检索或设置对象中过渡的动画效果，默认值为`ease`【平滑过渡】，如果提供多值，应以逗号进行分隔，`transition`不能以`all`代替所有属性，`transition-timing-function`的取值与`transition-property`一一对应，常用取值如下：
  1. `linear`：线性过渡/匀速过渡，等同于⻉塞尔曲线(0.0, 0.0, 1.0, 1.0)
  2. `ease`：平滑过渡，等同于⻉塞尔曲线(0.25, 0.1, 0.25, 1.0) 
  3. `ease-in`：由慢到快，等同于⻉塞尔曲线(0.42, 0, 1.0, 1.0) 
  4. `ease-out`：由快到慢。等同于⻉塞尔曲线(0, 0, 0.58, 1.0) 
  5. `ease-in-out`：由慢到快再到慢。等同于⻉塞尔曲线(0.42, 0, 0.58, 1.0)

  6. `cubic-bezier(<number>, <number>, <number>, <number>)`： 特定的⻉塞尔曲线类型，4个数值需在[0, 1]区间内 
     * https://matthewlein.com/tools/ceaser 
     * https://cubic-bezier.com/#.17,.67,.83,.67 ⻉塞尔曲线 

```css
.item {
    transition-timing-function: ease, cubic-bezier(.17, .67, 1, .24);
}
```

* `transition-delay`：检索或设置对象延迟过渡时间，默认值是`0`
  1. 如果设置多个值，以逗号进行分隔，这时`transition`不能采用`all`来代替所有属性，因为`transition-delay`的值要与`transition-property`一一对应

```css
.item {
    transition-delay: 1s; 
}
```

#### animation动画交互【关键帧动画】

| 属性                        | 描述                                                   |
| --------------------------- | ------------------------------------------------------ |
| `@keyframes`                | 规定动画                                               |
| `animation`                 | 所有动画属性的简写属性，除了`animation-play-state`属性 |
| `animation-name`            | 规定`@keyframes`动画名称                               |
| `animation-duration`        | 规定动画完成一个周期所花费的秒或毫秒，默认是`0`        |
| `animation-timing-function` | 规定动画的速度曲线，默认是`ease`                       |
| `animation-delay`           | 规定动画何时开始，默认是`0`                            |
| `animation-lteration-count` | 规定动画被播放的次数，默认是`1`                        |
| `animation-direction`       | 规定动画是否在下一个周期逆向地播放，默认是`normal`     |
| `animation-play-state`      | 规定动画是否正在运行或暂停，默认是`running`            |
| `animation-fill-mode`       | 规定对象动画时间之外的状态                             |

动画的定义步骤：

1. 先用`@keyframes`定义动画的帧
2. 在将定义好的动画通过`aniamtion`运用到元素上

* `@keyframes`定义动画帧
  1. 指定动画名称和动画效果
  2. `@keyframes`定义的动画名称用来被`animation-name`所使用
  3. 定义动画时，简单的动画可以直接使用`from==0%`和`to==100%`，即从一种状态过渡到另一种状态；

```css
@keyframes test {
    0%{opacity:1;}
    50%:{opacity:.5;}
    100%:{opacity:1}
}
```

* `animation`：  所有动画属性的简写属性，除了`animation-play-state`属性，其他顺序的书写可以不按顺序，但取值有以下几个注意点
  1. 当有数值并且是带`s`单位，如果只有一个，那就是**动画的持续时间**，如果有两个，第一个是**动画的持续时间**，第二个是**动画的延迟时间**
  2. 当数值没有`s`单位，那它一定是动画的循环次数

* `animation-name`：检索或设置对象所应用的动画名称，必须与规则`@keyframes`配合使用，因为动画名称由`@keyframes`定义，取值可以是
  1. `none`：不引用如何动画名称
  2. `identifier`：定义一个或多个动画名称（identifier标识）

```css
.item {
    animation-name:test;
}
```

* `animation-duration`：检索或设置动画的持续时间，默认值为`0`，
  1. 设置`time`,指定对象动画的持续时间

```css
.item {
    animation-duration:1s;
}
```

* `animation-timing-function`：检索或设置对象动画的过渡类型，默认是`ease`
  1. `linear`：线性过渡/匀速过渡，等同于⻉塞尔曲线(0.0, 0.0, 1.0, 1.0)
  2. `ease`：平滑过渡，等同于⻉塞尔曲线(0.25, 0.1, 0.25, 1.0) 
  3. `ease-in`：由慢到快，等同于⻉塞尔曲线(0.42, 0, 1.0, 1.0) 
  4. `ease-out`：由快到慢。等同于⻉塞尔曲线(0, 0, 0.58, 1.0) 
  5. `ease-in-out`：由慢到快再到慢。等同于⻉塞尔曲线(0.42, 0, 0.58, 1.0)

  6. `cubic-bezier(<number>, <number>, <number>, <number>)`： 特定的⻉塞尔曲线类型，4个数值需在[0, 1]区间内 
     * https://matthewlein.com/tools/ceaser 
     * https://cubic-bezier.com/#.17,.67,.83,.67 ⻉塞尔曲线 
  7. `steps(分几段,[start,end])`
     * 接收两个参数的步进函数。第一个参数必须是正整数，指定函数的步数。第二个参数取值可以是`start/end`，指定每一步的值发生变化的时间点。第二个参数是可选的，默认是`start`
     * `step-start`：等同于`steps(1,start)`
     * `step-end`：等同于`steps(1,end)`

```css
.itme {
    animation-timing-function: ease;
}
```

* `animation-delay`：检索或设置对象动画的延迟时间，默认值是：`0s`
  1. 设置`time`，指定对象动画延迟时间

```css
.item {
    animation-delay: .2s;
}
```

* `animation-lteration-count`：检索或设置对象动画的循环次数，取值如下
  1. `infinite`：无限循环
  2. `number`：指定对象动画的具体循环次数

```css
.item {
    animation-lteration-count:1;
}
```

* `animation-direction`：检索或设置对象动画在循环中是否反向运动
  1. `normal`：正常方向
  2. `reverse`：反向方向
  3. `alternate`：动画先正常运行再反方向运行，并持续交替运行
  4. `alternate-reverse`：动画先反运行再正常运行，并交替运行

```css
.item {
    animation-direction:alternate-reverse;
}
```

* `aniamtion-play-state`：检索或设置对象动画的状态，取值如下：
  1. `running`：运动
  2. `paused`：暂停

```css
.item {
    animation-play-state:paused;
}
```

* `animation-fill-mode`：检索或设置对象动画时间之外的状态，取值如下：
  1. `none`：默认值，不设置对象动画之外的状态
  2. `forwards`：设置对象状态为动画结束时的状态，也就是停留在动画结束位置
  3. `backwards`：设置对象状态为动画开始时的状态，也就是停留在动画开始的位置
  4. `both`：设置对象状态为动画结束或开始的状态，看动画最后在哪个位置就停留在哪个位置

```css
.item {
    aniamtion-fill-mode:forwards;
}
```

#### steps()功能符

[技术博客](https://www.zhangxinxu.com/wordpress/2018/06/css3-animation-steps-step-start-end/?shrink=1)

一句话介绍`steps()`功能符：`steps()`功能符可以让动画不连续

`stpes()`功能符合`css3 animation`中的`cubic-bezier()`功能符的地位和作用是一样的，都可以作为`animation-timing-function`的属性值，只不过`steps()`更像是楼梯坡道，`cubic-bezier()`更像无障碍坡道。

然后`steps()`简化出了`step-start`和`step-end`这两个关键字；`cubic-bezier()`则有`linear`，`ease`，`ease-in`，`ease-out`以及`ease-in-out`

**搞清楚steps()中的start和end**

`steps()`有一定的学习难度，总是搞不清楚，最主要就是`start`和`end`傻傻分不清楚。

常见`steps()`用法举例

```css
.itme {
    aniamtion-timing-function:steps(5,end);
    aniamtion-timing-function:steps(5,start);
}
```

用`steps()`语法表示就是

```
step(number,position)
```

* number：数值，这个很好理解，表示我们的动画分成了多少段

假设有如下CSS3动画`keyframes`，定义了一段从`0~100px`的位移：

```css
@keyframes move {
    0% { left: 0; }
    100% { left: 100px; }  
}
```

假设我们的`number`值是5，则相当于把这段移动距离分成了5端，如下示意图

![steps-number](/medias/imges/theory/css3/steps-number.png)

* position：关键字。表示动画从时间段的开头连续还是末尾连续。支持`start`和`end`两个关键字，hay分别为
  * `start`：表示直接开始
  * `end`：表示戛然而止。是默认值。

**为什么position非常难以理解**

我认为两个原因：

1. `steps()`属于timing function，也就是时间函数，时间这个东西是虚的，看不见，摸不着，联想乏力，所以认知成本高。这也是为什么那么多人都不珍惜时间的原因——无法感知。
2. CSS规范中对于`start`和`end`的定义是基于数学函数来的，函数这东西，多少人的噩梦，因为过于抽象，与现实难以关联，所以，如果我们盯着定义去理解`start`和`end`，那就是死胡同，不归路，就算现在弄懂了，过段时间再遇到，得了，全忘光光了，函数图哪个是哪个，鬼才记得。下面这张图就出自[规范文档](https://www.w3.org/TR/css-timing-1/#step-timing-functions)：

![steps-position](/medias/imges/theory/css3/steps-position.png)

按照规范图在细化解释极速：

* `start`：表示直接开始。也就是时间才开始，就已经执行一个距离段。于是动画执行的5个分段点是下面这5个，起点被忽略，因为时间一开始直接就到了第二个点：

![steps-start](/medias/imges/theory/css3/stpes-start.jpg)

* `end`：表示戛然而止。也就是时间一结束，当前距离位移就停止。于是，动画执行的5个分段点是下面这5个，结束点被忽略，因为等要执行结束点的时候，已经没时间了；

![steps-end](/medias/imges/theory/css3/stpes-end.jpg)

**基于现实感知重新理解position**

万物具有相对性。例如，苍蝇眼中的人类动作都是慢动作，但是人类眼中的苍蝇却非常敏捷。

同样的，`start`和`end`这里的开始和结束是相对于时间而言的，但是，如果站在人类可感知的具体事物而言，`start`和`end`却是相反的含义。

所以，我们可以这么理解：

* `start`：表示结束。分段结束的时候，时间才开始走。于是，动画执行的5个分段点是后5个点
* `end`：表示开始。分段开始的时候，时间跟着一起走。于是，动画执行的5个分段点是前5个点

### CSS工作原理和性能优化

内容：CSS布局模型，BFC的原理和功能，IFC的原理和功能，理解`font-size\line-height\vertical-align`，CSS渲染和解析原理，CSS性能优化

CSS布局模型：流行模型、浮动模型、层模型

#### 流动模型（Flow）

> 含义：
>
> * 流动（Flow）是默认的网页布局模型，也就是说网页在默认状态下的`HTML`网页元素都是根据流动模型来分布网页内容的
>
> 特征：
>
> * 块状元素都会在所处的包含元素内自上而下按顺序垂直延伸分布，因为在默认状态下，块状元素的宽高都为`100%`。实际上，块状元素都会以行的形式占据位置
> * 在流动模型下，内联元素都会在所处的包含元素内从左到右水平分布显示（内联元素可不像块状元素这么霸道独占一行）

应用技巧：

* 当布局中，我们想要元素独占一行就可以选用块级元素，或者将行内或行内块元素用样式进行转换`display:block/list-item/table...`
* 当布局中，我们想要多个元素同一行显示，可以选中行内或行内块元素或将块元素用样式进行转化`display:inline/inline-block`，但`display:inline-block`具有兼容性问题，现在开发基本用`float`浮动来代替，因为`float`元素的具有行内块的特点

#### 浮动模型（Float）

> * 通过`css`的`float`属性可以将元素设置为浮动元素。元素浮动之后不再占据原来的位置，它们会尽可能的往包裹它们的父元素的左边框或右边框靠，并会在它们元素所处位置的下面产生浮动流，影响下面的元素定位。
> * 浮动元素并没有完全脱离文档流，它只是从包裹它的盒子中浮动起来尽可能远的往左侧或者右侧进行移动
> * 浮动设计的初衷是为了实现文字在图片周围的环绕效果

应用技巧：浮动元素可以拥有行内块`inline-block`的特点，但其也会有缺点，那就是导致父元素的高度塌陷，从而影响页面布局效果，因此我们要进行浮动清除，常用的四种技巧：

* 强制给父元素固定宽高
* `overflow:hidden`触发`BFC`
* 运用css提供的`clear:both`

#### 层模型（Layer）

> 含义：
>
> * 层布局模型就像图像软件`photoShop`中非常流行的图层编辑功能一样，每个图层能够精确定位操作，但在页面设计领域，由于网页大小的活动性，层布局没能受到热捧。但是在页面上局部使用层布局还是有其方便之处的
>
> 层模型有三种形式：
>
> * 相对定位（`position:relative`）
> * 绝对定位（`position:absoult`）
> * 固定定位（`position:fixed`）

应用技巧：清楚认知`relative/absoult/fixed`三者的特点进行布局

#### BFC的原理和功能

https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context

BFC含义：

> * MDN的定义：块格式化上下文（Block Formatting Context，BFC）是Web页面的可视化`CSS`渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。
>
> * 表现原则：如果一个元素具有`BFC`，内部子元素在怎么翻江倒海，翻云覆雨，都不会影响外面的元素。所以，BFC元素是不可能发生`margin`重叠的，因为`margin`重叠是会影响外面的元素的；BFC元素也可以用来清除浮动的影响，因为如果不清除，子元素浮动则父元素高度塌陷，必然影响后面元素布局和定位，这显然有违BFC元素的子元素不会影响外部元素的设定。
>
> * 触发BFC的条件
>
>   * `<html>`根元素
>   * `float`的值不为`none`
>   * `overflow`的值为`auto、scroll或hidden`
>   * `display`的值为`table-cell、table-capation、和inline-block`中任何一个
>   * `position`的值不为`relative`和`static`
>
>   **所以，只要元素符合上面任意一个条件，就无须使用`clear:both`属性去清除浮动的影响了。**

BFC应用：

* 案例一：让浮动元素内容和周围的内容登高，也就是清除浮动
* 案例二：解决块级容器的外边距重叠问题，让两个块级容器产生各自的`BFC`空间即可。并且`margin`对内联替换元素（或为`display:inline-block`）元素有作用但不会发生外边距重叠问题。
* 案例三：自适应的两列布局（左图右问）效果：给父元素开启BFC，因为BFC区域不含与`float box`重叠

关于BFC的其他使用以及影响，建议去看<<CSS世界>> ----> 6.3节

#### IFC

IFC含义：

* IFC（inline Formatting Context）叫做**内联格式化上下文**，内部的元素从包含块的顶部开始，从左至右（默认）排列成一行形成的一个矩形盒子叫做`line box`
* 由不包含块级盒的块容器盒建立（块容器中只有内联级盒子）

布局规则：

* 盒子是水平一个接一个的排列，水平方向的`margin`，内边距，边框会被计算在所在行框内
* 行盒的高度由行盒内最高的`inline boxes`决定
* 行框中的内联盒子的高度小于行框的高度时，内联盒子的垂直方向对齐方式取决于`vertical-align`的值，可能是底部对齐，顶部对齐，也可能是基线对齐（默认的）。
* 当一个行框水平不能容纳内联盒子时，他们将会在垂直方向上产生多个行框，他们上下一个挨着一个，但不会重叠
* 一般来说，行框的左边界紧挨这包含容器的左边界，行框的右边界紧挨着包含容器的右边界，（是两个边都紧挨着）。然而，浮动盒子可能存在于包含框边界和行框边界之间，并且浮动元素的宽高会被计算在行框内
* 多个内联盒子的宽度**小于**包含他们的行框时，他们在水平方向的分布取决于`text-align`属性

IFC的作用：

* 案例一水平居中：当⼀个块要在环境中⽔平居中时候，设置其为inline-block则会在外层产⽣IFC，通过text-align:center则可以使其⽔平居中。

### 面试题总结

#### 让元素水平居中

> 情况将分成两种情况，内联级元素 和 块级元素

文字元素和内联级元素：

* 文字或单个元素可以使用`text-align:center`来控制

块级元素：

* 可以使用`margin:0 auto`来控制
* 多个块级容器我们可以使用`display:inline-block`与`text-align:center`来进行控制

#### 让元素垂直居中

文字元素和内联级元素：

* 文字使用`line-height`来设置，相当于将行框用`line-height`进行上下平分

块级元素：

* 一个块级元素采用绝对定位`absoult`的`top:50%`
  * 在结合`margin-top:-高度的一半`
  *  `transform:translateY(-50%)`
  * 使用格式化宽度的原理：`top:0;bottom:0;margin auto 0`
  * 或将容器元素转化为`display:table-cell`，在结合`vertical:middle`控制子元素的垂直排列 -----> 特殊方法，利用td的特点
* 多个块级元素可以使用`flex`布局，或者改变当前父元素内的文档流流向`writing-model`

#### 让元素水平垂直居中

文字和内联级元素

* 采用`text-algin:center`让其水平居中，在通过`line-height`让其垂直居中

* 采用`text-algin:center`让其水平居中，在通过`vertical-align:middle`让盒子的垂直中心点在行框的x显示的交汇点上

块级元素：

* 使用绝对定位`position:absoult`并且方位`left:0;right:0;top:0;bottom:0;`
  * 结合`margin-left/margin-top`负值的作用来实现水平垂直居中
  * 结合`transform:translate(-50%,-50%)`来实现
  * 利用格式化宽度的特点并结合`margin:auto`自动计算来实现水平居中
* 使用将**包裹容器**转化为`display:table-cell`并使用`vertical:middle`控制子元素的垂直排列，在结合子元素的`margin:auto`控制水平排列
* 多个块级容器使用`flex`控制

#### 盒子模型

> 盒子模型分为`content-box`与`border-box`，他们的区别在于元素的实际值的大小

* 当`box-sizing:content-box`(默认值)：
  * 实际宽度：`border-left + padding-left + width + padding-right + border-right`
  * 实际高度：`border-top + padding-top + height + padding-bottom + border-bottom`
* 当`box-sizing:border-box`：
  * 实际宽度：`width`设置多大就多大，因为`widht`已将`padding+border`计算在内
  * 实际高度：`height`设置多大就多大，因为`height`已将`padding+border`计算在内

#### 让元素消失的方法

> 看<<css世界>>第10章





