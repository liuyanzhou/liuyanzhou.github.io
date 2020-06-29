---
title: DOM
date: 2020-06-27 12:39:00
categories: JS
top: false
summary: DOM
tags: 
 - JS
---

## 一、什么是DOM

> ​	文档对象模型（Document Object Model，简称DOM，是 [W3C](https://baike.baidu.com/item/W3C) 组织推荐的处理[可扩展标记语言](https://baike.baidu.com/item/%E5%8F%AF%E6%89%A9%E5%B1%95%E7%BD%AE%E6%A0%87%E8%AF%AD%E8%A8%80)（html或者xhtml）的标准[编程接口](https://baike.baidu.com/item/%E7%BC%96%E7%A8%8B%E6%8E%A5%E5%8F%A3),通过这些 DOM 接口可以改变网页的内容、结构和样式并且所有的浏览器都遵循这套标准。

在网页中HTML的元素可以看做被渲染成是一种树形结构，也就是我们所说的**DOM树**

![DOM树](/medias/imges/js/dom/img1.png)

DOM树 又称为文档树模型，把文档映射成树形结构，通过节点对象对其处理，处理的结果可以加入到当前的页面。

- 文档：一个页面就是一个文档，DOM中使用document表示
- 节点：网页中的所有内容，在文档树中都是节点（标签、属性、文本、注释等），使用node表示
- 标签节点：网页中的所有标签，通常称为元素节点，又简称为“元素”，使用element表示

## 二、获取元素

| 方法名                                  | 描述                       | 返回值                                     |
| --------------------------------------- | -------------------------- | ------------------------------------------ |
| document.getElementById(id)             | 根据ID获取元素对象         | 元素对象 或 null                           |
| document.getElementsByTagName('标签名') | 根据标签名获取元素对象     | 元素对象集合（伪数组，数组元素是元素对象） |
| document.getElementsByClassName('类名') | 根据类名获取元素           | 根据类名返回对象集合                       |
| document.querySelector('选择器')        | 根据指定选择器获取元素对象 | 第一个元素对象                             |
| document.querySelectorAll('选择器')     | 根据指定选择器获取元素对象 | 返回数据集合                               |

**body和html元素获取**

* body

```js
document.body //返回body元素对象
```

* html

```js
document.documentElement // 返回html元素对象
```

## 三、事件

### 3.1注册事件

* 传统注册方式：
  * 利用`on`开头的事件 例如 btn.onclick = function() {}
  * 特点:注册事件具有唯一性，同个元素同个事件只能设置一个，最后注册的处理函数将会覆盖前面注册的处理函数
* 监听注册方式：
  * w3c标准推荐方式 `addEventListener()`它是一个方法
  * IE9之前的IE不支持此方法，可使用`attachEvent()`代替
  * 特点：同个元素同个事件可以注册多个监听器，按注册顺序依次执行

### 3.2事件监听

> **addEventListener()事件监听(IE9以后支持)**

语法:

```js
eventTarget.addEventListener(type,listener,[useCapture])
```

eventTarget.addEventListerner()方法指定的监听器注册到`eventTarget(目标对象)`上，当改对象触发指定的事件时，就会执行事件处理函数

参数;

| 参数名     | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| type       | 事件类型字符串，比如 click、mouseover、**注意这里不要带on**  |
| listener   | 事件处理函数，事件发生时，会调用该监听函数                   |
| useCapture | 可选参数，是个布尔值，默认是false，执行冒泡过程，改为true则为捕获过程 |

**扩展:attachEvent()事件监听 （IE678支持)**

语法：

```js
eventTarget.attachEvent(eventNameWithOn,callback)
```

> eventTarget.attachEvent()方法将指定的监听器注册到 eventTarget（目标对象） 上，当该对象触发指定的事件时，指定的回调函数就会被执行。

参数:

| 参数名          | 描述                                                   |
| --------------- | ------------------------------------------------------ |
| eventNameWithOn | 事件类型字符串，比如 onclick、onmouseover 这里要带`on` |
| callback        | 事件处理函数，当目标触发时间的回调函数被调用           |

**代码演示**

```html
<button>传统注册事件</button>
<button>方法监听注册事件</button>
<button>ie9 attachEvent</button>
<script>
    var btns = document.querySelectorAll('button');
    // 1. 传统方式注册事件
    btns[0].onclick = function() {
        alert('hi');
    }
    btns[0].onclick = function() {
            alert('hao a u');
        }
   // 2. 事件侦听注册事件 addEventListener 
   // (1) 里面的事件类型是字符串 必定加引号 而且不带on
   // (2) 同一个元素 同一个事件可以添加多个侦听器（事件处理程序）
    btns[1].addEventListener('click', function() {
        alert(22);
    })
    btns[1].addEventListener('click', function() {
            alert(33);
    })
    // 3. attachEvent ie9以前的版本支持
    btns[2].attachEvent('onclick', function() {
        alert(11);
    })
</script>
```

**事件监听兼容性解决方案**

兼容性处理的原则： 首先照顾大多数浏览器，再处理特殊浏览器 

```js
function addEventListener(element, eventName, fn) {
    // 判断当前浏览器是否支持 addEventListener 方法
    if (element.addEventListener) {
        element.addEventListener(eventName, fn); // 第三个参数 默认是false
    } else if (element.attachEvent) {
        element.attachEvent('on' + eventName, fn);
    } else {
        // 相当于 element.onclick = fn;
        element['on' + eventName] = fn;
    }
```

### 3.3删除事件(解绑事件)

两种方式：

* 传统方式:

  > eventTarget.onclick  = null

* 监听注册方式:

  > eventTarget.removeEventListener(type,listener,[userCapture]) 
  >
  > 或
  >
  > eventTarget.detachEvent(eventNmaeWithOn,callback) **适用于attachEvent()绑定的事件解绑**

```html
<style>
    div {
        width: 100px;
        height: 100px;
        background-color: pink;
    }
</style>
<body>
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <script>
        var divs = document.querySelectorAll('div');
        divs[0].onclick = function() {
            alert(11);
            // 1. 传统方式删除事件
            divs[0].onclick = null;
        }
        // 2. removeEventListener 删除事件
        divs[1].addEventListener('click', fn) // 里面的fn 不需要调用加小括号

        function fn() {
            alert(22);
            divs[1].removeEventListener('click', fn);
        }
        // 3. detachEvent
        divs[2].attachEvent('onclick', fn1);

        function fn1() {
            alert(33);
            divs[2].detachEvent('onclick', fn1);
        }
    </script>
</body>
```

**删除事件兼容性解决方案**

```js
function removeEventListener(element, eventName, fn) {
 // 判断当前浏览器是否支持 removeEventListener 方法
 if (element.removeEventListener) {
 element.removeEventListener(eventName, fn); // 第三个参数 默认是false
 } else if (element.detachEvent) {
 element.detachEvent('on' + eventName, fn);
 } else {
 element['on' + eventName] = null;
}
```

### 3.4DOM事件流

> html中的标签都是相互嵌套的，我们可以将元素想象成一个盒子装一个盒子，document是最外面的大盒子。
> 当你单击一个div时，同时你也单击了div的父元素，甚至整个页面。执行过程可分为**捕获阶段**和**冒泡阶段**

事件流：描述的是从页面中接收事件的顺序,事件发生时会在元素节点之间按照热定的顺序传播，这个传播过程即：**DOM事件流**

比如：我们给页面中的一个div注册了单击时间，当你单击了div时，也就单击了body，html，甚至document

**事件流触发过程**

![事件流触发过程](/medias/imges/js/dom/img2.png)

图中提到的事件冒泡和事件捕获

* 事件捕获：网景最早提出，有DOM最顶层节点开始，然后逐级向下传播到具体的元素接收的过程

* 事件冒泡：IE最早提出，事件开始时由最具体的元素接收，然后逐级向上传播到`DOM`最顶层节点的过程

> 当时的2大浏览器霸主谁也不服谁！
> IE 提出从目标元素开始，然后一层一层向外接收事件并响应，也就是冒泡型事件流。
> Netscape（网景公司）提出从最外层开始，然后一层一层向内接收事件并响应，也就是捕获型事件流。
>
> 江湖纷争，武林盟主也脑壳疼！！！
>
> 最终，w3c 采用折中的方式，平息了战火，制定了统一的标准 —--— 先捕获再冒泡。
> 现代浏览器都遵循了此标准，所以当事件发生时，会经历3个阶段。

### 3.5DOM事件流经历过程

DOM 事件流会经历3个阶段： 

1. 捕获阶段
2. 当前目标阶段
3. 冒泡阶段 

> 我们可以将这个过程想象为:我们向水里面扔一块石头，首先它会有一个下降的过程，这个过程就可以理解为从最顶层向事件发生的最具体元素（目标点）的捕获过程；之后会产生泡泡，会在最低点（ 最具体元素）之后漂浮到水面上，这个过程相当于事件冒泡。 

![事件流](/medias/imges/js/dom/img3.png)

**注意**：

* JS 代码中只能执行捕获或者冒泡其中的一个阶段。
* onclick 和 attachEvent 只能得到冒泡阶段。 
* addEventListener(type, listener[, useCapture])第三个参数如果是 true，表示在事件捕 获阶段调用事件处理程序；如果false（不写默认就是false），表示在事件冒泡阶段调用事件处理程序。 
* **实际开发中我们很少使用事件捕获，我们更关注事件冒泡**
* **有些事件是没有冒泡的，比如 onblur、onfocus、onmouseenter、onmouseleave**

### 3.6事件对象

```js
eventTarget.onclick = function(event) {} 
 eventTarget.addEventListener('click', function(event) {}）
 // 这个 event 就是事件对象，我们还喜欢的写成 e 或者 evt
```

官方解释:event 对象代表事件的状态，比如键盘按键的状态、鼠标的位置、鼠标按钮的状态。 

简单理解：事件发生后，跟事件相关的一系列信息数据的集合都放到这个对象里面，这个对象就是事件对象 event，它有很多属性和方法。比如：谁绑定了这个事件，鼠标触发事件的话，会得到鼠标的相关信息，如鼠标位置。键盘触发事件的话，会得到键盘的相关信息，如按了哪个键 

**事件对象的使用:**

```js
eventTarget.onclick = function(event) {
    // 这个 event 就是事件对象，我们还喜欢的写成 e 或者 evt 
} 
eventTarget.addEventListener('click', function(event) {
    // 这个 event 就是事件对象，我们还喜欢的写成 e 或者 evt 
}）
```

注意：这个 event 是个形参，系统帮我们设定为事件对象，不需要传递实参过去。 

当我们注册事件时， event 对象就会被**系统自动创建**，并依次传递给事件监听器（事件处理函数）。

**事件对象的兼容性处理**

1. 标准浏览器中是浏览器给方法传递的参数，只需要定义形参 e 就可以获取到。
2. 在 IE6~8 中，浏览器不会给方法传递参数，如果需要的话，需要到 window.event 中获取查找。

```html
<div>123</div>
<script>
    var div = document.querySelector('div');
    div.onclick = function(e) {
        // 事件对象
        e = e || window.event; // 采用 || 短路运算符处理
        console.log(e);
    }
</script>
```

### 3.7事件对象的属性和方法

| 事件对象属性方法    | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| e.target            | 返回触发事件的对象 **(标准)**                                |
| e.srcElement        | 返回触发事件的对象 **（非标准 IE678使用）**                  |
| e.type              | 返回事件的类型 比如 click、mouseover、不带`on`               |
| e.cancelBubble      | 该属性阻止冒泡**（非标准 IE6~8使用）**，比如不让链接跳转     |
| e.returnValue       | 该属性阻止了默认事件（默认行为）**非标准** IE6~8使用 比如不让链接跳转 |
| e.preventDefault()  | 该属性阻止了默认事件（默认行为）**标准** IE6~8使用 比如不让链接跳转 |
| e.stopPropagation() | 阻止冒泡 **(标准)**                                          |
| e.currentTarget     | 指向绑定事件元素和`this`相似,但IE6~8不认识                   |

* e.target 和 this 与 的区别

> 常情况下terget 和 this是一致的，
> 但有一种情况不同，那就是在事件冒泡时（父子元素有相同事件，单击子元素，父元素的事件处理函数也会被触发执行），
> 	这时候this指向的是父元素，因为它是绑定事件的元素对象，currentTarget 指向的也是父元素，由于兼容性我们一般使用this
> 	而target指向的是子元素，因为他是触发事件的那个具体元素对象。
>
> 

```html
<ul>
    <li>abc</li>
    <li>abc</li>
    <li>abc</li>
</ul>
<script>
    var ul = document.querySelector('ul');
    ul.addEventListener('click', function(e) {
        // 我们给ul 绑定了事件  那么this 就指向ul  
        console.log(this); // ul
        console.log(e.currentTarget) // ul

        // e.target 触发了事件的对象 我们点击的是li e.target 指向的就是li
        console.log(e.target); // li
    });
</script>
```

* 阻止默认行为

> html中一些标签有默认行为，例如a标签被单击后，默认会进行页面跳转。

| 方式                  | 描述                         |
| --------------------- | ---------------------------- |
| e.preventDefault()    | 标准方法法                   |
| e.returnValue = false | 低版本浏览器                 |
| return false;         | 会将阻止执行return之后的代码 |

```html
<a href="http://www.baidu.com">百度</a>
<script>
    // 2. 阻止默认行为 让链接不跳转 
    var a = document.querySelector('a');
    a.addEventListener('click', function(e) {
        e.preventDefault(); //  dom 标准写法
    });
    // 3. 传统的注册方式
    a.onclick = function(e) {
        // 普通浏览器 e.preventDefault();  方法
        e.preventDefault();
        // 低版本浏览器 ie678  returnValue  属性
        e.returnValue = false;
        // 我们可以利用return false 也能阻止默认行为 没有兼容性问题
        return false;
    }
</script>
```

* 阻止事件冒泡

| 方式                             | 描述      |
| -------------------------------- | --------- |
| e.stopPropagation()              | 标准写法  |
| window.event.cancelBubble = true | 兼容IE678 |

```html
<div class="father">
    <div class="son">son儿子</div>
</div>
<script>
    // 常见事件对象的属性和方法
    // 阻止冒泡  dom 推荐的标准 stopPropagation() 
    var son = document.querySelector('.son');
    son.addEventListener('click', function(e) {
        alert('son');
        e.stopPropagation(); // stop 停止  Propagation 传播
        e.cancelBubble = true; // 非标准 cancel 取消 bubble 泡泡
    }, false);

    var father = document.querySelector('.father');
    father.addEventListener('click', function() {
        alert('father');
    }, false);
    document.addEventListener('click', function() {
        alert('document');
    })
</script>
```

**阻止事件冒泡的兼容性处理**

```js
if(e && e.stopPeopagation) {
    e.stopPropagation();
}else {
    window.event.cancelBubble = true;
}
```

### 3,8事件委托

> 事件委托也称为事件代理， 在 jQuery 里面称为事件委派。 
>
> 原理：不是每个子节点单独设置事件监听器，而是事件监听器设置在其父节点上，然后利用冒泡原理影响设置每个子节点。 
>
> **作用：**
>
>   1.我们只操作了一次 DOM ，提高了程序的性能。
>
> 2. 动态新车间的子元素，也拥有事件

```html
<ul>
    <li>知否知否，点我应有弹框在手！</li>
    <li>知否知否，点我应有弹框在手！</li>
    <li>知否知否，点我应有弹框在手！</li>
    <li>知否知否，点我应有弹框在手！</li>
    <li>知否知否，点我应有弹框在手！</li>
</ul>
<script>
    // 事件委托的核心原理：给父节点添加侦听器， 利用事件冒泡影响每一个子节点
    var ul = document.querySelector('ul');
    ul.addEventListener('click', function(e) {
        // e.target 这个可以得到我们点击的对象
        e.target.style.backgroundColor = 'pink';
    })
</script>
```

### 3.9常用的事件

* 鼠标事件

| 事件名      | 触发条件         |
| ----------- | ---------------- |
| onclick     | 鼠标点击左键触发 |
| ondblclick  | 鼠标双击触发     |
| onmouseover | 鼠标经过触发     |
| onmouseout  | 获取离开触发     |
| onfocus     | 获取鼠标焦点触发 |
| onblur      | 失去鼠标焦点触发 |
| onmousemove | 鼠标移动触发     |
| onmouseup   | 鼠标弹起触发     |
| onmousedown | 鼠标按下触发     |
| scroll      | 滚动条滚动事件   |

* 鼠标事件对象

| 鼠标事件对象 | 描述                                         |
| ------------ | -------------------------------------------- |
| e.clientX    | 返回鼠标相对于浏览器窗口可视区 X 坐标        |
| e.clientY    | 返回鼠标相对于浏览器窗口可视区的 Y 坐标      |
| e.pageX      | 返回鼠标相对于文档页面的 X 坐标 ,(IE9+支持)  |
| e.pageY      | 返回鼠标相对于文档页面的 Y 坐标 （IE9+支持） |
| e.screenX    | 返回鼠标相对于电脑屏幕的 X 坐标              |
| e.screenY    | 返回鼠标相对于电脑屏幕的 Y 坐标              |

**代码演示:**

```js
<script>
    // 鼠标事件对象 MouseEvent
    document.addEventListener('click', function(e) {
    // 1. client 鼠标在可视区的x和y坐标
    console.log(e.clientX);
    console.log(e.clientY);
    console.log('---------------------');

    // 2. page 鼠标在页面文档的x和y坐标
    console.log(e.pageX);
    console.log(e.pageY);
    console.log('---------------------');

    // 3. screen 鼠标在电脑屏幕的x和y坐标
    console.log(e.screenX);
    console.log(e.screenY);

})
</script>
```



* 键盘事件

| 事件名     | 触发条件             |
| ---------- | -------------------- |
| onkeydown  | 键盘按下触发         |
| onkeyup    | 键盘弹起触发         |
| onkeypress | 按下产生一个字符触发 |

> onkeypress 和 前面2个区别在于，它不识别功能键，比如ctrl 、 shift、方向键等
>
> **注意:** 三个事件的执行顺序  keydown -- keypress -- keyup

```js
    console.log('我弹起了');
})

//3. keypress 按键按下的时候触发  不能识别功能键 比如 ctrl shift 左右箭头啊
document.addEventListener('keypress', function() {
    console.log('我按下了press');
})
//2. keydown 按键按下的时候触发  能识别功能键 比如 ctrl shift 左右箭头啊
document.addEventListener('keydown', function() {
    console.log('我按下了down');
})
```

* 键盘事件对象

| 键盘事件对象 | 描述                  |
| ------------ | --------------------- |
| keyCode      | 返回按下键的 ASCII 值 |

> onkeydown 和 onkeyup 不区分字母大小写，onkeypress区分字母大小写

```js
// 键盘事件对象中的keyCode属性可以得到相应键的ASCII码值
// 1. 我们的keyup 和keydown事件不区分字母大小写  a 和 A 得到的都是65
// 2. 我们的keypress 事件 区分字母大小写  a  97 和 A 得到的是65
document.addEventListener('keyup', function(e) {
    // console.log(e);
    console.log('up:' + e.keyCode);
    // 我们可以利用keycode返回的ASCII码值来判断用户按下了那个键
    if (e.keyCode === 65) {
        alert('您按下的a键');
    } else {
        alert('您没有按下a键')
    }

})
document.addEventListener('keypress', function(e) {
    // console.log(e);
    console.log('press:' + e.keyCode);

})
```



## 四、操作元素

> JavaScript的 DOM 操作可以改变网页内容、结构和样式，我们可以利用 DOM 操作元素来改变元素里面的内容、属性等。**（注意：这些操作都是通过元素对象的属性实现的）**

### 4.1改变元素内容(获取和设置)

|                   | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| element.innerText | 获取元素起始到终止位置的内容，但它去除元素包裹的标签，同时空格和换行也会去掉 ，在设置元素内容方面上，它不识别标签，只是将设置的东西都当成字符串。 |
| element.innerHTML | 获取元素起始到终止位置的全部内容，包括元素包裹的标签，同时保留空格和换行，在设置元素内容方面上，它能识别标签并解析。 |

两者区别：

* 获取内容时的区别

  innerText会去除空格和换行，而innerHTML会保留空格和换行	

* 设置内容时的区别

  innerText不会识别html，而innerHTML会识别

### 4.2常见的元素属性操作

* innerText、innerHTML -> 改变元素内容
* sec 、 href 
* id 、alt 、title
* 表单元素：type 、value 、checked 、selected 、 disabled

### 4.3样式属性操作

| 方式                       | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| element.style.样式属性= 值 | 行内样式操作，将修改后的值都赋值到元素的上，也就是元素的`style`属性上，**注意：样式属性采用驼峰命名法(font-size -> fontSize)** |
| element.classNmae = 类名   | 类名样式操作，它会覆盖类名                                   |
| element.classList.xxx      | classList属性是HTML5新增的一个属性，返回元素的类名。但是ie10以上版本支持。**下方解析** |

* element.classList 该属性用于在元素中添加 、移除、及切换css类

| 方法                             | 描述                    |
| -------------------------------- | ----------------------- |
| element.classList.add('类名')    | 添加类                  |
| element.classList.remove('类名') | 移除类                  |
| element.classList.toggle('类名') | 切换类,有就删，无就添加 |

**代码演示:**

```html
<style>
    .bg {
        background-color: black;
    }
</style>
<body>
    <div class="one two"></div>
    <button> 开关灯</button>
    <script>
        // classList 返回元素的类名
        var div = document.querySelector('div');
        // console.log(div.classList[1]);
        // 1. 添加类名  是在后面追加类名不会覆盖以前的类名 注意前面不需要加.
        div.classList.add('three');
        // 2. 删除类名
        div.classList.remove('one');
        // 3. 切换类
        var btn = document.querySelector('button');
        btn.addEventListener('click', function() {
            document.body.classList.toggle('bg');
        })
    </script>
</body>
```



### 4.4自定义属性

* 设置属性值

| 方式                                | 描述                                                    |
| ----------------------------------- | ------------------------------------------------------- |
| element.属性 = '值'                 | 设置内置属性值                                          |
| element.setAttribute('属性名','值') | 可以设置内置或自定义属性，主要设置自定义属性(是w3c标准) |

```js
// 2. 设置元素属性值
// (1) element.属性= '值'
div.id = 'test';
div.className = 'navs';
// (2) element.setAttribute('属性', '值');  主要针对于自定义属性
div.setAttribute('index', 2);
div.setAttribute('class', 'footer'); // class 特殊  这里面写的就是
```

* 获取属性值

| 方式                           | 描述                         |
| ------------------------------ | ---------------------------- |
| element.属性名                 | 只能获取内置属性             |
| element.getAttribute("属性名") | 可以获取自定义属性和内置属性 |

* 移除属性

| 方式                              | 描述                     |
| --------------------------------- | ------------------------ |
| element.removeAttribute('属性名') | 可以移除内置或自定义属性 |

* H5自定义属性

> 自定义属性目的：是为了保存并使用数据。有些数据可以保存到页面中而不用保存到数据库中。
>
> 自定义属性获取是通过getAttribute(‘属性’) 获取。
>
> 但是有些自定义属性很容易引起歧义，不容易判断是元素的内置属性还是自定义属性。
>
> H5给我们新增了自定义属性以`data-`开头作为属性名并且赋值

对于`data-`开头的自定义属性获取

| 方式                                                | 描述                  |
| --------------------------------------------------- | --------------------- |
| element.getAttribute('data-开头的属性名')           | 获取data-开头的属性值 |
| element.dataset.属性名 或 element.dataset[’属性名‘] | 获取data-开头的属性值 |

```html
<div getTime="20" data-index="2" data-list-name="andy"></div>
<script>
    var div = document.querySelector('div');
    console.log(div.getAttribute('getTime'));
    div.setAttribute('data-time', 20);
    console.log(div.getAttribute('data-index'));
    console.log(div.getAttribute('data-list-name'));
    // h5新增的获取自定义属性的方法 它只能获取data-开头的
    // dataset 是一个集合里面存放了所有以data开头的自定义属性
    console.log(div.dataset);
    console.log(div.dataset.index);
    console.log(div.dataset['index']);
    // 如果自定义属性里面有多个-链接的单词，我们获取的时候采取 驼峰命名法
    console.log(div.dataset.listName);
    console.log(div.dataset['listName']);
</script>
```

## 五、节点操作

​	网页中的所有内容都是节点（标签、属性、文本、注释等），在DOM 中，节点使用 node 来表示。

​	HTML DOM 树中的所有节点均可通过 JavaScript 进行访问，所有 HTML 元素（节点）均可被修改，也可以创建或删除。

​	一般的节点至少拥有nodeType（节点类型）、nodeName（节点名称）和nodeValue（节点值）这三个基本属性。

* 元素节点 nodeType = 1 
* 属性节点 nodeType = 2
* 文本节点 nodeType = 3 （文本节点包含文字、空格、换行等）

> 我们在实际开发中，节点操作主要操作的是元素节点

### 5.1父级节点

| 方式            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| node.parentNode | parentNode属性可放回某节点的父节点，注意是**最近的一个父节点**,若没有则放回 null |

```html
<div class="demo">
    <div class="box">
        <span class="erweima">×</span>
    </div>
</div>
<script>
    // 1. 父节点 parentNode
    var erweima = document.querySelector('.erweima');
    // 得到的是离元素最近的父级节点(亲爸爸) 如果找不到父节点就返回为 null
    console.log(erweima.parentNode); // <div class="box">...</div>
</script>
```

### 5.2子节点

| 方式                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| parentNode.childrenNodes     (标准)                          | 返回包含指定节点的子节点的集合，该集合为即时更新的集合。**注意：返回的集合包括元素节点、文本节点等** |
| parentNode.children (非标准)                                 | 返回所有的子元素节点，它只返回子元素节点，其余节点不返回**（重点掌握）**，虽然是一个非标准方式，当各个浏览器都支持，放心使用 |
| parentNode.firstChild                                        | 返回第一个子节点，找不到则返回null,**注意它的查找返回范围包含元素、文本节点** |
| parentNode.lastChild                                         | 返回最后一个子节点，找不到则返回null，**注意它的查找返回范围包含元素、文本节点** |
| parentNode.firstElementChild                                 | 返回第一个子元素节点，找不到则返回null                       |
| parentNode.lastElementChild                                  | 返回最后一个字元素节点，找不到则返回 null                    |
| **parentNode.children[0] (开发常用)**                        | 获取第一个子元素节点                                         |
| **parentNode.children[parentNode.children.length-1] (开发常用)** | 获取最后一个子元素节点                                       |

```html
<ul>
    <li>我是li</li>
    <li>我是li</li>
    <li>我是li</li>
    <li>我是li</li>
</ul>
<script>
    // DOM 提供的方法（API）获取
    var ul = document.querySelector('ul');
    var lis = ul.querySelectorAll('li');
    // 1. 子节点  childNodes 所有的子节点 包含 元素节点 文本节点等等
    console.log(ul.childNodes);
    console.log(ul.childNodes[0].nodeType);
    console.log(ul.childNodes[1].nodeType);
    // 2. children 获取所有的子元素节点 也是我们实际开发常用的
    console.log(ul.children);
</script>

<ol>
    <li>我是li1</li>
    <li>我是li2</li>
    <li>我是li3</li>
    <li>我是li4</li>
    <li>我是li5</li>
</ol>
<script>
    var ol = document.querySelector('ol');
    // 1. firstChild 第一个子节点 不管是文本节点还是元素节点
    console.log(ol.firstChild);
    console.log(ol.lastChild);
    // 2. firstElementChild 返回第一个子元素节点 ie9才支持
    console.log(ol.firstElementChild);
    console.log(ol.lastElementChild);
    // 3. 实际开发的写法  既没有兼容性问题又返回第一个子元素
    console.log(ol.children[0]);
    console.log(ol.children[ol.children.length - 1]);
</script>

```

### 5.3兄弟节点

| 方式                        | 描述                                                         |
| --------------------------- | ------------------------------------------------------------ |
| node.nextSibling            | 返回当前元素的下一个兄弟元素节点，找不到则返回null，**它的查找返回范围包括元素节点、文本节点** |
| node.previousSibling        | 返回当前元素上一个兄弟元素节点，找不到则返回null,**它的查找返回范围包括元素节点、文本节点** |
| node.nextElementSibling     | 返回当前元素下一个**兄弟元素节点**，找不到则返回null，有兼容问题(IE9以上) |
| node.previousElementSibling | 返回当前元素上一个**兄弟元素节点**，找不到则返回null，有兼容问题(IE9以上) |

**解决兼容问题**

```js 
// 自己封装一个函数 获取后面的兄弟元素
function getNextElementSibling(element) {
    var el = element;
    while (el = el.nextSibling) {
        if (el.nodeType === 1) {
            return el;
        }
    }
    return null;
}
```

### 5.4创建 & 添加 & 删除 节点

* 创建节点

| 方式                              | 描述                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| document.createElement('tagName') | 有`tagName`指定的创建HTML元素，由于创建出来的元素是原先不存在的，所以这种创建元素的方式叫做**动态创建元素节点** |

* 添加节点

| 方式                              | 描述                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| node.appendChild(child)           | 将一个节点添加到指定父节点的子节点列表**末尾**，类似于css里面的after伪元素 |
| node.insertBefore(child,指定元素) | 将一个节点天机到父节点的指定字节点**前面**,类似于css里面的before 伪元素 |

```html
<ul>
    <li>123</li>
</ul>
<script>
    // 1. 创建节点元素节点
    var li = document.createElement('li');
    // 2. 添加节点 node.appendChild(child)  node 父级  child 是子级 后面追加元素
    var ul = document.querySelector('ul');
    ul.appendChild(li);
    // 3. 添加节点 node.insertBefore(child, 指定元素);
    var lili = document.createElement('li');
    ul.insertBefore(lili, ul.children[0]);
    // 4. 我们想要页面添加一个新的元素 ： 1. 创建元素 2. 添加元素
</script>
```

* 删除节点

| 方式                    | 描述                                  |
| ----------------------- | ------------------------------------- |
| node.removeChild(child) | 从DOM中删除一个子节点，返回删除的节点 |

