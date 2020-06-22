---
title: 03_移动端_rem布局
date: 2020-06-17 20:08:14
categories: Mobile
top: false
summary: 03_移动端_rem布局
tags: 
 - Mobile
---

## rem布局

> rem布局是采用 rem和媒体查询结合在一起实现的

## 一、rem是啥

rem (root em)是一个相对单位，类似于em，em是父元素字体大小。

不同的是rem的基准是相对于html元素的字体大小。

比如，根元素（html）设置font-size=12px; 非根元素设置width:2rem; 则换成px表示就是24px。

代码演示:

```html
<style>
    html {
        font-size: 12px;
    }

    div {
        font-size: 12px;
        width: 15rem;
        height: 15rem;
        background-color: purple;
    }

    p {
        /* 1. em相对于父元素 的字体大小来说的 */
        /* width: 10em;
        height: 10em; */
        /* 2. rem 相对于 html元素 字体大小来说的 */
        width: 10rem;
        height: 10rem;
        background-color: pink;
        /* 3.rem的优点就是可以通过修改html里面的文字大小来改变页面中元素的大小可以整体控制 */
    }
</style>

<body>
    <div>
        <p></p>
    </div>

</body>
```

> rem的优势：父元素文字大小可能不一致， 但是整个页面只有一个html，可以很好来控制整个页面的元素大小。

## 二、媒体查询

### 2.1什么是媒体查询

媒体查询（Media Query）是CSS3新语法。

- 使用 @media查询，可以针对不同的媒体类型定义不同的样式
- @media 可以针对不同的屏幕尺寸设置不同的样式
- 当你重置浏览器大小的过程中，页面也会根据浏览器的宽度和高度重新渲染页面 
- 目前针对很多苹果手机、Android手机，平板等设备都用得到多媒体查询

### 2.2 媒体查询语法规范

- 用 @media开头 注意@符号
- mediatype  媒体类型
- 关键字 and  not  only
- media feature 媒体特性必须有小括号包含

```css
@media mediatype and|not|only (media feature) {
    CSS-Code;
}
```

语法各细节介绍:

* mediatype 查询类型

  >   将不同的终端设备划分成不同的类型，称为媒体类型

  | 值            | 描述                               |
  | ------------- | ---------------------------------- |
  | all           | 用于所有设备                       |
  | print         | 用于打印机和打印预览               |
  | scree`(掌握)` | 用于电脑屏幕、平板电脑、智能手机等 |

* 关键字 and  not  only

  >  关键字将媒体类型或多个媒体特性连接到一起做为媒体查询的条件。

  | 值   | 描述                                              |
  | ---- | ------------------------------------------------- |
  | and  | 可以将多个媒体特性连接到一起，相对于`且`的意思。  |
  | not  | 排除某个媒体类型,相当于`非`的意思。`（了解即可）` |
  | only | 指定某个特定的媒体类型。`（了解即可）`            |

* 媒体特性

  > 每种媒体类型都具体各自不同的特性，根据不同媒体类型的媒体特性设置不同的展示风格。在移动段布局主要掌握下面三个。**这里的数值是相当于的开发像素来说的，并不是物理像素**

  | 值        | 描述                                 |
  | --------- | ------------------------------------ |
  | width     | 定义输出设备中页面可见区域的宽度。   |
  | min-width | 定义输出设备中页面最小可见区域宽度   |
  | max-width | 定义输出设备中页面最大可见区域宽度。 |

* 媒体查询书写规范

  > 注意： 为了防止混乱，媒体查询我们要按照从小到大或者从大到小的顺序来写,但是我们最喜欢的还是从小到大来写，这样代码更简洁

  ![媒体查询书写从小到大](/medias/imges/mobile/rem/3.png) 

## 三、rem + 媒体查询

###3.1 实现元素动态变化

代码演示:

```html
<style>
    * {
        margin: 0;
        padding: 0;
    }

    @media screen and (min-width: 320px) {
        html {
            font-size: 50px;
        }
    }

    @media screen and (min-width: 640px) {
        html {
            font-size: 100px;
        }
    }

    .top {
        height: 1rem;
        font-size: .5rem;
        background-color: green;
        color: #fff;
        text-align: center;
        line-height: 1rem;
    }
</style>
<body>
    <div class="top">购物车</div>
</body>
```

### 3.2 引入资源

代码演示:

```html
<head> 
    <style>
        /* 当我们屏幕大于等于 640px以上的，我们让div 一行显示2个 */
        /* 当我们屏幕小于640 我们让div一行显示一个 */
        /* 一个建议： 我们媒体查询最好的方法是从小到大 */
        /* 引入资源就是 针对于不同的屏幕尺寸 调用不同的css文件 */
    </style>
    <link rel="stylesheet" href="style320.css" media="screen and (min-width: 320px)">
    <link rel="stylesheet" href="style640.css" media="screen and (min-width: 640px)">
</head>
<body>
    <div>1</div>
    <div>2</div>
</body>
```

## 四、rem适配方案&原理

> 1.让一些不能等比自适应的元素，达到当设备尺寸发生改变的时候，等比例适配当前设备。
>
> 2.使用媒体查询根据不同设备按比例设置html的字体大小，然后页面元素使用rem做尺寸单位，当html字体大小变化元素尺寸也会发生变化，从而达到等比缩放的适配。

技术方案：

* less+rem+媒体查询
* flexible.js+rem

总结： 

两种方案现在都存在,flexible.js+rem 更简单

* `适配方案一:less+rem+媒体查询`

①假设设计稿是750px

②假设我们把整个屏幕划分为15等份（划分标准不一可以是20份也可以是10等份）

③每一份作为html字体大小，这里就是50px

④那么在320px设备的时候，字体大小为320/15就是  21.33px

⑤用我们页面元素的大小除以不同的 html字体大小会发现他们比例还是相同的

> 比如:
>
>   我们以750为标准设计稿,一个100*100像素的页面元素在  750屏幕下，  就是 100/ 50  转换为rem  是2rem*2rem  比例是1比1,320屏幕下，  html字体大小为21.33   则 2rem=  42.66px  此时宽和高都是 42.66  但是宽和高的比例还是 1比1,但是已经能实现不同屏幕下  页面元素盒子等比例缩放的效果

总结：

①最后的公式：页面元素的rem值 =  页面元素值（px） /  （屏幕宽度  /  划分的份数）

②屏幕宽度/划分的份数就是 htmlfont-size 的大小

③或者：页面元素的rem值 =  页面元素值（px） /  html font-size 字体大小

* `适配方案二：flexible.js+rem`：

> 手机淘宝团队出的简洁高效 移动端适配库,我们再也不需要在写不同屏幕的媒体查询，因为里面js做了处理
>
> 它的原理是把当前设备划分为10等份，但是不同设备下，比例还是一致的。引入改`js`文件，它会根据设备宽度并结合设备划分10等份的原理帮我们设置好html的`font-size`大小。

github地址：[https://github.com/amfe/lib-flexible](https://link.jianshu.com/?t=https://github.com/amfe/lib-flexible)

## 五 rem+less+媒体查询

> 一般按设计稿`750px`为开发标准做适配方案，rem来做元素适配，less做最不同宽度的手机来计算出所需的rem值，媒体查询是给不同宽度设备设置好不同的html的`font-size`大小

commonJS 公共样式：

```less
// 设置常见的屏幕尺寸 修改里面的html文字大小
a {
    text-decoration: none;
}
// 一定要写到最上面
html {
    font-size: 50px;
}
// 我们此次定义的划分的份数 为 15
@no: 15;
// 320
@media screen and (min-width: 320px) {
    html {
        font-size: 320px / @no;
    }
}
// 360
@media screen and (min-width: 360px) {
    html {
        font-size: 360px / @no;
    }
}
// 375 iphone 678
@media screen and (min-width: 375px) {
    html {
        font-size: 375px / @no;
    }
}

// 384
@media screen and (min-width: 384px) {
    html {
        font-size: 384px / @no;
    }
}

// 400
@media screen and (min-width: 400px) {
    html {
        font-size: 400px / @no;
    }
}
// 414
@media screen and (min-width: 414px) {
    html {
        font-size: 414px / @no;
    }
}
// 424
@media screen and (min-width: 424px) {
    html {
        font-size: 424px / @no;
    }
}

// 480
@media screen and (min-width: 480px) {
    html {
        font-size: 480px / @no;
    }
}

// 540
@media screen and (min-width: 540px) {
    html {
        font-size: 540px / @no;
    }
}
// 720
@media screen and (min-width: 720px) {
    html {
        font-size: 720px / @no;
    }
}

// 750
@media screen and (min-width: 750px) {
    html {
        font-size: 750px / @no;
    }
}
```

##六、flexible.js+rem

* 下载并引入flexible.js

* 确定好设计稿尺寸，这里以`750px`为例子

* 大于750px尺寸的将手动限制死font-size

  ```css
  /* 如果我们的屏幕超过了 750px  那么我们就按照 750设计稿来走 不会让我们页面超过750px */
  @media screen and (min-width: 750px) {
      html {
          font-size: 75px!important;
      }
  }
  ```

* 使用`vs code`插件来自动转换px->rem

  > - 插件名：cssrem ，在vsCode扩展中下载，由于cssrem中css自动转化为rem是参照默认插件的16转换的所以需要自己配置，修好为`以自己的设计稿尺寸除以划分好的font-size`,比如 750px 划分为10等分则为 `750/10 = 75`

  如何设置:

  * 在vscode中找到`settings.json`文件，添加下面这句话

    ```json
    "cssrem.rootFontSize": 75
    ```

  * 重启vscode便完成了

























































