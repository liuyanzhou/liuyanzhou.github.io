---
title: css高级布局汇总
date: 2021-05-06 23:23:34
categories: HTML5
top: false
summary: css高级布局汇总
tags: 
 - CSS
---

### 各种布局详解

什么是布局：

> 简单来说就是HTML页面的整体结构或骨架，类似于传统的报纸或杂志中的排版；布局不是某个技术内容，而是一种设计思想；布局是CSS知识体系的重中之重，早期以`table`为主（简单），后来以技巧性布局为主（难），现在有`flexbox/grid`（偏简单），响应式布局，他们都是我们前端工程师的必备知识

布局分类：

> table布局、float布局、inline-block布局、盒布局、flexbox布局、Grid布局、columns布局、shapes布局

![布局汇总](/medias/imges/theory/css3/buju.jpeg)

#### 基本布局

##### table布局

* table布局是围绕着`table`标签来做适配的，可以直接用`table`标签来布局，因为table标签及其的子元素标签（td、th....）都有自己的默认样式
  * 优点：table标签自带了一些布局样式，例如**多个元素默认是同行排列的，元素之间未设置宽度会自适应**
  * 缺点：代码结构被固定死，不便于后期的重构
* 可以使用`display`属性将元素转化为`table`标签类型
  * 优点：代码结构不被固定死，便于后期的重构
  * 缺点：要将元素进行`display`转化，书写的样式增多，增加学习成本

> table布局是很有年代感的方式了，现在我们前端都很少采用这种布局方式，本身table也不适合用于布局，它的出现是为了展示数据，会让重构增加难度，所以我们尽量不用使用

**display转化的table标签的属性表格**

| 属性值                         | 描述                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| table                          | 指定对象作为块元素级的表格，类同于`html`标签`<table>`，表格前后带有换行符。【css2】 |
| inline-table                   | 指定对象作为内联元素级的表格，类同于`html`标签`<table>`，表格前后没有换行符【css2】 |
| table-caption                  | 指定对象作为表格标题，类同于`html`标签`<caption>`【css2】    |
| table-cell                     | 指定对象作为表格单元格，类同于`html`标签`<td>`【css2】       |
| table-row                      | 指定对象作为表格行，类同于`html`标签的`<tr>`【css2】         |
| table-row-group                | 指定对象作为表格行组，类同于`html`标签的`<tbody>`【css2】    |
| table-column                   | 指定对象作为表格列，类同于`html`标签的`<col>`【css2】        |
| table-column-group             | 指定对象作为表格列组显示，类同于`html`标签`<colgroup>`【css2】 |
| table-header-group             | 指定对象作为表格标题组，类同于`html`标签`<thead>`【css2】    |
| table-footer-group             | 指定对象作为表格脚注组，类同于`html`标签`<tfoot>`【css2】    |
| table-layout:fixed\|auto(默认) | tableLayout 属性用来显示表格单元格、行、列的算法规则。       |

```css
.table {
    display:table;
}
```

```html
<!--作为HTML-->
<table>
    <thead>
        <tr>
            <td></td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td></td>
        </tr>
    </tfoot>
</table>
<!--作为css-->
<style><
    ul {
        /* display: table; =>table */
        display: table;
    }

    li {
        /* table-cell => td */
        display: table-cell;
        list-style: none;
        width: 80px;
        text-align: center;
    }</style>

<div class="table">
    <ul>
        <li>姓名</li>
        <li>性别</li>
        <li>年纪</li>
        <li>分数</li>
    </ul>
</div>
```

##### inline-block布局

将元素转化为行内块元素，让其具有块级元素的可以设置宽高和行内元素可以一行显示的优点。

注意点：

* 多个行内元素或行内块元素同行显示，它们默认之间会有间距产生，**这是因为行内或行内块元素标签受我们换行符影响**，解决办法是给父容器增加`font-size:0`去除间距，当然这样会将原本内容也会消失，我们就要在子容器将其加回`font-size`大小。
* 图标+文字同行显示，一般使用`display:inline-block`+`vertical-align:middle/top/bottom/px`，**因为行内或行内块元素之间或与文本默认是基线对齐**，我们可以通过`vertical-align`对对齐方式进行调整
* `text-align:left/center/right`可以让行内/行内块元素/文本在父容器的位置调整
* 要考虑兼容性问题

```html
<style>
    .main1 {
        width: 1080px;
        margin: auto;
        font-size: 0;
    }

    .main1 .item {
        display: inline-block;
        width: 33.33%;
        padding: 20px 0;
        font-size: 20px;
    }

    .main1 .icon {
        width: 50px;
        height: 50px;
        background: url(https://mobike.com/cn/assets/themes/moby/img/icon-wechat.png) no-repeat;
        background-size: contain;
    }

    .main1 .icon,
    .main1 .text {
        display: inline-block;
        vertical-align: middle;
    }

    .main1 .item:nth-child(1) {
        background-color: rebeccapurple;
        text-align: left;
    }

    .main1 .item:nth-child(2) {
        background-color: blue;
        text-align: center;
    }

    .main1 .item:nth-child(3) {
        background-color: green;
        text-align: right;
    }
</style>

<div class="main1">
    <div class="item">
        <div class="icon"></div>
        <div class="text">珠峰</div>
    </div>
    <div class="item">
        <div class="icon"></div>
        <div class="text">珠峰</div>
    </div>
    <div class="item">
        <div class="icon"></div>
        <div class="text">珠峰</div>
    </div>
</div>
```

##### float布局

> CSS清除浮动方法总结:   [https://juejin.im/post/582d98d5da2f600063e28f27](https://juejin.im/post/582d98d5da2f600063e28f27)
> BFC原理全面剖析:         [https://juejin.im/entry/59c3713a518825396f4f6969](https://juejin.im/entry/59c3713a518825396f4f6969)

浮动：元素会脱离文档流，但不脱离文本流，它不是真正意义上的脱离文档流，其可以取值为`float:left/right/none(默认)`，**并且它是可以是设定元素拥有行内块的元素的特点**

由于子元素的浮动会对页面的布局有所影响，比如父元素的高度坍塌，其他元素的位置显示不正确等，这些影响都是浮动造成的，由此我们就衍生出一系列清除浮动的办法。

**清除浮动**

* 给父元素添加高度
  * 优点：属性设置简单，就往父元素增加`height:子元素高度`
  * 缺点：高度写死，不利于复用代码块以及后期的维护
* 触发BFC：给父容器增加`overflow:hidden/auto`，为了兼容`IE`还要设置`*zoom:1`
  * 优点：设置简单
  * 缺点：`overflow:hidden/auto`，都会产生额外的效果，例如滚动条，裁剪容器以外的内容，不利于有下拉框的需求

```css
.news {
  background-color: gray;
  border: solid 1px black;
  overflow: hidden;
  *zoom: 1;
}
.news img {
  float: left;
}
.news p {
  float: right;
}
```

* 采用css的属性`clear:both`，需要往父容器底部设置一个`1.内容空的，2.块级元素 3.设置clear:both`

  * 优点：是css专门提供用来清除浮动的属性
  * 缺点：增加了很多无意义的标签
```html
<div class="main clearfix">
    <div class="left">1</div>
    <div class="right">2</div>
    <!--下面的元素-->
    <div style="clear: both;"></div> 
</div>
```

* 采用`::after`伪元素来增加上面的`clear:both`，例如我们将其封装，在以后写结构时，直接调用类就行
  * 优点：复用性强，推荐使用
  * 缺点：我们写的样式太多，其实可以接受

```css
.clearfix::after{
    content: "";
    display: block;
    clear: both;
	/* 下面的代码可有无，作用是为了让元素和文本消失 */
    font-size:0;
    width:0;
    height: 0;
    overflow: hidden;
    visibility: hidden;
}
.clearfix{
    /*  兼容IE */
    *zoom:1;
}
```

##### 盒布局

[技术博客](https://www.html5rocks.com/en/tutorials/flexbox/quick/)

- box-orient：horizontal | vertical | inherit; 用来确定父容器里子容器的排列方式，是水平还是垂直
- box-direction: normal | reverse   用来确定父容器里的子容器排列顺序
- box-align：start | end | center | baseline | stretch;  表示父容器里面子容器的垂直对齐方式；
- box-pack：start | end | center | justify;   表示父容器里面子容器的水平对齐方式 
- box-flex: 子元素之间比例

##### flexbox布局

[http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
[https://www.zhangxinxu.com/wordpress/2018/10/display-flex-css3-css/](https://www.zhangxinxu.com/wordpress/2018/10/display-flex-css3-css/)
https://css-tricks.com/snippets/css/a-guide-to-flexbox/

布局的传统解决方案，基于[盒状模型](https://developer.mozilla.org/en-US/docs/Web/CSS/box_model)，依赖 display 属性 + position属性 + float属性。它对于那些特殊布局非常不方便，比如，[垂直居中](https://css-tricks.com/centering-css-complete-guide/)就不容易实现；
2009年，W3C 提出了一种新的方案----Flex 布局，可以简便、完整、响应式地实现各种页面布局。目前，它已经得到了所有浏览器的支持，这意味着，现在就能很安全地使用这项功能。
Flex是Flexible Box的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性；任何一个容器都可以指定为Flex布局，行内元素也可以使用Flex布局; webkit内核的浏览器，必须加上-webkit-前缀。

- 弹性盒子
- 盒子本来就是并列的
- 指定宽度即可

全兼容写法: 

- display: -webkit-box; /* Chrome 4+, Safari 3.1, iOS Safari 3.2+ */
- display: -moz-box; /* Firefox 17- */
- display: -webkit-flex; /* Chrome 21+, Safari 6.1+, iOS Safari 7+, Opera 15/16 */
- display: -moz-flex; /* Firefox 18+ */
- display: -ms-flexbox; /* IE 10 */
- display: flex; /* Chrome 29+, Firefox 22+, IE 11+, Opera 12.1/17/18, Android 4.4+ */

##### Grid布局

[http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html](http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html)
[https://www.zhangxinxu.com/wordpress/2018/11/display-grid-css-css3/](https://www.zhangxinxu.com/wordpress/2018/11/display-grid-css-css3/)
https://zhuanlan.zhihu.com/p/33030746

网格布局（Grid）是最强大的 CSS 布局方案。
它将网页划分成一个个网格，可以任意组合不同的网格，做出各种各样的布局。以前，只能通过复杂的CSS框架达到的效果，现在浏览器内置了;
Grid 布局与 Flex 布局有一定的相似性，都可以指定容器内部多个项目的位置。但是，它们也存在重大区别。
Flex 布局是轴线布局，只能指定"项目"针对轴线的位置，可以看作是一维布局。Grid 布局则是将容器划分成"行"和"列"，产生单元格，然后指定"项目所在"的单元格，可以看作是二维布局。Grid 布局远比Flex布局强大。

##### columns布局

[https://www.zhangxinxu.com/wordpress/2019/01/css-css3-columns-layout/](https://www.zhangxinxu.com/wordpress/2019/01/css-css3-columns-layout/)
https://www.zhangxinxu.com/wordpress/2017/02/css3-multiple-column-layout-read-horizontal/

column-gap 列的间距

- 表示每一栏之间的那个空白间隙大小；
- normal默认值。在多栏布局中为1em，在其它类型的布局中为0。
- <length>具体的长度值。不支持负数。
- <percentage>百分比值。和column-width不同，column-gap支持百分比值。同样，不能是负数。

column-width 列的宽度

- 表示每一栏/列的最佳宽度。如果我们只设定column-width，浏览器会自动根据现有容器宽度划分栏目的个数。
- <length>表示设定的最佳列宽值。实际呈现的每一栏的宽度可能与指定值不同;
- auto默认值。表示每一栏的宽度由其它CSS属性决定，例如column-count。
- 一些细节：
- column-width有时候会无效。例如容器宽度400像素，设定的每一栏宽度是300像素，不足以分栏，此时内容填充填充表现为充分利用可用空间，最终呈现的列宽比设定的更宽。又例如容器宽度400像素，column-width设置为500像素，则最终分栏宽度不会超过容器宽度，比设定的500像素要小。
- column-width不支持负值，也不支持百分比值。

column-rule 列的边框

- column-rule是column-rule-width，column-rule-style和column-rule-color这3个CSS属性的缩写。正如border是border-style，border-width和border-color的缩写一样。
- column-rule-width: 表示每个栏目中间分隔线的宽度大小。支持的属性值和border-width是一模一样的,thin：薄薄的，等同于1px; medium（默认值）：薄厚均匀，等同于3px；thick：厚厚的，等同于5px；
- column-rule-style: 表示每个栏目中间分隔线的类型。支持的属性值和border-style是一模一样的;
- column-rule-color: 表示每个栏目中间分隔线的颜色;

column-span 横跨多列

- 有点类似于表格布局中的colspan这个HTML属性，表示某一个内容是否跨多栏显示。
- none表示不横跨多栏，默认值。
- all表示横跨所有垂直列。

column-fill 列的填充

- 用是当内容分栏的时候，如何平衡每一栏填充的内容。
- auto 按顺序填充每一列。内容只占用它需要的空间。
- balance 默认值。尽可能在列之间平衡内容。在分隔断开的上下文中，只有最后一个片段是平衡的。举例来说就是有多个<p>元素，正好最后一个<p>换行了，那这个<p>元素的内容前后等分，保持平衡。这就会造成最后一栏内容较少的情况。
- balance-all（可忽略）尽可能在列之间平衡内容。在分隔断开的上下文中，所有片段都是平衡的。

##### Shapes布局

https://www.zhangxinxu.com/wordpress/2019/02/css-css3-shapes/

CSS Shapes布局可以实现不规则的文字环绕效果，需要和浮动配合使用。

#### 布局实战

##### 水平居中布局【5种】

> 含义：指当前元素在父级元素容器中，水平方向是居中显示的；

* 文本/行内元素/行内块元素

> 原理：`text-align`只控制行内内容（文字、行内元素、行内块元素）如何相对他的块父元素对齐

```html
<!--下面的案例是针对于文字水平居中显示，要想子元素水平居中的话，要使用display:inline-block-->
<style>
    #parent {
        width: 100%;
        height: 200px;
        background: #ccc;
        /*文本对齐  left  居左  center 居中  right 居右 */
        text-align:center;
    }

    #child {
        width: 200px; /*必须定宽*/
        height: 200px;
        background: #c9394a;
        display: inline-block;
    }
</style>
<div id="parent">
    <div id="child">水平居中布局</div>
</div>
```

优点：简单快捷、容易理解、兼容性非常好

缺点：只对行内内容有效；属性会继承影响到其他后代行内内容；如果子元素宽度大于父元素宽度则无效，只有后代行内内容中宽度小于设置`text-align`属性的元素宽度的时候，才会水平居中。

* 单个块级元素

> 原理：在`margin`上下左右方位都设置`auto`，只有左右`margin`将会均分剩余空间。如果上下的`margin`设置了`auto`，其计算值为`0`，并不会将上下进行平分

```html
<style>
    #parent {
        width: 100%;
        height: 200px;
        background: #ccc;
    }

    #child {
        width: 200px;/*必须定宽*/
        height: 200px;
        background: #c9394a;
        margin: 0 auto;
    }
</style>
<div id="parent">
    <div id="child">水平居中布局</div>
</div>
```

优点：简单；兼容性好

缺点：必须定宽，并且值不能为`auto`；昆都要小于父元素，否则无效

* 多个块级元素

> 原理：`text-align`只控制行内内容（文字、行内元素、行内块级元素）如何相对他的块父元素对齐。

```html
<style>
    #parent {
        width: 100%;
        height: 200px;
        background: #ccc;
        text-align: center;
        /*清除行内/行内块元素彼此之间的间距*/
        font-size: 0;
    }

    #child {
        /*将多个块级元素转化为行内块元素*/
        display: inline-block;
        width: 300px;
        height: 200px;
        background: #c9394a;
    }
</style>
<!-- 定义父级元素 -->
<div id="parent">
    <!-- 定义子级元素 -->
    <div id="child"></div>
    <div id="child" style="background-color: rebeccapurple;"></div>
</div>
```

优点：简单、容易理解，兼容性非常好

缺点：只对行内内容有效；属性会继承影响到其他后代行内内容；块级元素改为`inline-block`行内块元素时，会被换行、空格影响彼此之间产生间隔。

* 使用绝对定位实现，适用于任意元素

> 原理：子绝（绝对定位）父相（相对定位），top、right、bottom、left的值是相对于父元素尺寸的，然后`margin`或者`transform`是相对于自身尺寸的，将其组合使用能达到水平居中的目的

```html
<style>
    #parent {
        width: 100%;
        height: 200px;
        background: #ccc;
        /* 作为子元素的参照物 */
        position: relative;
    }

    #child {
        /* 绝对定位 -  参照物  方位 */
        position: absolute;
        left:50%;
        /* 反向移动子元素宽度的一半  transform: translateX(-50%);  子元素宽度未知*/
        margin-left:-150px;
        width: 300px;
        height: 200px;
        background: #c9394a;
    }
</style>
<!-- 定义父级元素 -->
<div id="parent">
    <!-- 定义子级元素 -->
    <div id="child"></div>
</div>
```

优点：使用`margin-left`兼容性好；不管是块级还是行内元素都适用这套方案

缺点：代码较多；定位脱离文档流；使用`margin-left`需知道宽度值；使用`transform`可以不用知道宽度值，但其兼容性不好（ie9+）；

* 使用`flex`布局，适用于任意和任意个元素

> 原理：设置当前主轴对齐方式为居中

```html
<style>
    #parent {
        width: 100%;
        height: 200px;
        background: #ccc;

        display:flex;
        /* 水平居中 */
        justify-content: center;
    }

    #child {
        width: 300px;
        height: 200px;
        background: #c9394a;
        margin:0 10px;
    }
</style>
<!-- 定义父级元素 -->
<div id="parent">
    <!-- 定义子级元素 -->
    <div id="child"></div>
    <div id="child"></div>
</div>
```

优点：功能强大；简单方便；容易理解

缺点：PC端兼容性不好，移动端可以使用

##### 垂直居中对齐【4种】

* 单行文本/行内元素/行内块级元素

> 原理：`line-hegiht`的最终表现是通过`inline box`实现的，而无论`inline box`所占据的高度是多少（无论比文字还是比文字小），其占据的空间都是与文本内容公用水平中垂线

```html
<style>
    #parent {
        width: 200px;
        height: 600px;
        background: #ccc;
    }

    #child {
        width: 200px;
        height: 200px;
        background: #c9394a;
        /* 垂直居中 设置行高等于元素高度 */
       line-height: 200px; 
    }
</style>

<!-- 定义父级元素 -->
<div id="parent">
    <!-- 定义子级元素 -->
    <div id="child">水平垂直居中布局</div>
</div>
```

优点：简单，兼容性好

缺点：只能用于单行行内内容；要知道元素高度的值；

* 使用`table-cell+vertical-align`来实现

> 原理：`display:table-cell`将元素以表格`td`的样式显示，之后我们通过`vertical-align`属性能控制表格单元格内容的显示位置

```html
<style>
    #parent {
        width: 200px;
        height: 600px;
        background: #ccc;
        display: table-cell;
        vertical-align: middle;
    }

    #child {
        width: 200px;
        height: 200px;
        background: #c9394a;
    }
</style>

<!-- 定义父级元素 -->
<div id="parent">
    <!-- 定义子级元素 -->
    <div id="child">水平垂直居中布局</div>
</div>
```

优点：设置简单，兼容性好

缺点：设置`vertical-align`是整体设置子元素的显示位置，会影响其他后代元素的显示

* 使用绝对定位实现

> 原理：子绝父相，top、right、bottom、left的值是相对于父元素尺寸的，然后`margin`或者`transform`是相对于自身尺寸的，组合使用能达到水平居中的目的

```html
<style>
    #parent {
        width: 200px;
        height: 600px;
        background: #ccc;
        position: relative;
    }

    #child {
        /* 定位 - 参照物 父元素 */
        width: 200px;
        height: 200px;
        background: #c9394a;
        position: absolute;
        top:50%;
        /* margin-top:-100px; */
        transform: translateY(-50%);
    }
</style>

<!-- 定义父级元素 -->
<div id="parent">
    <!-- 定义子级元素 -->
    <div id="child"></div>
</div>
```

优点：使用`margin-top`兼容性好；不管是块级还是行内元素都可以实现；

缺点：代码较多；脱离文档流；使用`margin-top`需要知道高度值；使用`transform`则有兼容性问题（ie9+）

* 使用`flex`实现

> 原理：`flex`设置对齐方式

```html
<style>
    #parent {
        width: 200px;
        height: 600px;
        background: #ccc;
        display:flex;
        /* 垂直排列 */
        align-items: center;
    }

    #child {
        width: 200px;
        height: 200px;
        background: #c9394a;
    }
</style>

<!-- 定义父级元素 -->
<div id="parent">
    <!-- 定义子级元素 -->
    <div id="child"></div>
</div>
```

优点：简单灵活；功能强大

缺点：PC端兼容不太好，移动端适配

##### 水平垂直居中布局【三种】

* 利用`table-cell`实现

> 原理：CSS Table，使用表格内容垂直对齐方式为`middle`，实现垂直居中；然后根据是行内内容还是块级内容才去不同的方式达到水平居中

```html
<style>
    /* table-cell 单元表格td  对齐 vertical-align 内容垂直对齐   */

    #parent {
        width: 1000px;
        height: 600px;
        background: #ccc;
        /* 标签 td */
        display: table-cell;
        /* 垂直居中 */
        vertical-align: middle;
    }

    #child {
        width: 200px;
        height: 200px;
        background: #c9394a;
        /* 水平居中  块级元素 */
        margin: 0 auto;
    }
</style>
<!-- 定义父级元素 -->
<div id="parent">
    <!-- 定义子级元素 -->
    <div id="child"></div>
</div>
```

优点：简单；适用于宽度高度未知情况；兼容性好（ie8+）

缺点：`vertical-align`属性具有继承性，导致父元素的文本也是居中显示的

* 定位`position`实现

> 原理：子绝父相，top、right、bottom、left的值是相对于父元素尺寸的，然后`margin`或者`transform`是相对于自身尺寸的，组合使用达到几何上的水平垂直居中

定位有三种写法

```html
<style>
    #parent {
        /* 定位开启  作为子元素的参照物  */
        position: relative;
        width: 1000px;
        height: 600px;
        background: #ccc;
    }

    #child {
        position: absolute;
        /* 方位 */
        top:0;
        bottom:0;
        left:0;
        right:0;
        margin:auto;
        width: 200px;
        height: 200px;
        background: #c9394a;
    }
</style>

<style>
    #parent {
        /* 定位开启  作为子元素的参照物  */
        position: relative;
        width: 1000px;
        height: 600px;
        background: #ccc;
    }

    #child {
        position: absolute;
        /* 方位 */
        left: 50%;
        top: 50%;
        margin: -100px 0 0 -100px;
        width: 200px;
        height: 200px;
        background: #c9394a;
    }
</style>

<style>
    #parent {
        /* 定位开启  作为子元素的参照物  */
        position: relative;
        width: 1000px;
        height: 600px;
        background: #ccc;
    }

    #child {
        position: absolute;
        left: 50%;
        top: 50%;
        /*使用transform*/
        transform: translate(-50%, -50%);
        width: 200px;
        height: 200px;
        background: #c9394a;
    }
</style>
<!-- 定义父级元素 -->
<div id="parent">
    <!-- 定义子级元素 -->
    <div id="child"></div>
</div>
```

优点：使用`margin`兼容性好；不管是块级还是行内元素都可以实现

缺点：代码较多；脱离文档流；使用`margin`需要知道宽高；使用`transform`兼容性不好（ie9+）

* 利用`flex`实现

> 原理：`flex`设置对齐方式

```html
<style>
    #parent {
        width: 1000px;
        height: 600px;
        background: #ccc;
        display: flex;
        /* 水平居中 */
        justify-content: center;
        /* 垂直居中 */
        align-items:center
    }
    #child {
        width: 200px;
        height: 200px;
        background: #c9394a;
        margin:0 10px;
    }
</style>
<!-- 定义父级元素 -->
<div id="parent">
    <!-- 定义子级元素 -->
    <div id="child"></div>
    <div id="child"></div>
    <div id="child"></div>
</div>
```

优点：简单灵活；功能强大

缺点：PC端兼容性不好，移动端适配

##### 两列布局

> 含义：两列布局一般情况下是指定宽与自适应布局，两列中左列是确定的宽度，右列是自动填满剩余所有空间的一种布局效果；

左列定宽，右列自适应方案

1. 利用`float+margin`实现
2. 利用`float+margin(fix)`实现
3. 使用`float+overflow`实现
4. 使用`table+table-cell`实现
5. 使用`position`绝对定位实现
6. 使用`flex`属性实现
7. 使用`Grid`属性实现

* `float+margin`

```html
<style>
    #left {
        width:400px;
        height: 300px;
        background-color: #c9394a;
        /* 定宽容器  - 浮动 */
        float:left;
    }

    #right {
        height: 400px;
        background-color: #cccccc;
        /* margin加间距 */
        margin-left:410px;
    }

    #inner {
        height: 300px;
        background-color: green;
        /* 清楚浮动属性 clear：both */
        clear:both;
    }
</style>

<!-- 定宽 -->
<div id="left"></div>
<!-- 自适应容器 -->
<div id="right">
    <div id="inner"></div>
</div>
```

优点：实现方式简单

缺点：自适应元素`margin`属性值要与定宽元素的`with`属性保持一致；定宽元素浮动与自适应元素不浮动导致浏览器兼容性不好；右列自适应元素中样式加了`clear:both`之后，子级源会出现问题。故有下面的升级版，让float的兄弟元素也跟着浮动，例如`#right-fix`

```html
<style>
    #left {
        width: 400px;/* 定宽 */
        height: 300px;
        background-color: #c9394a;
        float:left;

    }

    #right-fix {
        width: 100%;
        /* 浮动 */
        float:right;
        /*由于上面右侧是自适应的，宽度为100%,但left的width已经400px,就会将这个元素挤下去，那么我们用margin-left负值将其拉回去*/
        margin-left:-400px;
    }

    #right {
        height: 400px;
        background-color: #cccccc;
        margin-left:410px;
    }

    #inner {
        background-color: green;
        height: 300px;
        clear: both;
    }
</style>

<div id="left"></div>
<!-- 为自适应元素定位父级元素 -->
<div id="right-fix">
    <div id="right">
        <div id="inner"></div>
    </div>
</div>
```

* `float+overflow`

> 原理：利用overflow产生BFC

```html
<style>
    #left {
        width: 400px; /* 定宽 */
        height: 300px;
        background-color: #c9394a;
        float: left;
    }

    #right {
        height: 400px;
        background-color: #cccccc;
        overflow: hidden;
        margin-left:410px;
    }
</style>
<div id="left"></div>
<div id="right"></div>
```

优点：简单易用，全兼容

缺点：overflow属性不仅解决了两列布局问题，同时设置了内容溢出的问题

* `table+table-cell`

> 原理：利用`table`表格的自带样式，单元格都是默认同行显示，并且table的单元格会自适应

```html
<style>
    #parent {
        width: 100%;
        height: 400px;
        /* table */
        display: table;
    }

    #left {
        width: 400px;  /* 定宽 */
        height: 300px;
        background-color: #c9394a;
        /* td */
        display: table-cell;
    }

    #right {
        height: 400px;
        background-color: #cccccc;
        /* td */
        display: table-cell;
    }
</style>
<div id="parent">
    <div id="left"></div>
    <div id="right"></div>
</div>
```

优点：浏览器兼容比较好

缺点：将所有的元素的`display`属性设置为`table`相关值，受到相应制约

* 使用定位

```html
<style>
    #parent{
        /* 开启定位  子元素就会以这个父容器为参照物 */
        position: relative;
    }
    #left {
        width: 400px;  /* 定宽 */
        height: 400px;
        background-color: #c9394a;

        /* 定位在父容器的左边 */
        position: absolute;
        top:0;
        left:0;
    }

    #right {
        height: 500px;
        background-color: #cccccc;
        /* 定位在父容器的右边 */
        position: absolute;
        top:0;
        left:410px;
        right:0;
    }
</style>

<div id="parent">
    <div id="left"></div>
    <div id="right"></div>
</div>
```

* 使用`flex`弹性盒模型

> 原理：给子容易设置`flex:xxx`属性，让其对剩余空间进行平分

```html
<style>
    #parent{
        width: 100%;
        height: 500px;
        /* flex 控制子元素的排列  默认:水平排列 */
        display:flex;
    }

    #left {
        width: 400px;  /* 定宽 */
        height: 400px;
        background-color: #c9394a;
    }

    #right {
        /* 占的大小 = 100% - 400 = flex:1;  剩下的大小 */
        flex:1;
        height: 500px;
        background-color: #cccccc;
    }
</style>
<div id="parent">
    <div id="left"></div>
    <div id="right"></div>
</div>
```

* 使用`Grid`网格布局

```html
<style>
    #parent{
        width: 100%;
        height: 500px;
        /* display:grid */
        display: grid;
        /* 左边 400px  右边 auto  自适应 */
        grid-template-columns:400px  auto;
    }

    #left {
        height: 400px;
        background-color: #c9394a;
    }

    #right {
        height: 500px;
        background-color: #cccccc;
    }
</style>
<div id="parent">
    <div id="left"></div>
    <div id="right"></div>
</div>
```

##### 三列布局

含义：三列布局一般情况下是指三列中左边两列是确定的宽度，右边一列是自动填满剩余所有空间的一种布局效果

两列定宽，一列自适应（右边）方案：

1. 利用`float+margin`实现
2. 使用`float+overflow`实现
3. 使用`table+table-cell`实现
4. 使用`flex`属性实现
5. 使用`Gird`属性实现]
6. 定位`position`

* `float+margin`

```html
<style>
    #left {
        width: 200px;
        /* 定宽 */
        height: 300px;
        background-color: #c9394a;
        /* 浮动 */
        float: left;
    }

    #center {
        width: 200px;
        /* 定宽 */
        height: 300px;
        background-color: green;
        /* 浮动 */
        float: left;
    }

    #right {
        /* 宽度自适应 */
        height: 400px;
        background-color: #cccccc;
        /* 整体移动往右移动 */
        margin-left: 410px;
    }
</style>
<div id="parent">
    <div id="left"></div>
    <div id="center"></div>
    <div id="right"></div>
</div>
```

注意这种方式和两列布局用`float + margin`实现一样，都有缺陷，并且解决办法是相同的

* `float+overflow`

```html
<style>
    #left {
        width: 200px; /* 定宽 */
        height: 300px;
        background-color: #c9394a;
        float:left;
    }

    #center {
        width: 200px; /* 定宽 */
        height: 300px;
        background-color: green;

        float:left;
    }

    #right {
        height: 400px;
        background-color: #cccccc;
        /* 利用BFC的特点  - 隔离的容器  不会与其他容器出现重叠的情况 */
        overflow: hidden;
        margin-left:410px;
    }
</style>
<div id="parent">
    <div id="left"></div>
    <div id="center"></div>
    <div id="right"></div>
</div>
```

* `table+table-cell`

```html
<style>
    #parent{
        width: 100%;
        /* table */
        display: table;
    }

    #left {
        width: 200px; /* 定宽 */
        height: 300px;
        background-color: #c9394a;

        /*  td */
        display: table-cell;
    }

    #center {
        width: 200px; /* 定宽 */
        height: 300px;
        background-color: green;

        /*  td */
        display: table-cell;
    }

    #right {
        height: 400px;
        background-color: #cccccc;

        /*  td */
        display: table-cell;
    }
</style>

<div id="parent">
    <div id="left"></div>
    <div id="center"></div>
    <div id="right"></div>
</div>
```

* 使用`flex`

```html
<style>
    #parent{
        height: 300px;
        /* flex 子元素 - 水平排列 */
        display:flex;
    }

    #left {
        width: 200px; /* 定宽 */
        height: 300px;
        background-color: #c9394a;
    }

    #center {
        width: 200px; /* 定宽 */
        height: 300px;
        background-color: green;
    }

    #right {
        /* flex:1  = 100% - 200 - 200 */
        flex:1;
        height: 400px;
        background-color: #cccccc;
    }
</style>
<div id="parent">
    <div id="left"></div>
    <div id="center"></div>
    <div id="right"></div>
</div>
```

* 使用`gird`实现

```html
<style>
    #parent{
        height: 400px;
        /* grid */
        display: grid;
        /* 左 = 400px  中间 =  400px  右 = auto 自适应 */
        grid-template-columns:400px 400px auto;
    }
    #left {
        height: 300px;
        background-color: #c9394a;
    }

    #center {
        height: 300px;
        background-color: green;
    }

    #right {
        height: 400px;
        background-color: #cccccc;
    }
</style>
<div id="parent">
    <div id="left"></div>
    <div id="center"></div>
    <div id="right"></div>
</div>
```

* 使用定位

```html
<style>
    #parent{
        height: 400px;
        position: relative;
    }
    #left {
        /* 定宽 */
        width: 200px; 
        height: 300px;
        background-color: #c9394a;
        /* 左容器靠左 */
        position: absolute;
        top:0;
        left:0;
    }

    #center {
        /* 定宽 */
        width: 200px; 
        height: 300px;
        background-color: green;
        /* 中间容器 - 在left的右边 */
        position: absolute;
        top:0;
        left:200px;
    }

    #right {
        height: 400px;
        background-color: #cccccc;
        /* 自适应 100% - 200 - 200 = */
        position: absolute;
        top:0;
        left:400px;
        right:0;
    }
</style>
<div id="parent">
    <div id="left"></div>
    <div id="center"></div>
    <div id="right"></div>
</div>
```

两侧定宽，中间自适应方案：

1. 圣杯布局方法
2. 双飞翼布局方法
3. 使用`Gird`实现
4. 使用`table`实现
5. 使用`flex`实现
6. 使用`position`实现

* 圣杯布局

> 圣杯布局是来源于该布局效果类似圣杯而得名。简单来说，就是指三行三列布局；
>
> 圣杯布局核心：主要是实现中间主体部分中的左右定宽+中间自适应的布局效果；

圣杯布局步骤：

1. 让三个列在一行显示，使用`float:left`
2. 父容器中间留出一些留白（使用内间距padding填充）为了放置左右容器
3. 移动元素，将`left`移动到`center`的左边，将`right`移动到`center`的右边
   * 使用`margin`的负值技巧，将左容器向左拉去`margin-left:-100%`，将右容器向左拉去自身宽度
   * 使用`position:relative`结合`left/right`将左右定位到正确位置，因为`relative`不会让元素脱离文档流，只是为了让方位`left/right`起作用

![圣杯布局](/medias/imges/theory/css3/grail.png)

```html

<style>

    *{margin: 0;padding: 0;}

    #parent {
        height: 300px;
        /* 给左右两边留出间距 - 为了放左右两个容器 */
        padding-left:300px;
        padding-right:300px;
    }

    #left,
    #center,
    #right {
        height: 300px;
        font-size:50px;

        /* 第一步: 三个容器在一行显示  => 已经在一行 但是我们看到的不在一行的原因 
        200 + 200 + 100%  > 100% 放不下了 所以就掉下去了
        */
        float: left;
    }

    #left,
    #right {
        width: 300px;
    }

    #left {
        /* 左 定宽 */
        width: 300px;
        background-color: #c9394a;
        /* 第三步: 进行位置的移动 */
        margin-left:-100%;
        position: relative;
        left:-300px;
    }

    #center {
        /* 中间 自适应 */
        width: 100%;
        background-color: green;
    }

    #right {
        /* 右 定宽 */
        width: 300px;
        background-color: yellowgreen;

        /* 第三步: 进行位置的移动 */
        margin-left:-300px;
        position: relative;
        right:-300px;
    }
</style>
</head>

<body>
    <!-- 三列布局 - 左右定宽   中间自适应   =>  圣杯布局   center在left和right的结构的前面 -->
    <div id="parent">
        <div id="center">中间</div>
        <div id="left">左边</div>
        <div id="right">右边</div>
    </div>
</body>
```

* 双飞翼布局

> 双飞翼布局最早是淘宝团队提出，是针对圣杯布局的优化解决方案。主要是优化了圣杯布局中要开启定位的问题

双飞翼布局步骤：

1. 让三个容器在一行显示，使用`float:left`
2. 让中间的容器留出间距（为了放左右两个容器），这里我们给中间容器的子容器（`inner`）加外边距（`margin`）
3. 进行移动：这里直接使用`margin`的负值技巧即可，因为我们是在`center`容器中的子元素`inner`设置留白

![双飞翼布局](/medias/imges/theory/css3/flying.png)

```html
<style>
    *{margin: 0;padding: 0;}

    #parent {
        height: 300px;
    }

    #left,
    #center,
    #right {
        height: 300px;
        font-size: 50px;
        /* 第一步: 在三个容器在一行排列 => 左右容器因为center的宽度是100% 所以放不下就掉下去了  */
        float:left;
    }


    #left {
        width: 300px;
        background-color: #c9394a;
        /* 第三步位置的移动 利用margin的负值的技巧 */
        margin-left:-100%;
    }

    #center {
        width: 100%;
    }

    #right {
        width: 300px;
        background-color: greenyellow;

        /* 第三步位置的移动 利用margin的负值的技巧 */
        margin-left:-300px;
    }

    #inner {
        height: 300px;
        background-color: green;
        /* 第二步: 加到中间的容器的身上  margin */
        margin-left:300px;
        margin-right:300px;
    }
</style>
</head>

<body>
    <div id="parent">
        <div id="center">
            <div id="inner">中间</div>
        </div>
        <div id="left">左边</div>
        <div id="right">右边</div>
    </div>
</body>
```

**对比圣杯布局和双飞翼布局**

相同点：

1. HTML的结构顺序一样，都是`center、left、right`
2. 布局的第一步都是一样的，让三个列都同一行显示，使用`float:left`

不同点：

1. 左右留白的方式不一样

   * 圣杯布局是直接在父容器上使用`padding-left/padding-right`留白
   * 双飞翼布局是在`center`容器的子容器增加`margin-left/margin-right`留白
2. 移动位置的方法不一样
* 圣杯布局是采用`margin`负值技巧+`posiotion:relative`+`left/right`方位技巧进行移动
  * 双飞翼是直接采用`margin`负值技巧即可

> 总之：可以将双飞翼布局看做是圣杯布局的一种优化，它去除了定位的设置。它俩的作用都是为了`center`优先被渲染，因为他们的结构都是`center`在最前面

#####等分布局

> 等分布局：就是指一行被分为若干列，每一列的宽度是相同的值。
>
> [技术博客](https://www.cnblogs.com/xiaohuochai/p/5457127.html)

![等分布局](/medias/imges/theory/css3/divideWidth.jpg)

实现方案：

1. `float+margin`数显实现等分布局效果
2. `display`属性的值有关`table`实现等分布局效果
3. `flex`属性实现等分布局效果
4. `Grid`属性实现等分布局效果

* `float`+`margin`

```html
<style>
    *{margin: 0;padding: 0;}
    
    /* 清除浮动利用.clearfix {} */
    .clearfix::after{
        /* 三个必备的条件 */
        content: '';
        display: block;
        clear: both;

        /* 更全的属性 */
        overflow: hidden;
        visibility: hidden;
        font-size:0;
        height:0;
        width:0;
    }
    #parent{
        /* 利用margin负值技巧  进行整体移动 - 往左移动 把间距给隐藏起来啦 */
        margin-left:-10px;
    }
    .col1,
    .col2,
    .col3,
    .col4 {
        height: 300px;
        float:left;
        width: 25%;
        /* box-sizing:border-box  为了容器之间留白，并同显示一行 将盒子模型进行转化，通过增加padding来实现间隙留白
        */
        box-sizing: border-box;
        border-left:10px solid #fff;
    }

    .col1 {
        background-color: hotpink;
    }

    .col2 {
        background-color: lightblue;
    }

    .col3 {
        background-color: green;
    }

    .col4 {
        background-color: yellow;
    }
</style>

<!-- 作为父级容器 -->
<div id="parent" class="clearfix">
    <div class="col1"></div>
    <div class="col2"></div>
    <div class="col3"></div>
    <div class="col4"></div>
</div>
```

* `table+table-cell`实现

利用`table`会让其子元素进行同行显示，并且宽度自适应的特点，我们将父元素进行`display:table`，子元素进行转化`diaplay:table-cell`

```html
<style>
    * {
        margin: 0;
        padding: 0;
    }
    /* 反向移动 */

    .wrap {
        /* 为了清除第一个子容器的padding-left */
        margin-left: -10px;
    }

    #parent {
        width: 100%;
        display: table;
    }

    .col1,
    .col2,
    .col3,
    .col4 {
        display: table-cell;
        padding-left: 10px;
        height: 300px;
    }

    .col1 .inner {
        height: 300px;
        background-color: hotpink;
    }

    .col2 .inner {
        height: 300px;
        background-color: lightblue;
    }

    .col3 .inner {
        height: 300px;
        background-color: green;
    }

    .col4 .inner {
        height: 300px;
        background-color: yellow;
    }
</style>


<body>
    <!-- 作为父级容器 -->
    <div class="wrap">
        <div id="parent">
            <div class="col1">
                <div class="inner"></div>
            </div>
            <div class="col2">
                <div class="inner"></div>
            </div>
            <div class="col3">
                <div class="inner"></div>
            </div>
            <div class="col4">
                <div class="inner"></div>
            </div>
        </div>
    </div>
</body>
```

* `flex`实现

```html
<style>
    *{margin: 0;padding: 0;}

    #parent {
        /* 控制排列的方法 - 默认水平排列 */
        display:flex;
        /* 为了清除第一个子容器的padding-left */
        margin-left:-10px;
    }

    .col1,
    .col2,
    .col3,
    .col4 {
        /* 平分 四个容器各占一等份  1/4 * 100%  = 25% */
        flex:1;
        height: 300px;
        padding-left:10px;
    }

    .col1 .inner{
        height:300px;
        background-color: hotpink;
    }

    .col2 .inner{
        height:300px;
        background-color: lightblue;
    }

    .col3 .inner{
        height:300px;
        background-color: green;
    }

    .col4 .inner{
        height:300px;
        background-color: yellow;
    }
</style>
<body>
    <!-- 作为父级容器 -->
    <div id="parent">
        <div class="col1">
            <div class="inner"></div>
        </div>
        <div class="col2">
            <div class="inner"></div>
        </div>
        <div class="col3">
            <div class="inner"></div>
        </div>
        <div class="col4">
            <div class="inner"></div>
        </div>
    </div>
</body>
```

##### 等高布局

> 等高布局：就是一行被划分成若干列，每一列的高度是相同的值
>
> [技术博客](https://www.cnblogs.com/xiaohuochai/p/5457127.html)

![等高布局](/medias/imges/theory/css3/divideHeight.jpg)

实现方案：

1. `display`属性的值有关`table`实现
2. `padding+margin`属性实现等高布局效果

* 利用`table+td`的特性来实现，因为`table`的子容器的高度是根据子容器中最大的为确认

```html
<style>
    *{margin: 0;padding: 0;}

    #parent {
        width: 100%;
        display:table;
    }

    #left,
    #right {
        width: 300px;
        display:table-cell;
    }

    #left {
        background-color: #c9394a;
    }

    #right {
        background-color: green;
    }
</style>

<body>
    <div id="parent">
        <div id="left">zhfueng</div>
        <div id="right">Never underestimate your power to change?Whatever is worth doing is worth doing well.Never underestimate your power to change?Whatever is worth doing is worth doing well.Never underestimate your power to change?Whatever is worth doing is worth doing well.</div>
    </div>
</body>
```

* `padding+margin`实现

```html
<style>
    *{margin: 0;padding: 0;}

    #parent {
        width: 100%;
        /* 超出溢出隐藏 */
        overflow:hidden
    }
    /* 伪等高布局 */
    #left,
    #right {
        width: 300px;
        float: left;

        /* 内填充 */
        padding-bottom: 9999px;
        /* 外间距  */
        margin-bottom: -9999px;
    }

    #left {
        background-color: #c9394a;
    }

    #right {
        background-color: #cccccc;
    }
</style>

<body>
    <div id="parent">
        <div id="left">zhfueng</div>
        <div id="right">Never underestimate your power to change?Whatever is worth doing is worth doing well.Never underestimate your power to change?Whatever is worth doing is worth doing well.Never underestimate your power to change?Whatever is worth doing is worth doing well.</div>
    </div>
</body>
```

##### 全屏布局

> 全屏布局就是指`HTML`页面铺满整个浏览器窗口，并且没有滚动条，而且还可以跟着浏览器的大小变化而变化；
>
> [技术博客](https://www.cnblogs.com/xiaohuochai/p/5458068.html)

![全屏布局](/medias/imges/theory/css3/fillScreen.jpg)

实现方案：

1. 利用绝对定位实现
2. 利用`flex`实现

* 绝对定位实现

```html
<style>
    * {
        margin: 0;
        padding: 0;
    }
    /* 头部 定高  */

    header {
        height: 100px;
        background-color: gold;
        /* 定位到头部 */
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
    }

    .content {
        background-color: lightblue;
    }
    /* 中间内容  -  左定宽   右自适应
    overflow-y: auto;   内容没有超出屏幕的高度  - 没有滚动条
    内容超出屏幕的高度      - 垂直滚动条出现
    */

    .content .left {
        width: 300px;
        height: 100%;
        background-color: lightcoral;
        /* 定到中间容器的左边 */
        position: fixed;
        top: 100px;
        bottom: 100px;
        left: 0;
    }

    .content .right {
        background-color: green;
        /* 定到中间容器的右边 */
        position: fixed;
        top: 100px;
        bottom: 100px;
        left: 300px;
        right: 0;
    }
    /* 底部 定高  */

    footer {
        height: 100px;
        background-color: gold;
        /* 定位到底部 */
        position: fixed;
        bottom: 0;
        left: 0;
        right: 0;
    }
</style>

<body>
    <header></header>
    <div class="content">
        <div class="left"></div>
        <div class="right"></div>
    </div>
    <footer></footer>
</body>
```

##### css3多列布局

| 属性                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| `columns`           | 它是`column-width`和`column-count`属性的缩写                 |
| `column-width`      | 表示每一栏/列的最佳宽度，如果我们只设定了`coulumn-width`，浏览器会自动根据现有容器宽度划分栏目的个数 |
| `column-count`      | 表示理想的分栏数目                                           |
| `column-rule-color` | 表示每个栏目中间分隔线的颜色                                 |
| `column-rule-style` | 表示每个栏目中间分隔线的类型。支持的属性值和`border-style`是一模一样的 |
| `column-rule-width` | 表示每个栏目中间分隔线的宽度大小。支持的属性值和`border-width`是一模一样的 |
| `column-rule`       | `column-rule`是`column-rule-width`，`column-rule-style`和`column-rule-color`这三个css属性的缩写。正如`border`是`border-style,border-width和border-color`的缩写一样 |
| `column-span`       | 有点类似于表格布局中`colspan`这个HTML属性，表示某一个内容是否跨多栏显示 |
| `column-fill`       | 作用是当内容分栏的时候，如何平衡每一栏填充的内容             |
| `column-gap`        | 表示每一栏之间的那个空白间隙大小                             |

##### 布局汇总

| 布局方式               | 描述                                                         | 优点                     | 弊端                                                         |
| ---------------------- | ------------------------------------------------------------ | ------------------------ | ------------------------------------------------------------ |
| `table`                | 利用`table`的样式或标签来进行布局。                          | 兼容性好                 | 用`table`标签来布局的话，其的标签会被写死，并且`table`标签并不适合布局复杂的页面结构，会增加后期的维护成本。而用`table`样式来布局，其要将每个元素进行`display`进行转化，代码量大。 |
| `display:inline-block` | 利用行内块元素能一行显示，并且能设置宽高的特点。             | 简单设置，便于实现效果   | ie低版本浏览器不兼容，行内块元素同行显示之间会有间隙产生，我们要通过给父元素设置`font-size:0`进行清除 |
| `float`浮动            | 利用设置`float:left/right`让元素进行浮动，同行显示           | 兼容性好                 | 浮动的元素会脱离文档流，造成父元素的高度坍塌，影响页面整体布局，因此我们要形成使用浮动就要给父元素进行清除浮动。 |
| `position`定位         | 利用设置`position:xxx`各种定位属性+方位`left/right/top/bottom`来实现 | 兼容性好                 | 设置`position`设置值那些是会让元素脱离文档流，并且我们要清楚知道其是根据那个元素进行参考点的。 |
| `flex`布局             | css3提供的弹性盒布局                                         | 父容易易控制子元素的排列 | 兼容性不好，普遍在移动端适配                                 |

PC项目：浮动 + 定位

PC + ipad + 手机项目：浮动 + 定位 + flex

手机项目：flex

技巧：

* 想要让盒子同行显示并且各容器之间有间隙的话，我们可以通过将盒容器转化`box-sizing:border-box`，之后通过添加`padding/border`
* 想要异常一列容器的一个容器的间隙，我们可以通过`margin-left`负值技巧，这样的好处就是不会导致一列容器中某个容器较大，如果用选择器清除，就会导致一列元素中某个元素的宽度会较其他容器大一些
* 兄弟盒子浮动，当前盒子可以采用`overflow:hidden`产生BFC来自适应父容器，或者可以采用调换容器之间的位置，因为浮动是脱离文档流的
* 采用定位的元素可以通过方位来设置宽度，但你设置宽度了并也使用了方位`left/right/bottom/top`，在设置`margin:auto`就会自行计算并布局元素在父容器的水平垂直居中位置

```css
#child {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
    width: 200px;
    height: 200px;
    background: #c9394a;
}
```

