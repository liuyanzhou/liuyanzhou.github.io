---
title: HTML5_CSS易被忽视的一面
date: 2020-06-14 11:39:42
categories: HTML5
top: false
summary: HTML5_css易被忽视的页面一面
tags: 
 - HTML5
---

#### 一、属性简写

##### 1.font属性用于对字体样式进行综合设置

- 基本语法格式如下：

```css
选择器 { font: font-style  font-weight  font-size/line-height  font-family;}
```

- 注意：
  - 使用font属性时，**必须按上面语法格式中的顺序书写，不能更换顺序，**各个属性以**空格**隔开。
  - 其中不需要设置的属性可以省略（取默认值），但必须保留font-size和font-family属性，否则font属性将不起作用。

##### 2.background：背景

- background：属性的值的书写顺序官方并`没有强制标准`的。为了可读性，建议大家如下写：

- background: 背景颜色 背景图片地址 背景平铺 背景滚动 背景位置;

- 语法：

  ```css
  background: transparent url(image.jpg) repeat-y  scroll center top ;
  ```

##### 3.边框综合设置

* border综合写法顺序也是没有强制性要求

```css
border : border-width || border-style || border-color 
/* 例如： */
border: 1px solid red;  没有顺序 
```

* 表格边框合并使用属性`border-collapse:collapse; 表示相邻边框合并在一起。`

```css
table{ border-collapse:collapse; }  
```

#### 二、text-align：文本水平对齐方式

- 作用：

  text-align属性用于设置文本内容的水平对齐，相当于html中的align对齐属性

- 其可用属性值如下：

| 属性   |       解释       |
| ------ | :--------------: |
| left   | 左对齐（默认值） |
| right  |      右对齐      |
| center |     居中对齐     |

- 注意：

  是让盒子里面的内容水平居中， 而不是让盒子居中对齐

  并且它也可以让父盒子里的 **行内 行内块元素**水平居中

#### 三、块级元素

> 主要注意：在未给宽度的时候，默认是父盒子的`100%`

#### 四、line-height：行高

行高我们利用最多的一个地方是： 可以让单行文本在盒子中垂直居中对齐。

> **文字的行高等于盒子的高度。**

这里情况些许复杂，开始学习，我们可以先从简单地方入手学会。

行高   =  上距离 +  内容高度  + 下距离 

![行高](/medias/imges/HTML5/ignore/1.png )

上距离和下距离总是相等的，因此文字看上去是垂直居中的。

**行高和高度的三种关系**

- 如果 行高 等 高度  文字会 垂直居中
- 如果行高 大于 高度   文字会 偏下 
- 如果行高小于高度   文字会  偏上 

#### 五、css的能继承的属性

>  子元素可以继承父元素的样式（**text-，font-，line-这些元素开头的可以继承，以及color属性**,
>
> 主要： 孩子继承过来的属性权重为`0`

#### 六、内边距(padding)产生的问题

当我们给盒子指定padding值之后， 发生了2件事情：

* 内容和边框 有了距离，添加了内边距。
* 盒子会变大了。

 内盒尺寸计算（元素实际大小）

![盒子模型](/medias/imges/HTML5/ignore/标准盒子模型.png)

- 宽度

  Element Height = content height + padding + border （Height为内容高度）

- 高度

  Element Width = content width + padding + border （Width为内容宽度）

- 盒子的实际的大小 =   内容的宽度和高度 +  内边距   +  边框   

padding不影响盒子大小情况

> 如果没有给一个盒子指定宽度， 此时，如果给这个盒子指定padding， 则不会撑开盒子。块级元素宽度会继承父亲

```html
<style>
    div {
        width: 200px;
        height: 200px;
        background-color: pink;
    }

    p {
        /* width: 200px; */
        height: 30px;
        background-color: purple;
        padding-left: 30px;
        /*特殊情况， 如果这个盒子啊，没有宽度 则padding 不会撑开盒子*/
    }
</style>

<body>
    <div>
        <p>哒哒哒</p>
    </div>
</body>
```
#### 七、盒子居中和文字居中区别

* 让一个块级盒子实现水平居中必须：

  - 盒子必须指定了宽度（width）
  - 然后就给**左右的外边距都设置为auto**，

  ```css
  .header{ width:960px; margin:0 auto;}
  ```

* 文字居中

  盒子内的文字水平居中是  text-align: center,  而且还可以让 `行内元素和行内块居中`对齐
  
#### 八、外边距合并

使用margin定义块元素的**垂直外边距**时，可能会出现外边距的合并。

* 相邻块元素垂直外边距的合并

  - 当上下相邻的两个块元素相遇时，如果上面的元素有下外边距margin-bottom

  - 下面的元素有上外边距margin-top，则他们之间的垂直间距不是margin-bottom与margin-top之和

  - **取两个值中的较大者**这种现象被称为相邻块元素垂直外边距的合并（也称外边距塌陷）。

    ![垂直外边距的合并](/medias/imges/HTML5/ignore/wbjhb.png)

* 嵌套块元素垂直外边距的合并（塌陷）

  - 对于两个嵌套关系的块元素，如果父元素没有上内边距及边框

  - 父元素的上外边距会与子元素的上外边距发生合并

  - 合并后的外边距为两者中的较大者

    ![嵌套垂直外边距的合并](/medias/imges/HTML5/ignore/czbjhb.png)

* **解决方法**

  1. 可以为父元素定义上边框。
  2. 可以为父元素定义上内边距
  3. 可以为父元素添加overflow:hidden。

#### 九、插入图片和背景图片区别

#####9.1缩放层面比较：

插入图片的缩放

> 由于`img`标签是行内块元素，设置的大小只要控制好`width`和`height`比例就好了

背景图片缩放`background-size`

background-size 属性规定背景图像的尺寸

```
background-size: 背景图片宽度 背景图片高度;
```

单位： 长度|百分比|cover|contain;

cover把背景图像扩展至足够大，以使背景图像完全覆盖背景区域。

contain把图像图像扩展至最大尺寸，以使其宽度和高度完全适应内容区域

```css
<style>
div {
    width: 500px;
    height: 500px;
    border: 2px solid red;
    /* 背景图片随意 */
    background: url(images/dog.jpg) no-repeat;
    /* background-size: 图片的宽度 图片的高度; */
    /* background-size: 500px 200px; */
    /* 1.只写一个参数 肯定是宽度 而高度省略了  会等比例缩放 */
    /* background-size: 500px; */
    /* 2. 里面的单位可以跟%  相对于父盒子来说的 */
    /* background-size: 50%; */
    /* 3. cover 等比例拉伸 要完全覆盖div盒子  可能有部分背景图片显示不全 */
    /* background-size: cover; */
    /* 4. contain 高度和宽度等比例拉伸 当宽度 或者高度 铺满div盒子就不再进行拉伸了 可能有部分空白区域 */
    background-size: contain;
}
</style>
<body>
    <div></div>
    <p></p>
</body>
```
##### 9.2移动层面比较：

* 插入图片 我们用的最多 比如产品展示类  移动位置只能靠盒模型 padding margin
* 背景图片我们一般用于小图标背景 或者 超大背景图片  背景图片 只能通过  background-position

```css
 img {  
		width: 200px;/* 插入图片更改大小 width 和 height */
		height: 210px;
		margin-top: 30px;  /* 插入图片更改位置 可以用margin 或padding  盒模型 */
		margin-left: 50px; /* 插入当图片也是一个盒子 */
	}

 div {
		width: 400px;
		height: 400px;
		border: 1px solid purple;
		background: #fff url(images/sun.jpg) no-repeat;
		background-position: 30px 50px; /* 背景图片更改位置 我用 background-position */
	}
```

#### 十、浮动应该注意

浮动：float属性会改变元素display属性。

任何元素都可以浮动。浮动元素会生成一个块级框，而不论它本身是何种元素。 生成的块级框和我们前面的`行内块`极其相似，可以在一行显示并可以被控制大小。

浮动后的盒子和其他盒子的关系

* 浮动元素与父盒子的关系
  * 子盒子的浮动参照父盒子对齐
  * 不会与父盒子的边框重叠，也不会超过父盒子的内边距

![浮动元素与父盒子的关系](/medias/imges/HTML5/ignore/fsgx.png)

* 浮动元素与兄弟盒子的关系

  在一个父级盒子中，如果**前一个兄弟盒子**是：

  - **浮动**的，那么**当前盒子**会与前一个盒子的顶部对齐；
  - **普通流**的，那么**当前盒子**会显示在前一个兄弟盒子的下方。 

  > 浮动只会影响当前的或者是后面的标准流盒子，不会影响前面的标准流。

![浮动元素与兄弟盒子的关系](/medias/imges/HTML5/ignore/ssgx.jpg)

> 建议:
>
> **如果一个盒子里面有多个子盒子，如果其中一个盒子浮动了，其他兄弟也应该浮动。防止引起问题**

#### 十一、 清除浮动

##### 1.为什么要清除浮动

因为父级盒子很多情况下，不方便给高度，但是子盒子浮动就不占有位置，最后父级盒子高度为0，就影响了下面的标准流盒子。

<img src="/medias/imges/HTML5/ignore/n.jpg" />





<img src="/medias/imges/HTML5/ignore/no.jpg" />

- 总结：
  - 由于浮动元素不再占用原文档流的位置，所以它会对后面的元素排版产生影响
  - 准确地说，并不是清除浮动，而是**清除浮动后造成的影响**

##### 2. 清除浮动本质

>  **清除浮动主要为了解决父级元素因为子级浮动引起内部高度为0 的问题。清除浮动之后， 父级就会根据浮动的子盒子自动检测高度。父级有了高度，就不会影响下面的标准流了**

##### 3 清除浮动的方法

在CSS中，clear属性用于清除浮动，

- 语法：

```css 
选择器{clear:属性值;}   clear 清除  
```

| 属性值 | 描述                                       |
| ------ | ------------------------------------------ |
| left   | 不允许左侧有浮动元素（清除左侧浮动的影响） |
| right  | 不允许右侧有浮动元素（清除右侧浮动的影响） |
| both   | 同时清除左右两侧浮动的影响                 |

但是我们实际工作中， 几乎只用 clear: both;

###### 3.1.额外标签法(隔墙法)

```html
是W3C推荐的做法是通过在浮动元素末尾添加一个空的标签例如 <div style=”clear:both”></div>，或则其他标签br等亦可。
```

- 优点： 通俗易懂，书写方便
- 缺点： 添加许多无意义的标签，结构化较差。

###### 3.2.父级添加overflow属性方法

```css
可以给父级添加： overflow为 hidden| auto| scroll  都可以实现。
```

优点：  代码简洁

缺点：  内容增多时候容易造成不会自动换行导致内容被隐藏掉，无法显示需要溢出的元素。

###### 3.3.使用after伪元素清除浮动

**:after 方式为空元素额外标签法的升级版，好处是不用单独加标签了** 

使用方法：

```css
 .clearfix:after {  content: ""; display: block; height: 0; clear: both; visibility: hidden;  }   

 .clearfix {*zoom: 1;}   /* IE6、7 专有 */
```

- 优点： 符合闭合浮动思想  结构语义化正确
- 缺点： 由于IE6-7不支持:after，使用 zoom:1触发 hasLayout。
- 代表网站： 百度、淘宝网、网易等

<img src="/medias/imges/HTML5/ignore/163.png"/>

###### 3.4.使用双伪元素清除浮动

使用方法：

```css
.clearfix:before,.clearfix:after { 
  content:"";
  display:table; 
}
.clearfix:after {
 clear:both;
}
.clearfix {
  *zoom:1;
}
```

- 优点：  代码更简洁
- 缺点：  由于IE6-7不支持:after，使用 zoom:1触发 hasLayout。
- 代表网站： 小米、腾讯等

###### 3.5代码演示

```html
<style>
    /*很多情况下，我们的父盒子，不方便给高度  根据内容撑开，有多少内容，我的父盒子就有多高*/
    .one {
        width: 500px;
        /*background-color: pink;*/
        border: 1px  solid red;

    }
    /*因为damao 二毛 浮动了，不占有位置， 而父级又没有高度， 所以two 就到底下去了*/
    .damao {
        float: left;
        width: 200px;
        height: 200px;
        background-color: purple;
    }
    .ermao {
        float: left;
        width: 250px;
        height: 250px;
        background-color: skyblue;
    }
    .two {
        width: 700px;
        height: 150px;
        background-color: #000;
    }
</style>
// 额外标签法
<style>
    /*清除浮动*/
    .clear {
        clear: both;
    }
</style>

<body>
    <div class="one">
        <div class="damao"></div>
        <div class="ermao"></div>
        <div class="clear"></div>
    </div>
    <div class="two"></div>
</body>

// overflow
<style>
    .one {
        width: 500px;
        /*background-color: pink;*/
        border: 1px  solid red;
        /*给父级添加 overflow 就可以清除浮动*/
        overflow: hidden;
    }
</style>

<body>
    <div class="one">
        <div class="damao"></div>
        <div class="ermao"></div>
    </div>
    <div class="two"></div>
</body>
// after伪元素
<style>
    .clearfix:after {
        content: "";
        display: block;
        height: 0;
        visibility: hidden;
        clear: both;
    }
    .clearfix {
        *zoom: 1;  /*ie6,7 专门清除浮动的样式*/
    }
</style>
<body>
    <div class="one clearfix">
        <div class="damao"></div>
        <div class="ermao"></div>
    </div>
    <div class="two"></div>
</body>

// 双伪元素
<style>
    /*声明清除浮动的样式*/
    .clearfix:before,
    .clearfix:after {
        content: "";
        display: table;
    }
    .clearfix:after {
        clear: both;
    }
    .clearfix {
        *zoom: 1;
    }

</style>
<body>
    <div class="one clearfix">
        <div class="damao"></div>
        <div class="ermao"></div>
    </div>
    <div class="two"></div>
</body>
```

###### 3.6 清除浮动总结

**我们以后什么时候用清除浮动呢？**

1. 父级没高度
2. 子盒子浮动了
3. 影响下面布局了，我们就应该清除浮动了。

| 清除浮动的方式          | 优点               | 缺点                               |
| ----------------------- | :----------------- | :--------------------------------- |
| 额外标签法（隔墙法）    | 通俗易懂，书写方便 | 添加许多无意义的标签，结构化较差。 |
| 父级overflow:hidden;    | 书写简单           | 溢出隐藏                           |
| 父级after伪元素【推荐】 | 结构语义化正确     | 由于IE6-7不支持:after，兼容性问题  |
| 父级双伪元素【推荐】    | 结构语义化正确     | 由于IE6-7不支持:after，兼容性问题  |

#### 十二、定位

> 在定位的过程中，如何你设置上定位之后，原本宽度继承父亲宽度的盒子宽度消失了，变为内容有多宽盒子就有多宽的情况下，请不要慌，这是盒子改变为了`行内块`元素的原因，这时在设置上你要的宽度就好了。

##### 1.静态定位（static）

* 元素的默认定位方式，无定位的意思。元素取消定位时用。

##### 2.相对定位（relative）

* 相对于 自己原来在标准流中位置来移动的

* 原来**在标准流的区域继续占有**，后面的盒子仍然以标准流的方式对待它。

  ![相对定位](/medias/imges/HTML5/ignore/04_相对定位案例.png)

##### 3.绝对定位（absolute）

* 绝对是以带有定位的父级**（绝对、固定或相对定位）**元素来移动位置 （拼爹型） 如果父级都没有定位，则以浏览器文档为准移动位置

* 不保留原来的位置，完全是脱标的

  ![父级无定位](/medias/imges/HTML5/ignore/05_绝对定位_父级无定位.png)

  ![父级有定位](/medias/imges/HTML5/ignore/06_绝对定位_父级有定位.png)

##### 4.固定定位（fixed）

> 固定的盒子一定要给宽度

**固定定位**是**绝对定位**的一种特殊形式： （认死理型）   如果说绝对定位是一个矩形 那么 固定定位就类似于正方形

* **完全脱标** —— 完全不占位置；
* 只认**浏览器的可视窗口** —— `浏览器可视窗口 + 边偏移属性` 来设置元素的位置；
  - 跟父元素没有任何关系；单独使用的
  - 不随滚动条滚动。

##### 5.绝对定位的盒子居中

> **注意**：**绝对定位/固定定位的盒子**不能通过设置 `margin: auto` 设置**水平居中**。

在使用**绝对定位**时要想实现水平居中，可以按照下图的方法：

![.绝对定位的盒子居中](/medias/imges/HTML5/ignore/10_绝对定位水平居中.png)

1. `left: 50%;`：让**盒子的左侧**移动到**父级元素的水平中心位置**；
2. `margin-left: -100px;`：让盒子**向左**移动**自身宽度的一半**。

##### 6.堆叠顺序（z-index）

在使用**定位**布局时，可能会**出现盒子重叠的情况**。

加了定位的盒子，默认**后来者居上**， 后面的盒子会压住前面的盒子。

应用 `z-index` 层叠等级属性可以**调整盒子的堆叠顺序**。如下图所示：

![堆叠顺序](/medias/imges/HTML5/ignore/12_zindex示意图.png)

`z-index` 的特性如下：

1. **属性值**：**正整数**、**负整数**或 **0**，默认值是 0，数值越大，盒子越靠上；
2. 如果**属性值相同**，则按照书写顺序，**后来居上**；
3. **数字后面不能加单位**。

##### 7.定位改变display属性

display 是 显示模式， 可以改变显示模式有以下方式:

- 可以用inline-block  转换为行内块
- 可以用浮动 float 默认转换为行内块（类似，并不完全一样，因为浮动是脱标的）
- 绝对定位和固定定位也和浮动类似， 默认转换的特性 转换为行内块。

所以说， 一个行内的盒子，如果加了**浮动**、**固定定位**和**绝对定位**，不用转换，就可以给这个盒子直接设置宽度和高度等。

**同时注意：**

浮动元素、绝对定位(固定定位）元素的都不会触发外边距合并的问题。 （我们以前是用padding border overflow解决的）

也就是说，我们给盒子改为了浮动或者定位，就不会有垂直外边距合并的问题了。

#### 十三、元素的显示与隐藏

##### 1.display

```css
display: none 隐藏对象

display：block 除了转换为块级元素之外，同时还有显示元素的意思
```

特点： 隐藏之后，不再保留位置。

![display](/medias/imges/HTML5/ignore/29none.png)

##### 2. visibility 可见性

```css
visibility：visible ; 　对象可视

visibility：hidden; 　  对象隐藏
```

特点： 隐藏之后，继续保留原有位置。（停职留薪）

![visibility](/medias/imges/HTML5/ignore/30visibility.png)



#### 十四、vertical-align 垂直对齐

- 有宽度的块级元素居中对齐，是margin: 0 auto;
- 让文字居中对齐，是 text-align: center;

但是我们从来没有讲过有垂直居中的属性。

vertical-align 垂直对齐，它只针对于**行内元素**或者**行内块元素**，

##### 1.图片、表单和文字对齐

所以我们知道，我们可以通过vertical-align 控制图片和文字的垂直关系了。 默认的图片会和文字基线对齐。

![默认](/medias/imges/HTML5/ignore/基线对齐.jpg)

![其他](/medias/imges/HTML5/ignore/show.png)

##### 2.去除图片底侧空白缝隙

![去除图片底侧空白缝隙](/medias/imges/HTML5/ignore/35vertical.png)

* 原因：

  图片或者表单等行内块元素，他的底线会和父级盒子的基线对齐。

  就是图片底侧会有一个空白缝隙。

* 解决的方法就是： 

  * 给img vertical-align:middle | top| bottom等等。  让图片不要和基线对齐。
  * 给img 添加 display：block; 转换为块级元素就不会存在问题了。

#### 十五、溢出的文字省略号显示

##### 第一种方式：

1.white-space

* white-space设置或检索对象内文本显示方式。通常我们使用于强制一行显示内容 

```css
white-space:normal ；默认处理方式

white-space:nowrap ；　强制在同一行内显示所有文本，直到文本结束或者遭遇br标签对象才换行。
```

2.text-overflow 文字溢出

设置或检索是否使用一个省略标记（...）标示对象内文本的溢出

```css
text-overflow : clip ；不显示省略标记（...），而是简单的裁切 

text-overflow：ellipsis ； 当对象内文本溢出时显示省略标记（...）
```

**注意**：

一定要首先强制一行内显示，再次和overflow属性  搭配使用

![text-overflow](/medias/imges/HTML5/ignore/dot.png)**总结三步曲**

```css
/*1. 先强制一行内显示文本*/
white-space: nowrap;
/*2. 超出的部分隐藏*/
overflow: hidden;
/*3. 文字用省略号替代超出的部分*/
text-overflow: ellipsis;
```

##### 第二种方式：

在需要隐藏文本的盒子写上下面的css属性：

```css
/* 将对象作为弹性伸缩盒子模型显示  */
display: -webkit-box;
/* 设置或检索伸缩盒对象的子元素的排列方式 */ 
-webkit-box-orient: vertical;
/* -webkit-line-clamp用来限制在一个块元素显示的文本的行数 */
-webkit-line-clamp: 2;
overflow: hidden;
text-overflow: ellipsis;
```

