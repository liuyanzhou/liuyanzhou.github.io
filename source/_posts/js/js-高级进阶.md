---
title: js-高级进阶
date: 2021-05-28 16:25:23
categories: JS
top: false
summary: js-高级进阶
tags: 
 - JS
---

### JS-高级进阶

### JS的数据类型与类型检测

#### JS的数据类型

> `js`的数据类型可分为**原始值类型（基本类型/值类型）**和**对象数据类型**

**原始值类型（基本值类型/值类型）**

* `number`：数字：NAN、Infinity、正常数字....
* `string`：字符串：普通字符串、模板字符串
* `boolean`：布尔：true、false
* `null`：空
* `undefined`：未定义
* `symbol`：唯一值
* `bigInt`：大数：`10n`n代表大数

对于大数有一些补充：

> 在我们`js`中有最大/最小的数字界限：`Number.MAX_SAFE_INTEGER:9007199254740991`，`Number.MIN_SAFE_INTEGER:-9007199254740991`，分别为：最大安全数字，最小安全数字，如果我们运算或者服务器端返回一个大于最大安全数字或者小于最小安全数字的数进行运算的话，结果会是不准确也是不稳定的。解决这类大数运算一般有两种方法：
>
> * 转化为字符串，使用大数相加、相乘....算法
> * 转化为`bigInt`类型处理

**对象数据类型**

> 除了以上的基本数据类型，其他都能统一称为对象数据类型，但在对象数据类型中，一个比较特殊的是函数（被称为：**可执行对象**），并且因其能够调用`call`方法，我们在使用`typeof`进行类型检测时，将其从对象中抽离出来，值为`function`

* `object`：对象类型，可分为`普通对象: {}(plainObject)`、`数组 [](array)`、`正则 /^$/ (RegExp)`、`new Date()`.....
* `function`：函数类型【可执行对象】，可分为：`普通函数/构造函数/箭头函数/生成器函数`

#### 数据类型转换

* 把其他类型【原始值类型】转换为对象，直接使用`Object([value])`
* 把其他类型转换为布尔

> 规则：只有  0、NaN、null、undefined、空字符串 会变为`false`，其余都是转为`true`，可以通过以下手段进行转换
>
> * Boolean([value])
> * `!![value]`
> * `![value]`:转为布尔值取反
> * 条件判断：例如`if(xx){}`
> * 逻辑符运算：`A||B` `A&&B`要先将`AB`转为布尔
> * .....

```js
console.log(!![]); // true
console.log(!!''); // false
```

* 把其他类型转换为字符串

> 规则：原始值转换成字符串是直接用引号包裹起来就行，但【bigInt会去除`n`标识】，除对象转为字符串是比较特殊的，另外讨论，除去对象进行转换，其他都可以通过以下手段进行转换：
>
> * 调用`toString()`【排除Object.prototype.toString，它是用来检测数据类型的】
> * 字符串 / 模板字符串进行拼接转换 【+号，在js中出现在+号之后除了数学运算，还可能是字符串拼接】

case1：+ 号只有一边是将其转换为数字，或进行数字运算

```js
let n ='10';
console.log(+n); // 10 -> 转换为数字
console.log(++n); // 11 -> 转换为数字然后累加1
console.log(n++); // 11 -> 转换为数字然后累加1

// 面试题  i+=1 / i=i+1 / i++ 三者是否一样？
// i+i+1 && i+=1结果是一样的，但i++的结果一定是返回数字，而前两者可能是字符串运算
```

case2：+ 号一边出现对象，下面的例子可以理解成一个特例

```js
let n=10;
// {}+n --> 10 把左侧的{}当做代码块，不参与运算，运算的只有+n
// n+{} --> '10[Object object]' 字符串拼接，这里的{}调用了toString,规则如case3
```

case3: 对象进行字符串拼接，规则如下

> * 先去调用对象的 `Symbol.toPrimitive` 方法获取属性值，如果没有则进行下一步
>* 在去调用对象的 `valueOf()`获取原始值，如果不是原始值类型，进行下一步【valueOf是实例原型上的方法】
> * 在去调用对象的`toString()`转换为字符串，如果是进行数字原始，还可能调用Number进行隐式转换 【toString是实例原型上的方法】

```js
console.log(10+[10,20]); // -> 1010,20
console.log(10+ new Number(10)); // -> 20 因为这里的new Number(10).valueOf() 有原始值为数字10 
console.log(+new Date()); // -> 1622217307077
```

面试题：

```js
let result = 100 + true + 21.2 + null + undefined + "Tencent" + [] + null + 9 + false;
//  122.2  + undefined + "Tencent" + [] + null + 9 + false;
// NAN + "Tencent" + [] + null + 9 + false;
// NaNTencentnull9false 
console.log(result);
```

由此我们可以在对象转化的过程中能对数据进行修改

```js
const object1 = {
  [Symbol.toPrimitive](hint) {
    if (hint === 'number') {
      return 42;
    }
    return null;
  }
};

console.log(+object1); // 42 
```

* 把其他类型转换为数字

> 把其他类型转换为数字，有以下2套规则
>
> * Number([value])：一般用于隐式转换【数学运算，isNaN, == 比较,...】
>   * 字符串 -> 数字 ：空字符串为`0`，而只要字符串出现**非有效数字字符**结果就是`NaN`
>   * 布尔 -> 数字：`true为1`,`false为0`
>   * null ： 0
>   * undefined ： NaN
>   * Symbol ：会报错
>   * 对象：遵循 `Symbol.toPrimitive -> valueOf -> toString -> Number隐式转换`
>
> * parseInt / parseFloat([value])：首先会把【value】先转换为字符串，从字符串左侧第一个字符开始查找，直到找到一个非有效数字字符为止，会将找到的结果转为数字，如果一个有效数字都没有，结果就是`NaN`,而`parseFloat()比parseInt()`多识别一个小数点
>
>   * parseInt([value]) -> 完整语法`parseInt([string],[radix])`，`[radix]`有效值在`2~36`之间，如果不传递默认是`10`进制，但是如果字符串是`0x`开头默认是`16进制`；如果写`0`，其和不写是一样的规则（10进制或16进制）
>   * 在`parseInt([string],[radix])`其会把`[string]`看做`radix`进制，从左侧找到所有符合该进制的字符，遇到不符合的字符结束查找，在将找到的字符转为数字（10进制）
>   * **`parseInt([value])`中`value`是以`0`开头浏览器会将其先转为8进制，如果是以`0x`开头的，浏览器会将其转化为`16`进制**
>   
>   ```js
>   console.log(parseInt(0123)); // -> 83
> console.log(parseInt(0xA)); // 10
>   ```
>   
>   * 如果`[radix]`不在`2~36`之间，比如`radix=1`，结果都是`NaN`，但0是一个特列
>   * 扫盲1：2进制 -> `0~1`，8进制 -> `0~7`，16进制 -> `0-15`
>   * 扫盲2 ：把其他进制转换为10进制如何处理？
>   
>   ```js
>    let str = '12345.23; =>八进制
>    3*8^-2 + 2*8^-1 + 5*8^0 + 4*8^1 + 3*8^2 + 2*8^3 + 1*8^4
>   ```
>   
> * `parseFloat([string])`其是没有`radix`选项的
>
>   * **`parseInt([value])`中`value`是以`0`开头浏览器会将其先转为8进制，如果是以`0x`开头的，浏览器会将其转化为`16`进制**
>
>   ```js
>   console.log(parseFloat(0123)); // -> 83
>   console.log(parseFloat(0xA)); // 10
>   ```

```js
console.log(Number('10px')) // -> NaN
console.log(parseInt('10px')) // -> 10
console.log(Number('10.xxx')) // -> NaN
console.log(parseInt('10.1xxx')) // -> 10
console.log(parseFloat('10.1xxx')) // -> 10.1

// 面试题
let arr = [27.2, 0, '0013', '14px', 123];
// 27.2 => parseInt(27.2,0) => 27 
// 0 => parseInt(0,1) => NaN
// 0013 => parseInt('0013',2) => 将2进制转为10进制 => parseInt(001) => 1*2^0+0*2^1+0^2^2 = 1 
// '14px' => parseInt('14px',3)  => 将3进制转为10进制=> parseInt('1') => 1*3^0 = 1 
// 123 => parseInt(123,4)  => 将4进制转为10进制=> 3*4^0 + 2*4^1 + 1*4^2 = 3+8 + 16 = 27 
arr = arr.map(parseInt);
console.log(arr); // 27 NaN 1 1 27上
```

#### == 和 === 的区别

JS中验证两个值是否相等，有以下方法

* `==`：相等，如果两边的类型一致，则比较值；如果两边的类型不一样，首先会隐式转化为相同的类型，然后在做比较，规则如下：
  * 对象 == 字符串 ：将对象转化为字符串
  * null == undefined：结果为`true`，但在其在`null === undefined`是不相等，并且`null/undefined`与其他值比较都是`false`
  * `NaN == NaN`：结果是`false`并且`NaN===NaN`也是`false`，但在`Object.is(NaN,NaN)`是相等的
  * `Symbol() == Symbol()`结果是`false`，`Symbol() === Symbol()`也是`false`
  * 剩余的情况，[例如：对象==数字、字符串==布尔...]都是要两者进行转化为数字，在进行比较
* `===`：绝对相等，要求两边的类型和值都要相等，`switch case`的进制也是这样子，但`NaN===NaN`,`Symbol() === Symbol()`的结果都为`false`
* `Object.is([val],[val])`，其`Object.is(NaN,NaN)`的结果为`true`

```js
// 面试题
var a =?;
if(a == 1 && a==2 && a==3) {
    console.log('ok')
}
// 解题思路1：掌握数据类型转换规则，如果a是一个对象，我们就可以利用“对象->数字”的的规则做一些处理
var a = {
    i: 0,
    [Symbol.toPrimitive]() {
        return ++this.i;
    }
};
if (a == 1 && a == 2 && a == 3) {
    console.log('Ok')
}
// ------
a = [1, 2, 3];
a.toString = a.shift
if (a == 1 && a == 2 && a == 3) {
    console.log('Ok')
}

// 解题思路2：我们可以劫持对象成员访问
let i = 0;
Object.defineProperty(window, 'a', {
    get() {
        return ++i;
    }
})

if (a == 1 && a == 2 && a == 3) {
    console.log('Ok')
}
```

#### JS的数据类型检测

* `typeof` ：返回一个字符串，其次字符串中包含对应的数据类型（`number|string|boolean|function|undefied|object`）
* `typeof`检验数据类型，除特殊对象【函数】返回`function`，其余都是`object`，因为函数是做了特殊处理的对象类型

实例：

```js
typeof typeof typeof [10,20]  // => string 因为第一个typeof 就返回字符串`array`，接下来的typeof都是在检测字符串,故返回的结果都是`string`
typeof null // => object
```

##### typeof的检测机制

> `typeof`检测数据类型的底层机制：所有数据类型在计算机中都是按照二进制存储的【根据IEEE-754规范使用64位二进制存储的】，可分为：
>
> 整数：`1`开头，浮点：`010`开头，字符串：`100`开头，对象：`000`开头，undefined的值为`-2^30`，null的值为`000000(6个零)`，那么`typeof`底层机制就是判断这些二进制值，如果是以`000`开始的，都是对象类型`“object”`（特殊性：内部排除函数{通过判断是否能调用`call`方法}），但是`null`也是以`000`开头的，因此`null`中枪了，所以`typeof null == "object"`

缺点：其对对象类型（除函数外）判断不精确

优点：判断机制是根据计算机二进制来，稳定性和性能很好，并且使用起来也很方便

> 详细看**JS内置对象那篇**

##### instanceof 

> 本意是用来检测当前实例对象是否率属于这个类，而是数据类型检测，只不过是我们依托它的这个特点，可以检测数据类型【可以说是，临时拉来充壮丁，所以准确性不好】
>
> 原理：【实例对象 obj】instanceof 【构造函数 Ctor】
>
> * 检测构造函数是否拥有`Symbol.hasInstance`这个属性，如果有这个属性，则调用`Ctor[Symbol.hasInstance](obj)`得到结果，并且因为`Function.prototype[Symbol.hasInstance]`在`Function.prototype`有，故所以的构造函数都可以调用这个方法
> * 不论是否基于这个方法处理，最后的本质都是：检测当前实例`obj`的原型链上是否出现`Ctor`构造函数的原型对象【最终找到`Object.prototype`】，如果出现则结果就是`true`，否则就是`false`

`instanceof`不准确的例子：

* 知道arr到底是array还是object，虽然说arr也是object对象，但要区分{}与[]就做不到

```js
let arr = [10,20]
console.log(arr instanceof Array); // true
console.log(arr instanceof RegExp) // false
console.log(arr instanceof Object) // true
```

* 我们可以肆意修改原型链指向

```js
function Fn() {}
Fn.prototype = Array.prototype;
let f = new Fn;
console.log(f instanceof Array) // true 但是f本质上不应该是称为数组，因为不具备数组应有的结构
```

关于`[Symbol.hasInstance]`

* `ES5`创建构造函数的语法上是不允许我们修改这个属性

```js
function Fn(){}
Fn[Symbol.hasInstance] = function(obj) {
  return false
}
let fn = new Fn;
console.log(fn instanceof Fn) // true
```

* `ES6`创建构造函数语法上是支持修改的

```js
class Fn {
  static [Symbol.hasInstance](obj) {
    return false
  }
}
let fn = new Fn
fn.name = 10;
console.log(fn instanceof Fn) // false
```

扩展：

```js
console.log(1 instanceof Number);// false 基于instanceof 进行处理，并不会把原始值进行装箱处理，也就是原始值类型是不具备原生链的，所以`instanceof`是不能处理原始值类型的
console.lgo(new Number(1) instanceof Number); // true
```

`instanceof`源码

```js
function instance_of(obj,Ctor) {
  let ctorType = typeof Ctor,
  objType = typeof obj,
  reg = /^(object|function)$/i,
  hasInstance,
  proto;
    // 校验Ctor是否是一个对象/函数
    if(Ctor == null || !reg.test(ctorType)) throw new TypeError('Right-hand side of 'instanceof' is not an object')
    // 校验obj是否一个对象/函数
    if(obj ==null || !reg.test(objType)) return false;
    // 校验Ctor是否有prototype
    if(!Ctor.hasOwnProperty('prototype')) throw new TypeError("Function has non-object prototype 'undefined' in instanceof check")
    // 正常检测处理
    hasInstance = Ctor[Symbol.hasInstance];
    if(hasInstance) return Ctor[Symbol.hasInstance](obj) // hasInstance.call(Ctor,obj);
    proto = Object.getPrototypeOf(obj);
    while(proto) {
      if(proto == Ctor.prototype) return true
      proto = Object.getPrototypeOf(proto)
    }
    return false
}
let arr =[10,20]
console.log(instance_of(arr,Number))
```

##### constructor

> `constructor`和`instanceof`一样，并不是专业进行数据类型检测的，我们也可以肆意修改原型对象上的`constructor`属性，影响检测准确

```js
let arr = [10,20],
num=10;
console.log(arr.constructor ===Array) // true
console.log(num.constructor === Number) // true

// 可以修改
Number.prototype.constructor = Array
num=10;
console.log(num.constructor === false) // true
```

##### Object.prototype.toString.call()

> `Object.prototype.toString.call([value])`是万能检测数据的方法，很准确，注意：
>
> * 除了`Object.prototype.toString`是用来检测数据类型的，其余数据类型内置类原型上的`toString`方法，一般都是用来转换字符串的
>
> 原理：`obj.toStirng=>object Object`
>
> `obj`基于`__proto__`找到`Object.prototype.toString`并且把这个方法执行，`toString`方法中`this`是`obj`，所以检测`obj`的数据类型；只要让`toString`方法执行，方法中的`this`是谁，就是在检测谁的数据类型。=> `Object.prototype.toString.call([value]) | ({}).toString.call([value])`
>
> * 返回的结果：`[object 实例[Symbol.toStringTag] / 当前实例所属的构造函数{但自定义构造函数返回的是Object,自己是不可见的}]`，先去读取`[Symbol.toStringTag]`的值

```js
let class2type = {},
toString =class2type.toString // Object.prototype.toString

// 检验自定义构造函数实例的数据类型---> 自定义类的结果不见得和内置类的一样
class Fn{}
let f = new Fn;
console.log(toString.call(f)) // [object Object]

class Fn{
  [Symbol.toStringTag] = 'Fn'
}
let f = new Fn;
console.log(toString.call(f)) // [object Fn]
```

### JS中的堆栈

任何编程语言中，都离不开堆栈，而`JS`中是没有提供堆栈数据结构的，但我们的`js`的是要允许在宿主上（浏览器），浏览器工作是离不开我们的堆栈的，浏览器工作都要往我们内存条中申请分配内存，因此js间接有了堆栈的概念。在这部分我们要理解以下的概念：

* `ECStack`：(Execution Context Stack) 执行环境栈
* `EC(G)`：全局执行上下文                  
* `VO(G)`：全局变量对象，存储当前上下文中声明的变量
* `GO`：全局对象，存放浏览器默认提供给`js`调用的`api`，比如`setTimeout,setInterval...`

> 区分`VO(G)`和`GO`：
>
> * GO是一个全局对象，存放很多API -> 浏览器端有一个叫做`window`的全局变量指向它，`window.xxx`，Node端有一个`global`的全局变量指向它
> * VO/VO(G)是用来存储当前上下文中声明的变量的，他是一个区域【我们可以把它理解为是一个栈内空间】

![stack-heap](/medias/imges/js/jsUp/stack-heap.png)

在代码执行时，有优先级：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence

![访问优先级影响](/medias/imges/js/jsUp/访问优先级.png)

> 上面的例子，a.x的优先级更高，执行完之后，a有被重新赋值，所以a.x就是`undefined`，也引出一个知识点：访问对象中的摸一个成员，如果成员不存在，不会报错，但是结果是`undefined`

![变量声明](/medias/imges/js/jsUp/变量声明区别.png)

> 上面的例子是忽略了变量提升，并且是在**全局上下文**中
>
> * 基于`var/function`声明的变量或者函数，老版本浏览器是在`VO(G)与GO`都存储，而新版的浏览器目前是不会向`VO(G)`中存储了，直接存储到`GO[当做对象键值对]`中。例子：`console.log(a) ` -> 首先检查在全局变量`VO(G)`中是否存在，存在则读取值，否则在去`GO`中查找是否存在，如果也没有则报错`ReferenceError`。上面的例子中`a=13`也是怎么过程，这里`VO(G)`没有，则向`GO`查找，查找到了就修改`a`的值。相当于内部`window.a=13`
> * 基于`let/const`声明的变量，还是乖乖的存储在`VO(G)`中的，和`GO`没啥关系
> * 如果不带任何声明关键字，则也是直接给`GO`设置的，类似于`window.f = 14`

### JS的常见数据结构

#### 数组结构 Array

> JS中的数据结构非常简单，已经是浏览器帮助我们进行封装处理好的
>
> * 其值可以存储不同的数据类型值
> * 数组容量伴随存储的内容自动缩放
> * `Array.prototype`上提供很多供数组操作的方法
>
> 数组对象相对于对象的特殊性在于：普通对象的的键名是不能出现对象类型的，如果出现会将其转化为字符串，规则为前面说的 **对象->字符串**那一套，但数组的键名是以数字开始递增的，并且以0开始，并且内置`length`属性表示数组的长度。

```js
let obj = {
    [{name:1}]:10
} // 输出会是 {[object Object]:10}
```

面试题

```js
let a = {name:'zhangsan'}
let b = {name:'lisi'}
let obj = {
    [a]:10
}
obj[b] = 20
console.log(obj); // -> {[object Object]:20};
```

#### 栈结构 Stack

特点：后进先出 或 先进后出

*  新进栈的内容在栈的顶端，这样会把之前进栈的内容压缩到栈的底部
* 出栈也是需要从顶端开始出，上面的出不去，下面的也出不去

```js
class Stack {
    container = [];
    enter(element) {
        // 进栈
        this.container.unshift(element);
    }
    leave() {
        // 出栈
        return this.container.shift();
    }
    size() {
        // 查看大小
        return this.container.length;
    }
    value() {
        // 获取栈的内容 slice实现浅克隆，目的是保证外部接受到的内容，在进行操作的时候，不会直接影响container
        return this.container.slice(0);
    }
}
```

面试题：十进制转二进制

在js中已经为我们提供了数字之间进制转化的方法`toString([radix])`，默认是10进制

```js
// 10进制转二进制
let num = 2
console.log(num.toString(2)); //10
```

```js
// 封装的10进制转2进制的方法
function decimal2binary(decimal) {
    if (decimal === 0) return 0;
    let negative = decimal < 0; // 判断是否为负数
    decimal = Math.abs(decimal); // 将传入的值都去绝对值
    let merchant = Math.floor(decimal / 2), // 取商
        remainder = decimal % 2, // 取余
        sk = new Stack;
    sk.enter(remainder);
    while (merchant > 0) {
        remainder = merchant % 2;
        sk.enter(remainder);
        merchant = Math.floor(merchant / 2);
    }
    return `${negative?'-':''}${sk.value().join('')}`;
}
```

### 上下文和函数运行机制

#### 上下文

> 在当前上下文（**全局上下文/私有上下文/块级上下文**），`JS`代码自上而下执行之前，首先会把当前上下文中带`var/function`关键字进行变量提升：带`var`的只是提前声明，并**没有赋值（定义）**，但是带`function`关键字的，只是提前的**声明+赋值（定义）**；而基于`let/const`声明的变量不具有这个机制

变量提升的讨论

```js
// 这是foo是有被进行变量提升，而bar却没有
console.log(foo); // undefined
console.log(bar); // 报错 bar is not define 
var foo = bar = 1; 
```

对于函数声明的提升有情况进行分析：

```js
// 这样子才能声明提升*****
function a() {
    
}
// 而下面这些是不存在变量提升
// 开发中推荐使用这种，避免函数调用混乱
const fn = function() {
    ....
}

function a() {
    // return 后面的函数是不存在变量提升的
    return function(){
        // ....
    }
}
// 值得注意的是匿名函数以及对象属性是函数的时候，函数都不具备提前声明和定义，他们是代码执行到才声明+定义
(function())();
 let obj= {
   fn:function() {}
 }
```

 **函数的底层运行机制：**

* 创建函数：声明函数 + 赋值

> 1. 在`Heap`（堆内存）中分配一块空间【16进制的内存地址】
> 2. 除了存储一些静态方法（键值对【和普通对象一样】），还做了很多其他的事情
>    * 声明了函数的作用域`[[scope]]`，作用域的值是当前创建函数时候所在的上下文
>    * 把函数体中的代码当做 “字符串” 存储到堆内存中
>    * 和对象一样，存储一些自己的键值对【静态私有的属性和方法】
> 3. 把内存地址赋值给对应的变量【函数名】即可

* 执行函数

> 1. 形成一个私有的上下文`EC(x)`，供后期函数体中的代码执行
>
>    * 在私有上下文中，有一个给后期代码存储的当前上下文中声明的变量的地方，叫做**私有变量AO对象**【AO是VO的分支，在函数中，变量对象是AO】
>    * 进栈执行
>
> 2. 在代码执行之前，它还会处理很多事情
>
>    * **初始化作用域链`[[scope chain]]`**：<自己的上下文，函数的作用域{创建时候声明的}>
>
>      > 作用：但凡代码执行的时候，遇到一个变量，首先看是否是自己上下文中的私有变量（从AO中找）。如果AO中有，接下来对变量的操作和外界没有任何 关系；但如果不是自己的私有的（AO中的），则继续按照作用域链查找是否为其上级上下文（也就是函数作用域）中的....如果查找过程中一直没有，机制运行查找到`EC(G)`即可结束
>
>    * 初始化`this`指向
>
>    * 初始化`arguments`（实参集合）
>
>    * 形参赋值：形参变量也是当前上下文中的私有变量，是要存储到`AO`中的
>
>    * 变量提升，函数的变量提升也是在`AO`中声明的，和`GO/VO`无关
>
> 3. 代码执行
>
> 4. 一般情况下，在代码执行完，当前形成的私有上下文会出栈释放，以此来优化内存空间，但如果形成了闭包机制，则不会被自动私放，需要我们手动。

#### 块级上下文：

> 在ES6中，除了全局上下文和函数执行的私有上下文，还多了一种上下文机制：**块级私有上下文【作用域】**，在除函数/对象等之外的大括号中【例如：判断体、循环体....】，如果出现`let/const/function`这些关键词任意其中一个，就会产生一个块级上下文；在此块级上下文中，基于`let/const/function`声明的变量都是私有的；
>
> 特殊性：
>
> * `function`会在块级上下文中`声明+定义`，而在其上级上下文中是只声明不定义，其在上级上下文定义是在其代码在块级上下文执行时才会同步上来
> * 基于`var`关键字声明的变量，即不会产生块级上下文，也不会受到块级上下文的影响

![块级作用域](/medias/imges/js/jsUp/block-scope.png)

基于循环会有情况讨论

```js
// 这样处理 i 都是全局的,因为在块级作用域找不到的变量会沿着其作用域链去查找
let i = 0;
for (; i < 5; i++) {
    // ...
    // console.log(i);
}
console.log(i); //5 

// 这样处理i是局部的，并且这样子的运行机制可看下图解析
for (let i = 0; i < 5; i++) {

}
// console.log(i); //Uncaught ReferenceError: i is not defined
```

![块级作用域](/medias/imges/js/jsUp/for-scope.png)

#### 一些特殊的机制：

**函数带有形参：**

> 函数一但设定“**形参赋值默认值**【不论是否生效，并且同时具备以下条件】
>
> * 函数体中有基于`let/const/var`声明的变量的操作【不论是声明别的变量还是声明和形参相同的变量（注意：`let/const`声明的变量是不能和形参一致，因为两者都不允许变量重复声明）】这是就会产生一种很“恶”的新机制，以下进行该新机制的描述
>   * 除了函数执行产生的私有上下文`AAA`【函数上下文执行了：初始化作用域链、初始化this、初始化arguments、形参赋值之后就任务结束，之后的全交给由此产生的块级作用域】
>   * 接下来会把函数体处理，抛离开这个私有上下文，形成一个全新的私有块级上下文`BBB`来进行处理【初始化作用域链，变量提升，代码执行】

![函数带有形参](/medias/imges/js/jsUp/func-block.png)

**在JS非严格模式下，arguments的映射机制：**

> 在JS**非严格模式下**【现在一般都是严格模式`use strict`】，在初始化`arguments`和形参参数赋值后，会让`arguments`中的每一项和形参建立**映射/关联**机制，后期改一个另外一个也会被修改

![非严格模式下arguments的映射机制](/medias/imges/js/jsUp/arguments.png)

#### 匿名函数的特殊性

> 匿名函数通俗来讲就是没有名字的函数，其主要出现的形式是：

```js
// 自执行函数
(function(){xxx})()
// 函数表达式:把函数作为做值，赋值给别的东西（变量、事件、回调函数...）
const fn = function(){} 
document.click = function() {}
function xxx() {return function(){}}
setTimeOut(function(){xxx})
```

匿名函数要实现**递归操作**，有以下两种方法

* **处于非严格模式**，巧妙使用`arguments`的属性。
  * `arguments.callee()`：当前执行函数的本身
  * `arguments.callee.caller`：当前函数执行所在的环境（全局下执行时null）

存在的问题：

1. `arguments.callee()`再次执行的时候，方法中的`this`是`arguemnts`，而之前第一次执行的`this->window`
2. 在严格的模式下，第一次执行`this->undefined`，而`argument.call`在严格模式下无法使用并且会报错

```js
let index=0;
(function(){
    index++;
    if(index>5)return 
    console.log(index,this);
    arguments.callee();
})()
```

* 匿名函数具名化：原本应该是匿名函数，但是现在我们设置了名字，和正式的实名函数还是有很大区别
  * 具名化的名字，不允许在所处上下文中使用【不能在声明外部】，因为所处上下文并不会声明这个变量
  * 但是可以在函数内部使用【相当于在函数私有上下文中的`AO`中，声明一个`b`变量，赋值为当前的函数】
  * 并且直接修改这个名字的值是不生效的
  * 但是这个名字的值默认是函数本身，这个的优先级比较低，但凡当前函数上下文中，基于`let/const/var/function`重新声明这个`b`，都是以重新声明的为主

```js
(function b(){
    b = 20; // 覆盖无效
    console.log(b); // 函数
})()
console.log(b) // b is not define

// 递归实现
let i = 1;
(function b() {
    i++;
    if (i > 5) return
    console.log(i)
    b();
})();
console.log(b);
```

面试题解析：

```js
var x = [12, 23];
function fn(y) {
    y[0] = 100;
    y = [100];
    y[1] = 200;
    console.log(y); // [100,200]
}
fn(x);
console.log(x); // [100,23]
```

![面试题-1](/medias/imges/js/jsUp/interview-1.png)

```js
var i = 0;

function A() {
    var i = 10;

    function x() {
        console.log(i);
    }
    return x;
}
var y = A();
y();

function B() {
    var i = 20;
    y();
}
B();
```

![面试题-1](/medias/imges/js/jsUp/interview-1.png)

```js
var i = 0;

function A() {
    var i = 10;

    function x() {
        console.log(i);
    }
    return x;
}
var y = A();
y();

function B() {
    var i = 20;
    y();
}
B();
```

![面试题-1](/medias/imges/js/jsUp/interview-1.png)

```js
var i = 0;

function A() {
    var i = 10;

    function x() {
        console.log(i);
    }
    return x;
}
var y = A();
y();

function B() {
    var i = 20;
    y();
}
B(); // 输出结果: 10 10 
```

![面试题-2](/medias/imges/js/jsUp/interview-2.png)

> 函数不论在哪执行，他的上级上下文一定是他的作用域，而作用域是和函数在哪创建有关系。所以无论函数在哪执行，遇到变量不是自己私有的，都会按照作用域链查找其上级上下文中，也只和在哪创建有关系，和在哪执行时没有关系的。

```js
let x = 5;

function fn(x) {
    return function (y) {
        console.log(y + (++x));
    }
}
let f = fn(6);
f(7);
fn(8)(9);
f(10);
console.log(x); // 输出结果: 14 18 18 5
```

![面试题-3](/medias/imges/js/jsUp/interview-3.png)

```js
let a = 0,
    b = 0;

function A(a) {
    A = function (b) {
        alert(a + b++);
    };
    alert(a++);
}
A(1);
A(2); // 输出结果: 1 4 
```

![面试题-4](/medias/imges/js/jsUp/interview-4.png)

```js
var a = 9;
function fn() {
    a = 0;
    return function (b) {
        return b + a++;
    }
}
var f = fn();
console.log(f(5));
console.log(fn()(5));
console.log(f(5));
console.log(a);
```

![面试题-5](/medias/imges/js/jsUp/interview-5.png)

```js
var x = 5,
    y = 6;
function func() {
    x += y;
    func = function (y) {
        console.log(y + (--x));
    };
    console.log(x, y);
}
func(4);
func(3);
console.log(x, y); 
```

![面试题-6](/medias/imges/js/jsUp/interview-6.png)

```js
var num = 10;
var obj = {
    num: 20
};
obj.fn = (function (num) {
    this.num = num * 3;
    num++;
    return function (n) {
        this.num += n;
        num++;
        console.log(num);
    }
})(obj.num);
var fn = obj.fn;
fn(5);
obj.fn(10);
console.log(num, obj.num);
```

![面试题-7](/medias/imges/js/jsUp/interview-7.png)

```js
var a = 9;
function fn() {
    a = 0;
    return function (b) {
        return b + a++;
    }
}
var f = fn();
console.log(f(5));
console.log(fn()(5));
console.log(f(5));
console.log(a);
```

![面试题-8](/medias/imges/js/jsUp/interview-8.png)

```js
var x = 5,
    y = 6;
function func() {
    x += y;
    func = function (y) {
        console.log(y + (--x));
    };
    console.log(x, y);
}
func(4);
func(3);
console.log(x, y);
```

![面试题-9](/medias/imges/js/jsUp/interview-9.png)

```js
var num = 10;
var obj = {
    num: 20
};
obj.fn = (function (num) {
    this.num = num * 3;
    num++;
    return function (n) {
        this.num += n;
        num++;
        console.log(num);
    }
})(obj.num);
var fn = obj.fn;
fn(5);
obj.fn(10);
console.log(num, obj.num);
```

![面试题-10](/medias/imges/js/jsUp/interview-10.png)

内存释放：

* 堆内存：看当前堆内存地址是否被其他事物（变量/事件绑定）所占用，如果被占用就不能释放【因为一旦释放，以后别的东西基于这个地址就找不到堆了】，如果没有被占用，则可以释放...
* 栈内存【执行上下文】：
  * 全局上下文，只有在页面关闭的时候才会释放
  * 函数执行形成的私有上下文，一般情况下，在函数代码执行完毕后，私有上下文会自动出栈释放；但是有一些特殊的情况是无法出栈释放的：当前上下文中的某些内容，被上下文以外的事物占用了，则不能出栈释放，一般都是对象类型变量的地址作为返回值，让除当前上下文之外所引用

闭包机制：

* 保护作用：保护私有上下文中的私有变量不受外界的干扰（防止全局变量污染）
* 保存作用：一旦当前上下文不被释放，上下文的私有变量及其值都会被保存起来，供其下一级上下文调取使用

每次函数执行都会产生一个私有上下文，这个上下文有两个大作用，也可以说是副作用，它们也被称为闭包机制【市面上一般都是认为：只有产生不被释放的上下文才算闭包，但其实函数执行就是一个闭包，只是说这个闭包周期短】

### this的指向

> this：函数执行的主体【谁执行了函数】，它与函数上下文是两个概念，函数上下文是函数执行时创建的一个环境，而this是执行对象。举个例子，张三去兰州拉面馆吃兰州拉面，我们将这里的**吃兰州拉面**比作是我们的函数，this则为张三，函数上下文为兰州拉面馆。对应`this`的讨论，我们基于以下几种情况：
>
> * 事件绑定
> * 函数执行【普通函数，成员访问，匿名函数，回调函数】
> * 构造函数
> * 箭头函数[生成器函数generator]
> * 基于call/apply/bind强制修改this指向

* 事件绑定：
  * DOM0：`xxx.onxxx=function(){}`
  * DOM2：`xxx.addEventListener('xxx',function(){})`，IE6,7:`xxx.attatchEvent('onxxx',function(){})`

给当前元素的某个事件行为绑定方法【此时是创建方法，方法没执行】，当事件行为触发，浏览器会把绑定的函数执行，此时函数中的this-》就是当前元素本身

 特殊：基于attachEvent实现事件绑定，方法执行，方法中的this是window

```js
document.body.addEventListener('click', function() {
    console.log(this) // -> body
});
```

* 函数执行【普通函数，成员访问，匿名函数，回调函数】

  *   正常的普通函数执行：看函数执行前是否有“点”，有“点”前面是谁this就是谁，没有的话this就是window【严格模式下是undefined】

  ```js
  function fn() {
      console.log(this)
  }
  let obj = {
    fn
  };
  fn(); //this -> window/undefined
  obj.fn(); //this -> obj
  ```
  
  * 匿名函数：
  
    * 函数表达式:等同于普通函数或者事件绑定机制
  
    ```js
    var fn = function() {
        console.log(this) // this->window
    }
    fn()
    ```
    
    * 自执行函数：this一般都是window/undefined
    
    ```js
    (function(x) {
        console.log(this) // this -> window / undefined
    })(10);
    ```
    
    * 回调函数：一般都是window/undefined 但是如果另外函数执行中，对回调函数的执行做了特殊处理，以自己处理的为主
    
    ```js
    function B(callback) {
        // callback ->匿名函数
        callback();
    }
    
    B(function() {
        console.log(this); // this->window/undefined
    })s
    ```
    
  *     括号表达式: 小括号中包含“多项”，这样也取最后一项，但是this受到影响 (一般是window/undefined)
  
  ```js
  (obj.fn)(); // this -> obj
  (19, obj.fn)(); // this-> window/undefined
  ```

* 构造函数

构造函数实在普通函数执行的基础上，会多创建一个实例对象，并且在初始化`this`时并让这个`this`指向这个实例对象，在函数执行的函数体中`this.xxx`就是往这个实例对象里添加私有属性。

* 箭头函数

箭头函数在普通函数上少了`初始化this/初始化arguments`，其没有自己的this，而在其内部使用的`this`其实就是其上级上下文中的`this`，可以说是继承。

对比箭头函数和普通函数的几大核心区别：

1. 语法区别
2. THIS区别：箭头函数没有自己的`this`
3. 原型区别：箭头函数没有`prototype`属性，所以不能被`new`执行
4. arguments：箭头函数中没有`arguments`实参集合

所以说，我们通过`bind/apply/call`等强行改变箭头函数`this`的指向是没有意义的。

* 基于`call/apply/bind`强行改变`this`指向

接下来我们来进行手撕`call/apply/bind`让我们对其内部更加理解，而在此之前我们要先对它们三者的用法与区别有一定认知

> 我们将`call/apply`能立即执行分为一类，而`bind`并不能立即执行分为一类。
>
> * `call/apply`的区别在于执行的时候，传递实参的方式不一样，call是一个一个传递实参，而`apply`需要把实参放在一个数组中传递给函数
> * `bind`：其是利用闭包思想，预处理函数中的`THIS`指向和参数的

```js
// 我们要想fn执行时的this是obj
window.name = 'window';
const fn = function fn(x, y, ev) {
    console.log(this.name, x + y, ev);
    return '@zs';
};
let obj = {
    name: 'obj'
};
```

手撕call

> 思路：`obj.fn(10,20)`，我们要想fn执行时的`this`指向obj，那么`obj`这个对象中属性必须要有`fn`。

```js
Function.prototype.call = function call(context,...params) {
    // 当context没传或者undefined时，context就指向window
    if(context == null) context=window;
    // 传入的context必须是对象类型
    if (!/^(object|function)$/i.test(typeof context)) context = Object(context);
	// 怕与context的命名重复，使用Symbol设置唯一值
    let key = Symbol('KEY');
    context[key] = this;
    let result = context[key](...params);
    // 删除多余的值
    delete context[key];
    return result;
}

// fn.call(obj,10,20)执行的流程：fn通过原型链找到Function.prototype上的call方法，之后执行call；call里面将fn函数的this转换为obj并且执行函数
let res = fn.call(obj, 10, 20);
console.log(res);
```

手撕`apply`

```js
Function.prototype.apply = function apply(context,params) {
  if(context == null) context= window;
  if(!/^(object|function)$/i.test(typeof context)) context=Object(context)
  if(Object.prototype.toString.call(params) !== '[Object Array]') {
    throw new TypeError(`${params} is not a array`)
  }
  let key = Symbol('KEY');
  context[key]= this;
  let result =  context[key](...params)
  delete context[key];
  return result
}
let res = fn.apply(obj, [10, 20]);
console.log(res);
```

手撕`bind`

> 思路是利用闭包/柯里化函数的思想，对context和传递进来的实参进行预处理

```js
Function.prototype.bind = function bind(context, ...params) {
    // this:fn  context:obj  params:[10,20]
    let self = this;
    return function anonymous(...args) {
        // args:[ev]
        params = params.concat(args);
        return self.call(context, ...params);
    };
};
submit.onclick = fn.bind(obj, 10, 20);
```

面试题：

```js
function fn1(){console.log(1);}
function fn2(){console.log(2);}
fn1.call(fn2); // 1 
fn1.call.call(fn2); // 2
Function.prototype.call(fn1); // 不输出任何
Function.prototype.call.call(fn1); // 1
```

```js
var name = '珠峰培训';
function A(x,y){
    var res=x+y;
    console.log(res,this.name);
}
function B(x,y){
    var res=x-y;
    console.log(res,this.name);
}
B.call(A,40,30); // 10 'A'
B.call.call.call(A,20,10); // NaN undefined
Function.prototype.call(A,60,50); // Function.prototype 是个匿名空函数不输出任何
Function.prototype.call.call.call(A,80,70); // NaN undefined
```

![call复杂题](/medias/imges/js/jsUp/callApplyBind-review.png)

注意：多个`call`时，比如`Function.prototype.call.call.call(A,80,70);`，最后的结果是让`A`去执行，A执行的内部`this->80`，其余的参数照常传入；一个`call`就正常执行

### let & const VS var的区别解析

主要区别在于:

* 变量提升：`var`存在变量提升，`let`不存在变量提升
* 重复声明：在相同的上下文中，`var`可以重复声明，但是`let`不允许重复声明【不论之前基于何种方式，只要在当前上下文中声明过这个变量，那么则不能在基于`let/const`重复声明了】

```js
// var的重复声明的讨论
// 根据ECMScript的规范说明，当前上下文中基于var声明的变量重复声明时，重复声明相当于无效【跳过这行代码执行】，但若是赋值操作则会覆盖原来的值。
var a =10;
var a;
console.log(a); // 10

// 函数形参与函数体变量（带var）同名的讨论
// 这种情况也和上面的ECMScript的处理机制一致，当函数的形参名和函数体的变量名重复时候，重复声明相当于无效【跳过这行代码执行】，但若是赋值则是覆盖原来的值。
function test(x) {
    var x ;
    console.log(x) // 5
}
test(5);
```

* 和全局`GO`的关系：全局上下文中，基于`let`声明的变量存储到`VO(G)`中，而基于`var`声明的变量，存储到`GO`中
* 块级上下文：除函数和对象的`{}`外，如果`{}`中出现`let/const/function`则会产生块级上下文，但`var`却不会产生块级上下文并且块级上下文也对`var`的变量没有任何的影响
* 暂时性死区：可以说是浏览器的一个`bug`

#### let VS const

> 他们两个都是用来声明**变量**的，而市面上有人却说`const`是来声明常量的，这个说法是不准确的。变量和常量的定义可为：
>
> * 变量：可变的量，其实就是一个名字，用来关联一个值【指针指向】，而且关联的值是可以改变的
> * 常量：不可变的量，其实就是一个值
>
> 市面常说`const`是来声明常量的，是因为`const`声明的量的值一定要在声明赋值，并且后期这个值不可以被修改。而他们忽略了当值是对象类型的时，我们可以通过变量去操作其对应的存储内容，因此我认为`const`也是声明变量的，但其声明的变量是比较特殊的。
>
> * 定义就要赋值
> * 变量的指针不允许重新指向

```js
// let 声明的变量是可以被修改的
let n = 10;
n = 20;
console.log(n); // => 20

// 基于const声明的变量是不允许指针重新指向
const m=10;
m =20; // Uncaught TypeError: Assignment to constant variable

// 声明就要定义值
const m; // Uncaught SyntaxError: Missing initializer in const declaration
m = 10;

// 基于const声明的变量的值是对象类型，我们可以修改对象其内容
const m = {
    name:'zs'
}
m.name = 'ls'; // 这样子是允许的
```

#### let/const 新语法规范 VS var/function 老语法规范

> 其实在代码执行之前，我们浏览器是先去将我们的代码转化成复合其解析语法的`AST`语法树，得到`AST`语法树就能判断我们的代码书写是否规范且正确

```js
// 关于变量提升: var 存在变量提升 let不存在
console.log(n); // undefined
console.log(m); //  Uncaught ReferenceError: Cannot access 'm' before initialization
var n =10;
let m =20;

// 关于变量存储位置: 在全局上下文 var 存储在GO中，let 存储在VO中
var n =10;
let m =20;
console.log(n,m); // 先找VO(G),再找GO  10/20
console.log(window.n,window.m); // 直接查找GO 10/undefined

// 关于重复声明: var可以重复声明，let不可以
console.log(n);
var n = 10;
var n = 20;
let n = 30; // Uncaught SyntaxError: Identifier 'n' has already been declared

// 关于暂时性死区问题：例如：基于typeof检测一个未被声明的变量，不会报错，结果是`undefined`,
console.log(n); //  Uncaught ReferenceError: n is not defined
console.log(typeof n); // undefined
// 而let/const 可以规避这个暂时性死区问题，因为let/const的变量使用一定是在其声明之后
console.log(typeof n); //Uncaught ReferenceError: Cannot access 'n' before initialization
let n = 10;
```

#### var/let关于块级作用域

从一个例子中引出问题：为何下面的例子是连续输出`5`，而不是`0,1,2,3,4`呢?有以下原因：

* 循环中的`i`都是访问全局上下文中的
* 定时器是异步的【第一轮循环设置一个定时器，不需要等待定时器触发执行，继续下一轮循环...】
* 循环结束后【全局 `i->5`】,设置了`5`个定时器，等到定时器到了触发阶段，在分别把五个定时器进行触发
* 当定时器中设置的回调函数执行，形成私有上下文，遇到`i`不是自己的私有变量，就往上级上下文进行查找，这里是全局，并且`i=5`

```js
for (var i = 0; i < 5; i++) {
    // 循环五次设置五个定时器
    setTimeout(function () {
        console.log(i); // 5 5 5 5 5
    }, 0);
} 
```

> 那我们的需求就是要`0,1,2,3,4`输出呢？咋解决：**利用闭包机制**【let天然闭包 & 自己创造闭包 & 一些方法提供传入参数内部也是利用闭包机制】，缺点：**形成不被内存释放多个块级上下文，多个对象内存，多个事件绑定对象，不利于内存优化**，例如下面**按钮实例**的就有：`5个不被释放的块级上下文 + 5个不被释放的函数事件 + 5个DOM对象`

* `let`的天然闭包机制

```js
for (let i = 0; i < 5; i++) {
    // @1 每一轮循环产生一个私有的块级上下文「每一个块级上下文中的函数被全局中的setTimeout占用 -> “不被释放|闭包”」
    // @2 每一个块中都有一个私有变量 i,存储的是每一轮循环i的值 0 1 2 3 4
    let timer = setTimeout(function () {
        console.log(i); // 0 1 2 3 4
        // 清除使用过的定时器，这样把每一个块级上下文中，创建的函数以及其引用移除掉，让浏览器空闲的时候，回收这些不被释放的内存
        clearTimeout(timer);
        timer = null;
    }, 0);
} 

// ===========
for (let i = 0; i < buttons.length; i++) {
    buttons[i].onclick = function () {
        console.log(`索引是:${i}`);
};
```

* 自己创建闭包

```js
let i = 0;
for (; i < 5; i++) {
    // 自己在每一轮循环构建私有上下文「闭包」
    (function (i) {
        // i私有变量「形参」0 1 2 3 4 
        setTimeout(function () {
            console.log(i);
        }, 0);
    })(i); //把每一轮循环时候i的值，作为实参传递给私有上下文中的i
} 
```

* 一些内置方法根据出入的参数并内部使用闭包保存

```js
let i = 0;
for (; i < 5; i++) {
    setTimeout(function (i) {
      console.log(i);
   }, 0, i);
    // 定时器第三个参数，是传递给定时器设定回调函数的形参的值「定时器内部其实也是按照闭包的机制处理的：把每一次传递的值，预先存储起来，后期定时器触发执行，再从之前存储的值中拿出来用即可」机制类似下面这种
    let fn = function (i) {
        console.log(i);
    };
    setTimeout(fn.bind(null, i), 0);
} 
```

**利用自定义属性**：一般，其较上面的内存优化了一些，但其还是存在多余的事件源，和DOM对象存储于内存中，例如这里的不在产生不被释放块级上下文优化了`5个块级上下文对象`，但还是存在`5个事件源对象+5个DOM对象`

```js
// buttons是一个节点集合NodeList，集合中的每一项是一个元素对象「对象/堆内存：内置的属性和方法」
let box = document.querySelector('.box'),
    buttons = box.querySelectorAll('button');
let i = 0;
for (; i < buttons.length; i++) {
    // 每一轮循环的时候，我们把当前这一轮的索引，基于自定义属性myIndex，存储到元素对象的堆内存中
    buttons[i].myIndex = i;
    buttons[i].onclick = function () {
        // this:当前点击的这个元素对象
        console.log(`索引是:${this.myIndex}`);
    };
}
```

**事件委托**【开发推荐】：内存利用最好，不在产生不被释放块级上下文，少去了子元素的事件源对象，少去了DOM子元素

```js
let box = document.querySelector('.box');
box.onclick = function(ev) {
    let target = ev.target;
    if (target.tagName === 'BUTTON') {
        // 点击的是BOX盒子中的按钮
        console.log(`当前点击按钮的索引是:${target.getAttribute('data-index')}`);
    }
};
```

借花献佛：这里扩展一下`DOM`的属性设置

> DOM的属性设置我们可以通过`.`，或者`setAttribute(属性名，属性值)`来定义，但其两者还是有很大的区别

* 通过点（`.`）来设置属性是往DOM对象内存中去设置，设置值的类型可以是任意，所以我们在获取的时候也要通过那个对象的内存地址才能获取属性值

```js
let box =document.getElementById('#box');
box.myId=1; // 设置
console.log(box.myId); // 获取
// 但其通过`getAttribute`就获取不到，并且DOM结构上也不会显示设置的属性，前提设置的属性是在DOM结构上默认不存在的
```

* 通过`.setAttribute(属性名,属性值)`设置是往其`DOM`结构设置，并且值一定为字符串

```js
let box =document.getElementById('#box');
box.setAttribute('myId',1);
console.log(box.getAttribute('myId')); // 1
// 页面DOM结构为 <div class="box" myid="1"></div>
```

区别总结：

* 从设置与访问来看：`.`设置值是往其对象内存设置，获取也要通过对象地址访问。`setAttribute()`是设置在DOM结构上，必须通过`getAttribute()`来获取对应的值
* 从设置值的类型来看：`.`可以设置任意值，但`setAttribute()`只能设置字符串值

### JS的模块化编程

> JS模块化编程发展史：`JS`本身是弱化命名空间概念的，只有全局作用和函数私有作用域（ES6中新增块作用域），而模块化开发，从某种意义上来说，是强化了命名空间的概念，但其有以下的好处：
>
> * 有利于代码分离、解耦以及复用
> * 团队并行开发
> * 避免变量命名冲突
> * 可以模块之间进行相互引用和按需加载
>
> 发展史：
>
> 单例设计模式----> AMD (require.js) ---> CommonJS (Node.js)  ----> CMD（sea.js）[把commonjs规范搬到浏览器端] ---> ES6Module

我们从一个例子来说明模块化出现的意义？

```js
let name = 'zs'
let age = 12;

let name = 'ls';
let age = 18;
```

> 当这个例子运行，就会报错，因为`let/const`不允许变量的重复声明。那如何来解决呢？最为直观的方法就是使用**闭包**，例如下面例子，基于闭包解决问题，保证了私有性，每个闭包相当于一个独立的空间；并且想要实现相互之间调用，则可以基于`window.xxx`把其暴露到全局上，但其**局限性：{并未真正彻底解决变量命名冲突，因为当我们过多向全局暴露内容，也有可能导致冲突}**

```js
(function(){
    let name = 'zs'
    let age = 12;
    const fn = function fn(){}
    window.fn = fn;
})();

(function(){
    let name = 'ls';
    let age = 18;
    fn();
})()
```

> 基于闭包确实能解决大部分的问题，但因其存在局限性，早期为了彻底解决这种问题就提出了**单例设计模式**

#### 单例设计模式

> 所有的设计模式其实就是一种思想，用来解决某一类问题。单例设计模式也是如此。其解决问题是：每个实例（模块）之间相互独立，避免全局变量的污染，而且还可以相互调用方法

最为简单并且符合单例设计模式要求的就是**对象类型变量**，原因如下：

* 每一个对象都是`Object`这个类的一个单独实例
* 每一个对象都是一个堆内存空间，存储自己的私有键值对【私有化，符合独立】
* `person1/person2`除了被称为对象名，也可以称为**命名空间nameSpace**
* 我们后期可以基于命名空间，访问到指定空间中的内容【相互引用】

```js
let person1 = {
    name:'zs',
    age:12,
    fn(){}
}
let pserson2 = {
    name:'ls',
    age:18,
    handle(){
        // 调用了person1的方法
        person1.fn()
    }
}
```

而在真实项目中的应用是：**闭包+单例=高级单例设计模式**来实现，它既解决了闭包的局限性也让单例模式更加健壮

```js
let AModule = (function(){
    let name = 'zs';
   	let age = 12;
    const fn = function fn(){};
    const sum = function sum(){};
    // 基于return的方式，把当前模块中需要供其他模块调用的内容暴露出去
    return {
        sum
    }
});

let BModule=(function(){
    let name = 'ls';
    let age =18;
    const fn = function fn(){}
    AModule.sum();
    return {
        name,
        fn
    }
})();
```

#### AMD

> 单例的设计模式出现之后，由于其要我们自己构建闭包，并且存在多余不被释放的闭包，书写代码较为繁琐，因此前端界盛行其了`AMD`模块化开发规范，ADM：即可以实现模块化开发，也能有效解决模块之间的依赖问题。最为典型代表就是`require.js`，官网：https://requirejs.org

```js
// define创建一个模块 AModule
define([
    // 设定此模块需要依赖的其他模块
    'BModule'
],function(require,factory){
    // ... 模块中私有内容
    return {
        // 供其他模块调用的内容【暴露API】
    }
})
```

main.js

```js
//全局配置
require.config({
    baseUrl: 'js/lib',
});

require(['moduleB', 'moduleA'], function (moduleB, moduleA) {
    console.log(moduleB.average(10, 20, 30, 40, 50));
});
```

moduleA.js

```js
define(function () {
    return {
       // 任意数求和
        sum(...args) {
            let len = args.length,
               firstItem = args[0];
           if (len === 0) return 0;
           if (len === 1) return firstItem;
            return args.reduce((total, item) => {
               return total + item;
           });
       }
    };
});

define(['moduleA'], function (moudleA) {
    return {
        // 求平均数（去掉最大最小值）
       average(...args) {
           let len = args.length,
               firstItem = args[0];
            if (len === 0) return 0;
           if (len === 1) return firstItem;
          args.sort((a, b) => a - b);
           args.pop();
           args.shift();
            return (moudleA.sum(...args) / args.length).toFixed(2);
        }
    };
});
```

自己实现一套AMD模块机制

```js
let factories = {};

function define(moduleName, factory) {
    factories[moduleName] = factory;
}

function require(modules, callback) {
    modules = modules.map(function (item) {
        let factory = factories[item];
       return factory();
    });
    callback(...modules);
}

/* 使用AMD */
define('moduleA', function () {
   return {
       fn() {
           console.log('moduleA');
       }
   };
});

define('moduleB', function () {
  return {
        fn() {
          console.log('moduleB');
      }
   };
});

require(['moduleA', 'moduleB'], function (moduleA, moduleB) {
    moduleB.fn();
    moduleA.fn();
});
```

#### CommonJS

>AMD模仿规范使用起来就要处理好当前模块所有的依赖项，不能做到我们想用就直接导入使用的方便程度，因此`Commonjs`规范孕育而生，`commonJS`规范是`Node`自带的模块化管理规范【但在浏览器端是不支持的】
>
>* 它是以一个`js`文件为一个模块
>* 通过`module.exports/exports`暴露模块内容
>* 通过`require()`导入当前模块依赖的其他模块

moduleA.js

```js
let name='zhufeng';
const sum = function sum(x,y) {
    return x+y;
}
// 暴露API
module.exports = {
    sum
}
```

moduleB.js

```js
// require:导入需要依赖的模块
let moduleA = require('./moduleA');
const handle = function handle() {
    let result = moduleA.sum(10, 20);
    return result;
};
module.exports = {
    handle
};
```

main.js

```js
let moduleB = require('./moduleB');
console.log(moduleB.handle());

// ...

let moduleA = require('./moduleA');
console.log(moduleA.sum(100, 200));
```

#### CMD

> 由于`commonjs`规范只能在`node`环境下支持，而在浏览器下不支持。因此为了让`commonjs`这种开发方便且简单的模块化规范用于浏览器上，孕育而生的就是`CMD`规范，它将我们的`commonjs`规范搬到了浏览器端上，最为典型的代表就是`sea.js`，这个规范仅作为了解，因为其最后被`webpack`这类强大的打包工具所打败， `webpack`支持`commonjs/ES6Module`规范，经过webpack编译后，会把`commonjs/es6module`进行处理，处理后的代码浏览器直接支持，因此其退出了前端模块化 舞台。

#### ES6Module

> `commonjs`规范需要我们进行编译浏览器才能识别，在简单的开发中多少是有些繁琐的。有没有类似`commonjs`规范那样简单并不需要的webpack编译的模块化规范呢？由此`ES6module`规范就登上了前端模块化舞台。它可以直接在浏览器运行，但要在引入标签加上`type=module`：`<script  src="xxx" type="module"/>`
>
> * 以一个`js`文件为一个模块
>
> * 导入：`import xxx from '文件路径'`，并且导入要在一个模块的最顶端
> * 导出：`export default 导出内容`

moduleA.js

```js
const sum = (x, y) => {
    return x + y;
};
// ES6Module规范中，模块暴露API
export default sum;
```

moduleB.js

```js
// 导入模块中暴露的API
//   + @1 必须放在当前模块最开始的位置
//   + @2 如果是浏览器端直接使用ES6Module，则必须加 .js 后缀名「如果在webpack中使用，则无需增加」
import sum from './moduleA.js';

const handle = () => {
    return sum(10, 20);
};

export default handle;

// 导出一个
export default func;
import func from './xxx.js'
// 导出多个
export default {
    func,
    sum
    ...
}
import utils,{sum} from './xxx.js'
// 可以直接通过utils调用导入的模式内容，或者直接进行解构导入
utils.func()
sum()
// ----使用export，比如一个模块导出多个方法------- 
export const sum = function(){}
export const handle = function() {}
import * as utils from './xxx.js';
```

main.js

```js
import sum from './moduleA.js';
import handle from './moduleB.js';

console.log(sum(100, 200));
console.log(handle());
console.log('A');
```

### 闭包的进阶运用

#### 惰性函数

> 所谓的惰性函数就是“懒函数”，能做一次的事情就绝不做第二次，其经常运用于我们项目开发中，最常见的就是**角色权限校验**【函数中最开始是要进行角色校验之后才执行真正的逻辑代码时，我们就可以使用惰性函数实现来重构这个函数，让其之后的调用不用在进行权限验证，直接执行逻辑代码】，在例如下面的例子：**获取元素的样式**

获取元素的样式我们可以通过：`window.getComputedStyle(element)`其获取的元素样式都是浏览器计算过的，所以的样式都能拿到。不像我们`element.style.属性`这种方法只能获取的是元素的行内属性

* `element.style.属性名`：其只能获取到我们的`element`的行内样式，不是行内样式是获取不到的

> 何为行内样式，就是写在`HTML`结构上的`style`属性内的属性：`<div style="width:300px" />`，例如这里的`width:300px`，如果我们没设置`style="width:300px"`，则通过`element.style.width是获取不到的`

* `window.getComputedStyle(element)`：其获取元素被浏览器计算后的所有属性集合。但其在`ie678`不兼容，要使用`element.currentStyle`进行兼容ie678处理

这时我们来写一个获取元素属性的兼容函数：

```js
function getCss(element,attr) {
    if(window.getComputedStyle){
        return window.getComputedStyle(element)[attr];
    }
    return element.currentStyle[attr];
}
console.log(getCss(document.body, 'width'));
console.log(getCss(document.body, 'margin'));
console.log(getCss(document.body, 'padding'));
```

> 你会发现这个函数在每一次调用时都要进行一次兼容判断，这是完全没有必要的，因为我们的浏览器没有变，我们页面也没有刷新，只要第一次函数执行时进行兼容判断即可，之后的函数执行兼容判断就没有必要。这要怎么做呢？**这时我们就可以利用惰性函数的思想来完成**。重写为下面的代码

```js
function getCss(element,attr) {
    // 第一次执行，根据兼容情况，重构getCss函数
    if(window.getComputedStyle) {
        getCss = function(element,attr) {
            return window.getComputedStyle(element)[attr];
        }
    }else {
        getCss = function(element,attr) {
            return element.currentStyle[attr];
        }
    }
    // 第一次把重构的函数执行一次，获取对应的结果
    return getCss(element,attr);
}
console.log(getCss(document.body, 'width'));
console.log(getCss(document.body, 'margin'));
console.log(getCss(document.body, 'padding'));
```

#### 柯里化函数

> 柯里化函数：基于闭包的**保存**作用，我们是可以把一些信息预先存储起来（预处理），供其下级上下文中后续拿来使用，而这种预先存储/预先处理的机制就被称之为**函数柯里化思想**

我们从最为简单的面试题开始入手，揭开柯里化思想（函数）的神秘面纱

```js
// 写一个fn函数，让其执行输出res为6
let res = fn(1,2)(3)
console.log(res); // => 6

// 开始编写代码
// 我们利用了ES6的剩余运算符，将所有传入的实参转为数组集合
// 基于闭包我们将params保存在内存中: 
//  + fn(1,2)先将params->[1,2]预先存储起来，不做处理
//  + fn(1,2)(3) 之后 args -> [3] 在将之前上级上下文存储中的params值拿出来进行求和计算
const fn = function fn(...params){
    return function proxy(...args) {
        // 拼接两个数组 params -> [1,2,3]
        params = params.concat(args);
        return params.reduce((result,item)=>result+item);
    }
};
let res =fn(1,2)(3);
console.log(res); // =>6
```

当我们不知道一个函数要被调用多少次，每次传入的参数是多少个时，但又要我们输出结果，例如下面的面试题：编写`curring函数`

```js
let add = curring();
let res = add(1)(2)(3);
// +''是为让res转为字符串
console.log(res+''); // -> 6

add = curring();
res = add(1,2,3)(4);
console.log(res+''); // -> 10

add = curring();
res = add(1)(2)(3)(4)(5);
console.log(res+''); // -> 15 

```

开始编写：

```js
const curring = () => {
    // 在闭包中创建一个params集合，用来存储每一次add执行传递进来实参
    let params = [];
    const add = (...args) => {
       params = params.concat(args);
        // 因为不知道外界会把add函数调用多少次，我们直接返回一个创建函数的内存地址
        return add;
    }
    // 面试题最后是要console.log()或者alter都要将我们函数最后一次的add转化为字符串，这是我们可以利用对象转字符串步骤进行修改，并且利用闭包机制进行求和运算
    // + 若我们并不是要求在控制台显示或者alter，而是要得到其计算后的值的话，我们可以将内部通过对象转字符串的形式获取到对应的值，例如 add + '',add就会被转为字符串
    add[Symbol.toPrimitive] = () => {
        return params.reduce((result,item)=> result + item);
    }
    return add;
}
```

#### compose组合函数

> 在函数式编程当中有一个很重要的概念就是函数组合，实际上就是把处理数据的函数像管道一样连接起来，然后让数据穿过管道得到最终的结果，例如

```js
const add1 = x => x+1;
const mul3 = x => x*3;
const div2 = x => x/2;
div2(mul3(add1(add1(0)))); // => 3
```

> 像上面这种写法明显可读性太差了，我们可以构建一个`compose`函数，它可以接受任意多个函数作为参数（这些函数都只接受一个参数），然后`compose`函数返回的也是一个函数，达到以下结果

```js
const operate = compose(div2,mul3,add1,add1);
operate(0); // => 相当于 div2(mul3(add1(add1(0))));
operate(2); // => 相当于 div2(mul3(add1(add1(2))));
```

> 简而言之：`compose`可以把类似于`f(g(h(x)))`这种写法简化成`compose(f,g,h)(x)`的扁平化编程函数形式，大大提高了代码可读性和后期可维护性

开始编写compose函数

* 方案一：结合`reduceRight`写法，**推荐开发使用**，因为这种写法产生较少的不被释放的函数上下文

```js
const compose = function compose(...funcs){
    let len = funcs.length;
    if(len === 0) return x=>x;
    if(len === 1) return funcs[0];
    return function operate(x) {
        return funcs.reduceRight((result,func)=>{
            return func(result);
        },x);
    }
}

let result = compose(div2, mul3, add1)(0);
console.log(result);

result = compose()(0);
console.log(result);

result = compose(add1)(0);
console.log(result);
```

* 方案二：redux写法，但其创建了较多个不被释放的函数执行上下文

```js
function compose(...funcs) {
    if (funcs.length === 0) {
        return arg => arg;
    }
    if (funcs.length === 1) {
        return funcs[0];
    }
    return funcs.reduce((a, b) => {
        // @1 a:div2  b:mul3  return:AN1
        // @2 a:AN1 b:add1 return:AN2
        return x => {
            return a(b(x));
        };
        // AN2(0) -> AN1(add1(0)) -> div2(mul3(add1(0)))
    });
}
```

#### 防抖（debounce）

> 防抖`debounce`：在用户频繁触发某个行为的时候，我们只识别一次即可，有处理边界可言【开始边界：第一次点击触发；结束边界：等到最后一次触发】

```js
/*
 * 函数防抖处理
 *  @params
 *    func:最终要执行的函数「必传」
 *    wait:频繁操作的设定时间「默认300MS」
 *    immediate:设置边界「默认false:结束边界触发  true:开始边界触发」
 */
const debounce = function debounce(func, wait, immediate) {
    if (typeof func !== "function") throw new TypeError('func must be an function');
    if (typeof wait === "boolean") {
        immediate = wait;
        wait = 300;
    }
    if (typeof wait !== "number") wait = 300;
    if (typeof immediate !== "boolean") immediate = false;
    let timer;
    return function proxy(...params) {
        let runNow = !timer && immediate,
            self = this,
            result;
        if (timer) {
            clearTimeout(timer);
            timer = null;
        }
        timer = setTimeout(() => {
            if (timer) {
                clearTimeout(timer);
                timer = null;
            }
            // 结束边界触发
            if (!immediate) result = func.call(self, ...params);
        }, wait);
        // 开始边界触发
        if (runNow) result = func.call(self, ...params);
        return result;
    };
};

let submit = document.querySelector('#submit');
const queryDate = function(callback) {
    setTimeout(() => {
        callback('OK')
    }, 1000)
}

const handle = function(ev) {
    queryDate((result) => {
        console.log(result, ev, this)
    })
}
submit.onclick = debounce(handle, 300, true);
// debounce的作用是：让每次点击触发的proxy函数内部控制handle在300ms重复触发无效，并只触发最后一次或最开始的一次执行
```

#### 节流（throttle）

> 节流：在频繁操作的时候，我们能降低触发的频率，不存在边界，其要执行多次。

```js
/**
 * 
 * @param {*} func 最终要执行的函数【必传】
 * @param {*} wait 设定的触发频率【默认是300ms】
 * @returns 
 */
const throttle = function throttle(func, wait) {
    if (typeof func !== 'function') throw TypeError('func must a function');
    if (typeof wait !== 'number') wait = 300;
    let timer,
        pervious = 0;
    return function proxy(...params) {
        let now = +new Date(),
            remaining = wait - (now - pervious),
            self = this,
            result;
        if (remaining <= 0) {
            // 连续点击时，是不会走进来这里【除了第一次点击】
            if (timer) {
                clearTimeout(timer);
                timer = null;
            }
            // 间隔时间已经超过wait【包含第一次触发】，无需等待 立即执行
            result = func.call(self, ...params);
            pervious = now;
        } else if (!timer) {
            // 没设定时器才设定时器，设了就不用了
            timer = setTimeout(() => {
                if (timer) {
                    clearTimeout(timer);
                    timer = null;
                }
                result = func.call(self, ...params);
                pervious = +new Date();
            }, remaining)
        }
        return result;
    }
}

let submit = document.querySelector('#submit');
const queryDate = function(callback) {
    setTimeout(() => {
        callback('OK')
    }, 1000)
}
const handle2 = function(ev) {
    console.log('OK')
}
// 浏览器有一个最短的反应时间: [谷歌5-7MS IE:10MS-17MS]
// 默认情况下：每间隔5MS左右，只要滚动条在滚动，我们就会把handle2执行一次，【触发频率是5MS】 
window.onscroll = throttle(handle2, 1000);
// throttle的作用:让每隔5MS触发执行proxy的内部控制handle2每间隔300ms触发执行一次
```

### Jquery

对于JQ基本架子的认识：JQ为了兼容`浏览器/webview/webpack/node`下都能使用，做了一些特殊处理，下面那我们来分解一下：其的环境区分和多库冲突的处理

```js
/*
 * jQuery部分源码分析
 *   @1 整体架子「环境区分 & 全局暴露」
 *   @2 冲突处理 noConflict
 */
(function(global, factory) {
    // 2.
    // global:window OR global
    // factory:回调函数
    "use strict";
    if (typeof module === "object" && typeof module.exports === "object") {
        // 支持CommonJS模块规范{node || webpack}
        // webpack环境下： global.document成立说明global是window，因为window.document是存在的；如果是node环境，global存储的是global，global对象没有document这个属性的；
        module.exports = global.document ?
            factory(global, true) :
        function(w) {
            if (!w.document) {
                throw new Error("jQuery requires a window with a document");
            }
            return factory(w);
        };
    } else {
        // 浏览器(webview)
        factory(global);
    }
})(
    // 1.浏览器(webview)或者webpack环境下是window，node环境下运行是global
    typeof window !== "undefined" ? window : this,
    // 回调函数 在判断完环境之后执行的函数
    function(window, noGlobal) {
        // 3.
        // 如果是浏览器(webview)环境下运行JS，window===window && noGlobal===undefined
        // 如果是webpack环境下编译JS，window===window && noGlobal=true
        // ...
        var version = "3.6.0",
            jQuery = function(selector, context) {
                // return new jQuery.fn.init(selector, context);
            };

        // 多库共存，全局暴露名字冲突后的解决方案
        var _jQuery = window.jQuery,
            _$ = window.$;
        jQuery.noConflict = function(deep) {
            if (window.$ === jQuery) {
                window.$ = _$;
            }
            if (deep && window.jQuery === jQuery) {
                window.jQuery = _jQuery;
            }
            return jQuery;
        };

        // 暴露API
        if (typeof noGlobal === "undefined") {
            window.jQuery = window.$ = jQuery;
        }
        // 为了让上面你的module.exports导出
        return jQuery;
    }
);
```

基于对上面的理解，我们在开发自己的内部方法库时候也可以采取JQ思想，来兼容多环境下使用和多库冲突问题

```js
(function() {
    // 利用闭包的保护作用，把自己写的方法写在私有上下文中，防止全局变量污染
    let utils = {};

    // 多库共存问题
    let _$ = window.$;
    utils.noConflict = function noConflict() {
        if (window.$ === utils) {
            window.$ = _$;
        }
        return utils;
    }

    // 暴露API[需要支持所有：支持浏览器直接导入，webpack环境编译 、node环境执行]
    if (typeof window !== "undefined") {
        window.utils = utils
    }
    if (typeof module === "object" && typeof module.exports === "object") {
        module.exports = utils;
    }
})();
```

![Jquery](/medias/imges/js/jsUp/jquery.png)

### 对闭包的理解

```text
维度：基础知识、实战应用、高阶应用、源码阅读、插件组件封装「切忌背书式回答，需要润色一个故事」
    @1 STACK、HEAP、EC、VO、AO、GO、SCOPE、SCOPE-CHAIN
    @2 GC浏览器垃圾回收机制: 标记清除 && 引用计数
    @3 实际应用及优缺点
    @4 进阶专题:单例模式、惰性函数、柯理化函数、compose组合函数...
    @5 再次进阶:手撕源码「jQuery源码、Lodash源码、Redux源码...」
    @6 臻于大成:插件组件封装 
注：中国人儒家思想“温良恭俭让”；不要展开太细了「留一些悬念给面试官接着问即可」“引导面试官”；剧本精神；
-----
几个题，如果让面试官看到你的与众不同，对你很欣赏，其实面试就差不多了！！

参考答案:剧本：
你好，经理。我确实有对闭包进行一些较为深入的研究。这是因为有次在开发项目中出现了一个bug，之后通过排查发现原来是一个闭包问题，之后我为了弄清闭包的原理，通过阅读掘金、知乎、b站等资源去深入研究原理。在这我就说一下我认为的闭包是怎么样的，如果有不足或者不正确的地方，希望经理能多多指正。

我认为闭包其实就是函数运行产生的一种特殊机制。我们函数代码要在内存中执行的，内存有分为栈、堆内存。栈内存存储我们进栈执行的函数上下文，并且里面的变量对象存储着我们函数上下文的私有变量，堆内存存储我们的对象类型。一般情况下，函数执行完就要出栈释放，但如果这个函数上下文中的某个内容被外界所应用，那这个函数上下文就不被释放，这也是浏览器的垃圾回收机制导致。这个上下文不被释放，那之前存储在这个上下文的私有变量也不会被释放，之后这些不被释放的变量我们可以通过作用域链给下级上下文使用，这样整体构成了闭包机制。

我认为闭包在我们实际开发中有很多应用，比如说：元素循环事件绑定，我们要给一系列元素绑定点击事件的时候，并且要获取点击元素的对应的索引值的时候，我们可以利用闭包来将每个元素对应值存储起来。当然这种简单的需求不应该使用闭包，毕竟闭包是不被释放的，滥用了我们内存就开销大，我们会用的情况下也要注重内存的优化。这里可以完全使用事件委托的机制。

我还看过一些文章并且还挺感触的对闭包的理解，比如说早起我们模块化规范还没出来的时候，我们前辈为了解决命名冲突问题，提出了单例设计模式，这里的单例设计模式就有利用闭包对应变量的保存性和私有性。而且在开发中，我们经常使用的所谓惰性函数、柯里化函数、compose函数也是有闭包身影出现。我阅读过VUE2的源码，发现VUE2对闭包的使用处处可见，比如它在状态监听的时候就是利用播闭包存储对应属性的dev等属性。

而且我在组件封装方法库的时候，也是处处使用了闭包。
```

### 对面向对象思想的重新理解

何为面向对象？

> 面向对象，面向对象思想中的对象并不是指数据存储的结构。它是一种极为抽象的概念，你可能听说过:万物皆对象呢？我们先给面向对象思想下一个定义：**面向对象是一种非常方便的、强大的、用来构建和管理整个知识体系的思想。**

我们从客观事物出发来思考:

> 其实我们也可以将地球的生物划分管理思想理解为面向对象。在地球上所有的生物都是我们研究的对象，我们将其划分为动物类、植物类、微生物类；动物类又划分为：哺乳动物类等；哺乳动物在细分人类等。而人类中的每一个人都是这个类的实例。看这一层层的划分其实就是我们面向对象思想的体现：将所有研究对象根据特征来进行分类，从大类到小类，最后具体到每一个类的实例，构建出了一整套的管理划分体系。

我们在将面向对象与我们编程联系起来思考：

> 比如说我们`JS`的知识点是非常庞大的。每一个知识点都存在于对应所属的体系(类)中。我们学习可以从实例出发，一个实例搞懂了，那么对应所在类的其他实例（知识点）也差不多搞懂。这样子一层层的学习，最终才能较为全面的掌握js的整个体系。【但这还要持之以恒的学习精神】

我们在将面向对象具象化：那就只有两个

* 类
* 实例
  * 每个实例都有自己私有属性和方法【私有化】
  * 每个实例也都具备类赋予他们的公共属性和方法【公有化】

其实我们在编程中面向对象就是研究：类和实例。在JS中也给我们内置了很多的类，比如说`Number/String/Boolean/Symbol/BigInt/Object`...

### 构造函数的深入学习

#### 构造函数执行 VS 普通函数执行

> 上面所提到的`JS`其实内部已经给我们内置了很多内置类。但在平时开发中，JS内置类不能满足我们的需求，而我们也想按照面向对象的思想，构建一个自定义类，并且创建类的很多实例【好处：实例和实例之间既可以有独立的私有属性方法，也可以拥有类赋予他们的公共属性方法】
>
> 需要记住的一句话：**内置类/自定义类都是函数数据类型的值**，并且这种基于构造函数来创建类的设计模式，被称为：**构造函数设计模式**

构造函数执行的需要注意几点：

* 在初始化作用域链之前会创建一个空的实例对象
* 初始化this，会将this指向于第一步创建的空实例对象，之后的函数体`this.xxx`其实就是往实例对象中添加属性或方法
* 如果构造函数体代码执行完，没有写`return `（或者写了但返回的是原始值类型），则浏览器默认会把创建的实例对象返回；但如果`return`返回的是对象类型，则以自己返回的为主，不在是实例对象。

带参的构造函数执行和不带参的构造函数执行的注意点：

* 都是把函数执行了，只不过不带参执行无法传达实参值
* 运算优先级上有一些区别，无参数`new -> 19`，带参数`new -> 20`

```js
function Fn(x, y) {
    let sum = 10;
    this.total = x + y;
    this.say = function () {
        console.log(`我计算的和是:${this.total}`);
    };
}
let res = Fn(10, 20); //普通函数执行
let f1 = new Fn(10, 20); //构造函数执行
console.log(f1.sum);
console.log(f1.total);
console.log(f1.say === f2.say);
```

![构造函数执行VS普通函数执行](/medias/imges/js/jsUp/gzFunVSBfn.png)

#### prototype &&  \__proto__

**对应`prototype`我们记住下面这句话：**

> 大部分**函数数据类型**的值都具备`prototype`（原型/显示原型）属性，属性值本身是一个对象【浏览器会默认为其开辟一个堆内存，用来存储实例可调用的公共的属性和方法】，在浏览器默认开辟的这个堆内存中【原型对象】有一个默认属性`constructor`（构造函数/构造器），其属性值是当前函数/类本身

函数数据类型有以下几类：

* 普通函数（实名函数或者匿名函数）
* 箭头函数
* 构造函数/类【内置类/自定义类】
* 生成器函数 `Generator`
* ....

并不是所有的函数数据类型都有`prototype`这个私有属性，以下几类是不具备的：

* 箭头函数
* 基于ES6给对象某个成员赋值函数值的快捷操作

```js
const fn = () => {};
const obj = {
    fn(){}
}
// 这里的console.dir()得到的值内部是不含prototype
```

**对应\__prototype__我们需要记住以下这句话：**

> 每个**对象数据类型的值**都具备一个属性`__proto__`（原型链/隐式原型），属性值**指向自己所属类的原型`prototype`**

所谓对象数据类型的值是指：普通对象、特殊对象：数组、正则、日期....、函数对象、实例对象、构造函数.prototype ....

下面我们通过一套题将上面的知识点串联起来

```js
function Fn() {
    this.x = 100;
    this.y = 200;
    this.getX = function () {
        console.log(this.x);
    }
}
Fn.prototype.getX = function () {
    console.log(this.x);
};
Fn.prototype.getY = function () {
    console.log(this.y);
};
let f1 = new Fn;
let f2 = new Fn;
console.log(f1.getX === f2.getX);
console.log(f1.getY === f2.getY);
console.log(f1.__proto__.getY === Fn.prototype.getY);
console.log(f1.__proto__.getX === f2.getX);
console.log(f1.getX === Fn.prototype.getX);
console.log(f1.constructor);
console.log(Fn.prototype.__proto__.constructor);
f1.getX();
f1.__proto__.getX();
f2.getY();
Fn.prototype.getY();
```

![对于上面知识的串联](/medias/imges/js/jsUp/prototype.png)

对于为何`Object`内置类的`prototype`属性中的`__proto__`的值为`null`？

> 因为`Object`类是所有对象数据类型的基类，所有的数据对象都是它的实例，所以当`Object.prototype`上如果有`__proto__`也是指向自己，故`Object.prototype.__proto__==null`

对应`f1.getX`这种对对象成员访问，我们衍生出了**原型链机制**，以下是其运行的机制

> * 首先对自己的私有属性查找，如果不是私有的，继续下一步
> * 默认基于`__proto__`向所属类原型对象中去查找，如果还找不到，继续下一步
> * 基于原型对象中的`__proto__`继续向上查找，直到`Object.prototype`为止

对应`f1.hasOwnProperty('getY')`检测当前属性是否为对象的私有属性的运行机制如下

> * `f1`首先基于原型链查找机制，找到`Object.prototype.hasOwnProperty`这个方法，并且把这个方法执行
> * `hasOwnProperty`方法执行，传递实参`getY`，方法中的`this->f1`，内部则会：检测`this`(这里是`f1`)对象中是否拥有（实参，这里是`getY`）这个私有属性，由此返回布尔值

由于`hasOwnProperty`的运行机制，我们可以将上面的方法改为

```js
Object.prototype.hasOwnProperty.call(f1,'getY');
```

#### 内置类扩展

我们先来看看`Array`这个内置的实例调用方法是如何根据原型链机制查找

![array-class](/medias/imges/js/jsUp/array-class.png)

由上面的图示可以看出，js在内置类原型对象上暴露给我们很多的API，但实际中这些远远不够我们开发需求。当我们也想调用扩展方法和调用原型对象上的方法一样方便时，那我们可以往所对应类的原型对象上扩展方法。**所以说：基于内置类的原型扩展方法是为了实例可以直接调用我们自定义的方法，从而方便实现某些特殊功能。**

需要注意：

* 我们自定义的方法名字最好设置前缀，防止自己写的方法覆盖内部方法

这种方式的好处：

* 可以实现链式调用：执行一个方法，返回的结果可以继续调用所属类原型上的其他方法
* 这样操作简化了我们以往的调用方式，无需在传递实参，因为在扩展方法的内部`this`一般是我们所处理的数据值

知识扩展：创建值的两个种方案

* 字面量创建
* 基于构造函数创建

> 需要注意：
>
> * 对于原始值：这两种方法创建的结果是不一样的。字面量创建出来的就是**标准的原始值数据类型**，而基于构造函数创建出来的是**当前类的实例（对象数据类型）**
> * 对应对象数据类型：这两种方法除了语法不一样，其本质是一样的，结果也是一样的，创建的值都是对象数据类型

```js
let n = 10; // 字面量创建
let m = new Number(10); // 基于构造函数创建
console.log(typeof n); // number
console.log(typeof m) ; // object
```

js的装箱和拆箱操作

```js
console.log(n.toFixed(2)); // n 是原始值类型其会进行一种“装箱操作”,首先把原始值类型n变为对象实例类型的n【new Number(n)】
console.log(m.toFixed(2)); // m 是标准的实例对象，基于__proto__调用Number.prototype上的方法
```

```js
console.log(n + 10);  // n是原始值类型则直接参与运算
console.log(m + 10); //  m 是标准的实例对象要进行运算就要进行“拆箱操作” 首先会把m变为原始值类型（Symbol.toPrimitive/valueOf/toString/Number），再进行运算
```

前置知识铺垫完毕，我们则才开始这就开始对内置类上扩展我们方法：例如：我们增加一个数组去重函数

需要注意：在内置类扩展方法中，`this`都是当前类的实例，比如如果是`1.toFixed(2)`，在`toFixed`内部方法中的`this`会是：`1`被`Number`这个内置类包裹后的一个对象数据类型

```js
Array.prototype.myUnique = function myUnique() {
    let self = this;
    return Array.from(new Set(self));
}
let arr = [10, 20, 30, 10, 20, 30];
console.log(arr.myUnique().reverse()); // [30,20,10]
```

面试题：

```js
let n = 10;
let m = n.plus(10).minus(5);
console.log(m); // => 15 (10+10-5)

// 解题
(function() {
    const checkNum = num => {
        num = +num;
        return isNaN(num) ? 0 : num;
    }
    Number.prototype.plus = function(num) {
        num = checkNum(num);
        // console.log(this); // this 一定是实例对象10
        return this + 10;
    }
    Number.prototype.minus = function(num) {
        num = checkNum(num);
        return this - 5;
    }
})();
```

#### 检测公有和私有属性

检测私有属性我们可以有两种方法

* 调用`Object`类原型上的`Object.prototype.hasOwnProperty(key)`，key所要检测属性名
* 使用关键字`in`，但其检测当前对象是否有这个属性【私有和公有都行】，原理：先检测私有属性是否有，没有的话按照原型链一级一级的向上查找，直到`Object.prototype`为止，只要能找到结果就是`true`

```js
let obj = {
    name:'zs',
    age:12
}
// 'toString'方法是在Object.prototype上的方法
console.log(obj.hasOwnProperty('name')) // true
console.log(obj.hasOwnProperty('toString')) // false
console.log('name' in obj) // true 
console.log('toString' in obj) // true 
```

面试题：检测一个属性是否是对象的公有属性

* 思路一：是它的一个属性，但是还不是私有属性，则一定是公有属性

```js
Object.prototype.hasPubProperty = function hasPubProperty(attr) {
    let self = this;
    return (attr in self) && !self.hasOwnPrototype(attr)
}
console.log(obj.hsaPubProperty('name')) // false
console.log(obj.hsaPubproperty('toString')) // true
```

缺陷：这个思路是不完善的，比如公有属性和私有属性都有同一个属性名的函数时，结果就是不追却的

```js
let obj = {
    toString(){}
}
// 这里的toString不管在obj的私有属性或者公有属性都存在，使用上面的方法hasPubProperty('toString')的值直接是false，因为在私有属性查找的时候已经被判断出了结果
```

* 思路二：跳过私有的查找，直接到对象原型链上去查找，一直找到`Object.prototype`为止，当某一个原型对象上有这个属性，结果就返回`true`
  * 依靠`Object.getPrototypeOf(xxx)`可获取`xxx.__proto__`所指向的原型对象

```js
Object.prototype.hasPubProperty = function hasPubProperty(attr){
    let self =this,
        proto = Object.getPrototypeOf(self);
    while(proto) {
        if(proto.hasOwnPrototype(attr)) return true
        proto = Object.getPrototypeOf(proto)
    }
    return false
}

// 也可以直接采用in的原理
Object.prototype.hasPubProperty = function hasPubProperty() {
    let self =this,
        proto = Object.getPrototypeOf(self);
    return attr in proto;
} 
console.log(obj.hasPubProperty('name')) // false
console.log(obj.hasPubProperty('toString')) // true
```

#### 对比 for 循环和for in循环

遍历对象：我们使用`for in`循环，但是`for in`循环有很多问题

* 性能比较差，`for in`在迭代的时候，除了迭代所有的私有属性，其原型上的公有属性也会被迭代，当能被迭代出来的属性前提是：**可枚举的属性**(粗略的认为：内置属性一般都是不可枚举的，自定义的属性是可枚举的)，`for in`默认也会对不可枚举的属性进行迭代，只是不会给我们返回迭代结果，而这样在性能是大大折扣的，所以使用`for in`循环的时候，我们需要手动去除它对公有属性的迭代
* 有限迭代所有数字属性（从小到大，其次才是迭代非数字，与自己编写的属性顺序不完全一致，不能迭代`Symbol`类型的私有属性）

```js
let obj = {
  name:'zs',
  age:10,
  [Symbol('AA')]:10,
  10:10,
  0:0
}
// 手动去除对公有属性的遍历
for(let key in obj ){
  if(!obj.hasOwnProperty(key)) break
  console.log(key)
}
```

对比for 与for in

```js
let arr = new Array(999999).fill(0)
console.time('AA')
for(let i=0;i<arr.length;i++) {} // AA: 3.929931640625 ms
console.timeEnd('AA')
console.time('BB')
for(let key in arr) {
} // BB: 125.285888671875 ms
console.timeEnd('BB') 
// 对比之后可知,能不用for in 就不用,可以用 for / for of...来替代,即使用了for in 也要"if(!obj.hasOwnProperty(key)) break"加上这个判断手动去除迭代原型链上的属性
```

对于`for in`的缺点，我们可以结合`Object.keys(obj)`与`Object.getOwnPropertySymbols(obj)`来模拟修复

* `Object.keys(obj)`：获取obj所有非`Symbol`且可枚举的属性，也可使用`Object.getOwnPropertyNames(obj)`其是获取到obj对象所有非`Symbol`的私有属性【比如说数组的私有属性内值length，`Object.keys`是获取不到的，`Object.getOwnPropertyNames`是可以获取到的】，推荐使用`Object.keys`
* `Object.getOwnPropertySymbol(obj)`获取obj对象所有`Symbol`类型的私有属性

> 解决了`for in`会对公有属性进行迭代和不可迭代Symbol属性的问题

```js
let keys = Object.keys(obj);
if(!typeof Symbol !== 'undefined') keys=keys.concat(Object.getOwnPropertySymbols(obj))
keys.forEach(key=>{
  console.log('属性名',key,'属性值',obj[key])
})
```

#### 迭代器（Iterator）与FOR OF机制

> 迭代器（Iterator）是一种机制：为各种不同的数据结构提供了统一的访问机制，任何数据结构只要部署了`Iterator`接口，就可以完成遍历操作【for of机制】，依次处理该数据结构的所有成员，接口规范如下：
>
> * 拥有`next`方法用于依次遍历数据结构的成员
> * 每一次遍历返回的结果是一个对象，`{done:false,value:xxx}`
>   * done：记录是否遍历完成
>   * value：当前遍历的结果

需要注意：`js`中如果数据结构拥有这个接口规范，其原型`prototype`上就会有一个属性`Symbol.iterator`，那么基于这个属性，这个数据结构就可以被`for of`迭代了，具体哪些数据结构存在：

1. 数组、部分类数组：`arguments/NodeList/HTMLCollection...`
2. String
3. Set/Map
4. generator function 

注意：普通对象是不具备该接口，所以普通对象无法基于`for of`迭代

手写`Iterator`接口规范

```js
class Iterator {
    constructor(assemble) {
        this.assemble =assemble
        this.index = 0
    }
    next() {
        const assemble = this.assemble;
        if(this.index > assemble.length-1) {
            return {
                done:true,
                value:undefined
            }
        }
        return {
            done:false,
            value:this.assemble[this.index++];
        }
    }
}
const itor = new Iterator([10,20,30,40]);
console.log(itor.next()); //->{value:10,done:false}
console.log(itor.next()); //->{value:20,done:false}
console.log(itor.next()); //->{value:30,done:false}
console.log(itor.next()); //->{value:40,done:false}
console.log(itor.next()); //->{value:undefined,done:true}
```

**FOR OF机制**

> FOR OF 遍历的时候，会先调用对象原型链上的`[Symbol.iterator]`，获取一个迭代器对象`itor`，每一轮迭代中，都是执行`itor.next()`，并且把返回对象中的`value`值拿到，所以`FOR OF`在迭代中获取的值就是`value`，一旦返回对象中的`done`为`true`时，则终止迭代

用数组来演示FOR OF 机制

```js
Array.prototype[Symbol.Iterator] = function(){
    let assemble = this,
        index=0;
    return {
        next() {
            if(index>assemble.length - 1) {
                return {
                    done:true,
                    value:undefined
                }
            }
            return {
                done:false,
                value:assemble[index++]
            }
        }
    }
}
let arr = [10,20,30]
for(let value of arr) {
    console.log(value)
}
```

基于刚才所说，要想`FOR OF`进行迭代，则需要的是该数据结构提供了`Iterator`规范，但原生普通对象原型上是没有该规定的，所以无法使用`FOR OF`迭代，至此有面试就会问如何用`FOR OF `来迭代对象，下面我们往普通对象原型上扩展该接口规范

```js
Object.prototype[Symbol.Iterator] = function values() {
    let assemble = this,
        keys = Object.keys(assemble).concat(Object.getOwnPropertySymbols(assemble)),
        index=0;
    return {
        next() {
            if(index>keys.length-1) {
                return {
                    done:true,
                    value:undefined
                }
            }
            const key = keys[index++]
            return {
                done:false,
                value:assemble[key]
            }
        }
    }
}
let obj = {
    name: 'zhufeng',
    age: 12,
    0: 100,
    1: 200
};
for (let value of obj) {
    console.log(value); // zhufeng | 12 | 100 | 200
} 
```

真实项目中，我们对象结构往往会是一个特殊的类数组结构，那么我们可以直接将数组原型上的`Iterator`接口规范嫁接到类数组结构上接口

```js
let obj = {
    0: 10,
    1: 20,
    2: 30,
    3: 40,
    length: 4
};
obj[Symbol.iterator] = Array.prototype[Symbol.iterator];
for (let value of obj) {
    console.log(value);
}
```

JQ的做法也是，因为用JQ获取的元素集合是一个类数组集合

```js
if (typeof Symbol === "function") {
    jQuery.fn[Symbol.iterator] = arr[Symbol.iterator];
}
```

#### 对于ES6和ES5创建构造函数

```js
// 构造函数体
function Modal(x,y) {
    this.x = x;
    this.y = y;
}

// 原型对象上扩展的供其实例调取使用的公有属性付费, m.z/getX/getY
Modal.prototype.z= 10;
Modal.prototype.getX= function() {
    console.log(this.x)
}
Modal.prototype.getY = function() {
    console.log(this.y)
}
// 把函数当作一个对象,给其设置的静态私有属性方法[和实例没啥关系,Modal.n/setNumber....]
Modal.n = 200
Modal.setNumber = function(n) {
    this.n = n
}
let m = new Modal(10,20)
```

```js
class Modal {
    // 构造函数体:this.xx 给实例设置私有属性
    constructor(x,y) {
        this.x= x;
        this.y = y;
    }
    // 这样处理也是给实例设置的私有属性
    z=10
say = function(){}
hello = ()=>{}
// 向类的原型对象上扩充公有方法[无法扩充公有属性]
// @1 语法上只能这么写,无法扩充公有属性
// @2 所有扩充的方法是没有prototype的
// @3 并且这些方法是实例对象不可枚举的属性[ES5中,基于类.prototype公有方法是可枚举的]
getX() {
    console.log(this.x)
}
getY(){
    console.log(this.y)
}
// 把其当作对象,设置静态属性和方法
static n = 200
static sayName = () =>{}
}
// 给原型扩充属性
Modal.prototype.z = 10
let m = new Modal(10,20)
// Modal(10,20) // Uncaught TypeError: Class constructor Modal cannot be invoked without 'new',不允许当作普通函数执行,只能new执行
```

#### 函数的多种角色

* 函数（第一角色）
  * 普通函数：拥有私有上下文，AO，作用域，作用域链，形参赋值，变量提升，闭包....
  * 构造函数【类】：拥有普通函数执行的特点，另外还有类、实例、prototype、`__proto__`...
* 对象【静态属性和方法】

我们来搞搞函数与对象之间的爱恨情仇

```js
function Modal(x, y) {
    this.x = x;
    this.y = y;
}
Modal.prototype.getX = function () {
    console.log(this.x);
};
Modal.prototype.getY = function () {
    console.log(this.y);
};
Modal.n = 200;
Modal.setNumber = function (n) {
    this.n = n;
};
let m = new Modal(10, 20);
```

![函数多种角色](/medias/imges/js/jsUp/Func-Obj.png)

通过分析提取出来的知识点：

* `instanceof`：检测当前实例对象是否属于这个类，其是沿着该实例对象的原型链去查找，如果该类出现在该实例原型链中，则返回`true`
* `Function.prototype`是一个匿名`anonymouse`空函数`empty`，操作起来和其他原型对学校没有区别，而其他类的原型对象就是对象数据类型，所以出现以下情况不要意外

```js
Function.prototype();// 可行，其他类
Object.prototype(); // 报错
```

> 究竟`Function`和`Object`谁大呢，其实我认为他们是同等级的，我们要站在不同的角度来分析，当我们只针对对象类型来分析的话`Object`等级大于`Function`，而正对函数来分析的话`Function`大于`Object `

面试题：

```js
function Foo() {
  getName = function () {
      console.log(1);
  };
  return this;
}
Foo.getName = function () {
  console.log(2);
};
Foo.prototype.getName = function () {
  console.log(3);
};
var getName = function () {
  console.log(4);
};
function getName() {
  console.log(5);
}
Foo.getName();
getName();
Foo().getName();
getName();
new Foo.getName();
new Foo().getName();
new new Foo().getName();
```

![函数多种角色面试题](/medias/imges/js/jsUp/interview-11.png)

#### 手撕new源码

我们先来看`new`做了哪些事

* 把构造函数当做普通函数一样执行
* 创建当前类的一个空实例对象
  * 对象是空对象
  * 并且这个对象的隐式原型（`__proto__`）指向的是这个构造函数显式原型`prototype`
* 让方法执行的时候，方法中的`this`指向这个实例对象，``this.xxx`就是在给实例对象设置私有属性和方法
* 看方法的返回结果，如果没有返回值/或者返回值的是原始值，我们默认都返回实例对象，如果返回的是对象类型，则以用户自己手动返回的为主

注意：

* 传入的构造函数不能是`Symbol/BigInt`，也不能是没有`prototype`的箭头函数和ES6新语法赋值的函数
* `Object.create(proto)`是来解决`__proto__`在IE下不兼容的问题，该方法是创建一个空对象，并且把传入的proto作为新创建对象的原型，也就是`新对象.__proto__==proto`；并且`proto`只能是`null`和对象类型值，如果是`null`则创建的新对象不具备`__proto__`，它不是任何类的实例

```js
function Dog(name) {
    this.name = name;
}
Dog.prototype.bark = function () {
    console.log('wangwang');
};
Dog.prototype.sayName = function () {
    console.log('my name is ' + this.name);
};

function _new(Ctor, ...params) {
    let obj,
        result,
        proto = Ctor.prototype;
    if (Ctor === Symbol || Ctor === BigInt || !proto || typeof Ctor !== "function") throw new TypeError(`${Ctor} is not a constructor`);
    // Object.create(proto):创建一个空对象,并且把proto作为新创建对象的原型「新对象.__proto__===proto」；proto只能是null和对象类型值，如果是null则创建的新对象不具备__proto__，它不是任何类的实例；
    obj = Object.create(Ctor.prototype);
    result = Ctor.call(obj, ...params);
    if (result && /^(object|function)$/i.test(typeof result)) return result;
    return obj;
}
let sanmao = _new(Dog, '三毛');
sanmao.bark(); //=>"wangwang"
sanmao.sayName(); //=>"my name is 三毛"
console.log(sanmao instanceof Dog); //=>true
```

#### 鸭子类型

> 所谓的鸭子类型就是两个实例对象的结构是非常相似的，以至于能相互借用方法调用。比如类数组和数组，大部分的数组方法都能被类数组进行调用。借用的方式有一般两种
>
> * 把需要借用的方法当做值赋值给对象的私有属性
> * 直接改变借用方法中的this，使用`call/bind/apply`

```js

Array.prototype.push = function push(item){
  // this -> arr 向末尾新增一项
  this[this.length]=item;
  // 对于数组来说,新增一项后,它的length也会主动的累加"数组结构特点"
  this.length++;
  return this.length;
}
let obj = {
  2: 3, //1
  3: 4, //2
  length: 2, //4
  // 把需要借用的方法当做值赋值给对象的私有属性push，后期基于obj.push()其实就是调用Array.prototype.push方法执行「真实项目中，我们也会基于这种简单粗暴的模式，实现“某个实例借用其它类原型上的某些方法”」
  push: Array.prototype.push
};
obj.push(1)
// this:obj item:1 -> obj[2]=1 obj.length++
obj.push(2)
// this:obj item:2 -> obj[3]=2 obj.length++
console.log(obj)
```

```js
// 模拟一下数组的浅克隆
// Array.prototype.slice = function slice() {
//     let result = [];
//     for(let i=0;i<this.length;i++) {
//       result.push(this[i])
//     }
//     return result
// } 
let utils = (function(){
  function toArray(){
    return [].slice.call(arguments)
  } 
  return {
      toArray
  };
})();
let ary = utils.toArray(10,20,30); //=>[10,20,30]
console.log(ary)
ary = utils.toArray('A',10,20,30); //=>['A',10,20,30]
console.log(ary)
```

#### 原型重定向问题

我们再给自定义类增加原型的时候，在ES5是要用`xxx.prototype.xxx=xxx`这样写过于繁琐，但这在ES6的`class`语法中就解决了。当然我们想不使用`class`语法来做到批量给某个原型增加方法，可以使用原型重定向

可以采用`Object.assign(A,B)`来进行指向，我们先来了解`Object.assign()`的语法

```js
// 用B中的内容替换A [B有的，A没有，把B的东西放到A里；B没有的A有，则不做处理，以A为主；B和A都有的，以B为主]，返回的结果是A这个内存地址[对象]
Object.assign(A,B); 
// 最后返回的是一个新的堆内存，和A/B都没关系，其是先拿A和{}合并，再拿B和{}合并
// 并且Object.assign合并的时候对于不可枚举的属性是处理不了的
Object.assign({},A,B)
```

例如:`constructor`是不可枚举属性，所以我们原型重定向之后要手动设置一个即可

```js
function Fn() {
    this.x = 100;
    this.y = 200;
}
Fn.prototype.sum = function sum() {};
Fn.prototype = Object.assign({}, Fn.prototype, {
    constructor: Fn,
    getX() {},
    getY() {}
});

```

面试题：

```js
function Fn() {
    let a = 1;
    this.a = a;
}
Fn.prototype.say = function () {
    this.a = 2;
}
Fn.prototype = new Fn;
let f1 = new Fn;
Fn.prototype.b = function () {
    this.a = 3;
};
console.log(f1.a);
console.log(f1.prototype);
console.log(f1.b);
console.log(f1.hasOwnProperty('b'));
console.log('b' in f1);
console.log(f1.constructor == Fn);
```

![stack-heap](/medias/imges/js/jsUp/prototype-renew.png)

#### 继承

> 面向对象中：类有三大特点  封装、多态、继承
>
> * 封装：把实现某个功能的代码进行封装处理，后期想实现中国功能，直接调用函数执行即可，体现低耦合、高内聚的特点
> * 多态：重载【方法名相同，参数类型或者个数不同，这样会认为是多个方法，但在js中是不存在函数重载概念，java则可以】或者子类重写父类的方法也算
> * 继承：子类继承父类的方法，子类的实例即拥有子类赋予的私有/公有属性方法，也具备父类赋予的私有/公有属性方法

继承可分为：原型继承、call继承、寄生组合继承

* 原型继承

> 特点：和传统后台语言中的继承不一样【后台：子类继承父类，会把父类的方法COPY一份给子类】，而`js`没有把父类的方法`copy`一份给子类，而是建立子类和父类之间的原型链指向，后期子类实例访问父类中提供的属性和方法，也是基于`__proto__`原理链一层层查找的。

```js
function Parent() {
    this.x=100;
}
Parent.prototype.getX=function() {}

function Child() {
    this.y=200;
}
Child.prototype = new Parent;
Child.prototype.getY=function() {}

let ch = new Child;
console.dir(ch);
```

出现的问题：

1. 父类想要赋予其实例私有属性的`x`，此时变为了子类实例`ch`的公有属性

2. 子类实例可以基于原型链修改父类原型上的方法，这样会对父类的其他实例也产生影响

* call继承

> call继承：把父类当做普通方法执行，让方法中的`THIS`是子类的实例，这样可以达到让父类中赋予其实例私有的属性，最后也变成子类实例私有的属性

```js
function Parent() {
    this.x=100;
}
Parent.prototype.getX=function(){}

function Child() {
    Parent.call(this);
    this.y=200;
}
Child.prototype.getY = function() {}

let ch = new Child;
console.dir(ch);
```

出现的问题：

1. `call`继承之后，虽然解决了将父类的私有属性也加到子类的私有属性中，但存在的问题是子类原型链指向并没有发生改变，导致子类不同通过原型链找到父类的原型上的方法，也就没法继承到父类的公有属性和方法

* 寄生组合式继承【推荐使用】

> 寄生组合式继承：就是把`call`继承和**变形的原型链继承**（`child.prototype=Object.create(Parent.prototype)`）混合在一起，就实现了寄生组合式继承

```js
function Parent(){
    this.x=100;
}
Parent.prototype.getX = function(){}

function Child() {
    Parent.call(this);
    this.y = 200;
}
// Object.create()还是会丢失constructor属性
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor=Child
Child.prototype.getY = function() {}

let ch = new Child;
console.dir(ch);
```

* ES6中类的继承`extend`(非常类似寄生组合继承)

```js
class Parent {
    constructor() {
        this.x = 100;
    }
    getX() {}
}

class Child extends Parent {
    /* constructor() {
        // 一但使用extends，并且编写了constructor，必须在constructor函数第一行写上 super()
        //   从原理上类似call继承  super() ==> Parent.call(this)
        super();
        this.y = 200;
    } */
    y = 200;
    getY() {}
}

let ch = new Child;
console.dir(ch);
```

![组合继承](/medias/imges/js/jsUp/inherit.png)

#### 工厂设计模式

> 把一个构造函数执行，最后还可以获取到当前构造函数的实例

```js
function Fn() {
    return new Init()
}
Fn.prototype = {
    constructor:Fn
}
const init = function init() {
    this.xxx = 'xxx'
}
init.prototype = Fn.prototype;
Fn()
```

> 生成器本身具备这个特点：当作普通函数执行，返回一个迭代器对象`itor`，并且`itor.__proto__===fn.prototype`

```js
function* fn() {}
fn.prototype = {
    constructor: fn,
    name: 'fn'
};
let itor = fn();
console.log(itor); 
```

#### 编写queryURLParams方法

```js
String.prototype.queryURLParams = function queryURLParams(attr) {
    // this -> url
    let self = this,
        link = document.createElement('a'),
        obj = {};
    link.href = self;
    let {
        search,
        hash
    } = link;
    if (hash) obj['_HASH'] = hash.substring(1);
    if (search) {
        search = search.substring(1).split('&');
        search.forEach(item => {
            let [key, value] = item.split('=');
            obj[key] = value;
        });
    }
    return typeof attr !== "undefined" ? obj[attr] : obj;
};

String.prototype.queryURLParams = function queryURLParams(attr) {
    // this -> url
    let self = this,
        obj = {};
    self.replace(/#([^?=&#]+)/g, (_, $1) => obj['_HASH'] = $1);
    self.replace(/([^?=&#]+)=([^?=&#]+)/g, (_, $1, $2) => obj[$1] = $2);
    return typeof attr !== "undefined" ? obj[attr] : obj;
};

let url = "http://www.zhufengpeixun.cn/?lx=1&from=wx#video";
console.log(url.queryURLParams("from")); //=>"wx"
console.log(url.queryURLParams("_HASH")); //=>"video"
console.log(url.queryURLParams());  
```

### ajax & axios & fetch

ajax、axios、$.ajax、fetch

共同点：都是基于`TCP(HTTP/HTTPS)`从服务器获取数据

区别：

* ajax、axios、$.ajax 它们的核心：`XMLHttpRequest`
  * ajax 是原生操作
  * axios是基于`promise`封装的ajax库【推荐使用】
  * `$.ajax`是基于回调函数的方式封装的`ajax`库
* `fetch`：是ES6新增的API，和`XMLHttpRequest`没有关系，这是浏览器新提供的一种和服务器通信的机制，而且默认就是基于`promise`管理的，但是兼容性较差，除了`EDGE`新版本，其余的`IE`浏览器都不支持

### Promise的恶心题

```js
Promise.resolve().then(() => {
    console.log(0);
    // return 4;
    return Promise.resolve(4); //如果返回的是一个Promise实例，需要等待Promise是成功的，才能让“异步微任务2”执行；但是如果返回的Promise实例立即是成功的，也不会让“异步微任务2”变为立即可执行的，而是需要“递归”再去验证一次...  「推测：内置Promise中多等了两步」这里相当于 x.then(4).then(4)
}).then((res) => { //异步微任务2
    console.log(res);
});

Promise.resolve().then(() => {
    console.log(1);
}).then(() => {
    console.log(2);
}).then(() => {
    console.log(3);
}).then(() => {
    console.log(5);
}).then(() => {
    console.log(6);
});
```

![解析](/medias/imges/js/jsUp/then内有Promise的解析.png)

如果`await`后面跟着是`promise`，则会有两种情况，老版浏览器或node环境，会解析成`x.then(xx).then(xx).then(7)`两次then，而新版的则只会解析成一个then，`x.then(7)`

```js
setTimeout(function() {
    console.log(1) 
}, 0);

new Promise(function(resolve, reject) {
    console.log(2); 
    resolve();
}).then(function() {
    console.log(3) 
}).then(function() {
    console.log(4)  
}).then(function() {
    console.log('ssss')
}).then(function() {
    console.log('aaa')
});
async function test() {
    console.log(6) 
    await Promise.resolve(xx) // x.then(xx).then(xx).then(7) -> x.then(7)
    console.log(7) 
}
console.log(5); 
test()

// 新版的顺序 2 5 6 3 7 4 sss aaa  1 
// 老版的顺序 2 5 6 3 4 ssss 7 aaa 1
```

如果await后面的是一个async函数，并且函数内部返回值也是个promise，则：解析成x.then().then().then(xx)，新版老版的解析规则都一样，如果不是async函数，则老版解析成`x.then().then().then(xxx)`，新版解析成`x.then(xxx)`

```js
async function async1() {
    console.log('async1 start');
    await async2(); // x.then().then().then(async1 end) |  x.then(async1 end)
    console.log('async1 end');
}

async function async2() {
    console.log('async2 start');
    return new Promise((resolve, reject) => {
        resolve();
        console.log('async2 promise');
    })
}
console.log('script start');
setTimeout(function() {
    console.log('setTimeout');
}, 0);
async1();
new Promise(function(resolve) {
    console.log('promise1');
    resolve();
}).then(function() {
    console.log('promise2');
}).then(function() {
    console.log('promise3');
});
console.log('script end');
// 老版/新版加了async函数：script start  | 'async1 start' | async2 start |  async2 promise |  promise1 | script end  | promise2 | promise3 | async1 end | setTimeout
// 新版去掉async或者不返回promise：script start  | 'async1 start' |  async2 start | async2 promise | promise1 | script end| async1 end  | promise2 |promise3 | setTimeout
```



### 对元素事件的重新理解

> 这里我们要分清楚**什么是事件？&& 什么是事件绑定**，举个例子

```js
document.body.onclick = function() {}
```

我们对上面的例子，大部分人常说：我们给body绑定了一个点击事件；其实这是不正确的，应该说是：

> 我们给`body`的点击事件行为绑定了方法，其中元素的事件行为是**天生（浏览器赋予）**存在的，并不是说我们让其拥有的。这叫好比：你打我这个动作，我没绑定我被打后的事件行为触发的方法，那就什么也不会发生，但如果我们绑定了我被打后的事件行为触发的方法（比如说我叫一声），那么我被打后就会叫一声。所以说

* 什么是事件？

> 事件是浏览器赋予元素的默认行为，也可以理解为事件是天生具备的，不论我们是否为其绑定方法；当某些行为触发的时候，其实相关的事件都会被触发执行，只不过我们没给其事件行为绑定方法，所以才好像什么也没发生。

浏览器的事件：https://developer.mozilla.org/zh-CN/docs/Web/Events

我们将经常使用的事件做一些汇总：

鼠标事件：

| 事件名      | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| click       | 点击事件（PC频繁点击N次，触发N次点击事件），在移动端中称为单击事件：300ms内没有发生第二次点击操作则算单击事件行为，如果出发了第二次操作，则属于双击行为。所以在移动端中click事件行为方法在执行有300ms延迟 |
| dblclick    | 双击事件                                                     |
| contextmenu | 鼠标右键点击触发                                             |
| mousedown   | 鼠标按下                                                     |
| mouseup     | 鼠标抬起                                                     |
| mousemove   | 鼠标移动                                                     |
| mouseover   | 鼠标经过                                                     |
| mouseout    | 鼠标划出                                                     |
| mouseleave  | 鼠标离开                                                     |
| mouseenter  | 鼠标进入                                                     |
| wheel       | 鼠标滚轮滚动                                                 |

键盘事件

| 事件名   | 描述                                  |
| -------- | ------------------------------------- |
| keydown  | 键盘按下                              |
| keyup    | 键盘抬起                              |
| keypress | 长按（除了`Shift/Fn/Capslock`键之外） |

手指事件：touch event 单手指模型

| 事件名     | 描述     |
| ---------- | -------- |
| touchstart | 手指按下 |
| touchmove  | 手指移动 |
| touchend   | 手指松开 |

表单事件

| 事件名 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| focus  | 获取焦点                                                     |
| blur   | 失去焦点                                                     |
| submit | 表单提交（前提：表单元素都包含在form中，并且点击的按钮时`submit`） |
| reset  | 表单重置（前提：表单元素都包含在form中，并且点击的按钮时`reset`） |
| select | 下拉框内容选中                                               |
| change | 内容改变                                                     |
| input  | 移动端经常使用，监控文本框中的内容随着输入的内容而出发       |

资源事件

| 事件名       | 描述                                                 |
| ------------ | ---------------------------------------------------- |
| load         | 加载成功（window.load / img.load）                   |
| error        | 加载失败                                             |
| beforeunload | 资源卸载之前（window.beforeunload 页面关闭之前触发） |

css过渡事件

| 事件名         | 描述                  |
| -------------- | --------------------- |
| transitionend  | transtion 动画结束    |
| transtionstart | transtion 动画开始    |
| transtionrun   | transition 动画进行中 |

视图事件

| 事件名 | 描述                   |
| ------ | ---------------------- |
| resize | 元素（浏览器）大小改变 |
| scroll | 滚动条滚动             |

* 什么是事件绑定？

> 给元素的默认行为绑定方法，这样可以在行为触发的时候执行这个方法。事件绑定有两种方法：`DOM0事件绑定`与`DOM2事件绑定`

**DOM0事件绑定：**

语法：[元素].on[事件] = [函数]  

其的事件移除方式：将事件行为的触发函数赋值为`null或其他非函数值即可` 

```js
// 绑定
document.body.onclick = function(){}
// 移除
document.body.onclick = null
```

原理及优缺点：

> 原理：每一个DOM元素对象上都有很多形式为`onxxx`的的私有属性，我们使用DOM0的绑定方式就是往对应的私有属性赋值。
>
> 缺点：
>
> * 只能是DOM上存在的私有属性才能绑定事件行为触发方法，而DOM上却不整体包含浏览器赋予源的事件行为，则很多方法不能采用`DOM0`的绑定方式。例如：DOM元素`DCMContentLoaded`不存在这个私有属性，则无法基于DOM0实现事件绑定
> * 只能给当前元素的某个事件行为绑定一个方法，绑定多个方法时，最后一个覆盖前面所有的
>
> 优点：
>
> * 执行效率快，而且开发者使用起来方便

**DOM2事件绑定：**

语法：[元素].addEventListener([事件],[方法].[捕获/冒泡])

移除：[元素].removeEventListener([事件],[方法],[捕获/冒泡])

```js
// 事件绑定
document.body.addEventListener('click',fn1,false);
// 移除:参数要和绑定的一致才行
document.body.removeEventListener('click',fn1,false);
```

原理及其优缺点

> 原理：每一个DOM元素都会基于原型链的查找机制，查找到`EventTarget.prototype`上的`addEventListener/removeEventListener`等方法，基于这些方法实现事件绑定和移除；而且DOM2事件绑定采用的是事件池机制
>
> 缺点：执行效率稍比DOM0绑定差点，但仅仅只是差一点
>
> 优点：
>
> * 绑定方法一般不是匿名函数，因为在解绑事件的时候我们也要知道移除是哪个事件。
> * 凡是浏览器提供的事件行为，都可以基于这种模式完成时间的绑定和移除（例如：`window.onDOMContentLoaded`是不行的，因为window元素没有这个私有属性），但我们可以通过`window.addEventListener('DOMContentLoaded',func)`绑定
> * 可以给当前元素的某个事件行为绑定多个**不同**的方法（进行事件池），这样当事件行为触发，就会从事件池中依次（按照绑定顺序）取出对应的方法然后执行

#### 事件对象的理解

什么是事件对象？

> 其含义：存储当前事件操作及触发的相关信息的集合（浏览器自身记录当前这次操作的信息，和哪个函数触发无关），常用的事件对象可分为：鼠标事件对象（MouseEvent）、键盘事件对象（KeyboardEvent）、手指事件对象（TouchEvent）、普通事件对象（Event）

* 鼠标事件对象：MouseEvent，需要记住的是以下常用`mouseEvent`事件对象的属性和方法
  * `clientX/clientY`：鼠标触发点距离当前窗口的`X/Y`轴的坐标
  * `pageX/pageY`：鼠标触发点距离`body`的`X/Y`轴的坐标
  * `type`：事件类型
  * `target/srcElement`：获取当前事件源（当前操作的元素）
  * path：事件传播的途径
  * `ev.preventDefault()` / `ev.returnValue=false(兼容IE678)`：阻止默认行为
  * `ev.stopPropagation` /  `ev.cancelBubble=true(兼容IE678)`：阻止冒泡行为
  * ......

```js
document.body.onclick = function(ev){
    console.log(ev); // MouseEvent
}
```

* 键盘事件对象：KeyboardEvent，需要记住以下常用的属性和方法
  * `key / code`：存储按键名字
  * `which / keyCode`：获取按键的键盘码
    * 方向键：左:37 上38 右39 下40
    * Space：32
    * Backspace：8
    * Del：6  ----> MAC电脑没有Backspace,delete是8
    * Enter：13
    * Shift：16
    * Ctrl：17
    * Alt：18
    * ....
  * `altKey`：是否按下`Alt`键（为了组合按键）
  * `ctrlKey`：是否按下了`Ctrl`键
  * `shiftKey`：是否按下了`shift`键

```js
document.onkeydown = function(ev) {
    console.log(ev); 
}
```

* 手指事件对象：TouchEvent（移动端使用），需要记住的是以下常用`mouseEvent`事件对象的属性和方法
  * `changedTouch/targetTouches/touches`：都是用来记录手指的信息的，平时常用的是`changedTouches`。手指按下、移动、离开屏幕`changedTouches`都会存储对应的手指信息，哪怕离开屏幕后，存储的是最后一次手指在屏幕中的信息；而`touches`在手指离开屏幕后，就没有任何信息了；它们获取的结果都是一个`TouchList`集合：记录着每一根手指的信息
    * `ev.changedTouches[0]`：第一根手指信息，里面有`clientX/clientY/pageX/pageY/...`

```js
body.addEventListener('touchstart', function(ev) {
    console.log(ev)
})
```

* 普通对象：Event

```js
window.onload = function(ev) {
    console.log(ev)
}
```

对事件对象的应用

* 阻止浏览器自带的右键菜单

```js
// 禁用右键菜单 (后续可能要改为自己的右键菜单)
window.oncontextmenu = function(ev) {
    ev.preventDefault(); // 阻止默认行为 禁用右键自带菜单
    let contextMenu = document.querySelector('.contextmenu');
    // 没有右键菜单则创建一个
    if (!contextMenu) {
        contextMenu = document.createElement('div')
        contextMenu.className = 'contextmenu'
        contextMenu.innerHTML = `  <ul>
<li>跳转首页</li>
<li>进入到详情</li>
<li>逗你玩</li>
</ul>`;
        document.body.appendChild(contextMenu)
    }
    // 控制菜单位置
    contextMenu.style.left = ev.clientX + 10 + 'px';
    contextMenu.style.top = ev.clientY + 10 + 'px';
}

// 点击空白元素以及其它元素（不包含contextmenu及其内容）让右键菜单消失
window.onclick = function(ev) {

    let target = ev.target,
        targetTag = target.tagName;
    if (targetTag === 'LI') {
        target = target.parentNode;
        targetTag = target.tagName;
    }
    if (targetTag === "UL" && target.parentNode.className === "contextmenu") {
        return;
    }
    let contextMenu = document.querySelector('.contextmenu');
    if (contextMenu) {
        document.body.removeChild(contextMenu);
    }
}
```

* 阻止`<a>`标签的默认行为：页面跳转，锚点定位（定位到当前页面指定的ID的盒子位置，URL地址会加入HASH值）

```html
<a href="http://www.baidu.com" id="link">哈哈</a>

<!-方案一-!>
<a href="javascript:;" id="link">哈哈</a>

<!-方案二-!>
<script>
 // 点击A标签 先触发器点击事件行为 然后才默认跳转
    link.onclick = function(ev) {
        ev.preventDefault();
        // or
        // return false;

    }
</script>
```

#### 事件传播机制

当我们触发某一个元素的事件行为的时候，浏览器其不只是简单的触发你点击的元素事件行为，而是做了一系列的流程，我们称之为**事件传播机制。**分为三个阶段：

* 阶段一：捕获阶段`CAPTURING_PHASE`：从最外层元素一直往里层逐级查找，直到找到事件源位置，目的是为冒泡阶段的传播提供路径，也就是我们`ev.path`存放的路径就是捕获阶段收集的

* 阶段二：目标阶段`AT_TARGET`：把当前事件源相关事件行为触发
* 阶段三：冒泡阶段`BUBBLING_PHASE`：按照捕获阶段收集的传播路径，不仅仅当前事件源的相关事件行为被触发，而且从内到外，其祖先所有元素的相关事件行为也都会被触发（如果做了事件绑定，绑定的方法也会被执行）

注意：

* 当元素的事件绑定是采用`DOM0`方式，则事件绑定的方法都是在目标阶段/冒泡阶段触发的，其不能控制事件被触发的简单
* 而采用`DOM2`方式事件绑定，`元素.addEventListener(事件名,执行函数,控制冒泡还是捕获阶段执行)`，最后一个参数控制事件时冒泡阶段执行还是捕获阶段执行，默认是`false`为冒泡阶段执行，当设置为`true`则为捕获阶段执行，个人认为这在我们日常开发中并无实际意义。

```html
<style>
    .box {
        height: 500px;
        width: 500px;
        margin: auto;
        padding-top: 20px;
        box-sizing: border-box;
        background: lightblue;
    }

    .out {
        height: 300px;
        width: 300px;
        padding-top: 20px;
        box-sizing: border-box;
        margin: auto;
        background: lightcoral;
    }

    .inner {
        height: 100px;
        width: 100px;
        margin: auto;
        background: lightgreen;
    }
</style>

<div class="box">
    <div class="out">
        <div class="inner"></div>
    </div>
</div>

<script>
    // DOM0事件绑定方法都是在目标阶段/冒泡阶段触发的
    let boxDom = document.querySelector('.box');
    let outDom = document.querySelector('.out');
    let innerDom = document.querySelector('.inner');

    innerDom.onclick = function(ev) {
        ev.stopPropagation();
        console.log('INNER===>', ev)
    };
    outDom.onclick = function(ev) {
        ev.stopPropagation();
        console.log('OUR===>', ev)
    };
    boxDom.onclick = function(ev) {
        console.log('BOX===>', ev)
    }
</script>

<script>
    // DOM2事件绑定可以控制事件在捕获还是冒泡阶段执行
    boxDom.addEventListener('click', function(ev) {
        console.log('BOX===>', ev)
    }, false);
</script>
```

![事件传播机制](/medias/imges/js/jsUp/thing-cb.png)

由于存在事件传播机制，由此衍生出来了我们常用的事件委托/事件代理。

事件委托/事件代理：核心就是利用**事件传播机制**，我们可以把一个容器A中所有后代元素的某个事件行为E触发要做的操作，委托给容器A的事件行为E，这样后期触发了容器A后代元素的事件行为E，就会传播到容器A的事件行为E上，并且执行我们给容器A的事件行为E绑定的方法，之后我们可以根据触发源（`ev.target -> ev.target.className`）的不同而做不同的事情。好处：

* 性能高：比普通给每个元素都绑定事件行为方法的效率高出`60%`
* 可以操作动态增加的元素

```js
document.body.onclick = function(ev) {
    let target = ev.target,
        targetClass = target.className;
    if (targetClass === 'inner') {
        console.log('inner');
        return;
    }
    if (targetClass === 'out') {
        console.log('out');
        return;
    }
    if (targetClass === 'box') {
        console.log('box');
        return;
    }
}
```

#### 分辨mouse系列

在元素`mouse`事件行为中，个人认为有两组较为难以分辨的事件：`mouseover(划入)/mouseout(划出)`、`mouseenter(进入)/mouseleave(离开)`

```html
<style>
    .out {
        height: 300px;
        width: 300px;
        padding-top: 50px;
        box-sizing: border-box;
        margin: 50px auto;
        background: lightcoral;
    }

    .inner {
        height: 100px;
        width: 100px;
        margin: auto;
        background: lightgreen;
    }
</style>

<div class="out">
    <div class="inner"></div>
</div>

<script>
    // 鼠标划入划出
    outDom.onmouseover = function() {
        console.log('OUT OVER');
    }
    outDom.onmouseout = function() {
        console.log('OUT OUT')
    }
    innerDom.onmouseover = function() {
        console.log('INNER OVER')
    }
    innerDom.onmouseout = function() {
        console.log('INNER OUT')
    }
</script>

<script>
    // 鼠标进入和离开
    outDom.onmouseenter = function() {
        console.log('OUT ENTER');
    }
    outDom.onmouseleave = function() {
        console.log('OUT LEAVE')
    }
    innerDom.onmouseenter = function() {
        console.log('INNER ENTER')
    }
    innerDom.onmouseleave = function() {
        console.log('INNER LEAVE')
    }
</script>
```

![mouse两系列分辨](/medias/imges/js/jsUp/mouse-list.png)

* `mouseover/mouseout`【划入/划出】：存在事件的冒泡传播机制，并且他们还有一种较为恶心的触发机制，也叫不正常的触发。
  * 从父元素“划入”到子元素，属于“划出”父元素，“划入”子元素
  * 从子元素“划入”到父元素，属于“划出”子元素，“划入”父元素

> 可见，他们其忽略了父子的层级关系，一切按照鼠标在谁上面来决定触发事件

* `mouseenter/mouseleave`【进入/离开】：默认阻止了时间的冒泡机制，其的事件触发机制就可以完全符合我们现实世界的进入离开理解。
  * 从父元素“进入”子元素：属于“进入”子元素，但是并没有离开父元素，所以父元素`leave`不触发
  * 从子元素“进入”父元素，属于“离开”子元素，但是因为一直处于父元素中，所以也不算重新进入父元素，由此父元素的`enter`也不会触发

> 可见，他们是受到元素的层级关系的影响，也符合我们开发中的逻辑，由此我们开发一般都用`enter/leave`而少使用甚至不使用`over/out`

#### 基于事件学习的一些案例

**放大镜**

```html
<style>
    .box {
        width: 500px;
        margin: 100px auto;
        display: flex;
    }

    .box .abbre {
        width: 200px;
        height: 200px;
        position: relative;
    }

    .box .abbre img {
        width: 100%;
        height: 100%;
        display: block;
    }

    .box .abbre .mark {
        display: none;
        position: absolute;
        left: 0;
        top: 0;
        height: 80px;
        width: 80px;
        background: rgba(255, 0, 0, .4);
        cursor: move;
    }

    .box .detail {
        display: none;
        width: 300px;
        height: 300px;
        overflow: hidden;
        position: relative;
    }

    .box .detail img {
        position: absolute;
        left: 0;
        top: 0;
    }
</style>

<body>
    <section class="box">
        <div class="abbre">
            <div class="mark"></div>
            <img src="./images/1.jpg" alt="">
        </div>
        <div class="detail">
            <img src="./images/2.jpg" alt="">
        </div>
    </section>
</body>

<script>
    let boxDom = document.querySelector('.box');
    let abbreDom = document.querySelector('.abbre');
    let markDom = abbreDom.querySelector('.mark');
    let detailDom = document.querySelector('.detail');
    let detailImgDom = detailDom.querySelector('img');
    // 动态计算出大图的大小
    let abbreW = parseInt(window.getComputedStyle(abbreDom).width),
        abbreH = parseInt(window.getComputedStyle(abbreDom).width),
        // 得到盒子对应body的偏移量
        abbreOT = abbreDom.offsetTop,
        abbreOL = abbreDom.offsetLeft,
        markW = parseInt(window.getComputedStyle(markDom).width),
        markH = parseInt(window.getComputedStyle(markDom).height),
        detailW = parseInt(window.getComputedStyle(detailDom).width),
        detailH = parseInt(window.getComputedStyle(detailDom).width),
        detailIMGW = 0,
        detailIMGH = 0;
    detailIMGW = detailW / (markW / abbreW);
    detailIMGH = detailH / (markH / abbreH);

    detailImgDom.style.width = detailIMGW;
    detailImgDom.style.height = detailH;

    // 计算MARK/大图移动位置
    const computed = function computed(ev) {
        let curL = ev.pageX - abbreOL - markW / 2,
            curT = ev.pageY - abbreOT - markH / 2;
        // 边界处理
        let minL = 0,
            minT = 0,
            maxL = abbreW - markW,
            maxT = abbreH - markH;
        curL = curL < minL ? minL : (curL > maxL ? maxL : curL);
        curT = curT < minT ? minT : (curT > maxT ? maxT : curT);
        markDom.style.left = curL + 'px';
        markDom.style.top = curT + 'px';
        detailImgDom.style.left = (-curL / abbreW * detailIMGW) + 'px';
        detailImgDom.style.top = (-curT / abbreH * detailIMGH) + 'px';
    }

    // 事件触发
    abbreDom.onmouseenter = function(ev) {
        markDom.style.display = "block";
        detailDom.style.display = "block";
        computed(ev);
    }
    abbreDom.onmousemove = function(ev) {
        computed(ev);
    }
    abbreDom.onmouseleave = function(ev) {
        markDom.style.display = "none";
        detailDom.style.display = "none";
    }
</script>
```

![放大镜思路](/medias/imges/js/jsUp/magnifier.png)