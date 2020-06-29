---
title: JS内置对象
date: 2020-06-21 21:07:11
categories: JS
top: false
summary: JS内置对象
tags: 
 - JS
---

## 一、JS内置对象

> JavaScript 中的对象分为3种：**自定义对象 、内置对象、 浏览器对象**
> 前面两种对象是JS 基础 内容，属于 ECMAScript；  第三个浏览器对象属于 JS 独有的， JS API 讲解内置对象就是指 JS 语言自带的一些对象，这些对象供开发者使用，并提供了一些常用的或是**最基本而必要的功能**（属性和方法），内置对象最大的优点就是帮助我们快速开发
>
> ​	 	JavaScript 提供了多个内置对象：Math、 Date 、Array、String等	
>
> 最好的学习是去查阅MDN文档，那里解释很全，本章小编只介绍我们开发中最常用的内置对象一些属性和方法，顺便巩固一下😊
>
> MDN:https://developer.mozilla.org/zh-CN/

## 二、Math 数字对象

> Math 对象不是构造函数，它具有数学常数和函数的属性和方法，跟数学相关的运算`(求绝对值、取整、最大值等)` 可以使用 Math 中的成员

| 属性\方法名            | 描述                                             |
| ---------------------- | ------------------------------------------------ |
| Math.PI                | 圆周率                                           |
| Math.floor()           | 向下取整                                         |
| Math.ceil()            | 向上取整                                         |
| Math.round()           | 四舍五入 就近取整 注意 .5 往大的取 例如-1.5 取-1 |
| Math.abs()             | 绝对值                                           |
| Math.max()或Math.min() | 求最大或最小值                                   |
| Math.random()          | 获取范围在[0,1)内的随机值，注意：不包括0         |

**代码演示**

```js
console.log(Math.PI); // 一个属性 圆周率
console.log(Math.max(1, 99, 3)); // 返回一组的最大值 99
console.log(Math.max(-1, -10)); // 返回一组的最小值 -1
console.log(Math.max(1, 99, 'MATH')); // 如果一组数据出现不是数字的，返回NAN
console.log(Math.max()); // 啥也不写 返回-Infinity 

// 1.绝对值方法
console.log(Math.abs(1)); // 1
console.log(Math.abs(-1)); // 1
console.log(Math.abs('-1')); // 隐式转换 会把字符串型 -1 转换为数字型
console.log(Math.abs('Math')); // NaN 

// 2.三个取整方法
// (1) Math.floor()   地板 向下取整  往最小了取值
console.log(Math.floor(1.1)); // 1
console.log(Math.floor(1.9)); // 1

// (2) Math.ceil()   ceil 天花板 向上取整  往最大了取值
console.log(Math.ceil(1.1)); // 2
console.log(Math.ceil(1.9)); // 2

// (3) Math.round()   四舍五入  其他数字都是四舍五入，但是 .5 特殊 它往大了取  
console.log(Math.round(1.1)); // 1
console.log(Math.round(1.5)); // 2
console.log(Math.round(1.9)); // 2
console.log(Math.round(-1.1)); // -1
console.log(Math.round(-1.5)); // 这个结果是 -1
```

**Math.random()**

```js
// 1.Math对象随机数方法   random() 返回一个随机的小数  0 =< x < 1
// 2. 这个方法里面不跟参数
// 3. 代码验证 
console.log(Math.random());


// 4. 我们想要得到两个数之间的随机整数 并且 包含这2个整数
// Math.floor(Math.random() * (max - min + 1)) + min;
function getRandom(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
}
console.log(getRandom(1, 10)); 随机去 [1,10]的数
```

## 三、Date 日期对象

> ​	Date 对象和 Math 对象不一样，**Date是一个构造函数**，所以使用时需要实例化后才能使用其中具体方法和属性。Date 实例用来处理日期和时间

* 使用 Date 实例化日期对象

  * 获取当前时间必须实例化

  ```js
  var now = new Date(); // Sun Jun 21 2020 21:42:27 GMT+0800 (中国标准时间)
  ```

  

  * 获取指定时间的日期对象

  ```js
  var date1 = new Date(2020, 10, 1); // 这个形式有些许问题，对时间+1,不推荐
  console.log(date1); // Sun Nov 01 2020 00:00:00 GMT+0800 (中国标准时间) 返回的是 11月 不是 10月 
  var date2 = new Date('2020-10-1 8:8:8');
  console.log(date2); // Thu Oct 01 2020 08:08:08 GMT+0800 (中国标准时间)
  var date3 = new Date('2020 / 10 / 1')
  console.log(date3) // Thu Oct 01 2020 00:00:00 GMT+0800 (中国标准时间)
  ```

* 其他是方法

| 方法名        | 描述                          | 使用               |
| ------------- | ----------------------------- | ------------------ |
| getFullYear() | 获取当年                      | dObj.getFullYear() |
| getMonth()    | 获取当月 (0-11)               | dObj.getMonth()    |
| getDate()     | 获取当天日期                  | dObj.getDate()     |
| getDay()      | 获取星期几(**周日0 到周六6**) | dObj.getDay()      |
| getHours()    | 获取当前小时                  | dObj.getHours()    |
| getMinutes()  | 获取当前分钟                  | dObj.getMinutes()  |
| getSeconds()  | 获取当前秒钟                  | dObj.getSeconds()  |

**代码演示**

```js
// 格式化日期 年月日 
var date = new Date();
console.log(date.getFullYear()); // 返回当前日期的年  2020
console.log(date.getMonth() + 1); // 由于是(0-11)，真实月份的+1操作
console.log(date.getDate()); // 返回的是 几号
console.log(date.getDay()); // 3  周一返回的是 1 周六返回的是 6 但是 周日返回的是 0
// 我们写一个 格式为 2020年 10月 1日 星期三 
var year = date.getFullYear();
var month = date.getMonth() + 1;
var dates = date.getDate();
var arr = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'];
var day = date.getDay();
console.log('今天是：' + year + '年' + month + '月' + dates + '日 ' + arr[day]);

// 可以传入特定的时间来获取该年月
var date = new Date('2020-12-01');
const year = date.getFullYear();
console.log(year) // 2020 ，其他月日也是一样获取的
```

* 通过Date实例总毫秒数

  * 总毫秒数的含义

    > 基于1970年1月1日（世界标准时间）起的毫秒数

  * 获取总毫秒数

  ```js
  // 获得Date总的毫秒数(时间戳)  不是当前时间的毫秒数 而是距离1970年1月1号过了多少毫秒数
  // 1. 通过 valueOf()  getTime()
  var date = new Date();
  console.log(date.valueOf()); // 就是 我们现在时间 距离1970.1.1 总的毫秒数  1592748835027
  console.log(date.getTime());
  // 2. 简单的写法 (最常用的写法)
  var date1 = +new Date(); // +new Date()  返回的就是总的毫秒数
  console.log(date1);
  // 3. H5 新增的 获得总的毫秒数
  console.log(Date.now());
  ```

  * 实例：


  ```js
  // 倒计时效果
  // 1.核心算法：输入的时间减去现在的时间就是剩余的时间，即倒计时 ，但是不能拿着时分秒相减，比如 05 分减去25分，结果会是负数的。
  // 2.用时间戳来做。用户输入时间总的毫秒数减去现在时间的总的毫秒数，得到的就是剩余时间的毫秒数。
  // 3.把剩余时间总的毫秒数转换为天、时、分、秒 （时间戳转换为时分秒）
  // 转换公式如下： 
  //  d = parseInt(总秒数/ 60/60 /24);    //  计算天数
  //  h = parseInt(总秒数/ 60/60 %24)   //   计算小时
  //  m = parseInt(总秒数 /60 %60 );     //   计算分数
  //  s = parseInt(总秒数%60);            //   计算当前秒数
  function countDown(time) {
      var nowTime = +new Date(); // 返回的是当前时间总的毫秒数
      var inputTime = +new Date(time); // 返回的是用户输入时间总的毫秒数
      var times = (inputTime - nowTime) / 1000; // times是剩余时间总的秒数 
      var d = parseInt(times / 60 / 60 / 24); // 天
      d = d < 10 ? '0' + d : d;
      var h = parseInt(times / 60 / 60 % 24); //时
      h = h < 10 ? '0' + h : h;
      var m = parseInt(times / 60 % 60); // 分
      m = m < 10 ? '0' + m : m;
      var s = parseInt(times % 60); // 当前的秒
      s = s < 10 ? '0' + s : s;
      return d + '天' + h + '时' + m + '分' + s + '秒';
  }
  console.log(countDown('2020-6-22'));// 00天01时34分17秒
  ```

## 四、Array 数组对象

> [点击查看MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array)

### 创建数组的两种方式

* 字面量创建

  ```js
  var arr = [1,2,3];
  ```

* 实例化创建

  ```js
  var arr = new Array();
  ```

  > 注意:实例化创建出来的数组是个空数组，如果需要使用构造函数Array创建非空数组，可以在创建数组时传入参数

  * 如果只传入一个参数，则参数规律数组的长度
  * 如果传入了多个参数，则参数成为数组的元素

### 使用方法创建数组

| 方法名                   | 描述                                                         | 返回值 |
| ------------------------ | ------------------------------------------------------------ | ------ |
| Array.from(想转化的对象) | 从一个类似数组或可迭代对象创建一个新的，浅拷贝的数组实例     | 新数组 |
| Array.of()               | 创建一个具有可变数量参数的新数组实例，而不考虑参数的数量或类型。 | 新数组 |

**代码演示**

```js
// Array.from()
let datas = {
    0: "a",
    1: "b",
    2: "c",
    length: 3
};  
datas = Array.from(datas,function(item,index){
    return item.toUpperCase();
},document);
console.log(datas); // ["A", "B", "C"]

// Array.of()
 let arr = Array.of(1, 2, 3, 4, 5, 5, 6, '123');
console.log(arr); // [1, 2, 3, 4, 5, 5, 6, "123"]
```

### 检测是否为数组

| 方法名          | 描述           |
| --------------- | -------------- |
| instanceof      | 使用关键字判断 |
| Array.isArray() | 使用数组方法   |

**代码演示**

```js
// (1) instanceof  运算符 它可以用来检测是否为数组     
var arr = [];
var obj = {};

console.log(arr instanceof Array); // true
console.log(obj instanceof Array); // false

// (2) Array.isArray(参数);  H5新增的方法  ie9以上版本支持
console.log(Array.isArray(arr)); // true
console.log(Array.isArray(obj)); // false
```

### 添加/删除/截取/连接/查找数组元素的方法

* 数组中有进行添加、删除元素的方法，常用如下表

| 方法名                           | 描述                                                         | 返回值                  |
| -------------------------------- | ------------------------------------------------------------ | ----------------------- |
| push(参数1,.....)                | 末尾添加一个或多个元素，**注意修改原数组**                   | 并返回新的长度          |
| pop()                            | 删除数组最后一个元素,把数组长度减1 **无参数，修改原数组**    | 返回他删除的元素的值    |
| unshift(参数1,....)              | 向数组的开头添加一个或更多元素，**注意修改原 数组**          | 并返回新的长度          |
| shift()                          | 删除数组的第一个元素，数组长度减一，**无参数，修改原数组**   | 并返回第一个元素的值    |
| concat()                         | 连接两个或多个数组 **不影响原数组**                          | 返回一个新数组          |
| slice()                          | 数组截取 slice(begin,end)，**不影响原数组**                  | 返回被截取项目的新数组  |
| splice()                         | 数组删除 splice(第几个开始，要删除的个数)**注意：这个会影响原数组** | 返回被删除项目的新数组  |
| include(要查找的元素,[从哪开始]) | 用来判断一个数组是否包含一个指定的值，默认从索引0的位置开始查找**不影响原数组** | 存在元素:true,否则false |

> 注意:
>
> push 、unshift -----> 添加元素
>
>  pop 、 shift ---------> 删除元素

```js
let arr = [1, 2, 3]
let arr2 = [4, 5, 6]
let arr3 = [7, 8, 9]
console.log(arr.concat(arr2, arr3)) // [1, 2, 3, 4, 5, 6, 7, 8, 9]
console.log(arr.slice(1, 2)); // [2]
console.log(arr.splice(1, 2)) // [2, 3]
console.log(arr) // [1]

// include
let arr = ["a","b","c","d"];
console.log(arr.includes("a")); // true
console.log(arr.includes("a",-3)); // 从索引4-3 = 1 的位置开始查找 返回flase
```

### 数组排序

* 数组中有队数组本身排序的方法，常用如下表:

| 方法名    | 说明                             | 是否修改原数组                     |
| --------- | -------------------------------- | ---------------------------------- |
| reverse() | 颠倒数组中元素的顺序，**无参数** | 该方法会改变原来的数组，返回新数组 |
| sort()    | 对数组的元素进行排序             | 该方法会改变原来的数组，返回新数组 |

> 注意： sort方法需要传入参数来设置升序、降序排序
>
>  *  如果传入"function(a,b){return a-b;}" 则为升序
>  *  如果传入“function(a,b){return b-a;}” 则为降序

```js
// 数组排序
// 1. 翻转数组
var arr = ['pink', 'red', 'blue'];
arr.reverse();
console.log(arr); // ["blue", "red", "pink"]

// 2. 数组排序（冒泡排序）
var arr1 = [13, 4, 77, 1, 7];
arr1.sort(function(a, b) {
    //  return a - b; 升序的顺序排列
    return b - a; // 降序的顺序排列 
});
console.log(arr1); // [77, 13, 7, 4, 1]
```

### 数组索引方法

* 数组中有获取数组指定元素索引值的方法，常用如下表

| 方法名        | 说明                           | 返回值                                     |
| ------------- | ------------------------------ | ------------------------------------------ |
| indexOf()     | 数组中查找给定元素的第一个索引 | 如果存在返回索引号，如果不存在，则返回`-1` |
| lastIndexOf() | 在数组中的最后一个的索引       | 如果存在返回索引号，如果不存在，则返回`-1` |

> 注意：索引从`0`开始

```js
var arr = ['red', 'green', 'pink'];
console.log(arr.indexOf('blue')); // 0

var arr = ['red', 'green', 'blue', 'pink', 'blue'];
console.log(arr.lastIndexOf('blue')); // 4
```

### 数组转换为字符串

* 数组中吧数组转化为字符串的方法，常用如下表

| 方法名         | 说明                                       | 返回值         |
| -------------- | ------------------------------------------ | -------------- |
| toString()     | 把数组转换成字符串，逗号分隔每一项         | 返回一个字符串 |
| join('分隔符') | 方法用于把数组中的所有元素转换为一个字符串 | 返回一个字符串 |

> 注意： join 方法通过不传入参数，则按照`,`拼接元素

**代码演示**

```js
// 1. toString() 将我们的数组转换为字符串
var arr = [1, 2, 3];
console.log(arr.toString()); // 1,2,3

// 2. join(分隔符) 
var arr1 = ['green', 'blue', 'pink'];
console.log(arr1.join()); // green,blue,pink
console.log(arr1.join('-')); // green-blue-pink
console.log(arr1.join('&')); // green&blue&pink
```

### 数组的遍历

> 下面的方法都是**不改变原数组**

| 方法名                                       | 描述                                                         | 返回值                    |
| -------------------------------------------- | ------------------------------------------------------------ | ------------------------- |
| arr.forEach(callback(item,index))            | 将arr数组中的每一项都执行一次`callback`函数,若要中途跳出循环则需要抛出结束循环的语句， | undefined                 |
| arr.every(callback(item,index))              | 测试一个数组内的`所有元素`是否都能通过某个指定函数的测试，   | 布尔值                    |
| arr.some(callback(item,index))               | 测试数组中是不是`至少有1个元素`通过了被提供的函数测试,如果找到第一个符合条件的元素便终止遍历 | 布尔值                    |
| arr.find(callback(item,index))**(ES6)**      | 遍历寻找数组中第一个符合`callback`函数条件的元素，找到则终止遍历 | 符合条件元素或`undefined` |
| arr.findIndex(callback(item,index))**(ES6)** | 遍历寻找数组中第一个符合`callback`函数条件的元素并获取到其的`索引`，并终止遍历 | 找到返回索引值,否则`-1`   |
| arr.map(callback(item,index))**(ES6)**       | 遍历数组，将每一个元素执行一次`callback`之后返回一个新数组   | 新数组                    |
| arr.filter(callback(item,index))**(ES6)**    | 遍历数组，将每一个元素执行`callback`之后，若元素满足函数返回条件，则保留 | 新数组                    |

**代码演示**

```js
// forEach
const arraySparse = [1,3,,7];
arraySparse.forEach(function(item){
    console.log(element); // 1 3 7
});

// every
[12, 5, 8, 130, 44].every(x => x >= 10); // false
[12, 54, 18, 130, 44].every(x => x >= 10); // true

// some 
[2, 5, 8, 1, 4].some(x => x > 10);  // false 实际执行5次callback
[12, 5, 8, 1, 4].some(x => x > 10); // true  实际执行1次callback

// find 
let arr = [10, 1, 3]
const flag = arr.find((item, index) => {
    return item > 9
})
console.log(flag) // 10

// findIndex
let arr = [10, 1, 3]
const flag = arr.findIndex((item, index) => {
    return item == 10
})
console.log(flag) // 0 

// map 
const array1 = [1, 4, 9, 16];
const map1 = array1.map(x => x * 2);
console.log(map1); // [2, 8, 18, 32]

// filter
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];
const result = words.filter(word => word.length > 6);
console.log(result); // ["exuberant", "destruction", "present"]
```

### 数据数组扁平化

| 方法名                             | 描述                                                         | 返回值 |
| ---------------------------------- | ------------------------------------------------------------ | ------ |
| arr.flat([depth])**(ES6)**         | 方法会按照一个可指定的**深度**递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。 深度默认为1，无限次可指定为`Infinity` | 新数组 |
| arr.flatMap((item,index))**(ES6)** | 方法首先使用映射函数映射每个元素，然后将结果压缩成一个新数组。它与 `map` 连着深度值为1的 `flat` 几乎相同，但 `flatMap` 通常在合并成一种方法的效率稍微高一些。 | 新数组 |

> 简单来讲：flat 是将多维度的数组数据结构进行降维操作，传入`Infinity`之后，返回的新数组中会是一维原数组；flatMap 是将数组的所有数据进去`callback`操作之后，强行把结果压缩为一个数组存储。

**代码演示**

```js
// flat  注意：arr的数据结构一定要按下面的形式定义
let arr = [
    [1, 2],
    [3, 4],
    [
        [6, 7],
        [
            [8],
            [9, 10]
        ]
    ]
];
console.log(arr.flat(Infinity)); // [1, 2, 3, 4, 6, 7, 8, 9, 10]

// flatMap
let arr1 = ["it's Sunny in", "", "California"];
arr1.flatMap(x => x.split(" "));
// ["it's","Sunny","in", "", "California"]

let arr = [
    [1, 2],
    [3, 4],
    [6, 8]
];
let arr2 = arr.flatMap(item => {
    item = item.filter(item => item >= 2);
    return item;
});
console.log(arr2);
```



## 五、String 字符串对象

> 注意：字符串具有不可变性，也就是说字符串在重新赋值的时候，只是在内存空间中在开辟了一个存储空间，改变变量的指针指向新值，而旧值依然存在于内存中，并未销毁。所以不要过多次的进行字符串拼接，由于字符串的不可变性，下面对字符串的操作都是**不会影响原字符串**
>
> [点击查看MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String)

### 根据字符返回位置

| 方法名                                | 描述                                                         | 返回值     |
| ------------------------------------- | ------------------------------------------------------------ | ---------- |
| indexOf('要查找的字符'，开始的位置)   | 返回指定内容在元字符串中的位置，如果找不到就返回`-1`，开始的位置是`index`索引号 | 索引或`-1` |
| lastIndexOf()                         | 从后往前找，只找到第一个匹配的                               | 索引或`-1` |
| includes("要查找的字符串",[从哪开始]) | 判断该字符串是否存在我们需要查到的字符串                     | 布尔值     |

**代码演示**

```js
// 字符串对象  根据字符返回位置  str.indexOf('要查找的字符', [起始的位置])
var str = '改革春风吹满地，春天来了';
console.log(str.indexOf('春')); // 第一次出现的位置索引值 2
console.log(str.indexOf('春', 3)); // 从索引号是 3的位置开始往后查找,返回的值字符在整个字符串的位置索引值  这里是 8
```

### 根据位置返回字符

| 方法名            | 描述                                             | 使用                         |
| ----------------- | ------------------------------------------------ | ---------------------------- |
| charAt(index)     | 返回指定位置的字符(index 字符串的索引号),从0开始 | str.charAt(0)                |
| charCodeAt(index) | 获取指定位置处字符的`ASCLL`码(index索引号)       | str.charCodeAt(0)            |
| str[index]        | 获取指定位置处的字符                             | HTML5,IE8+支持和charAt()等效 |

**代码演示**

```js
// 根据位置返回字符
// 1. charAt(index) 根据位置返回字符
var str = 'andy';
console.log(str.charAt(3)); // y
// 遍历所有的字符
for (var i = 0; i < str.length; i++) {
    console.log(str.charAt(i));
}
// 2. charCodeAt(index)  返回相应索引号的字符ASCII值 目的： 判断用户按下了那个键 
console.log(str.charCodeAt(2)); // d : 100
// 3. str[index] H5 新增的
console.log(str[0]); // a
```

### 字符串操作方法

> 注意：下面的方法都是不影响原字符串，这也体现了字符串的不可变性

| 方法名                                       | 描述                                                         | 返回值                 |
| -------------------------------------------- | ------------------------------------------------------------ | ---------------------- |
| concat(str1,str2,str3....)                   | concat() 方法用于连接两个或多个字符串，凭借字符串，等效于+,而 + 更常用 | 新字符                 |
| substr(start,length)                         | 从 start 位置开始(索引号)，length 去的个数 **不影响原字符，重点掌握** | 截取的字符             |
| slice(start,end)                             | 从 start 位置开始，截取到 end 位置，end去不到（两个都是索引号）**不影响原字符** | 截取的字符             |
| substring(start,end)                         | 从 start 位置开始,截取到 end 位置，end取不到， 基本 slice相同，但是不接受负值 | 截取的字符             |
| replace(regexp\|substr, newSubStr\|function) | 字符串中用一些字符替换另一些字符                             | 返回被替换的整个字符串 |
| split("分割字符")                            | 切分字符串，它可以将字符串切分为数组。在切分完毕之后，返回的是一个新数组。 | 新数组                 |
| repeat(重复的次数)                           | 将字符串重复复制并连接在一起                                 | 新字符                 |

**代码演示**

```js
// 字符串操作方法
// 1. concat('字符串1','字符串2'....)
var str = 'andy';
console.log(str.concat('red')); // andyred
console.log(str) // andy

// 2. substr('截取的起始位置', '截取几个字符'); 
// 3. slice('截取的起始位置', '截取的截止位置'); 索引号

var str1 = '改革春风吹满地';
console.log(str1.slice(2, 3)) // 春
console.log(str1.substr(2, 2)); // 第一个2 是索引号的2 从第几个开始  第二个2 是取几个字符  结果：春风
console.log(str1) // 改革春风吹满地

// repeat
let str = "123";
console.log(str.repeat(10)); // 123123123123123123123123123123
console.log(str) // 123
```

**replace 语法格式**

```js
字符串.replace(被替换的字符串， 要替换为的字符串)；
```

**代码演示**

```js
// 替换字符 replace('被替换的字符', '替换为的字符')  它只会替换第一个字符
var str = 'andyandy';
console.log(str.replace('a', 'b')); // 结果： bndyandy
// 有一个字符串 'abcoefoxyozzopp'  要求把里面所有的 o 替换为 *
var str1 = 'abcoefoxyozzopp';
while (str1.indexOf('o') !== -1) {
    str1 = str1.replace('o', '*');
}
console.log(str1); // abc*ef*xy*zz*pp
```

**split()方法**

```js
字符串.split("分割字符")
```

**代码演示**

```js
// 字符转换为数组 split('分隔符')    前面我们学过 join 把数组转换为字符串
var str2 = 'red, pink, blue';
console.log(str2.split(',')); // ) ["red", " pink", " blue"]
var str3 = 'red&pink&blue';
console.log(str3.split('&')); // ["red", "pink", "blue"]
```

### 检查字符串是否以某个字符开头/结尾

| 方法名                                          | 描述                                                         | 返回值 |
| ----------------------------------------------- | ------------------------------------------------------------ | ------ |
| str.startsWith(要判断的字符串,[从哪个位置])     | 判断该字符串是否以传入的字符为开头的,默认为0，即字符串开头位置 | 布尔值 |
| str.endsWith(要判断的字符串,[要查找的字符长度]) | 判断该字符串是否以传入的字符为结尾的，默认为字符串长度       | 布尔值 |

**代码演示**

```js
// startWidth
const str1 = 'Saturday night plans';
console.log(str1.startsWith('Sat')); // true
console.log(str1.startsWith('Sat', 3)); // false

// endWidth
const str1 = 'Cats are the best!';
console.log(str1.endsWith('best', 17)); // true
console.log(str1.endsWith('best', 2)); // false
```

## 六、Set对象 

> set对象允许你存储任何类型的**唯一值**，无论是原始值或者是对象引用。 可以将它想做是一种数据结构，一般我们是将它作为数组去重作用，出现于**（ES6）**
>
> [详细点击查看MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)

**自身携带的属性**

| 属性名 | 描述                                            |
| ------ | ----------------------------------------------- |
| size   | set对象存储数据结构的长度，和数组的`length`类似 |

**自身携带的方法**

| 方法名               | 描述                             | 返回值      |
| -------------------- | -------------------------------- | ----------- |
| setObj.add(value)    | 在set对象后尾添加多一个数值      | 新的set对象 |
| setObj.has(value)    | 判断set对象中是否存在值为`value` | 布尔值      |
| setObj.delete(value) | 删除set对象中的值为`value`       | 布尔值      |
| setObj.clean()       | 移除set对象中所有的元素          | 空的set对象 |

**代码演示**

```js
// 数组去重
let arr = [1, 2, 3, 4, 1, 3, 5, 2, "c"];
let data = new Set(arr); // {1, 2, 3, 4, 5, "c"}

// add()
data.add(6) // {1, 2, 3, 4, 5, "c",6}

// has 
data.has(1) // true

// clean
data.clean() // {}
```

## 七、Map对象

> Map 对象保存键值对，并且能够记住键的原始插入顺序。任何值(对象或者原始值都可以作为一个键或一个值。 简单来说就是：它能够把特定的数据结构进行结构转化为`key:value`的形式
>
> [详细点击查看MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map)

**自身携带的方法**

| 方法名              | 描述                           | 返回值              |
| ------------------- | ------------------------------ | ------------------- |
| mObj.set(key,value) | 给map对象添加值                | map对象             |
| mObj.has(key)       | 判断map对象是否存在key对应的值 | 布尔值              |
| mObj.get(key)       | 获取map对象中key对应的值       | 返回键对应的值      |
| mObj.delete(key)    | 删除map对象中key对应的值       | 布尔值              |
| mObj.keys()         | 获取map对象中所有key值         | 新的 `Iterator`对象 |
| mObj.values()       | 获取map对象中所有value值       | 新的 `Iterator`对象 |

**代码演示**

```js
let data = new Map([
    ["zmouse", 30],
    ["reci", 20]
]);
console.log(data); // Map(2) {"zmouse" => 30, "reci" => 20} 注意：一定要这种数据结构，数组中第一个为key值，第二个为value值

// set
data.set("刘伟", 41); // Map(3) {"zmouse" => 30, "reci" => 20, "刘伟" => 41}

// delete
data.delete("刘伟"); // Map(2) {"zmouse" => 30, "reci" => 20}

// has
console.log(data.has("reci")) // true

// get
console.log(data.get("zmouse")); // 30
```



## 八、数据类型

> Js中分为简单数据类型和复杂数据类型两类

### 8.1数据类型描述

| 类型         | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| 简单数据类型 | 简单数据类型/基本数据类型，在存储时变量中存储的是值本身，因此叫做值类型，string 、number、boolean、undefined、null |
| 复杂数据类型 | 复杂数据类型，在存储时变量存储的仅仅是地址(引用)，因此叫做引用数据类型，通过new关键字创建的对象(系统对象、自定义对象)，如 Object、Array、Date等 |

### 8.2堆栈

> 内存在我们看来是划分了两个结构栈和堆

|      | 描述                                                         |
| ---- | ------------------------------------------------------------ |
| 栈   | 由操作系统自动分配释放存放函数的参数值、局部变量的值等。其操作方式类似于数据结构中的栈；**简单数据类型存放到栈里面** |
| 堆   | 存储复杂类型(对象)，一般由程序员分配释放，若程序员不释放，由垃圾回收机制回收。**复杂类型存将地址存储在栈中，数据存储在堆中** |

> 注意：JavaScript中没有堆栈的概念，只是通过堆栈的方式，可以让大家更容易理解代码的一些执行方式，便于将来学习其他语言 

![内存中的堆栈](/medias/imges/js/neiObj/img.png)

### 8.3简单数据类型/复杂数据类型内存分配

> 简单数据类型类型变量的数据直接存放在变量（栈空间）中

![简单数据类型内存分配](/medias/imges/js/neiObj/img1.png)

> 复杂数据类型（栈空间）里存放的是地址，真正的对象实例存放在堆空间中

![复杂数据类型内存分配](/medias/imges/js/neiObj/img2.png)

### 8.4简单数据类型/复杂数据类型传参

* 简单数据类型

> 函数的形参也可以看做是一个变量，当我们把一个简单数据类型变量作为参数传给函数的形参时，其实在内存**栈**中对形参也开辟了一个存储空间，然后将简单数据类型在栈中的数值复杂一份往形参在栈中开辟的空间赋值

```js
function fn(a) {
    a++;
    console.log(a); // 11
}
var x = 10;
fn(x); 
console.log(x); // 10
```

![简单数据类型传参](/medias/imges/js/neiObj/img3.png)

* 复杂数据类型

> 函数的形参也可以看做是一个变量，当我们把复杂类型变量传给形参时，其实是把复杂数据变量在栈空间里保存的堆地址复制给了形参，形参和实参其实保存的是同一个堆地址，所以操作的是同一个对象。

```js
function Person(name) {
    this.name = name;
}
function f1(x) { // x = p
    console.log(x.name); // 2. 这个输出什么 -> 刘德华    
    x.name = "张学友";
    console.log(x.name); // 3. 这个输出什么 -> 张学友    
}
var p = new Person("刘德华");
console.log(p.name);    // 1. 这个输出什么 ->  刘德华   
f1(p);
console.log(p.name);    // 4. 这个输出什么 -> 张学友  
```

![复杂数据类型传参](/medias/imges/js/neiObj/img4.png)