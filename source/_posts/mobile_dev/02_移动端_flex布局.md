---
title: 02_移动端_flex布局
date: 2020-06-17 16:02:52
categories: Mobile
top: false
summary: 02_移动端_flex布局
tags: 
 - Mobile
---

## 一、传统布局和flex布局对比

### 1.1传统布局

+ 兼容性好
+ 布局繁琐
+ 局限性，不能再移动端很好的布局

### 1.2 flex布局

+ 操作方便，布局极其简单，移动端使用比较广泛
+ pc端浏览器支持情况比较差
+ IE11或更低版本不支持flex或仅支持部分

### 1.3 建议

+  如果是pc端页面布局，还是采用传统方式
+  如果是移动端或者是不考虑兼容的pc则采用flex

### 1.4 flex布局原理

+ flex 是 flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性，任何一个容器都可以指定为 flex 布局。
+ 当我们为父盒子设为 flex 布局以后，子元素的 float、clear 和 vertical-align 属性将失效。
+ flex 布局相当于将子元素设置成类似为`行内块元素`，何为类似，因为不换行显示的话，子元素的总宽度不能超过父盒子的宽度，若超过，整体子元素会根据父盒子的宽度来做自适应。
+ flex布局又叫伸缩布局 、弹性布局 、伸缩盒布局 、弹性盒布局 
+ 采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。

**总结**：就是通过给父盒子添加flex属性，来控制子盒子的位置和排列方式

## 二、flex的相关属性与操作

### 2.1父项常见属性

| 属性值名        | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| flex-direction  | 设置主轴的方向                                               |
| justify-content | 设置主轴上的子元素排列方式                                   |
| flex-wrap       | 设置子元素是否换行                                           |
| align-content   | 设置侧轴上的子元素的排列方式`(多行)`                         |
| align-items     | 设置侧轴上的子元素排列方式`(单行)`                           |
| flex-flow       | 复合属性，相当于同时设置了`flex-direction`和`flex-wrap`,取值可以是`flex-flow:row wrap` |

#### 2.1.1  flex-direction设置主轴的方向

- 在 flex 布局中，是分为主轴和侧轴两个方向，同样的叫法有 ： 行和列、x 轴和y 轴
- 默认主轴方向就是 x 轴方向，水平向右
- 默认侧轴方向就是 y 轴方向，水平向下

![flex-direction](/medias/imges/mobile/flex/1.jpg)

> 注意： 主轴和侧轴是会变化的，就看 flex-direction 设置谁为主轴，剩下的就是侧轴。而我们的子元素是跟着主轴来排列的

flex-direction 属性：

| 属性值名           | 描述           |
| ------------------ | -------------- |
| row**（掌握）**    | 默认值从左到右 |
| row-reverse        | 从右到左       |
| column**（掌握）** | 从上到下       |
| column-reverse     | 从上到下       |

```html
<style>
    div {
        /* 给父级添加flex属性 */
        display: flex;
        width: 800px;
        height: 300px;
        background-color: pink;
        /* 默认的主轴是 x 轴 行 row  那么y轴就是侧轴喽 */
        /* 我们的元素是跟着主轴来排列的 */
        /* flex-direction: row; */
        /* 简单了解 翻转 */
        /* flex-direction: row-reverse; */
        /* 我们可以把我们的主轴设置为 y轴 那么 x 轴就成了侧轴 */
        flex-direction: column;
    }

    div span {
        width: 150px;
        height: 100px;
        background-color: purple;
    }
</style>
<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </div>
</body>
```



#### 2.1.2 justify-content 设置主轴上的子元素排列方式`(单行元素)`

 justify-content 属性

| 属性值名      | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| flex-start    | 默认值从头部开始 如果主轴是`X轴`,则从左到右                  |
| flex-end      | 从尾部开始排列                                               |
| center        | 在主轴居中对齐(如果主轴是x轴 则水平居中)                     |
| space-around  | 平分剩余空间`(元素先排在一起，父盒子还剩余空间，子元素便平分)` |
| space-between | 先两边贴边，在平分剩余空间`(重要)`                           |

代码演示：

```html
<style>
    div {
        display: flex;
        width: 800px;
        height: 300px;
        background-color: pink;
        /* 默认的主轴是 x 轴 row */
        flex-direction: row;
        /* justify-content: 是设置主轴上子元素的排列方式 */
        /* justify-content: flex-start; */
        /* justify-content: flex-end; */
        /* 让我们子元素居中对齐 */
        /* justify-content: center; */
        /* 平分剩余空间 */
        /* justify-content: space-around; */
        /* 先两边贴边， 在分配剩余的空间 */
        justify-content: space-between;
    }

    div span {
        width: 150px;
        height: 100px;
        background-color: purple;
    }
</style>
<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
        <span>4</span>
    </div>
</body>
```

#### 2.1.3 flex-wrap 设置是否换行

| 属性值名 | 描述   |
| -------- | ------ |
| nowrap   | 不换行 |
| wrap     | 换行   |

代码演示:

```html
<style>
    div {
        display: flex;
        width: 600px;
        height: 400px;
        background-color: pink;
        /* flex布局中，默认的子元素是不换行的， 如果装不开，会缩小子元素的宽度，放到父元素里面  */
        /* flex-wrap: nowrap; */
        flex-wrap: wrap;
    }

    div span {
        width: 150px;
        height: 100px;
        background-color: purple;
        color: #fff;
        margin: 10px;
    }
</style>
<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
        <span>4</span>
        <span>5</span>
    </div>
</body>
```

- 默认情况下，项目都排在一条线（又称”轴线”）上。flex-wrap属性定义，flex布局中默认是不换行的。
- nowrap 不换行
- wrap 换行

#### 2.1.4 align-items 设置侧轴上的子元素排列方式（单行 ）

> 该属性是控制子项在侧轴（默认是y轴）上的排列方式  在子项为单项（单行）的时候使用

align-items 属性名:

| 属性值名   | 描述                                       |
| ---------- | ------------------------------------------ |
| flex-start | 从头开始                                   |
| flex-end   | 从尾部开始                                 |
| center     | 居中显示                                   |
| stretch    | 拉伸`(主轴是X不给高度，主轴是Y轴不给宽度)` |

```css
<style>
div {
    display: flex;
    width: 800px;
    height: 400px;
    background-color: pink;
    /* 默认的主轴是 x 轴 row */
    flex-direction: column;
    justify-content: center;
    /* 我们需要一个侧轴居中 */
    /* 拉伸，主轴是X不给高度，主轴是Y轴不给宽度*/
   	 align-items: stretch;
    /* align-items: center; */
    /* align-content: center; */
}

div span {
    /* width: 150px; */
    height: 100px;
    background-color: purple;
    color: #fff;
    margin: 10px;
}
</style>

<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </div>
</body>
```

#### 2.1.5  align-content  设置侧轴上的子元素的排列方式（多行）

> 设置子项在侧轴上的排列方式 并且只能用于子项出现 换行 的情况（多行）,也就是`flex-wrap="wrap`"生效的情况下，在单行下是没有效果的。

align-content 属性值：

| 属性值名      | 描述                                   |
| ------------- | -------------------------------------- |
| flex-start    | 默认在侧轴的头部开始排列               |
| flex-end      | 在侧轴的尾部开始排列                   |
| center        | 在侧轴中间显示                         |
| space-around  | 子项在侧轴平分剩余空间                 |
| space-between | 子项在侧轴先分布在两头，在平分剩余空间 |
| stretch       | 设置子项元素高度平分分元素高度         |

```html
<style>
    div {
        display: flex;
        width: 800px;
        height: 400px;
        background-color: pink;
        /* 换行 */
        flex-wrap: wrap;
        /* 因为有了换行，此时我们侧轴上控制子元素的对齐方式我们用 align-content */
        /* align-content: flex-start; */
        /* align-content: center; */
        /* align-content: space-between; */
        align-content: space-around;
    }

    div span {
        width: 150px;
        height: 100px;
        background-color: purple;
        color: #fff;
        margin: 10px;
    }
</style>
<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
        <span>4</span>
        <span>5</span>
        <span>6</span>
    </div>
</body>
```

#### 2.1.6 align-content 和align-items区别

- align-items  适用于单行情况下， 只有上对齐、下对齐、居中和 拉伸
- align-content适应于换行（多行）的情况下（单行情况下无效）， 可以设置 上对齐、下对齐、居中、拉伸以及平均分配剩余空间等属性值。 
- 总结就是单行找align-items  多行找 align-content

> 同时注意：不管 `align-items 或 align-content`的用法和` justify-content` 用法很相似，只是一个设置主轴还是侧轴之分。

####2.1.7 flex-flow 

> flex-flow 属性是 flex-direction 和 flex-wrap 属性的复合属性
>
> flex-flow:row wrap;

```html
<style>
    div {
        display: flex;
        width: 600px;
        height: 300px;
        background-color: pink;
        /* flex-direction: column;
        flex-wrap: wrap; */
        /* 把设置主轴方向和是否换行（换列）简写 */
        flex-flow: column wrap;
    }

    div span {
        width: 150px;
        height: 100px;
        background-color: purple;
    }
</style>
<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
        <span>4</span>
        <span>5</span>
    </div>
</body>
```

### 2.2 子项常见属性

| 属性名      | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| align-self  | 控制子项自己在侧轴的排列方式                                 |
| order       | 定义子项的排列顺序（前后顺序）                               |
| flex        | 子项目占的份数                                               |
| flex-grow   | 用于设置或检索弹性盒子的扩展比率                             |
| flex-shrink | 用于指定`flex`元素的收缩规则，`flex`元素仅在默认宽度之和大于容器的时候才会发生收缩，其收缩大小是依据`flex-shrink`的值 |
| flex-basis  | 用于设置或检索弹性盒伸缩基准值                               |

#### 2.2.1 order 属性定义项目的排列顺序

> 数值越小，排列越靠前，默认为0。
>
> 注意：和 z-index 不一样。

语法:

```css
.item {
    order: <number>;
}
```

代码演示:

```html
<style>
    div {
        display: flex;
        width: 80%;
        height: 300px;
        background-color: pink;
        /* 让三个子盒子沿着侧轴底侧对齐 */
        /* align-items: flex-end; */
        /* 我们想只让3号盒子下来底侧 */
    }

    div span {
        width: 150px;
        height: 100px;
        background-color: purple;
        margin-right: 5px;
    }

    div span:nth-child(2) {
        /* 默认是0   -1比0小所以在前面 */
        order: -1;
    }
</style>
<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </div>
</body>
```

#### 2.2.2 flex-grow

> `flex-grow`属性定义项目的放大比例，默认是`0`，即如果存在剩余空间，也不放大。如果所有项目`flex-grow`属性都为`1`，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为`2`，其他项目都为`1`，则前者占据的剩余空间将比其他项目多一倍，**前提该项目是弹性伸缩盒子**

![flex-grow](/medias/imges/mobile/flex/flex-grow.jpg)

```css
.item {
    flex-grow:<number> /* default:0 */
}
```

#### 2.2.3 flex-shrink

> `flex-shrink`属性定义了项目的缩小比例，默认为`1`，即如果空间不足，该项目将缩小，**前提该项目是弹性伸缩盒子**

![flex-shrink](/medias/imges/mobile/flex/flex-shrink.jpg)

```css
.item {
    flex-shrink:<number> /* default 1 */
}
```

如果所有项目的`flex-shrink`属性都为`1`，当空间不足时，都将等比例缩小。如果一个项目的`flex-shrink`属性为`0`，其他项目都为`1`，则空间不足时，前者不缩小。负值对该属性无效。

#### 2.2.4 flex-basis

> `flex-basis`属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。
>
> * 它的默认值为`auto`，即项目的宽度/高度初始值是根据我们设置的`width/height`的决定，
> * 当它的为`auto`，但我们未设置`width/height`时，项目的宽高初始值都为`0`
>
> https://www.jianshu.com/p/7827e7ecfd64

```css
.item {
    flex-basis: <length> | auto /* default : auto */
}
```

比如：

```html
<style>
    .parent {
        width: 400px;
        display: flex;
        height: 100px;
    }
    /* 剩余空间 400 - 50 = 350px  */

    .child1 {
        /* flex-basis:0 */
        width: 200px;
        flex-shrink: 1;
        flex-grow: 1;
        flex-basis:0;
        background: red;
        /* 0 + 87.5 */
    }

    .child2 {
        width: 300px;
        flex-shrink: 1;
        flex-grow: 2;
        flex-basis: 50px;
        background: gray;
        /* 50 + 175 = 225 */
    }

    .child3 {
        flex-shrink: 1;
        flex-grow: 1;
        background: green;
        /* 87.5 */
    }
</style>
<body>
    <div class="parent">
        <div class="child1"></div>
        <div class="child2"></div>
        <div class="child3"></div>
    </div>
</body>
```

> * 当child1，child2，child3的宽度初始值分配好，父容器的剩余空间为`400-50 =350px`
> * child1：由于`flex-basis:0`，所以即使`width=200px`，其`width`的初始值也是`0`
> * child2：由于`flex-basis:50px`，即使设置`wdith=300px`也不起作用，故其的`width`的初始值为`50px`
> * child3：由于`flex-basis:auto`，但未设置`width`值，故其`width`初始值为`0`
>
> 最终child1，child2，child3的宽度是由它们以上的初始宽度 + 其所占剩余宽度的百分比(`flex-grow`)，例如`child1`占`1/(1+2+1)`

#### 2.2.5 flex属性

> * flex属性是定义子项目分配剩余空间，用flex来表示占多少份数，并且其也是`flex-grow,flex-shrink,flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选
> * `flex:none;`代表的意思等同于`flex:0 0 auto`
> * `flex:auto`代表的元素是`flex:1 1 auto`
> * flex：number;当`flex`取值为一个非负数字，则该数字为`flex-grow`的值，`flex-shrink`的值为`1`，`flex-basis`的值为`0%`

语法:

```css
.item {
    flex: none | [<flex-grow> <flex-shrink> <flex-basis>]
}
```

代码演示:

```html
<style>
    section {
        display: flex;
        width: 60%;
        height: 150px;
        background-color: pink;
        margin: 0 auto;
    }

    section div:nth-child(1) {
        width: 100px;
        height: 150px;
        background-color: red;
    }

    section div:nth-child(2) {
        flex: 1;
        background-color: green;
    }

    section div:nth-child(3) {
        width: 100px;
        height: 150px;
        background-color: blue;
    }

    p {
        display: flex;
        width: 60%;
        height: 150px;
        background-color: pink;
        margin: 100px auto;
    }

    p span {
        flex: 1;
    }

    p span:nth-child(2) {
        flex: 2;
        background-color: purple;
    }
</style>
<body>
    <section>
        <div></div>
        <div></div>
        <div></div>
    </section>
    <p>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </p>
</body>
```

#### 2.2.6 align-self 控制子项自己在侧轴上的排列方式

align-self 属性允许单个项目有与其他项目不一样的对齐方式，可覆盖 align-items 属性。

默认值为 auto，表示继承父元素的 align-items 属性，如果没有父元素，则等同于 stretch。

![flex-shrink](/medias/imges/mobile/flex/align-self.jpg)

| 属性值名   | 描述                                       |
| ---------- | ------------------------------------------ |
| flex-start | 默认在侧轴的头部开始排列                   |
| flex-end   | 在侧轴的尾部开始排列                       |
| center     | 在侧轴中间显示                             |
| stretch    | 拉伸`(主轴是X不给高度，主轴是Y轴不给宽度)` |

```html
<style>
    div {
        display: flex;
        width: 80%;
        height: 300px;
        background-color: pink;
        /* 让三个子盒子沿着侧轴底侧对齐 */
        /* align-items: flex-end; */
        /* 我们想只让3号盒子下来底侧 */
    }

    div span {
        width: 150px;
        height: 100px;
        background-color: purple;
        margin-right: 5px;
    }
    div span:nth-child(3) {
        align-self: flex-end;
    }
</style>
<body>
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </div>
</body>
```