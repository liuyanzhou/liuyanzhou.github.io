---
title: HTML5-CSS篇
date: 2020-05-21 20:16:16
categories: HTML5
top: false
summary: 这个一篇关于HTML5中的CSS3
tags: 
 - HTML5
---

#### 一、CSS3介绍

1. 什么是 `CSS3`

   - 在 `CSS2` 的基础上拓展、新增的样式

2. `CSS3` 发展现状
   - 移动端支持优于 `PC` 端
   - `CSS3` 目前还草案，在不断改进中
   - `CSS3` 相对 `H5`，应用非常广泛

#### 二、CSS3选择器

##### 2.1属性选择器

|    选择符     |                 简介                  |
| :-----------: | :-----------------------------------: |
|    E[att]     |        选择具有att属性的E元素         |
| E[att="val"]  | 选择具有att属性且属性值等于val的E元素 |
| E[att^="val"] | 匹配具有att属性、且值以val开头的E元素 |
| E[att$="val"] | 匹配具有att属性、且值以val结尾的E元素 |
| E[att*="val"] | 匹配具有att属性、且值中含有val的E元素 |

```html
 <style>
        /* 属性选择器使用方法 */
        /* 选择的是：  既是button 又有 disabled 这个属性的元素 */
        /* 属性选择器的权重是 10 */
        /* 1.直接写属性 */
        
        button[disabled] {
            cursor: default;
        }
        
        button {
            cursor: pointer;
        }
        /* 2. 属性等于值 */
        
        input[type="search"] {
            color: pink;
        }
        /* 3. 以某个值开头的 属性值 */
       div[class^="icon"] {
            color: red;
        } 
        /* 4. 以某个值结尾的 */
        div[class$="icon"] {
            color: green;
        } 
        /* 5. 可以在任意位置的 */
        div[class*="icon"] {
            color: blue;
        } 
    </style>


<body>
    <!-- disabled 是禁用我们的按钮 -->
    <button>按钮</button>
    <button>按钮</button>
    <button disabled="disabled">按钮</button>
    <button disabled="disabled">按钮</button>

    <input type="text" name="" id="" value="文本框">
    <input type="text" name="" id="" value="文本框">
    <input type="text" name="" id="" value="文本框">
    <input type="search" name="" id="" value="搜索框">
    <input type="search" name="" id="" value="搜索框">
    <input type="search" name="" id="" value="搜索框">
    <div class="icon1">图标1</div>
    <div class="icon2">图标2</div>
    <div class="icon3">图标3</div>
    <div class="iicon3">图标4</div>
    <div class="absicon">图标5</div>
</body>
```

>注意：类选择器、属性选择器、伪类选择器，权重为 **10** 

##### 2.2结构伪类选择器

* 写法：

|      选择符      |            简介            |
| :--------------: | :------------------------: |
|  E:first-child   | 匹配父元素中的第一个子元素 |
|   E:last-child   | 匹配父元素中最后一个E元素  |
|  E:nth-child(n)  | 匹配父元素中的第n个子元素E |
| E:first-of-type  |     指定类型E的第一个      |
|  E:last-of-type  |    指定类型E的最后一个     |
| E:nth-of-type(n) |      指定类型E的第n个      |

* 实例:

```html
<style>
        ul li:first-child {
            background-color: pink;
        }
        
        ul li:last-child {
            background-color: deeppink;
        }
        /* nth-child(n)  我们要第几个，n就是几  比如我们选第8个， 我们直接写 8就可以了 */
        
        ul li:nth-child(8) {
            background-color: lightpink;
        }
        
        ul li:nth-child(3) {
            background-color: aqua;
        }
    </style>

<body>
    <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
        <li>4</li>
        <li>5</li>
        <li>6</li>
        <li>7</li>
        <li>8</li>
        <li>9</li>
        <li>10</li>
    </ul>
</body>
```

* 详细解释写法

  css3的结构伪类选择器**nth-child(n)**:

  * n可以是数字，关键字和公式 
  * n如果是数字，就是选择第n个
  * 常见的关键词 even 偶数 odd 奇数 
  *  常见的公式如下 ( 如果n是公式，则从0开始计算）
  * 但是 第0个元素或者超出了元素的个数会被忽略 ) 

  | 公式 |             取值              |
  | :--: | :---------------------------: |
  |  2n  |             偶数              |
  | 2n+1 |             奇数              |
  |  5n  |     5 10 15 ...(5的倍数)      |
  | n+5  | 从第5个开始(包含第五个)到最后 |
  | -n+5 |       前5个(包含第5个)        |

* 实例:

  ```html
      <style>
          /* n 可是关键词  even 是偶数  odd 是奇数 */
          /* ul li:nth-child(even) {
              background-color: pink;
          }
          
          ul li:nth-child(odd) {
              background-color: hotpink;
          } */
          /* n 是公式  但是 n 从0开始计算 */
          /* ul li:nth-child(n) {
              background-color: pink;
          } */
          /* 2n 偶数  类似于 even */
          /* ul li:nth-child(2n) {
              background-color: pink;
          } */
          /* 2n + 1  类似于 odd */
          /* ul li:nth-child(2n+1) {
              background-color: skyblue;
          } */
          /* 5n  选择第  0  5  10  15 ... */
          /* ul li:nth-child(5n) {
              background-color: pink;
          } */
          /* n+5 就是从第5个开始往后面选择 包含第5个 */
          /* ul li:nth-child(n+5) {
              background-color: pink;
          } */
          /* -n + 5 就是选择前面5个  */
          /* ul li:nth-child(-n+5) {
              background-color: pink;
          } */
          
          ul li:nth-child(n) {
              background-color: red;
          }
      </style>
  
  <body>
      <ul>
          <li>1</li>
          <li>2</li>
          <li>3</li>
          <li>4</li>
          <li>5</li>
          <li>6</li>
          <li>7</li>
          <li>8</li>
          <li>9</li>
          <li>10</li>
      </ul>
  </body>
  ```

* `nth-child` 和  `nth-of-type` 的区别

  * nth-child的查找顺序

    > 例如:`div span:nth-child(1)`这个选择器是：先从div这个父元素中找到第一个元素，在匹配此元素是否是span标签，是的话，这个选择器就生效，否则不生效，权重为`10`

  * nth-of-type

    > 例如：`div span:nth-of-type(1)`这个选择器是： 先从div元素中找到所有的span子元素，在从该子元素群中找到第一个元素，权重为`10`

  * 实例：

    ```html
        <style>
            div :nth-child(1) {
                background-color: pink;
            }
            /* div :nth-child(2) {
                background-color: purple;
            } */
            /* 这个选不到 */
            /* div span:nth-child(1) {
                background-color: pink;
            } */
            /* div span:nth-child(2) {
                background-color: pink;
            } */
            /* 总结： :nth-child(n)  选择 父元素里面的 第 n个孩子， 它不管里面的孩子是否同一种类型 */
            /* of-type 选择指定类型的元素 */
            
            div span:first-of-type {
                background-color: purple;
            }
            
            div span:last-of-type {
                background-color: skyblue;
            }
            
            div span:nth-of-type(2) {
                background-color: red;
            }
        </style>
    
    <body>
        <div>
            <p>我是一个屁</p>
            <span>我是span</span>
            <span>我是span</span>
            <span>我是span</span>
        </div>
        <!-- ul 里面我们只允许放li  所以 nth-child 和 nth-of-type 就一样了 -->
        <ul>
            <li></li>
            <li></li>
        </ul>
    </body>
    ```

    > 注意：当父元素中所有的子元素是一致的话,那么`nth-child`和`nth-of-type`的作用就一样了，看上面的代码块的`ul`，而且它们的权重都是`10`

##### 2.3伪元素选择器

* 写法：

|  选择符  |           简介           |
| :------: | :----------------------: |
| ::before | 在元素内部的前面插入内容 |
| ::after  | 在元素内部的后面插入内容 |

* 实例:

```html
    <style>
        div {
            width: 300px;
            height: 300px;
            border: 1px solid #000;
        }
        
        div::before {
            content: "我";
            display: inline-block;
            width: 100px;
            height: 100px;
            background-color: pink;
        }
        
        div::after {
            content: "小猪佩奇";
            display: inline-block;
            width: 100px;
            height: 100px;
            background-color: pink;
        }
    </style>


<body>
    <div>是</div>
</body>
```

* 注意：
  * `before`和`after`必须有`content`属性
  * `before`在内部的前面，`after`在内容的后面
  * `before`和`after`创建一个元素，但属于行内元素
  * 因为在dom里面看不见刚才创建的元素，所以我们称为`伪元素`
  * 伪元素和标签选择器一样，权重为1

#### 三、`2D` 转换之 `translate`

`2D`转换

* **2D转换**是改变标签在二维平面上的位置和形状
* 移动：`translate`
* 旋转：`rotate`
* 缩放：`scale`

##### 3.1.`translate`语法

* X 就是 X 轴上水平移动

* Y 就是 Y 轴上水平移动

  ```css
  transform: translate(x, y)
  transform: translateX(n)
  transfrom: translateY(n)
  ```

* 注意: 

  * `2D`的移动主要是指 水平、垂直方向上的移动

  * `translate` 最大的有点就是不影响其他元素的位置

  * `translate` 中的100%单位，是相对于本身的宽度和高度来进行计算的

  * 对行内标签没有效果

    ```html
        <style>
            /* 移动盒子的位置： 定位   盒子的外边距  2d转换移动 */
          div {
                width: 200px;
                height: 200px;
                background-color: pink;
            /* x就是x轴上移动位置 y 就是y轴上移动位置 中间用逗号分隔*/
            /* transform: translate(x, y); */
            /* transform: translate(100px, 100px); */
            /* 1. 我们如果只移动x坐标 */
            /* transform: translate(100px, 0); */
            /* transform: translateX(100px); */
            /* 2. 我们如果只移动y坐标 */
            /* transform: translate(0, 100px); */
            /* transform: translateY(100px); */
             }
        	div:first-child {
                transform: translate(30px, 30px);
            }
            
            div:last-child {
                background-color: purple;
            } 
    
        </style>
    
    <body>
        <div></div>
        <div></div>
    </body>
    ```

* 使用`translate`来让一个盒子水平垂直居中

  ```html
  <style>
          div {
              position: relative;
              width: 500px;
              height: 500px;
              background-color: pink;
          }
           p {
              position: absolute;
              top: 50%;
              left: 50%;
              width: 200px;
              height: 200px;
              background-color: purple;
         		/* translate(-50%, -50%)  盒子往上走自己高度的一半   */
          	transform: translate(-50%, -50%);
          }
          span { 
          /* translate 对于行内元素是无效的 */
      	 transform: translate(300px, 300px); 
           }     
      </style>
  
  <body>
      <div>
          <p></p>
      </div>
      <span>123</span>
  </body>
  ```

##### 3.2.`2D`转换之`rotate`

* rotate 旋转

  * `2D`旋转指的是让元素在二维平面内顺时针或者逆时针旋转

* `rotate` 语法

  ```css
  /* 单位是：deg */
  transform: rotate(度数) 
  transform: rotate(45deg);
  ```

* 注意:

  * `rotate` 里面写度数，单位是`deg`
  * 角度为正，顺时针，角度为负，逆时针
  * 默认旋转的中心点是元素的中心点

* 实例

  ```html
  <style>
      img {
          width: 150px;
          /* 顺时针旋转45度 */
          /* transform: rotate(45deg); */
          border-radius: 50%;
          border: 5px solid pink;
          /* 过渡写到本身上，谁做动画给谁加 */
          transition: all 0.3s;
      }
  
      img:hover {
          transform: rotate(360deg);
      }
  </style> 
  
  <body>
      <img src="media/dog.jpg" alt="">
  </body>
  ```

##### 3.3.`transform-origin:x y` 给元素设置中心点

* 语法

  ```css
  transform-origin : x y;
  ```

* 注意：

  * 注意后面的参数 X 和 Y 用空格隔开
  * X Y 魔毯旋转的中心点是元素的中心 (50% 50%) ,等价于 `center` `center`
  * 还可以给 X Y 设置像素或者方位名词(`top`、`left`、`right`、`center`)

* 实例

  ```html
  <style>
      div {
          width: 200px;
          height: 200px;
          border: 1px solid pink;
          overflow: hidden;
          margin: 10px;
          float: left;
      }
  
      div::after {
          content: "";
          width: 100%;
          height: 100%;
          display: block;
          background-color: purple;
          transform: rotate(180deg);
          transform-origin: left bottom;
          transition: all .4s;
      }
  
      div:hover::after {
          transform: rotate(0deg);
      }
  </style>
  
  <body>
      <div></div>
      <div></div>
      <div></div>
  </body>
  ```

##### 3.4.`2D`转换之`scale`

* `scale` 的作用

  * 用来控制元素的放大与缩小

* 语法:

  ```css
  transform: scale(x,y)
  ```

* 注意:

  * 注意，x 与 y 之间使用逗号进行分隔
  * `transform: scale(1, 1)`: 宽高都放大一倍，相当于没有放大
  * `transform: scale(2, 2)`: 宽和高都放大了二倍
  * `transform: scale(2)`: 如果只写了一个参数，第二个参数就和第一个参数一致
  * `transform:scale(0.5, 0.5)`: 缩小
  * `scale` 最大的优势：可以设置转换中心点缩放，默认以中心点缩放，而且不影响其他盒子

* 实例: 

  ```html
  <style>
      div {
          margin: 10px;
          overflow: hidden;
      }
  
      div img {
          transition: all .4s;
      }
  
      div img:hover {
          transform: scale(1.5);
      }
  </style>
  <body>
      <div>
          <a href="#"><img src="media/dog.jpg" alt=""></a>
      </div>
      <div>
          <a href="#"><img src="media/dog.jpg" alt=""></a>
      </div>
      <div>
          <a href="#"><img src="media/dog.jpg" alt=""></a>
      </div>
  </body>
  ```

* 分页按钮

  ```html
  <style>
      li {
          width: 30px;
          height: 30px;
          border: 1px solid pink;
          margin: 10px;
          list-style: none;
          line-height: 30px;
          text-align: center;
          border-radius: 50%;
          cursor: pointer;
          transition: all .4s;
          float: left;
          overflow: hidden;
      }
  
      li:hover {
          transform: scale(1.2);
      }
  </style>
  <body>
      <ul>
          <li>1</li>
          <li>2</li>
          <li>3</li>
          <li>4</li>
          <li>5</li>
          <li>6</li>
          <li>7</li>
      </ul>
  </body>
  ```

#####3.5. `2D`转换综合写法以及顺序问题

* 注意：

  - 同时使用多个转换，其格式为 `transform: translate() rotate() scale()`
  - 顺序会影响到转换的效果(先旋转会改变坐标轴方向)
  - 但我们同时有位置或者其他属性的时候，要将位移放到最前面

* 实例:

  ```html
  <style>
      div {
          width: 200px;
          height: 200px;
          background-color: pink;
          transition: all .5s;
      }
  
      div:hover {
          /* transform: rotate(180deg) translate(150px, 50px); */
          /* 我们同时有位移和其他属性，我们需要把位移放到最前面 */
          /* transform: translate(150px, 50px) rotate(180deg) scale(1.2); */
          transform: translate(150px, 50px) rotate(180deg) scale(1.2);
      }
  </style>
  <body>
      <div></div>
  </body>
  ```

#### 四、动画(animation)

##### 4.1.什么是动画

* 动画是 `CSS3` 中最具颠覆性的特征之一，可通过设置多个节点来精确的控制一个或者一组动画，从而实现复杂的动画效果

* 动画的基本使用

  * 先定义动画
  * 在调用定义好的动画

* 语法格式(定义动画)

  ```css
  @keyframes 动画名称 {
      0% {
          width: 100px;
      }
      100% {
          width: 200px
      }
  }
  ```

* 语法格式(使用动画)

  ```css
  div {
  	/* 调用动画 */
      animation-name: 动画名称;
   	/* 持续时间 */
   	animation-duration: 持续时间；
  }
  ```

* 实例:

  ```html
  <style>
      /* 我们想页面一打开，一个盒子就从左边走到右边 */
      /* 1. 定义动画 */
      @keyframes move {
          /* 开始状态 */
          0% {
              transform: translateX(0px);
          }
          /* 结束状态 */
          100% {
              transform: translateX(1000px);
          }
      }
  
      div {
          width: 200px;
          height: 200px;
          background-color: pink;
          /* 2. 调用动画 */
          /* 动画名称 */
          animation-name: move;
          /* 持续时间 */
          animation-duration: 2s;
      }
  </style>
  
  <body>
      <div></div>
  </body>
  ```

##### 4.2.动画序列：

  * 0% 是动画的开始，100 % 是动画的完成，这样的规则就是动画序列

  - 在 @keyframs 中规定某项 CSS 样式，就由创建当前样式逐渐改为新样式的动画效果
  - 动画是使元素从一个样式逐渐变化为另一个样式的效果，可以改变任意多的样式任意多的次数
  - 用百分比来规定变化发生的时间，或用 `from` 和 `to`，等同于 0% 和 100%

  ```html
  <style>
    @keyframes move {
        0% {
            transform: translate(0, 0);
        }
        25% {
            transform: translate(1000px, 0);
        }
        50% {
            transform: translate(1000px, 500px);
        }
        75% {
            transform: translate(0px, 500px);
        }
        100% {
            transform: translate(0, 0);
        }
      }
  
      div {
          width: 100px;
          height: 100px;
          background-color: pink;
          animation-name: move;
          animation-duration: 10s;
      }
  </style>
  <body>
      <div></div>
  </body>
  ```

##### 4.3.动画常见属性

| 属性                      | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| @keyframes                | 规定动画                                                     |
| animation                 | 所有动画属性的简写属性，除了`animation-play-state`属性       |
| animation-name            | 规定@keyframes 动画的名称。`（必须的）`                      |
| animation-duration        | 规定动画完成一个周期所花费的秒或毫秒,默认是0。`（必须的）`   |
| animation-timing-function | 规定动画的速度曲线，默认是`ease`                             |
| animation-delay           | 规定动画何时开始，默认是0                                    |
| animation-iteration-count | 规定动画被播放的次数，默认是1，还要infinite`(无限次)`        |
| animation-direction       | 规定动画是否下一周期逆向播放，默认是`normal` ，`alternate`逆播放 |
| animation-play-state      | 规定动画是否正在进行或者暂停，默认是`running`,还有`paused`（鼠标放上停止) |
| animation-fill-mode       | 规定动画结束后状态，`backwards`默认，`forwards`（停留在结束状态） |

  ```html
      <style>
          @keyframes move {
              0% {
                  transform: translate(0, 0);
              }
              100% {
                  transform: translate(1000px, 0);
              }
          }
          
          div {
              width: 100px;
              height: 100px;
              background-color: pink;
              /* 动画名称 */
              animation-name: move;
              /* 持续时间 */
              /* animation-duration: 2s; */
              /* 运动曲线 */
              /* animation-timing-function: ease; */
              /* 何时开始 */
              /* animation-delay: 1s; */
              /* 重复次数  iteration 重复的 conut 次数  infinite  无限 */
              /* animation-iteration-count: infinite; */
              /* 是否反方向播放 默认的是 normal  如果想要反方向 就写 alternate */
              /* animation-direction: alternate; */
              /* 动画结束后的状态 默认的是 backwards  回到起始状态 我们可以让他停留在结束状态 forwards */
              /* animation-fill-mode: forwards; */
              /* animation: name duration timing-function delay iteration-count direction fill-mode; */
              /* animation: move 2s linear 0s 1 alternate forwards; */
              /* 前面2个属性 name  duration 一定要写 */
              /* animation: move 2s linear alternate forwards; */
              /* animation-delay: 1s;
              animation-iteration-count: infinite;
              animation-duration: 2s;
              animation-direction: alternate; */
              /* animation-fill-mode: forwards; */
              /* animation: name duration timing-function delay iteration-count direction fill-mode; */
              animation: move 2s ease 1s infinite alternate;
          }
          
          div:hover {
              /* 鼠标经过div 让这个div 停止动画，鼠标离开就继续动画 */
              /* animation-play-state: paused; */
              animation-play-state: paused;
          }
      </style>
  
  <body>
      <div>
      </div>
  </body>
  ```

##### 4.5.动画简写方式

  * 语法顺序

    ```css
    /* animation: 动画名称 持续时间 运动曲线 何时开始 播放次数 是否反方向 起始与结束状态 */
    animation: name duration timing-function delay iteration-count direction fill-mode
    ```

  * 注意：

    - 简写属性里面不包含 `animation-paly-state`
    - 暂停动画 `animation-paly-state: paused`; 经常和鼠标经过等其他配合使用
    - 要想动画走回来，而不是直接调回来：`animation-direction: alternate`
    - 盒子动画结束后，停在结束位置：`animation-fill-mode: forwards` 

  * 代码演示

    ```css
    animation: move 2s linear 1s infinite alternate forwards;
    ```

##### 4.6.大数据热点图

```html
<style>
    body {
        background-color: #333;
    }

    .map {
        position: relative;
        width: 747px;
        height: 616px;
        background: url(media/map.png) no-repeat;
        margin: 0 auto;
    }

    .city {
        position: absolute;
        top: 227px;
        right: 193px;
        color: #fff;
    }

    .tb {
        top: 500px;
        right: 80px;
    }

    .dotted {
        width: 8px;
        height: 8px;
        background-color: #09f;
        border-radius: 50%;
    }

    .city div[class^="pulse"] {
        /* 保证我们小波纹在父盒子里面水平垂直居中 放大之后就会中心向四周发散 */
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 8px;
        height: 8px;
        box-shadow: 0 0 12px #009dfd;
        border-radius: 50%;
        animation: pulse 1.2s linear infinite;
    }

    .city div.pulse2 {
        animation-delay: 0.4s;
    }

    .city div.pulse3 {
        animation-delay: 0.8s;
    }

    @keyframes pulse {
        0% {}
        70% {
            /* transform: scale(5);  我们不要用scale 因为他会让 阴影变大*/
            width: 40px;
            height: 40px;
            opacity: 1;
        }
        100% {
            width: 70px;
            height: 70px;
            opacity: 0;
        }
    }
    </style>

<body>
    <div class="map">
        <div class="city">
            <div class="dotted"></div>
            <div class="pulse1"></div>
            <div class="pulse2"></div>
            <div class="pulse3"></div>
        </div>
</body>
```



##### 4.7.速度曲线细节

  * `animation-timing-function`: 规定动画的速度曲线，默认是`ease`

    | 值          | 描述                                         |
    | ----------- | -------------------------------------------- |
    | linear      | 动画从头到尾的速度都是相同的，匀速           |
    | ease        | 默认。动画以低速开始，然后加快，在结束前变慢 |
    | ease-in     | 动画以低速开始                               |
    | ease-out    | 动画以低速结束                               |
    | ease-in-out | 动画以低速开始和结束                         |
    | steps()     | 指定了时间函数中的间隔数量(步长)             |

  * 实例

    **奔走的熊大**

    ![熊大](/medias/imges/HTML5/css3/bear.png)

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            body {
                background-color: #ccc;
            }      
            div {
                position: absolute;
                width: 200px;
                height: 100px;
                background: url(media/bear.png) no-repeat;
                /* 我们元素可以添加多个动画， 用逗号分隔 */
                animation: bear .4s steps(8) infinite, move 3s forwards;
            }
            
            @keyframes bear {
                0% {
                    background-position: 0 0;
                }
                100% {
                    background-position: -1600px 0;
                }
            }
            
            @keyframes move {
                0% {
                    left: 0;
                }
                100% {
                    left: 50%;
                    /* margin-left: -100px; */
                    transform: translateX(-50%);
                }
            }
        </style>
    </head>
    
    <body>
        <div></div>
    </body>
    
    </html>
    ```


#### 五、3D 转换

##### 5.1.认识3D转换

* `3D` 的特点

  * 近大远小
  * 物体和面遮挡不可见

* 三维坐标系

  * x 轴：水平向右  -- **注意：x 轴右边是正值，左边是负值**
  * y 轴：垂直向下  -- **注意：y 轴下面是正值，上面是负值**
  * z 轴：垂直屏幕  --  **注意：往外边的是正值，往里面的是负值**

  ![3D坐标图](/medias/imges/HTML5/css3/sanwei.png)

##### 5.2.3D转换内容

1.  `3D` 转换知识要点
   * `3D` 位移：`translate3d(x, y, z)`
   * `3D` 旋转：`rotate3d(x, y, z)`
   * 透视：`perspctive`
   * `3D`呈现 `transfrom-style`

2.  `3D` 移动 `translate3d`

   * `3D` 移动就是在 `2D` 移动的基础上多加了一个可以移动的方向，就是 z 轴方向
   * `transform: translateX(100px)`：仅仅是在 x 轴上移动
   * `transform: translateY(100px)`：仅仅是在 y 轴上移动
   * `transform: translateZ(100px)`：仅仅是在 z 轴上移动
   * `transform: translate3d(x, y, z)`：其中x、y、z 分别指要移动的轴的方向的距离[**注意：x, y, z 对应的值不能省略，不需要填写用 0 进行填充**]

3. 演示

   ```css
   transform: translate3d(x, y, z)
   transform: translate3d(100px, 100px, 100px)
   /* 注意：x, y, z 对应的值不能省略，不需要填写用 0 进行填充 */
   transform: translate3d(100px, 100px, 0)
   ```

##### 5.3.透视 `perspective`

1. 知识点讲解

   - 如果想要网页产生 `3D` 效果需要透视(理解成 `3D` 物体投影的 `2D` 平面上)
   - 实际上模仿人类的视觉位置，可视为安排一直眼睛去看
   - 透视也称为视距，所谓的视距就是人的眼睛到屏幕的距离
   - 距离视觉点越近的在电脑平面成像越大，越远成像越小
   - 透视的单位是像素
   - `perspecitve` 给**父级进行设置**

2. 知识要点

   - **透视需要写在被视察元素的父盒子上面**
   - 注意下方图片
     - d：就是视距，视距就是指人的眼睛到屏幕的距离
     - z：就是 z 轴，z 轴越大(正值)，我们看到的物体就越大

     ![透视](/medias/imges/HTML5/css3/perspective.png)          

3. 代码演示

   ```css
   body {
     perspective: 1000px;
   }
   ```

##### 5.4.3D 旋转rotate

> 3D 旋转指可以让元素在三维平面内沿着 x 轴、y 轴、z 轴 或者自定义轴进行旋转

1. 语法
   - `transform: rotateX(45deg)` -- 沿着 x 轴正方向旋转 45 度
   - `transform: rotateY(45deg)` -- 沿着 y 轴正方向旋转 45 度
   - `transform: rotateZ(45deg)` -- 沿着 z 轴正方向旋转 45 度
   - `transform: rotate3d(x, y, z, 45deg)` -- 沿着自定义轴旋转 45 deg 为角度

2. **rotateX()**

   左手准则

   * 左手的手拇指指向 x 轴的正方向
   * 其余手指的弯曲方向就是该元素沿着 x 轴旋转的方向

   ​<img src="/medias/imges/HTML5/css3/rotateX.png">    

   ```html
   <style>
       body {
           perspective: 300px;
       }
   
       img {
           display: block;
           margin: 100px auto;
           transition: all 1s;
       }
   
       img:hover {
           transform: rotateX(45deg);
       }
   </style>
   <body>
       <img src="media/pic.jpg" alt="">
   </body>
   ```

3. **rotateY()**

   左手准则

  * 其余的手指弯曲方向就是该元素沿着 y 轴旋转的方向(正值)
  * 其余的手指弯曲方向就是该元素沿着 y 轴旋转的方向(正值)

<img src="/medias/imges/HTML5/css3/rotateY.png">

4. **rotate3d**

- `transform: rotate3d(x, y, z, deg)` -- 沿着自定义轴旋转 deg 为角度
- x, y, z 表示旋转轴的矢量，是标识你是否希望沿着该轴进行旋转，最后一个标识旋转的角度
  - `transform: rotate3d(1, 1, 0, 180deg)` -- 沿着对角线旋转 45deg
  - `transform: rotate3d(1, 0, 0, 180deg)` -- 沿着 x 轴旋转 45deg

1. 代码演示

   ```html
   <style>
       body {
           perspective: 500px;
       }
   
       img {
           display: block;
           margin: 100px auto;
           transition: all 1s;
       }
   
       img:hover {
           /* transform: rotateZ(180deg); */
           /* transform: rotate3d(x,y,z,deg); */
           /* transform: rotate3d(1, 0, 0, 45deg); */
           /* transform: rotate3d(0, 1, 0, 45deg); */
           transform: rotate3d(1, 1, 0, 45deg);
       }
   </style>
   <body>
       <img src="media/pic.jpg" alt="">
   </body>
   ```

##### 5.5`3D` 呈现 `transform-style`

   1. `transform-style`

      - 控制子元素是否开启三维立体环境
      - `transform-style: flat`  代表子元素不开启 `3D` 立体空间，默认的
      - `transform-style: preserve-3d` 子元素开启立体空间
      - 代码写给**父级**，但是影响的是子盒子

   2. 实例：

      盒子翻转

      ```html
      <style>
          body {
              perspective: 400px;
          }
      
          .box {
              position: relative;
              width: 300px;
              height: 300px;
              margin: 100px auto;
              transition: all .4s;
              /* 让背面的紫色盒子保留立体空间 给父级添加的 */
              transform-style: preserve-3d;
          }
      
          .box:hover {
              transform: rotateY(180deg);
          }
      
          .front,
          .back {
              position: absolute;
              top: 0;
              left: 0;
              width: 100%;
              height: 100%;
              border-radius: 50%;
              font-size: 30px;
              color: #fff;
              text-align: center;
              line-height: 300px;
          }
      
          .front {
              background-color: pink;
              z-index: 1;
          }
      
          .back {
              background-color: purple;
              /* 像手机一样 背靠背 旋转 */
              transform: rotateY(180deg);
          }
      </style>
      
      <body>
          <div class="box">
              <div class="front">黑马程序员</div>
              <div class="back">pink老师这里等你</div>
          </div>
      </body>
      ```

      旋转木马：

      ```html
      <style>
          body {
              perspective: 1000px;
          }
      
          section {
              position: relative;
              width: 300px;
              height: 200px;
              margin: 150px auto;
              transform-style: preserve-3d;
              /* 添加动画效果 */
              animation: rotate 10s linear infinite;
              background: url(media/pig.jpg) no-repeat;
          }
      
          section:hover {
              /* 鼠标放入section 停止动画 */
              animation-play-state: paused;
          }
      
          @keyframes rotate {
              0% {
                  transform: rotateY(0);
              }
              100% {
                  transform: rotateY(360deg);
              }
          }
      
          section div {
              position: absolute;
              top: 0;
              left: 0;
              width: 100%;
              height: 100%;
              background: url(media/dog.jpg) no-repeat;
          }
      
          section div:nth-child(1) {
              transform: rotateY(0) translateZ(300px);
          }
      
          section div:nth-child(2) {
              /* 先旋转好了再 移动距离 */
              transform: rotateY(60deg) translateZ(300px);
          }
      
          section div:nth-child(3) {
              /* 先旋转好了再 移动距离 */
              transform: rotateY(120deg) translateZ(300px);
          }
      
          section div:nth-child(4) {
              /* 先旋转好了再 移动距离 */
              transform: rotateY(180deg) translateZ(300px);
          }
      
          section div:nth-child(5) {
              /* 先旋转好了再 移动距离 */
              transform: rotateY(240deg) translateZ(300px);
          }
      
          section div:nth-child(6) {
              /* 先旋转好了再 移动距离 */
              transform: rotateY(300deg) translateZ(300px);
          }
      </style>
      <body>
          <section>
              <div></div>
              <div></div>
              <div></div>
              <div></div>
              <div></div>
              <div></div>
          </section>
      </body>
      ```




#### 六、背景图片缩放`background-size`

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

#### 七、盒子模型`box-sizing`

传统模式宽度计算：盒子的宽度 = CSS中设置的width + border + padding 

CSS3盒子模型：     盒子的宽度=  CSS中设置的宽度width 里面包含了 border 和 padding 

也就是说，我们的CSS3中的盒子模型， padding 和 border 不会撑大盒子了

```css
/*CSS3盒子模型*/
box-sizing: border-box;
/*传统盒子模型*/
box-sizing: content-box;
```

移动端可以全部CSS3 盒子模型

PC端如果完全需要兼容，我们就用传统模式，如果不考虑兼容性，我们就选择 CSS3 盒子模型

#### 八、盒子阴影

语法：

```css
box-shadow:水平阴影 垂直阴影 模糊距离（虚实）  阴影尺寸（影子大小）  阴影颜色  内/外阴影；
```

属性值:

| 属性值   | 描述                                 |
| -------- | ------------------------------------ |
| h-shadow | 必须，水平阴影的位置。也许负值。     |
| v-shadow | 必须，垂直阴影的位置。也许负值。     |
| blur     | 可选，模糊距离。                     |
| spread   | 可选，阴影的尺寸。                   |
| color    | 可选，阴影的颜色。                   |
| inset    | 可选，将外部阴影(outset)改为内部阴影 |

注意:

* 前两个属性是必须写的。其余的可以省略。
* 外阴影 (outset) 是默认的 但是不能写           想要内阴影可以写  inset 

代码演示：

```html
<style>
    div {
        width: 200px;
        height: 200px;
        background-color: pink;
        margin: 50px auto;
        /*box-shadow:水平阴影 垂直阴影 模糊距离（虚实）  阴影尺寸（影子大小）  阴影颜色  内/外阴影；*/
        box-shadow: 0 15px 30px rgba(0,0,0,.3);
    }
</style>
<body>
    <div></div>
</body>
```

#### 九、背景线性渐变

语法：

```css
background: linear-gradient(起始方向, 颜色1, 颜色2, ...);
background: -webkit-linear-gradient(left, red , blue);
background: -webkit-linear-gradient(left top, red , blue);
```

注意：

* 背景渐变必须添加浏览器私有前缀 
* 起始方向可以是： 方位名词 或者 度数 ， 如果省略默认就是 top 

代码演示:

```html
<style>
    div {
        width: 600px;
        height: 200px;
        /* 背景渐变必须添加浏览器私有前缀 */
        /* background: -webkit-linear-gradient(left, red, blue); */
        /* background: -webkit-linear-gradient(red, blue); */
        background: -webkit-linear-gradient(top left, red, blue);
    }
</style>
<body>
    <div></div>
</body>
```

