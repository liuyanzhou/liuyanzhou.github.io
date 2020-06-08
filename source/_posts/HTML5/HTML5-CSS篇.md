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