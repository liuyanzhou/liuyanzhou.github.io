---
title: ES6
date: 2020-08-03 09:37:52
categories: JS
top: false
summary: ES6
tags: 
 - JS
---

### ES6

### 摘要：

* 新的标准规范：ECMScript2015是js的一种新的标椎规范，就是对js写法上提出了新的语法要求和写法格式。
* ECMScript和JS关系：前者是后者的规格，后者是前者的一种实现。javascript是netscape创造的并交给国际标椎化组织 ECMA，之所以不叫做 JavaScript是由于商标问题，java是 sun 公司的商标，根据授权协议只有 Netscape公司可以合法使用 JavaScript 这个名字，另外就是为了体现 javaScript的标准制定者不是 ECMA 使用取名为ECMAScript
* ES6 与 ECMAScript2015 的关系： ES6是ECMA为JavaScript指定的第6个版本标准，标准委员会最终决定，标准在每年的6月份正式发布一次，作为当年的正式版本。ECMAScript2015是在2015年6月份发布的 ES6的第一个版本。依次类推 ECMAScript 2016是ES6第二个版本、ECMAScript2017是ES6第三个版本........

### 一、块级作用域

1. ECMAScript2015 为js提出的第三个作用域，凡是带`{}`的都是一个块级作用域，if语句的{}，for循环中的{}；while中的{}，或者是我们单独写的{}，try{}catch(err){}这些提供了块级作用域

为什么需要块级作用域？

* 内层遍历会覆盖外层变量

```js
var a = '123'
function fn() {
    console.log(a) // undefined
    var a = '456'
}
fn()
console.log(a) // 123
```

这是因为 fn 函数体内已进有var 声明的变量 a，只是没有赋值，默认是 undefined

* 用来计数的循环变量泄露为全局变量

```js
for(var i=0;i<10;i++) {}
console.log(i)
```

* 块级作用域的成员

块级作用域内的成员需要使用`let`或`const`命令定义的变量

```js
var lagou = "拉勾";
function fn() {
  console.log(lagou); //拉勾
  if (true) {
    let lagou = "hello";
  }
}
fn();
```

#### 1.1 let

基本用法：ECMAScript2015新增了`let`命令，用来声明变量，它的用法了类似于`var`，但是所有声明的变量，只在`let`命令所在的代码块内有效

let主要声明块级作用域下的成员，这个成员变量的作用域范围只能在当前块级作用域下

#### 1.2 const

const 声明变量的同时必须要赋值，const声明之后，不允许去修改它的值，这里面的值说的是不允许修改它，是声明之后不允许重新指向一个新的内存地址，可以去修改内存地址中的属性成员

### 二、数组

#### 2.1数组的解构

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值。这被称为解构

* 完全解构，将数组中的每一个值对应上相应的变量

```js
var arr = ["a","b","c"]
let [com,ind,work] = arr
console.log(work) // "c"
```

* 不完全解构，数组中的部分值对应上了相应的变量

```js
var arr =["a","b","c"]
let [,,work] = arr
console.log(work) // c
```

* 解构不成功，右边的变量的个数超过了等号左边中数组的元素个数

```js
let [a,b,c] = [12]
console.log(b) // undefined
```

如果解构没有成功，则变量的值是`undefined`，如果是展开运算的变量则是空数组

``` js
let [a,b,...c]=[12]
console.log(c) // []
```

#### 2.2 展开运算符

展开运算符(spread)是三个点`...`它好比rest参数的逆运算。将一个数组转为逗号分隔的参数序列，替代 `apply()`的使用技巧。我们之前正在求一个数组中的最大值的时候采用的方式是 Math.max.apply(null,[12,34,56,43]) == 56

```js
var max = Math.max.apply(null,[12,34,56,43])
console.log(max) // 56
var max2 = Math.max(...[12,34,56,43])
console.log(max2) // 56
```

#### 2.3Array 类扩展方法

* Array.from()

Array.from 方法是用于将两类对象转为真正数组：类似数组的对象(array-like object)和可遍历(Iterable) 的对象（包括ES6新增的数据结构 Set 和 Map）

```js
var arraylike = {
    0: 'a',
    1: 'b',
    2: 'd',
    length: 3
}
var arr = Array.from(arraylike)
// ["a","b","d"]
```

Array.from 还可以接受第二个参数，作用类似于数组的`map`方法，用来对每个元素进行处理，将处理后的值放入返回的数组中

```js
var arr = Array.from([1,2,3],function(x) {
    return x * x
})
console.log(arr) // [1,4,9]
```

* Array.of()

Array.of 方法用于将一组值，转换为数组，这个方法的主要目的是弥补数组构造函数`Array()`的不足。因为参数个数的不同，会导致`Array()`的行为有差异

这里3表示数组中元素的长度

```js
var arr = Array(3) // [empry x 3]
```

当`Array()`里面的参数个数大于1的时候，表示的是数组元素

```js
var arr = Array(2,3,4) // [2,3,4]
```

Array.of() 方法不管里面参数的个数多少，都将其转为数组的元素

```js
var arr = Array.of(3)
console.log(arr) // [3]
```

* Array.prototype.includes(value)

查找value是否存在于数组中，存在则返回 true，否则 false

```js
const arr = ['foo',1,NaN,fasle]
console.log(arr.includes('foo')) // true
console.log(arr.includes(NAN)) // true
```

### 三、对象 [Object]

#### 3.1 对象中有关变量的解构赋值

解构不仅可以用于数组，还可以用于对象，对象的解构与数组有一个重要的不同，数组的元素是按次序排序的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取得正确的值

```js
// 普通解构
let {name,work}= {name:'zs',work:'web'}
console.log(name,work) // zs web
let {name:nickName,work}= {name:'zs',work:'web'}
console.log(nickName,work) // zs web
// 深度解构
const obj = {
    name: 'zs',
    work: 'web',
    hobbay: {
        game: 'cs'
    }
}
const { hobbay: { game } } = obj
console.log(game) // cs

```

#### 3.2 对象的扩展

* 对象的简写：当变量和属性名同名时，可以省略同名的属性值

```js
const foo ='bar'
const baz = {foo}
// 等同于
const baz = {foo:foo}
```

* 省略方法中的`function`

```js
const obj= {
    method() {
        return 'ok'
    }
}
// 等同于 
const obj = {
    method:function() {
        return 'ok'
    }
}
```

* 属性的赋值器(setter) 和取值器 (getter)

```js
const lagou = {
    name:'拉钩',
    get com(){
        return this.name
    }
    set work(value){
    this.name = this.name + value
}
}
console.log(lagou.com) // 拉钩
lagou.work = "招聘"
console.log(lagou.name) // 拉钩招聘
```

* 属性名表达式：es5中定义对象的属性有两种方法，一种是用标识符做属性，一种是用表达式做属性

```js
var obj = {}
// 方法一：
obj.name = '拉钩'
// 方法二：
obj['name'] = '拉钩'
```

如果使用大括号定义对象，那么在es6之前只能使用标识符定义属性

```js
var lagou = {
    name:'拉钩'
}
```

而在es6中允许使用大括号定义对象的时候，使用表达式定义属性，把表达式放在方括号中

```js
let name = 'lagou'
const lagou = {
    [name]:'web'
}
console.log(lagou) // [lagou:"web"]
```

#### 3.3 三点运算符在对象中的用途

* 对象的解构

对象的解构赋值用于从一个对象取值，相当于将目标对象自身的所有可遍历的(enumerable)、但尚未被读取的属性，分配到指定的对象上面，所有的键和它们的值都会拷贝到新对象上面

```js
let {x,y,...z} = {x:1,y:2.a:3,b:4}
console.log(z) // {a:3,b:4}
```

上面代码中，变量`z`是解构赋值所在的对象。它获取等号右边所有尚未读取的键(`a`和`b`)，将它们连同值一起拷贝过来

注意：1. 解构赋值必须是最后一个参数。2.解构赋值的拷贝是浅拷贝

* 用于扩展运算

对象的扩展运算符(`...`)用于取出参数对象的所有可遍历属性，拷贝到当前对象之中

```js
let z = {name:"lagou",work:"web"}
let n = {...z}
console.log(n) // {name："lagou",work:"web"}
```

#### 3.4 对象新增方法

* Object.assign()

`Object.assign`方法用于对象的合并，将源对象(source)的所有可枚举属性，复制到目标对象（target）

```js
const target = {
    a:123,
    b:123
}
const source = {
    a:456,
    c:456
}
const result = Object.assign(target,source)
console.log(target) // {a:456,b:123,c:456}
console.log(target === result) // true
```

如果目标对象与源对象有同名属性，则后面的属性会覆盖前面的属性，且`assign()`则返回值就是第一个对象

如果有多个源对象有同名属性，依然是后面的覆盖前面的属性

```js
const target = {a:1,b:1}
const source1 = {b:2,c:2}
const source2 = {c:3}
Object.assign(target,source1,source2) 
console.log(target) // {a:1,b:2,c:3}
```

利用`Object.assign()`复制一个对象，且其中一个对象的修改不会影响到另一个对象

```js
const source = {
    a:123
}
var obj = Object.assign({},source)
obj.a = 456
console.log(obj) // {a:456}
console.log(source) // {a:123}
```

* Object.is()

`Object.is`就是用来比较两个值是否严格相等，与严格比较运算符（===）的行为一致。ES5 比较值是否相等，只有两个运算符：相等运算符(`==`)和严格相等运算符(`===`).它们都有缺点，前者会自动转换数据类型，后者的`NAN`不等于自身，以及`+0`等于`-0`。JavaScript缺乏一种运算，在所有环境中，只要两个值是一样的，它们就应该相等

```js
console.log(Object.is(+0, -0)); //false
console.log(+0 === -0); //true
console.log(Object.is(NaN, NaN)); //true
console.log(NaN === NaN); //false
```

### 四、字符串

#### 4.1 字符串模板

* 传统的字符串里不能使用换行符，必须使用转义符`\n`代替，字符串模板里可以使用。模板字符串是增强版的字符串，用反引号(`)标识，它可以当做普通字符串使用，也可以用来定义多行字符串。或者在字符串中嵌入变量
* 模板字符串中嵌入变量，需要将变量写在`${}`之中，大括号内部可以放入任意的 JavaScripts 表达式，可以进行运算，以及引用对象属性

```js
var name = '拉钩'
var st = `欢迎来到${name}`
console.log(st) // 欢迎来到拉钩
```

#### 4.2 标签模板

* 模板字符串的功能，不仅仅是上面这些，它可以紧跟在一个函数名后面，该函数将被调用来处理这个模板字符串。这被称为标签模板功能

```js
console.log`hello`
等同于
console.log(['hello'])
```

标签模板确实不是模板，而是函数调用的一个特殊形式。“标签”指的就是函数，紧跟在后面的模板字符串就是它的参数

注意：如果模板字符串里面有变量。就不是简单的调用。而是会将模板字符串先处理成多个参数，再调用函数,其实就是将模板字符串进行变量分割，在把静态值取出，放在函数的第一个参数，变量依次存放在后面的参数中

```js
var name = "laogou"
var work = "web"
function tag(art,a,b) {
    console.log(art)
    console.log(a)
    console.log(b)
    return "hello lagou"
}
var st = tag`hello ${name} ,职业${work}开发`
console.log(st) // hello lagou
['hello',",职业","开发"，raw：Array(3)]
laogou
web
```

函数内的返回值，就是`tag`函数处理模板字符串后的返回值

```js
var name = "laogou"
var work = "web"
function tag(st,a,b) {
    console.log(st)
    console.log(a)
    console.log(b)
    return "hello lagou" //如果没有返回值，则默认是undefined
}
var st = tag`hello${name},职业${work}开发`;
console.log(st); //hello lagou
```

#### 4.3 字符串扩展方法

字符串实例的方法

* includes() 返回布尔值，表示是否找到了参数字符串

```js
var st = "lagou web";
var b = st.includes("web");
console.log(b); //true
```

	* startsWith() 返回布尔值，表示参数字符串是否在原字符串的头部

```js
var st = "lagou web";
var b = st.startsWith("la");
console.log(b); //true
```

	*   endsWith()  返回布尔值，表示参数字符串是否在原字符串的尾部

```js
var st = "lagou web";
var b = st.endsWith("web");
console.log(b); //true
```

### 五、函数

#### 5.1 参数默认值

* ES6 允许为函数的参数设置默认值，即直接写在参数定义的后面

```js
function fn(a,b="lagou") {
    console.log(a+b)
}
fn("hello"); // hello lagou
```

* 注意：

  * 参数变量是默认声明的，所以不能用`let`或`const`在次声明
  * 使用参数默认值，函数不能有同名参数

* 参数默认值的位置

  通常情况下，定义了默认值的参数，应该是函数的尾参数。因为这样比较容易看出来，到底省略了哪些参数，如果非尾部的参数设置默认值，实际上参数是没法省略的，若调用不传入参数就会报错

```js
function f(x=1,y) {
    return [x,y]
}
fn() // [1,undefined]
fn(2) // [2,undefined]
fn(,1) // 报错
```

* ES6 引入rest 参数(形式为`...变量名`)，用于获取函数的多余参数，这样就不需要使用`arguments`兑现率，rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中

```js
function add(...values) {
    console.log(values)
}
add(2,5,3) // [2,5,3]
```

* rest 参数和函数中的参数解构有什么区别
  1. rest 参数是发生在函数的定义阶段，函数的参数解构是发生在函数的调用阶段
  2. 二者是一种互为逆运算，rest参数解构是将参数收集为一次数组，函数参数解构是将数据拆分并传入函数中去

```js
function add(...values) {
    // 这是rest 参数
    console.log(values)
}
add(2,5,3) // [2,5,3]
var arr  [1,2,3]
function fn(a,b,c) {
    console.log(a+b+c)
}
fn(...arr) // 6 这是参数的解构
```

#### 5.2 箭头函数

* ES6 允许使用箭头`=>`定义函数

```js
var f = (v) => v
// 等同于
Var f = function(v) {
    return v
}
```

如果箭头函数不需要参数或需要多个参数，就使用一个圆括号代表参数部分

```js
var f = () => 5
// 等同于
var f = function () {
    return 5
}

var sum = (num1,num2)=> num1+num2
// 等同于
var sum = function(num1,num2) {
    return num1+num2
}
```

如果箭头函数的代码块部分多一条语句，就要使用大括号将它们括起来，并且使用`return`语句返回

```js
var sum = (num1,num2)=> {
    return num1 + num2
}
```

由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错

```js
let getItem = id => {id:id,name:"Temp"} ; // 报错
let getItem = id => ({id:id,name:"Temp"}); // 不报错
```

* 注意点：
  * 函数体内的`this`对象，就是定义时所在的对象，而不是使用时所在的对象，箭头函数外面的this是什么。箭头函数内的this还是什么。(箭头函数不绑定this)
  * 不可以当作构造函数，也就是说，不可以使用`new`命令，否则会抛出一个错误
  * 不可以使用`arguments`对象，该对象在函数体内部存在，如果要用，可以用 rest 参数代替

```JS
var name="web"
var obj={
    name:"lagou",
    fn(){
        var t=setTimeout(function(){
            console.log(this.name)//web  this是window
        },1000)
    }
}
obj.fn()
-----------------------------------
var name="web"
var obj={
    name:"lagou",
    fn(){
        var t=setTimeout(()=>{
            console.log(this.name)//lagou this是obj
        },1000)
    }
}
obj.fn()
```

###六、Proxy

概述：Proxy 可以理解成一个快递员，我们发快递还是接受快递，都需要这个快递员充当一个代理的作用。ES6 原生提供了 Proxy 构造函数，用来生成 Proxy 实例，这个实例就是一个代理对象 (快递员)

```js
var proxy = new Proxy(target,handler)
```

* 目标对象

这个代理对象有两个参数，一个是代理的目标对象，第二个也是一个对象，它是配置对象，用来定制代理的拦截行为

```js
const person = {
    name:'zce',
    age:20
}
const perosonProxy = new Proxy(peroson,{
    get(target,property) {
        console.log(target,property) // person{name:"zce",age:20}
        return 100
    }
    set() {}
})
```

* 配置对象

配置对象中一般有两个方法`get`和`set`，`get`是用来拦截对目标对象属性的访问请求。`get`方法中有两个参数。第一个参数是目标参数，第二个参数是访问的那个属性

```js
const person = {
  name: "zce",
  age: 20,
};
const personProxy = new Proxy(person, {
  get(target, property) {
    console.log(target, property);
    return 100;
  },
  set() {},
});

console.log(personProxy.name); //100
```

注意：这个`get`方法的返回值就是我们获取的这个属性的返回值

* 这个`get`方法中有三个参数，一个是代理的目标对象，一个是代理的处理对象，第三个参数是 proxy 实例本身，且第三个参数是可选参数。

```js
const person = {
  name: "zce",
  age: 20,
};
const personProxy = new Proxy(person, {
  get(target, property, o) {
    console.log(o); //proxy{name:"zec",age:20}
    return property in target ? target[property] : undefined;
  },
  set() {},
});

console.log(personProxy.age); //20
```

* 这个`set`方法用来拦截某个属性的赋值操作，可以接受四个参数，依次为目标对象、属性名、属性值和 Proxy 实例本身，其中最后一个参数可选

```js
const person = {
  name: "zce",
  age: 20,
};
const personProxy = new Proxy(person, {
  get(target, property, o) {
    return property in target ? target[property] : undefined;
  },
  set(obj, pro, value, o) {
    console.log(obj, pro, value, o);
  },
});

console.log((personProxy.name = "zhang"));
//{name: "zce", age: 20} "name" "zhang" Proxy {name: "zce", age: 20}
```

可以去设置一些属性或修改

```js
const person = {
  name: "zce",
  age: 20,
};
const personProxy = new Proxy(person, {
  get(target, property, o) {
    return property in target ? target[property] : undefined;
  },
  set(target, pro, value, o) {
    //可以做一些内部校验
    target[pro] = value;
  },
});
console.log((personProxy.name = "lagou"));
person; //{name:"lagou",age:20}
```

在Proxy没出来之前前端是采用`defineProperty`来进行数据监听,但这种劫持要一个一个手动添加并不像Proxy方便，性能也没`Proxy`好

```js
let data = {
    message: "测试数据",
    age:10
}
// console.log(data);
Object.defineProperty(data, "message", {
    configurable:true, // 允许对对象操作
    enumerable:true, // 对象可枚举
    get() {
        console.log("get..");
        return "测试数据"; // data['message']
    },
    set(newValue) {
        console.log("set..", newValue);
    }
})

Object.defineProperty(data, "age", {
    get() {
        console.log("get..");
        return "10"; // data['message']
    },
    set(newValue) {
        console.log("set..", newValue);
    }
})
```



### 七、Reflect

概述：`Reflect`对象与`Proxy`对象一样，也是ES6 为了操作对象而提供的新`API`。Reflect对象的设计目的有这样几个：

* 将`Object`对象的一些明显属于语言内部的方法(比如`Object.defineProperty`),放到`Reflect`对象上。现阶段，某些方法同时在`Object`和`Reflect`对象上部署，未来的新方法将只部署在`Reflect`对象上，也就是说，从`Reflect`对象上可以拿到语言内部的方法
* 修改某些`Object`方法的返回结果，让其变得更加合理。比如：`Object.defineProperty(obj,name,desc)`在无法定义属性时，会抛出一个错误，而`Reflect.defineProperty(obj,name,desc)`则返回`false`

```js
// 老写法
try {
    Object.defineProperty(target,property,attributes)
    // success
}catch(e) {
    // failure
}

// 新写法
if(Reflect.defineProperty(target,property,attributes)) {
    // success
}else {
    // failure
}
```

* 让`Object`操作都变成函数行为。某些`Object`操作是命令式，比如`name in obj`和`delete obj[name]`,而`Reflect.has(obj,name)`和`Reflect.deleteProperty(obj,name)`让它们变成了函数行为

```js
// 老写法
"assgin" in Object // true
// 新写法
Reflect.has(Object,"assign") // true
```

####Reflect静态方法

* Reflect.get

  * Reflect.get(target,name,receiver) , `Reflect.get`方法查找并返回`target`对象的`name`属性，如果没有该属性，则返回`undefined`

    ```js
    var myObject = {
        foo:1,
        bar:2,
        get baz() {
            return this.foo +this.bar
        }
    }
    Reflect.get(myobject,"foo") // 1
    Reflect.get(myobject,"bar") // 2
    Reflect.get(myobject,"baz") // 3 
    ```

  * 如果`name`属性部署了读取函数 (getter) ,则读取的`this`绑定`receiver`

    ```js
    var myObject = {
        foo:1,
        bar:2,
        get baz() {
            return this.foo + this.bar
        }
    }
    var myReceiverObject = {
        foo:4,
        bar:4
    }
    Reflect.get(myObject,"baz",myReceiverObject) // 8
    ```

  * 如果第一个参数部署对象，`Reflect,get`方法会报错

    ```js
    Reflect.get(1,"foo") // 报错
    Reflect.get(false,"foo") // 报错
    ```

* Reflect.set

  * `Reflect.set(target,value,receiver)`,`Reflect.set`方法设置`target`对象的`name`属性等于`receiver`

    ```js
    var myObject = {
        foo:1
    }
    myObject.foo; //1 
    Reflect.set(myObject,"foo",2)
    myObject.foo;//2
    ```

  * 如果`name`属性设置了赋值函数，则赋值函数的`this`绑定`receiver`

    ```js
    var myObject = {
        foo:4,
        set bar(value) {
            return (this.foo = value)
        }
    }
    var myReceiverObject = {
        foo:0
    }
    
    Reflect.set(myObject,"bar",1,myReceiverObject)
    myObject.foo // 4
    myReceiverObject.foo // 1
    ```

* Reflect.has

  * `Reflect.has(obj,name)`,`Reflect,has`方法对应`name in obj`里面的`in`运算符

    ```js
    var myObject = {
        foo:1
    }
    // 旧写法
    "foo" in myObject // true
    // 新写法
    Reflect.has(myObject,"foo") // true
    ```

    如果`Reflect.has()`方法中的第一个参数部署对象，会报错

* Reflect.deleteProperty

  * Reflect.deleteProperty(obj,name),`Reflect.deleteProperty`方法等同于`delete obj[name]`,用于删除对象的属性

    ```js
    const myobj = {foo:"bar"}
    // 旧写法
    delete myObj.foo
    // 新写法
    Reflect.deleteProperty(myobj,"foo")
    ```

    该方法放回一个布尔值，如果删除成功，或者被删除的属性不存在，则返回`true`，删除失败，被删除的属性依然存在，则返回`false`.如果`Reflect.deleteProperty()`方法的第一个参数不是对象，会报错

* Reflect.ownKeys 

  `Reflect.ownkeys(obj)`,获取到obj对象中所有属性的键

  ```js
  const obj = {
      name: 'zce',
      age: 18
  }
  console.log(Reflect.ownKeys(obj)) // [ 'name', 'age' ]
  ```

  

### 八、Class

概述：ES6提供了更接近传统语言的写法，引入了 Class (类)这个概念，作为对象的模板。通过`class`关键字可以定义类。基本上，ES6的`class`可以看做只是一个语法糖。它的绝大部分功能，ES5都可以做到，新的`class`写法只是让对象原型的写法更加清晰，更像面向对象编程的语法而已。写入如下

```js
class Point {
    constructor(x,y) {
        this.x = x;
        this.y=y;
    }
    toString() {
        return "("+this.x+","+this.y+")";
    }
}
```

#### 基本介绍

* constructor()

  constructor方法是类的默认方法，通过`new`命令生成对象实例时，自动调用改方法，一个类必须有`constructor`方法，如果没有显示定义，一个空的`constructor`方法会被默认添加

* 类的实例

  * 生成类的实例的写法，与ES5完全一样，也是使用`new`命令，如果忘记加上`new`，像函数那样调用`class`会报错

    ```js
    class Point {
        // ...
    }
    // 报错
    var point = Point(2,3)
    // 正确
    var point = new Point(2,3)
    ```

  * 与ES5一样，实例的属性除非显示定义在其本身（即定义在`this`对象上），否则都是定义在原型上(即定义在`class`上)

    ```js
    class Point {
        constructor(x,y) {
            this.x = x
            this.y = y
        }
        toString() {
             return "("+this.x+","+this.y+")";
        }
    }
    var point = new Point(2,3)
    point.toString() // (2,3)
    ponit.hasOwnProperty('x') // true
    point.hasOwnProperty('y') // true
    point.hasOwnProperty("toString") // false
    point.__proto__.hasOwnProperty("toString") // true
    ```

  * 与ES5 一样，类的所有实例共享一个原型对象

    ```js
    var p1 = new Point(2,3)
    var p2 = new Point(3,2)
    
    p1.__proto__ === p2.__proto__ // true
    ```

* getter 和 setter

  与 ES5 一样 ，在类的内部可以使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截改属性的存取行为

  ```js
  class MyClass {
      constructor() {
          // ....
      }
      get prop() {
          return "getter"
      }
      set prop(value) {
          console.log("setter:"+value)
      }
  }
  
  let inst = new Myclass()
  inst.prop = 123
  // 输出:setter:123
  console.log(inst.prop) // getter
  ```

* 属性表达式

  类的属性名，可以采用表达式，下面代码中，`Square`类的方法名`getArea`是从表达式得到的

  ```js
  let methodName = "getArea"
  class Square {
      constructor(length) {
          //...
      }
      [methodName]() {
          //....
      }
  }
  ```

#### 类的静态

类相当于**实例的原型**，所有在类中定义的方法，都会被实例继承，如果在一个方法前。加上`static`关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就被称为`静态方法`

```js
class Foo {
    static classMethod() {
        return "hello"
    }
}

Foo.classMethod() // "hello"

var foo = new Foo()
foo.classMethod() // TypeError:foo.classMethod is not a function
```

解说：上面代码值，`Foo`类的`className`方法前有`static`关键字，表明该方法是一个静态方法，可以直接在`Foo`类上调用(`Foo.className()`),而不是在`Foo`类的实例上调用，如果在实例撒花姑娘调用静态方法，会抛出一个错误，表示不存在该方法

* 注意：如果静态方法包含`this`关键字，这个`this`指的是类，而不是实例

```js
class Foo {
    static bar() {
        this.baz()
    }
    static baz() {
        console.log("hello")
    }
    baz() {
        console.log("world")
    }
}

Foo.bar(); // hello
```

解说：上面的代码中，静态方法`bar`调用了`this.baz`,这里的`this`指的是`Foo`类，而不是`Foo`的实例，等同于调用`Foo.baz`,另外，从这个例子还可以看出，静态方法可以与非静态方法重名

* 父类的静态方法，可以被子类继承

```js
class Foo{
    static classMethod() {
        return 'hello'
    }
}

class Bar extends Foo {
}

Bar.classMethod() // 'hello'
```

* 静态属性

  静态属性指的是 Class 本身的属性，即`class.propName`,而不是定义在实例对象(`this`)上的属性，ES6 明确规定，Class内部只有静态方法，没有静态属性，现在有一个[提案](https://github.com/tc39/proposal-class-fields)提供了类的静态属性，写法是在实例属性的前面，加上`static`关键字

  ```js
  class MyClass {
      static myStaticProp = 42;
      constructor() {
          console.log(Myclass.myStaticProp)
      }
  }
  ```

#### 类的继承

* 简介：

  * Class 可以通过`extends`关键词实现继承，这比 ES5 的通过修改原型链实现继承要清晰和方便多

    ```js
    class Point {}
    class ColorPoint extends Point {}
    ```

    解说：上面的代码定义了一个`ColorPoint`类，该类通过`extends`关键字，继承了`Point`类的所有属性和方法。但是由于没有部署任何代码，所有两个属性和方法完全一样，等于复制一个`Point`类

  * 子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错，这是因为子类自己的`this`对象，必须通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后在对其进行加工，加上子类自己的实例属性和方法，通过不调用`super`方法，子类就得不到`this`对象

    ```js
    class Point {
        //...
    }
    class ColorPoint extends Point {
        constructor() {}
    }
    let cp = new ColorPoint() // ReferenceError
    ```

  * 在子类的构造函数中，只有调用`super`之后，才可以使用`this`关键字，否则会报错，这是因为子类实例的构建中，基于父类实例，只有`super`方法才能调用父类实例

    ```js
    class Point {
        constructor(x,y) {
            this.x = x
             this.u = y
        }
    }
    class ColorPoint extends Point {
        constructor(x,y,color) {
            this.color = color // ReferenceError
            super(x,y)
            this.color = color // 正确
        }
    }
    ```

* super 

  * `super`这个关键字，既可以当做函数使用，也可以当做实例对象来使用，在这两种情况下，它的用法完全不同

    * 第一种情况，`super`作为函数调用时，代表父类的构造函数，ES6 要求，子类的构造函数必须执行一次`super`函数

      ```js
      class A{}
      class B extends A {
          constructor() {
              super()
      }
      ```

      解说：上面代码中，子类`B`的构造函数之中的`super()`,代表调用父类的构造函数。这是必须的，否则 JavaScript 引擎会报错，`super`虽然代表了父类的`A`的构造函数，但是返回的是子类`B`实例，即`super`内部的`this`指的是`B的实例`,因此`super()`在这里相当于`A.prototype.constructor.call(this)`

    * 第二种情况，`super`作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类，也就是说子类可以调用父类的静态方法

      ```js
      class A {
          p() {
              return 2
          }
      }
      
      class B extends A {
          constructor() {
              super()
              console.log(super.p()) // 2
          }
      }
      
      let b = new B()
      
      // 调用父类的静态方法
      class Point {
          constructor() {
              this.f = '父类的f属性'
          }
          static hi() {
              console.log('hi')
          }
      }
      class ColorPoint extends Point {
          constructor() {
              super()
              this.z = '子类的z属性'
          }
          static sayHi() {
              super.hi()
          }
      }
      ColorPoint.sayHi() // hi
      ```

      由于`super`指向父类的原型对象(prototype)，所以挂载在父类实例上的方法或属性，是无法通过`super`调用的

      ```js
      class A {
          constructor() {
              this.p = 2
          }
      }
      
      class B extends A {
          get m() {
              return super.p
          }
      }
      let b = new B()
      console.log(b.m) // undefined
      ```

### 九、Set

基本用法：

* ES6 提供了新的数据结构`Set`,它类似于数组，**但是成员的值都是唯一的，没有重复的值**
* `Set`本身也是一个构造函数，用来生成 Set 数据结构
* `Set`函数可以接受一个数组(或者具有  **iterable**接口的其他数据结构)作为参数，用来初始化

```js
const set = new Set([1,2,3,4,4])
console.log(set) // [1,2,3,4]
```

数组去重

```js
[...new Set([1,2,3,2,4,5])] // [1,2,3,4,5]
```

字符串去重

```js
[...new Set("ababbc")].join("") // "abc"
```

#### 属性和方法

* Set 结构的实例有以下属性

  * `Set.prototype.constructor `: 构造函数，默认就是`Set`函数
  * `Set.prototype.size`:返回`Set`实例的成员总数

* Set 实例的方法分为两大类

  * 操作方法（用于操作数据）

    * `Set.prototype.add(value)`:添加某个值，返回 Set 结构本身

    ```js
    const items = new Set([])
    items.add(1).add(2).add(3)
    console.dir(items)// [1,2,3]
    ```

    * `Set.prototype.has(value)`:返回一个布尔值。表示该值是否为`Set`成员
    * `Set.prototype.clear()`：清除所有成员，没有返回值

    ```js
    const items  = new Set([12,23,34])
    var b = items.clear(12)
    console.log(b) // undefined
    console.log(items) // Set(0)[]
    ```

  * 遍历方法（用于遍历成员）

    * `Set.prototype.keys()`：返回键名的遍历器
    * `Set.prototype.values()`:返回键值的遍历器
    * `Set.prototype.entries()`:返回[value-value]格式的遍历器

    ```js
    let set = new Set(["red", "green", "blue"]);
    for (let item of set.keys()) {
      console.log(item);
    }
    // red
    // green
    // blue
    for (let item of set.values()) {
      console.log(item);
    }
    // red
    // green
    // blue
    for (let item of set.entries()) {
      console.log(item);
    }
    // ["red", "red"]
    // ["green", "green"]
    // ["blue", "blue"]
    ```

    * `Set.prototype.forEach()`

    ```js
    let set = new Set([1, 4, 9])
    set.forEach((value, key) => console.log(value + ":" + key))
    // 1:1
    // 4:4
    // 9:9
    ```

### 十、Map

概述：JavaScript 的对象(Object),本质上是键值对的集合（Hash结构），但是传统上只能用字符串当做键。这给他的使用带来了很大的限制。为了解决这个问题，ES6提供了 Map 数据结构。它类似于对象，也是键值对的集合，当“键”的范围不用限于字符串，各种类型的值（包括对象）都可以当作键，也就是说，Object 结构提供了“字符串-值”的对应，Map 结构提供了“值-值“的对应，是一种更完善的 Hash结构实现，如果你需要”键值对“的数据结构，Map 比 Object 更合适

### 基本用法

作为构造函数，Map也可以接受一个数组作为参数，该数组的成员是一个表示键值对的数组

```js
const map = new Map([
    ["name","张三"],
    ["title","Author"]
])

map.size // 2
map.has("name") // true
map.get("name") // "张三”
map.has("title") // true
map.get("title") // Author
```

#### 属性和方法

* size 属性，`size`属性返回 Map 结构的成员总数

```js
const map = new Map()
map.set("foo",true)
map.set("bar",false)

map.size
```

* Map.prototype.set(key,value),`set`方法设置键名`key`对应的键值为`value`,然后返回整个Map 结构，如果`key`已经有值，则键值会被更新，否则就新生该键，set() 方法返回的是 Set对象可以采用链式写法

```js
const m = new Map()

m.set("edition",6) // 键是字符串
m.set(262,"Standard") // 键是数值
m.set(undefined,"nan") // 键是 undefined
```

* Map.prototype.get(key) `get`方法读取`key`对应的键值，如果找不到`key`,返回`undefined`

``` js
const m = new Map()
const hello = function() {
    console.log('hello')
}
m.set(hello,"hello ES6") // 键是函数
m.get(hello) // hello ES6
```

* Map.prototype.has(key),`has`方法返回一个布尔值，表示某个键是否在当前 Map对象之中

```js
const m = new Map()

m.set("edition", 6);
m.set(262, "standard");
m.set(undefined, "nah");

m.has("edition"); // true
m.has("years"); // false
m.has(262); // true
m.has(undefined); // true
```

* Map.prototype.delete(key),`delete`方法删除某个键，返回`true`.如果删除失败，返回`false`

```js
const m = new Map()
m.set(undefined,"nan")
m.has(undefined) // true

m.delete(undefined)
m.has(undefined) // false
```

* Map.prototype.clear(),`clear`方法清除所有成员，没有返回值

```js
let map = new Map()
map.set("foo",true)
map.set("foo",false)

map.size // 2
map.clear() 
map.size // 0
```

#### 遍历

* `Map.prototype.keys()`:返回键名的遍历器
* `Map.prototype.values()`:返回键值的遍历器
* `Map.prototype.entries()`:返回所有成员的遍历器
* `Map.prototype.forEach()`:遍历 Map 的所有成员

``` js
const map = new Map([
    ["F","no"],
    ["T","yes"]
])

for(let key of map.keys()){
    console.log(key)
}
// "F"
// "T"

for(let value of map.values()) {
    console.log(value)
}
// "no"
// "yes"

for(let item of map.entries()) {
    console.log(item[0],item1)
}
// "F" "no"
// "T" "yes"

// 或者
for(let [key,value] of map.entries()) {
    console.log(key,value)
}
// "F" "no"
// "T" "yes"

map.forEach(function(value, key, map) {
    console.log(map)
    console.log("key: %s,value: %s", key, value)
})
```

`forEach`方法还可以接受第二个参数，用来绑定`this`。

### 十一、Symbol

概述：ES5 的对象属性名称都是字符串，这容易造成属性名的冲突，比如：你使用了一个提供的对象，但又想为这个对象添加新方法（mixin模式），新方法的名字就可能与现有方法产生冲突。如果有一种机制，保证每个属性的名字都是独一无二的就好了，这样就从根本上防止属性名冲突。这就是ES6引入 `Symbol`的原因，ES6 引入一种新到的原始数据类型`Symbol`，表示独一无二的值。它是 JavaScript 语言的第七种数据类型，前六种是:`undefined`、`null`、`布尔值(Boolean)`、`字符串(String)`、`数值(Number)`、`对象(Object)`。Symbol 值通过`Symbol`函数生成，这就是说，对象的属性名可以有两种类型，一种是原来就有的字符串，另一种就是新增的`Symbol`类型，凡是属性名属于Symbol 类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。

```js
var obj = {
   say:"lagou"
}
var say = Symbol() // say 是Symbol类型
obj[say] = "web"
console.log(obj) // {say:"lagou",Symbol():"web"}
```

#### 语法：

* Symbol 函数前不能加`new`命令，否则会报错
* Symbol 函数可以接受一个字符串作为参数，表示对 Symbol 实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易
* 每一个 Symbol值是不相等的，这意味着 Symbol 值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性

```js
var a = Symbol("a")
var b= Symbol("b")
console.log(a===b) // false
```

* Symbol 值不能与其他类型的值进行运算，会报错

```js
let sym = Symbol("my symbol")

"your symbol is " +
  sym // TypeError: can't convert symbol to string
  `your symbol is ${sym}`;
// TypeError: can't convert symbol to string
```

* Symbol 值作为对象属性名时，不能用点运算符 (还未验证)

```js
const mySymbol = Symbol()
const a = {}
a[mySymbol] = "Hello !"
console.log(a['mySymbol']) // undefined
console.log(a[mySymbol]) // Hello!
```

* Symbol 作为属性名，遍历对象的时候，改属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`Json.stringify()`f返回，但是它也不是私有属性，有一个`Object.getOwnPropertySymbols()`方法，可以获取指定对象的所有Symbol 属性名，该方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值

```js
const obj = {}
let a = Symbol("a")
let b= Symbol("b")
obj[a] = "hello"
obj[b]="world"
const objectSymbol = Object.getOwnPropertySymbols(obj)
console.log(objectSymbol)  //[Symbol(a), Symbol(b)]
```

* 有时，我们希望重新使用同一个 Symbol 值，`Symbol.for()`方法可以做到这一点，它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值，如果有，就返回这个 Symbol 值，否则就新建一个以该字符串为名称的 Symbol 值，并将其注册到全局

```js
let s1 = Symbol.for("foo")
let s2 = Symbol.for("foo")
s1 === s2 // true
```

Symbol.for 与 Symbol 这两种写法，都会生成新的Symbol 。它们的区别是，前者会被登记在全局环境中供搜索，后者不会。`Symbol.for()`不会每次调用就返回一个新的`Symbol`类型的值，而是会先检查给定的`key`是否已经存在，如果不存在才会新建一个值。比如，如果你调用`Symbol.for('cat')`30次，每次都会返回同一个 Symbol 值，但是你调用`Symbol("cat")`30次，会返回30个不同的 Symbol 值

```js
Symbol.for("bar") === Symbol.for("bar") // true
Symbol("bar") === Symbol("bar") // false
```

由于`Symbol()`写法没有登记机制，所以每次调用都会返回一个不同的值，`Symbol.for()`为Symbol值登记来的名字，是全局环境的，不管有没有在全局环境运行

```js
function foo() {
    return Symbol.for("bar")
}
const x = foo()
const y = Symbol.for("bar")
console.log(x===y) // true
```

### 十二、for...of 遍历

for...of 可以遍历有提供`Iterable`可迭代接口的任何数据结构,（其实是消费了iterable中的Iterator接口），现成的通过了接口的数据结构有: Set 、Map 、Array ,而普通的Object是没有提供接口的，所以在使用`for...of`遍历普通对象结构会报错

* Array

```js
const arr = [100, 200, 300, 400]
 for (const item of arr) {
   console.log(item)
}
// 100 200 300 400 
```

* Set

```js
const s = new Set(['foo', 'bar'])

for (const item of s) {
    console.log(item)
}
// foo bar

// for...of 循环可以替代 数组对象的 forEach 方法，但forEach不能使用break跳出循环

arr.forEach(item => {
    console.log(item)
})

for (const item of arr) {
    console.log(item)
    if (item > 100) {
        break
    }
}

```

* Map

```js
const m = new Map()
m.set('foo', '123')
m.set('bar', '345')

for (const [key, value] of m) {
    console.log(key, value)
}
// foo 123
// bar 345
```

### 十三、可迭代接口

#### 13.1 Iterator 的概念

* 简单介绍：JavaScript 原有的表示集合，的数据结构，主要是数组(`Array`)和对象(`Object`),ES6又添加了`Map`和`Set`。这样就有了四种数据集合，用户还可以组合使用它们，定义自己的数据结构，比如数组的成员是`Map`，`Map`的成员是对象，这样就需要一种统一的接口机制，来处理所有不同的数据结构。而遍历器（Iterator）就是这样一种机制，它是一种接口，为各种不同的数据结构提供统一的访问机制，任何数据结构只要部署`Iterator`接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）

* Iterator 的作用有三个：

  * 为各种数据结构，提供一个统一的，简便的访问接口
  * 使得数据结构的成员能够按某种次序排列
  * ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费

* Iterator 的遍历过程

  * 创建应该指针对象，指向当前数据结构的起始位置，也就是说，遍历器对象本质上，就是一个指针对象

  * 第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一次成员

  * 第二次调用指针对象的`next`方法，指针就是指向数据结构的第二个成员

  * 不断调用指针对象的`next`方法，直到它指向数据结构的结束位置

    每一次调用`next`方法，都会返回数据结构的当前成员的信息，具体来说，就是返回一个包含`value`和`done`两个属性的对象。其中，`value`属性是当前成员的值，`done`属性是一个布尔值，表示遍历是否结束

  ```js
  // 简单的 Iterator 遍历器的实现
  function easyIterator(array) {
      var nextIndex = 0
      return {
          next:function() {
              return nextIndex < array.length ?{value:array[nextIndex++],done:false} : {value:undefined,done:true}
          }
      }
  }
  var it = easyIterator(["a","b"])
  console.log(it.next()); // { value: "a", done: false }
  console.log(it.next()); // { value: "b", done: false }
  console.log(it.next()); // { value: undefined, done: true }
  ```

#### 13.2 实现可迭代接口

可迭代接口规定：

* 在`Iterable`（可迭代接口）必须要以`Symbol.iterator`这个Symbol常量为键，而值为一个函数
* 在`Iterator`(迭代器)接口必须返回一个`next`方法
* 在`IterationResult`(迭代结果接口)，必须返回两个值 value 与 done

```js
const obj = {
  store: ['foo', 'bar', 'baz'],
  [Symbol.iterator]: function () {
    let index = 0
    const self = this

    return {
      next: function () {
        const result = {
          value: self.store[index],
          done: index >= self.store.length
        }
        index++
        return result
      }
    }
  }
}

for (const item of obj) {
  console.log('循环体', item)
}
// 循环体 foo
// 循环体 bar
// 循环体 baz
```

#### 13.3 迭代器设计模式

场景：你我协同开发一个任务清单应用 

我的代码

```js
const todos = {
  life: ['吃饭', '睡觉', '打豆豆'],
  learn: ['语文', '数学', '外语'],
  work: ['喝茶'],

  // 提供迭代器（ES2015 统一遍历访问接口）
  [Symbol.iterator]: function () {
    const all = [...this.life, ...this.learn, ...this.work]
    let index = 0
    return {
      next: function () {
        return {
          value: all[index],
          done: index++ >= all.length
        }
      }
    }
  }
}
```

你的代码：

```js
for (const item of todos) {
  console.log(item)
}

```

这是解决 `for...of`不能遍历普通对象的现象

### 十四、生成器

出现的目的是：为了避免异步编程中回调嵌套过深，提供更好的异步编程解决方案

Generation函数，它也实现了`Iterator`接口机制，也有`next`方法，一般与`yield`关键字配置使用，特点是具有惰性，也就是抽一下，动一下

```js
function * foo () {
  console.log('1111')
  yield 100
  console.log('2222')
  yield 200
  console.log('3333')
  yield 300
}

const generator = foo()

console.log(generator.next()) // 第一次调用，函数体开始执行，遇到第一个 yield 暂停
console.log(generator.next()) // 第二次调用，从暂停位置继续，直到遇到下一个 yield 再次暂停
console.log(generator.next()) // 。。。
console.log(generator.next()) // 第四次调用，已经没有需要执行的内容了，所以直接得到 undefined

```

案例1:发号器,没执行一次就id+1

```js
function * createIdMaker () {
  let id = 1
  while (true) {
    yield id++
  }
}

const idMaker = createIdMaker()

console.log(idMaker.next().value) // 1
console.log(idMaker.next().value) // 2
console.log(idMaker.next().value) // 3
console.log(idMaker.next().value) // 4
```

案例2：使用 Generator 函数实现 iterator方法

```js
const todos = {
    life: ['吃饭', '睡觉', '打豆豆'],
    learn: ['语文', '数学', '外语'],
    work: ['喝茶'],
    [Symbol.iterator]: function*() {
        const all = [...this.life, ...this.learn, ...this.work]
        for (const item of all) {
            yield item
        }
    }
}

for (const item of todos) {
    console.log(item)
}
```

### 十六、ES2016概述

#### 16.1 增加了 Array.prototype.includes()

#### 16.2 指数运算符 

**x 代表几次方

```js
console.log(2 ** 10) // 1024
```

### 十七、ES2017概述

#### 17.1 对象新增方法 

* `Object.values(obj)`: 得到对象的所有值
* `Object.entries(obj)`:返回一个给定对象自身可枚举属性的键值对数组,返回的数组可以直接被`for...of`遍历，而且返回的数组格式是`Map`数据结构格式，适合做Map初始化
* `Object.getOwnPropertyDescriptors(obj)`: 用来获取一个对象的所有自身属性的描述符 

```js
const obj = {
    foo: 'value1',
    bar: 'value2'
}

// Object.values(obj)
console.log(Object.values(obj)) // [ 'value1', 'value2' ]

// Object.entries(obj)
console.log(Object.entries(obj)) // [ [ 'foo', 'value1' ], [ 'bar', 'value2' ] ]
// 返回的数组可以直接被 for...of 直接遍历
for (const [key, value] of Object.entries(obj)) {
    console.log(key, value) 
    // foo value1
	// bar value2
}

// Object.getOwnPropertyDescriptors(obj)
const p1 = {
    firstName: 'Lei',
    lastName: 'Wang',
    get fullName() {
        return this.firstName + ' ' + this.lastName
    }
}
console.log(p1.fullName) // Lei Wang
const p2 = Object.assign({}, p1)
p2.firstName = 'zce' 
console.log(p2) //{ firstName: 'zce', lastName: 'Wang', fullName: 'Lei Wang' }
// 上面代码将fullName属性赋值都转字符串了，这样里面就不存在this问题，我们需要通过完整信息来复制对象则需要 Object.getOwnPropertyDescriptors(obj) 来获取

const descriptors = Object.getOwnPropertyDescriptors(p1)
console.log(descriptors)
const p2 = Object.defineProperties({}, descriptors)
p2.firstName = 'zce' 
console.log(p2.fullName) // zce Wang

```

#### 17.2 字符串

* `String.prototype.padStart(targetLength [, padString]) `:用另一个字符串（如果需要，可以多次）填充当前字符串，直到结果字符串达到给定的长度为止。从当前字符串的开头开始应用填充 
* `String.prototype.padEnd(targetLength [, padString])  `:用一个字符串填充当前字符串（如果需要的话则重复填充），返回填充后达到指定长度的字符串。从当前字符串的末尾（右侧）开始填充 

注意：指定的长度小于要填充字符串的长度，这字符串会被直接返回

```js
// String.prototype.padStart(targetLength [, padString])
'abc'.padStart(10);         // "       abc"
'abc'.padStart(10, "foo");  // "foofoofabc"
'abc'.padStart(6,"123465"); // "123abc"
'abc'.padStart(8, "0");     // "00000abc"
'abc'.padStart(1);          // "abc"

//String.prototype.padEnd(targetLength [, padString])
'abc'.padEnd(10);          // "abc       "
'abc'.padEnd(10, "foo");   // "abcfoofoof"
'abc'.padEnd(6, "123456"); // "abc123"
'abc'.padEnd(1);           // "abc"
```

#### 17.3 函数

允许在函数参数中添加尾逗号

```js
function foo(
    bar,
    baz,
) {}
```

### 十八、ES2018

#### 18.1 展开和剩余在对象上的应用 

```JS
// ES2018
// + 展开和剩余在对象上的应用 ======================================================

const obj = { one: 1, two: 2, three: 3, four: 4, five: 5 }
const { one, four, ...rest } = obj
// one => 1, four => 4
// rest => { two: 2, three: 3, five: 5}

const obj2 = { foo: 'bar', ...rest }
// obj2 => { foo: 'bar', two: 2, three: 3, five: 5}

const obj3 = { foo: 'bar', two: 200, ...rest }
// obj3 => { foo: 'bar', two: 2, three: 3, five: 5}

const obj4 = { foo: 'bar', ...rest, two: 200 }
// obj4 => { foo: 'bar', two: 200, three: 3, five: 5}

// Object.assign({}, {}) 可以被上面替代，两者都是浅拷贝
```

####18.2 正则的增强

```js
// + 正则表达式的增强 =============================================================

// 环视
const intro = '张三是张三，张三丰是张三丰，张三不是张三丰，张三丰也不是张三'

// 向后否定
// const res = intro.replace(/张三(?!丰)/g, '李四')
// 向后肯定
const res = intro.replace(/张三(?=丰)/g, '李四')

// 'A00 B00'.replace(/(?<=A)00/g, '88')
// 'A00 B00'.replace(/(?<!A)00/g, '88')

console.log(res)

// 正则组命名
// const date = '2020-05-20'
// const reg = /(?<year>\d{4})-(?<mouth>\d{2})-(?<day>\d{2})/
// const res = reg.exec(date)
// console.log(res)
```

### 十九、MVVM案例

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <div id="app">
        {{message}}
        <div>
            {{divMessage}}
            <div>
                {{deepDivMessage}}
            </div>
            <div v-html="htmlTestMessage"></div>
            <input type="text" v-model="modeDate">{{modeDate}}
        </div>
    </div>
</body>
<script src="./myvue2.js"></script>
<script>
    let vm = new MyVue({
            el: "#app",
            data: {
                message: '测试',
                divMessage: 'div测试',
                deepDivMessage: '嵌套div',
                htmlTestMessage: "<h1>h1的测试数据</h1>",
                modeDate: 'mode绑定的数据'
            }
        })
        // console.log(vm.$options.data)
        //     //     // console.log(vm)

    // setTimeout(() => {
    //     console.log('1233213')
    //     vm.$options.data.message = '更改测试数据'
    // }, 2000)
</script>

</html>
```

```js
class MyVue extends EventTarget {
    constructor(options) {
        super()
            // console.log(options)
        this.$options = options
        this.compile()
        this.observe(this.$options.data)
    }
    observe(data) {
        let keys = Object.keys(data)
        keys.forEach(key => {
            // console.log(data, key, data[key])
            this.defineReact(data, key, data[key])
        })
    }
    defineReact(data, key, value) {
        const _this = this
        Object.defineProperty(data, key, {
            get() {
                console.log('get....')
                return value
            },
            set(newValue) {
                console.log('set...')
                let event = new CustomEvent(key, {
                    detail: newValue
                })
                _this.dispatchEvent(event)
                value = newValue;
            }
        })
    }
    compile() {
            this.el = document.querySelector(this.$options.el)
            this.compileMode(this.el)
        }
        // 渲染逻辑
    compileMode(el) {
        let childNodes = el.childNodes
        childNodes.forEach(node => {
            if (node.nodeType === 1) {
                // 标签节点
                // 得到标签节点属性
                let attrs = node.attributes
                if (attrs.length > 0) {
                    [...attrs].forEach(attr => {
                        let attrName = attr.name
                        let attrValue = attr.value
                        if (attrName.startsWith('v-')) {
                            attrName = attrName.substr(2)
                            if (attrName === 'html') {
                                node.innerHTML = this.$options.data[attrValue]
                            } else if (attrName === 'model') {
                                node.value = this.$options.data[attrValue]
                                node.addEventListener('input', e => {
                                    this.$options.data[attrValue] = e.target.value
                                })
                            }
                        }
                    })
                }
                // 判断是否存在子节点
                if (node.childNodes.length > 0) {
                    this.compileMode(node)
                }
            } else if (node.nodeType === 3) {
                // 文本节点
                let reg = /\{\{\s*(\S+)\s*\}\}/g
                let textContent = node.textContent
                if (reg.test(textContent)) {
                    let $1 = RegExp.$1
                    node.textContent = node.textContent.replace(reg, this.$options.data[$1])
                    this.addEventListener($1, e => {
                        // 重新选视图
                        // console.log("触发了修改..");
                        let oldValue = this.$options.data[$1]
                        let reg = new RegExp(oldValue)
                        node.textContent = node.textContent.replace(reg, e.detail)
                    })
                }
            }
        })

    }
}
```

