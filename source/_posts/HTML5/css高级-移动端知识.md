---
title: css高级-移动端知识
date: 2021-05-14 19:33:15
categories: HTML5
top: false
summary:  css高级-移动端知识
tags: 
 - CSS
---

### 移动端响应式布局技巧

> 知识体系：
>
> 1. 设备像素、设备独立像素，CSS像素，PPI，devicePixelRatio
> 2. layout viewport 与 visual viewport
> 3. viewport缩放适配
> 4. 媒体查询`@media`
> 5. vw，rem，flex适配

### 像素区分

* 设备像素（物理像素/像素分辨率）
  1. 显示器的最小物理像素单位（对于一个显示器来说是固定的）
  2. 以手机屏幕为例，`iphonex`像素分辨率为`1125*2436`，是指屏幕横向能显示`1125`个物理像素点，纵向能显示`2436`个物理像素点
  3. 通过说的`4k`显示屏指的是`4096x2160`
* 设备独立像素（dips）
  1. 比如我们偶尔会说电脑屏幕子啊`2560x1600`分辨率下不适合玩游戏，我们把它调为`1440x900`，这里的分辨率（非严谨说）指的就是**设备独立像素**
  2. 可在控制台通过`window.screen.width/window.screen.height`查看
  3. 另外，平时我们所说的`iphoneX`的逻辑分辨率是`375x812`指的就是设备独立像素，`chrome`检查元素模拟调试手机设备时显示如`375x667`和`320x480`都是设备独立像素
  4. 一个设备独立像素可能包含多个**物理像素**，包含的越多，显示越清晰
* CSS像素
  1. 在页面不缩放的情况下，`1px`的CSS像素 === 1 设备独立像素
  2. 页面放大`200%`时，页面的设备独立像素依旧不变，放大的是`CSS`像素，当时此时`CSS`像素与设备独立像素的关系变化了，`1px === 4px `独立像素（宽x2,高x2）
* PPI
  1. 指每英寸的物理像素数
  2. 以尺寸为`5.8`英寸（屏幕对角线长度）、分辨率为`1125x1436`的iphonex为例
  3. ppi = Math.sqrt(1125*1125 + 2436 \*2436) / 5.8，值为`463ppi`（屏幕对角线上的像素点 / 对角线的英寸数）
* devicePixelRatio
  1. 像素比：`window.devicePixelRatio`
  2. `devicePixelRatio`指的是物理像素和设备独立像素的比，即1独立像素由多少物理像素渲染
  3. dpr（device pixel ratio）：设备像素比，设备像素 / 设备独立像素，代表设备独立像素到设备像素的转换关系，在`JS`中可以通过`window.devicePixelRatio`获取，所以`window.devicePixelRatio = 物理像素 /设备独立像素(dips)`，经计算，iphonex的`devicePixelRatio=3`

高清屏幕部分图片失真

* 一些像素比较低的图片，在普通显示屏上可以显示，但在高清屏上会出现模糊的现象
* 原因是：假如有一张图片，设备宽高`100px`，在不同的屏幕上，呈现都是`100px`设备独立像素的图片，但对于高清屏来说，100px独立像素所需要的物理像素比普通屏多得多
* 1px独立像素所含的物理像素越多，屏幕越清晰；假如普通屏`100px`独立像素需要`1w`个物理像素，高清屏得`3w`个，但是图片本身包含的像素点可能远远达不到`3w`，这时候，图片就会拉伸自己的像素点，所以看起来就显得模糊了
* 解决办法是：高清屏上图片的宽高设小一点，这样所需的物理像素就不用那么多了，屏幕显示图片所需的物理像素越接近图片，图片越高清

矢量图永不失真：

因为矢量图形不是一个个像素点显色的，而是通过给定的坐标数据进行绘制的，所以不会失真。

**设计稿对应大小与像素转换**

> 开发中，我们常见的三种设计稿为`640*1136`、`750*1334`、`1242*2208`

* `640*1136`：参照物是`iphone5`，其设备独立像素为`320*568`，其`DPR=2`，故独立像素 * DPR
* `750*1334`：参照物是`iphone6`，其设备独立像素为`375*667`，其`DPR=2`，故独立像素 * DPR
* `1242*2208`：参照物是`iphone6P`，其设备独立像素为`414*736`，其`DPR=3`，故独立像素 * DPR

所以面对这类设计稿，我们要开发整屏效果的话，要`height==对应设计稿高度`

### 视口`layout viewport与visual viewport`

移动端视口分为三种：布局视口（layout viewport）、视觉视口（visual viewport）、理想视口（ideal viewport）

https://www.html.cn/archives/5975 

http://www.itheima.com/news/20200910/171437.html

https://blog.csdn.net/weixin_44060108/article/details/103783282

#### 布局视口

布局视口是指页面的宽度，一般移动端浏览器都默认设置了布局视口的宽度，根据设备的不同，布局视口的默认宽度有可能是`768px、980px、1024px`等，这个宽度并不适合在手机屏幕中展示。移动端浏览器之所以采用这种默认设置，是为了解决早期的PC端页面在手机上的显示问题。下面图即为布局视口：

![layout-viewport](/medias/imges/theory/css3/layout-viewport.jpg)

在图1中，当移动端浏览器展示PC端网页内容时，由于移动端设备屏幕比较小，不能像PC端浏览器那样完美地展示网页，这正是布局视口存在的问题。这样的网页在手机的浏览器中会出现左右滚动条，用户需要左右滚动才能查看完整的一行内容。

#### 视觉视口==> (物理像素)

视觉视口是指用户正在看到的网站的区域，这个区域的宽度等同于移动设备的浏览器的宽度，下面通过图2演示什么是视觉视口

![visual-viewport](/medias/imges/theory/css3/visual-viewport.jpg)

需要注意的是，当我们在手机中缩放页面的时候，操作的是视觉视口，而布局视口仍然保持原来的宽度

#### 理想视口==>dip(逻辑像素)

理想视口是指对设备来讲最理想的视口尺寸，采用理想视口的方式。可以使我也在移动端浏览器上获取最理想的浏览和阅读的宽度，下面通过图3演示什么是理想视口

![ideal-viewport](/medias/imges/theory/css3/ideal-viewport.jpg)

从图3可以看出，在理想视口情况下，布局视口的大小和屏幕宽度是一致的，这样就不需要左右滚动页面了，在开发中，为了实现理想视口，需要给移动端页面添加标签配置视口，通过浏览器来进行处理。

> 总结：
>
> * 布局视口 就是 内容宽度，**真实存在**
> * 视觉视口 就是 手机屏幕宽度，**真实存在**
> * 理想视口 就是 当布局视口 == 视觉视口 也就是 内容宽度 == 手机屏幕宽度时的一种情况，**它是虚拟的，是一种概念**

#### viewport缩放适配

屏幕尺寸：`window.screen.height`

浏览器窗口尺寸：`window.innerWidth、window.innerHight`==> 指的是CSS像素，并且要注意：`innerWidth、innerHeight`不包括滚动条的宽度高度，精确计量用

获取窗口可视宽度：`document.documentElement.clientWidth`和`document.documentElement.clientHeight`

**viewport**元标签控制布局，首先看一下`viewport`元标签其属性

```html
<meta id="viewport" name="viewport" content="width=device-width; initial-scale=1.0; maximum-scale=1; user-scalable=no;">
```

这里是每个属性的详细介绍

| 属性名          | 取值                    | 描述                                                  |
| --------------- | ----------------------- | ----------------------------------------------------- |
| width           | 正整数或`device-width`  | 定义视口的宽度，单位为像素(px)                        |
| height          | 正整数或`device-height` | 定义视口的高度，单位为像素(px)，一般不用              |
| initial-scale   | [0.0-10.0]              | 定义初始缩放值                                        |
| `minimum-scale` | [0.0-10.0]              | 定义缩小最小比例，它必须小于或等于`maximum-scale`设置 |
| `maximum-scale` | [0.0-10.0]              | 定义放大最大比例，它必须小于或等于`minimum-scale`设置 |
| user-scalable   | yes/no/0                | 定义是否允许用户手动缩放页面，默认值`yes`             |

* width

width属性被用来控制`layout viewport`(布局视口)的宽度，`layout viewport`（布局视口）宽度是根据浏览器根据设备的不同设置不同的值。可以是数值`980px`等，也可以设置`width=device-width`也就是将`layout viewport`（布局视口）的宽度设置为`ideal viewport`(理想视口)的宽度。页面缩放比例为`100%`时，一个CSS像素就对应一个`dip`（设备逻辑像素），此时`layout viewport`（布局视口）的宽度 == `ideal viewport`(理想视口)的宽度 == dip 的宽度值

* heigth

与width类型，但实际上却不常用

* initial-scale

`initial-scale`用于指定页面的初始缩放比例，`initial-scale=1`表示将`layout viewport`（布局视口）的宽度设置为`ideal viewport`（理想视口）的宽度，`initial-scale=1.5`表示将`layout viewport`的宽度设置为`ideal-scale`的宽度的`1.5`倍

* maximum-scale

maximum-scale 用于指定用户能够放大的最大比例，假设页面的默认缩放值`initial-scale=1`，那么用户最终能将页面放大到这个初始页面的`3`倍

* minimum-scale

类似`maximum-scale`的描述，不过`minimum-scale`是用来指定页面的最小缩放比例的，通常情况下，不会定义该属性的值，页面大小将难以阅读

* `user-scalable`

`user-scalable`来控制用户是否可以通过手势对页面进行缩放，该属性的默认值为`yes`，可被缩放，你也可以将该值设置为`no`，表示不允许用户缩放网页

```html
<meta name="viewport" content="user-scalable=no" />
```

### 媒体查询@media

语法：`@medai 媒体类型 逻辑操作符(媒体属性) {样式代码}`

逻辑操作符

* `and`：操作符用来把多个媒体属性组合起来，合并到同一条媒体查询中。只有当每个属性都为真时，这条查询的结果才为真；

```css
@media all and (min-width:700px) and (orientation:lanscape) {...}
```

* `not`：操作符用来对一条媒体查询的结果进行取反；

```css
@media not all and (monochrome){} == @media not (all and (monochrome)) {...}
```

* `only`：操作符表示仅在媒体查询匹配成功时应用指定样式。可以通过它让选中的样式在老式浏览器中不被应用

```css
@medai only screen and (max-width:1000px) {...}
```

媒体属性

* width | min-width | max-width
* height | min-height | max-height
* device-width | min-device-width | max-device-width
* device-height | min-device-height | max-device-height 
* aspect-ratio | min-aspect-ratio | max-aspect-ratio
* device-aspect-ratio | min-device-aspect-ratio | max-device-aspect-ratio
* color | min-color | max-color
* color-index | min-color-index | max-color-index
* monochrome| min-monochrome | max-monochrome
* resoluition| min-resolution |  max-resolution
* scan | grid

横竖屏

* 竖屏

```css
@media (orientation:portrait) {竖屏}
```

* 横屏

```css
@media (orientation:landscape) {横屏}
```

### 单位

> 我们开发中常见的单位有：
>
> * PC端：px 、%、em
> * 移动端：vw、vh、vmin、vmax、rem
>
> 除`px`是像素单位（固定写死），其他都是参照单位，通过参照物的`font-size/width/height...`某个值进行相对应的转换

**PC端**

* px

> 像素单位，写死，`1px`css像素就对应着`1px`的设备逻辑像素

```css
item {
    width:100px;
    height:100px;
}
```

* `%`

> 参照单位，参照物是父元素

```css
.parent{
    width:100px;
    hegiht:100px;
}
.child{
    width:100%;
    hegiht:100%;
}
```

* `em`

> 参照单位，参照物是父元素的字体`font-size`大小，查找规则是，先查看其父容器是否有`font-size`属性，有的话，`1em==当前font-size的值`，没有就继续往上查找，直到`body/html`为止，`body/html`的`font-size`默认是`16px`

```css
.parent {
    font-size:50px;
}
.child {
    width:1em;
    height:1em;
}
```

这里的`1em==50px`

**移动端**

* `vw/vh/vmin/vmax`

> 参照单位，参照物是`viewport`，含义：把视口（窗口）的大小平分为`100`份，比如：我们当前的视口是`iphone6` => `375 * 667`，则：
>
> * `1vw` = 375 / 100 = 3.75px
> * `1vw` = 667 / 100 = 6.67px
>
> 所以当我们知道我们要设置的盒子宽高为`300px*300px`，将其转化为`vw/vh`，则
>
> * `300px` = 300px / 3.75 = 80vw
> * `300px` = 300px / 6.67 = 44.94vh

```css
.box1 {
    /* vw */
    width: 80vw;
    /* vh */
    height: 44.94vh;
    background-color: lawngreen;
}
```

* `rem`单位

> 含义：`root element`，参照单位，参照的是**根元素**的默认字体大小`16px`，当然我们将其覆盖掉，为了方便计算，我们经常设置10的倍数作为根元素的`font-size`大小，但是要注意火狐的最小字体是`12px`，所以我们综合考虑设置为`font-size=100px`符合主流浏览器，比如我们要设置一个`300*300`的盒子宽高，相对于`font-size:100px`，`width:3rem = 300 / 100(font-size)`，`height= 3rem  = 300 / 100 (font-size)`

```css
html {
    font-size:100px;
}
.box {
    width:3rem;
    height:3rem;
    background:red;
}
```

动态计算`html`的`font-size`

```js
// 针对750的设计稿
function refreshRem() {
    // 设计稿尺寸
    var desW = 750,
        // 视口宽度
        winW = document.documentElement.clientWidth,
        // DPR
        ratio = winW / desW;
    // 给根元素设置fontSize
    document.documentElement.style.fontSize = ratio * 100 + 'px';
}
refreshRem();
window.addEventListener('resize', refreshRem);
```

