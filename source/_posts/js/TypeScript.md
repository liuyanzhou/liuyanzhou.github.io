---
title: TypeScript
date: 2020-08-05 12:14:17
categories: JS
top: false
summary: TypeScript
tags: 
 - JS
---

### TypeScript

### 一、语言类型系统

我们的编程语言可以分为根据类型安全分为：强类型语言和弱类型语言。而从类型检查可分为：静态类型和动态类型

* 强类型和弱类型
  * 强类型：
    * 有类型上的约束，不允许任意的隐式类型转换
    * 优势：1. 错误更早暴露 2. 代码智能，编码方便 3. 重构更加牢靠 4. 减少不必要的类型判断
  * 弱类型：
    * 没有类型上的约束，允许任意的隐式类型转换，灵活方便，但在现在的多人协同开发的大型web应用，这便成为它的劣势

* 静态类型和动态类型
  * 静态类型：一个变量声明时它的类型就是明确的，声明过后，他的类型就不允许再被修改
  * 动态类型：运行阶段才能够明确变量类型，而且变量的类型随时可以改变，可以说变量是没有数据类型的，变量存储的数据是有数据类型的，

  静态类型

  优点：

  * 程序编译阶段 （配合 IDE 、编译器甚至可以在编码阶段）即可发现一些潜在错误，避免程序在生产环境运行了以后再出现错误
  * 编码规范，有利于团队开发协作，也有利于大型项目开发，项目重构
  * 配置 IDE、编码器提供更强大的代码智能提示 / 检查
  * 代码即文档

  缺点：

  * 麻烦，在严格模式下我们要对每个变量进行类型标注
  * 缺少灵活性

  动态类型的优缺点与静态类型的优缺点相互对应

强类型和弱类型语言分布

![类型系统](/medias/imges/js/ts/leixin.png)

上述我们了解了语言的分类，那到底什么是类型系统呢？

类型系统包含两个重要组成部分

* 类型标注（定义、注释） - typing
* 类型检测（检查）- type-checking

类型标注：

类型标注就是在代码中给数据（变量、函数(参数、返回值)）添加类型说明，当一个变量或者函数（参数）等被标注以后就不能存储或传入与标注类型不符合的类型

有了标注，TS 编译器就能按照标注对这些进行类型合法检测

有了标注，各种编译器、IDE 等就能进行智能提示

在TS 中类型的基本语法格式为

```
数据载体:标注类型
```

TS 的类型标注，我们可分为：

* 基础的简单的类型标注
* 高级的深入的类型标注

类型检测：

顾名思义，就是对数据的类型进行检测，**注意这里,重点的是类型两字**，类型系统检测的是类型，而不是具体值，比如某个参数的取值范围 （1-100之间），我们不能依靠类型系统来完成这个检测，它应该是我们的依赖业务层具体逻辑来判断，而类型系统检测的是它的值类型是否是数字!

### 二、JS类型系统

* js没有编译环节，没有类型检测，只有在运行的时候，才能检测错误
* 它是弱类型动态类型语言

```js
let obj={}
console.log(obj.fn())
// 在语法上可行，但是在运行的时候报错
let obj = {}
setTimeOut(()=>{
    obj.fn() // 这是一件很可怕的事情，在我们测试时为检测出错误代码，会留下隐患，这是JS弱类型语言的弊端，如果是强类型语言。在语法上编译就会报错，不会遗留到运行阶段
},100000)
```

* 类型不明确，函数功能会发生改变

```js
function sum(a,b) {
    return a + b
}
sum(100,100) // 200
sum('100',100) // 100100
```

* 对象所引器的转换,将对象属性转为字符串类型

```js
const obj = {}
obj[true] = 100
console.log(obj['true']) // 100
```

小结：

由于弱类型在现代的web开发中，存在很多的弊端，所以为了解决这个弊端，市面上就出现了`flow`，`typescript`的技术让我们的 JS 在开发中的形式上变成强类型语言，之后在借助一些编译工具在转为我们能运行的 JS 脚本语言。

### 三、Flow

#### 3.1 概述

* flow是静态类型检测器
* 原理：在代码当中添加一些类型注释的方式标记每个变量和参数是什么类型，flow 根据这些类型注释，检查代码当中是否存在类型使用上的异常，从而在开发阶段就检测了类型异常的使用，从而避免了在运行阶段类型异常的错误。
* 类型注释写法：在变量后加上 `: type`进行类型注释，一定要在文件开头写上`// @flow`作为标识

```js
// @flow
function sum(a:number,b:number) {
    return a + b 
}
```

* Flow 使用

```bash
// 安装
yarn add flow-bin --dev
// 初始化 .flowconfig 文件
yarn flow init 
// 运行flow，必须在 flowconfig同目录下运行
yarn flow + 文件路径
```

* 去除类型注释

  * 使用 `flow-remove-types `（个人不推荐)

  ```bash
  // 安装
  yarn add flow-remove-types -D
  // 运行
  yarn flow-remove-types '文件路径' -d dist(处理好的文件存放目录)
  ```

  * 使用 `babel`处理 (个人推荐)

  ```bash
  // 安装
  yarn add @babel/core @babel/cli @babel/preset-flow -D
  ```

  ​      在`.babelrc`文件配置插件

  ```js
  {
      "presets":["@babel/preset-flow"]
  }
  ```

  ​	运行

  ```bash
  yarn babel '文件目录' -d dist(处理好的文件存放目录)
  ```
#### 3.2 注释类型

* 原始类型

```js
// 原始类型

// @flow
const a: string = '100'
const b: number = Infinity || NaN || 100 // number 类型可以检测 Infinity NaN 100
const c: boolean = false
const d: null = null
const e: void = undefined
const f: symbol = Symbol()
```

* 数组类型

```js
/**
 * 数组类型
 *
 * @flow
 */
// 标识arr1数组元素必须是数字
const arr1: Array<number> = [1, 2, 3]
// 标识arr2数组元素必须是数字
const arr2: number[] = [1, 2, 3]

// 元组:标识foo数组的值只有两个并类型与定义相对应
const foo: [string, number] = ['foo', 100]

```

* 对象类型

```js
/**
 * 对象类型
 *
 * @flow
 */
// 标识obj中的foo一定是string类型，bar一定是number类型，而其他属性并未限制
const obj1: { foo: string, bar: number } = { foo: 'string', bar: 100 }
// 标识foo可有可无，而bar一定得存在并未number类型，对其他属性并未限制
const obj2: { foo?: string, bar: number } = { bar: 100 }
// 标识obj3的属性key一定是string类型，值也为string类型
const obj3: { [string]: string } = {}

obj3.key1 = 'value1'
obj3.key2 = 'value2'
```

* 函数类型

```js
// 函数类型

// @flow
// 标识sum函数的参数a为number类型，b也为number类型
function sum(a: number, b: number) {
    return a + b
}

console.log(sum(100, 100))

// 标识foo的回调函数的参数一个是string 一个是number 没有返回值或为undefined
function foo(callback: (string, number) => void) {
    callback('string', 100)
}

foo(function(str, n) {
    console.log(str, n)
})
```

* 特殊类型

```js
/**
 * 特殊类型
 *
 * @flow
 */

// 字面量类型
// a的值一定为foo
const a: 'foo' = 'foo'
// type的值一定要是 success warning danger 其中的一个
const type: 'success' | 'warning' | 'danger' = 'success'

// ------------------------

// 声明类型

type StringOrNumber = string | number

const b: StringOrNumber = 'string' // 100

// ------------------------

// Maybe 类型

const gender: ?number = undefined
// 相当于
// const gender: number | null | void = undefined

```

* Mixed 和 Any : 这两个是标识任意的数据类型，但个人认为都不推荐使用，因为得我们在写一些类型判断逻辑

```js
/**
 * Mixed Any
 *
 * @flow
 */

// string | number | boolean | .... 
function passMixed (value: mixed) {
  if (typeof value === 'string') {
    value.substr(1)
  }

  if (typeof value === 'number') {
    value * value
  }
}

passMixed('string')

passMixed(100)

// ---------------------------------

function passAny (value: any) {
  value.substr(1)

  value * value
}

passAny('string')

passAny(100)
```

* 运行环境API的限制

```js
/**
 * 运行环境 API
 *
 * @flow
 */
// 获取元素，标识有就接收无就是null
const element: HTMLElement | null = document.getElementById('app')
```

![运行环境API的限制](/medias/imges/js/ts/api.png)

### 四、TypeScript

TypeScript是JavaScript的一个超集，可以说是对Javascript的一个扩展，弥补了JavaScript没有类型系统的缺陷，Ts与Js的关系：

![Ts与Js的关系](/medias/imges/js/ts/tsgn.png)

TypeScript 编写的程序并不能直接通过浏览器运行，我们需要先通过`TypeScript`编译器把 TypeScript 代码编译成 `JavaScript`代码， 在开发中，有时候项目中的依赖是ts识别不到的，编译时会报错，要求我们去安装对应的声明文件，这时我们可以通过`npm i @types/包的名字`，例如`npm i @types/node @types/koa @types/koa-router.....`，并且`TypeScript`的编译器是基于 `Node.js`的，所以我们需要先安装  `Node.js`

#### 4.1 快速上手

* 安装

```bash
yarn add typescript -D
# or 
npm i -g typescript
```

编译有两个方式：

* 直接编译

```js
yarn tsc '文件路径'
```

* 使用配置文件编译(推荐) ，或者自己新建 `tsconfig.json`

```bash
// 初始化配置文件
yarn tsc --init 
```

通过初始化的配置文件关注的属性

| 属性名    | 描述                                                         | 例子                             |
| --------- | ------------------------------------------------------------ | -------------------------------- |
| target    | 编译 TS文件的语法为几版本的Js                                | "target": "es5",   编译为ES5版本 |
| lib       | 标准库文件声明，默认是ES5标准库，我们可以添加，以适应JS新增方法 | "lib": ["ES2015","DOM","ES2017"] |
| sourceMap | 代码映射，方便调试                                           | "sourceMap": true,               |
| outDir    | 编译后的代码输出路径                                         | "outDir": "./dist",              |
| rootDir   | 编译的根路径                                                 | "rootDir": "./src",              |
| strict    | 是否采用严格模式，严格模式需要给每个参数都标注类型，而非严格模式则不需要，建议开启 | "strict": true,                  |

自定义配置文件一些有用的编译选项：

编译命令`tsc`还支持许多编译选项，下面是几个常用的

1. **--outDir** ：指定编译文件输出目录

```bash
tsc --outDir ./dist ./src/helloKaiKeBa.ts
```

2. **--target**：指定编码的代码版本目标，默认是 ES3

```bash
tsc --outDir ./dist --target ES6 ./src/helloKaiKeBa.ts
```

3. **--watch** ：在监听模式下运行，当文件发生改变的时候自动编译

```bash
tsc --outDir ./dist --target ES6 --watch ./src/helloKaiKeBa.ts
```

通过上面几个操作，我们基本可以了解 tsc 的使用了，当然大家也应该发现了，如果每次编译都输入这么一大堆的选项其实是很繁琐的，好在`TS`编译为我们提供了一个更加强大且方便的方式，编译配置文件`tsconfig.json`，我们可以把上面的编译选项保存到这个配置文件中，也就是我们上面所得`tsconfig.json`文件，自己手动创建,将上面操作配置如下：

```json
{
	"compilerOptions": {
		"outDir": "./dist",
		"target": "ES2015",
    "watch": true,
	},
  // ** : 所有目录（包括子目录）
  // * : 所有文件，也可以指定类型 *.ts
  "include": ["./src/**/*"]
}
```
有了这个单独的配置文件，我们就可以直接运行,它会自动去查找目录下的 tsconfig.json 文件

```bash
tsc
```

当我们的文件不在我们输入命令的命令行下，则我们可以指定特定配置文件，通过 `--project`或`-p`

```bash
# 特定目录下的 tsconfig.json 文件
tsc -p ./configs 
# 特定的json文件
tsc -p ./configs/ts.json
```

* 代码编译器 - vscode

vscode 和 ts 都是微软的产品，vscode 本身就是基于 TS 进行开发的，所以vscode 对 TS 有着天然友好的支持，默认情况下，TS的文件的后缀为`.ts`

```js
// ./src/helloKaiKeBai.ts
let str: string = '开课吧'
```

#### 4.2 原始类型

| 类型      | 描述                                                         |
| --------- | ------------------------------------------------------------ |
| string    | 字符串类型                                                   |
| number    | 数值类型，包含 Number 、 null、infinity                      |
| boolean   | 布尔类型                                                     |
| null      | null类型                                                     |
| void      | 用于标记函数没有返回值，或返回值为`undefined`                |
| undefined | undefined类型                                                |
| symbol    | symbol类型，但需要在配置文件的`lib`节点加上`es2015`,是因为这个是es2015新增的 |

```js
// 原始（基础）数据类型

const a: string = 'foobar'

const b: number = 100 // NaN Infinity

const c: boolean = true // false

// 在非严格模式（strictNullChecks）下，
// string, number, boolean 都可以为空
// const d: string = null
// const d: number = null
// const d: boolean = null

const e: void = undefined

const f: null = null

const g: undefined = undefined

// Symbol 是 ES2015 标准中定义的成员，
// 使用它的前提是必须确保有对应的 ES2015 标准库引用
// 也就是 tsconfig.json 中的 lib 选项必须包含 ES2015
const h: symbol = Symbol()
```

#### 4.3 作用域问题

```js
// 作用域问题

// 默认文件中的成员会作为全局成员
// 多个文件中有相同成员就会出现冲突
// const a = 123

// 解决办法1: IIFE 提供独立作用域
// (function () {
//   const a = 123
// })()

// 解决办法2: 在当前文件使用 export，也就是把当前文件变成一个模块
// 模块有单独的作用域 (推荐)
const a = 123

export {}
```

#### 4.4 空和未定义类型

因为在`Null`和`undefined`这两种类型有且只有一个值，在标注一个变量为 `null`和 `undefined`类型，那就表示该变量不能在被修改了

```js
let a:null 
let b:undefined
// ok
a=null
b=undefined
// error
a = 1
b=2
```

默认情况下`null`和`undefined`是所有类型的子类型，就是说你可以把`null`和`undefined`赋值给其他类型的变量

```js
let a:number
// ok
a = null
```

如果一个变量声明了，但是未被赋值，那么该变量的值为`undefined`，但是如果它同时也没有标注类型的话，默认类型为`any`

```js
// 类型为`number`，值为`undefined`
let a: number
// 类型为`any`,值为`undefined`
let b
```

**小技巧**

因为`null`和`undefined`都是其他类型的子类型，所以默认情况下会有一些隐藏的问题

```js
let a:number
a=null
// ok （实际运行是有问题的）
a.toFixed(1)
```

小技巧：指定`strictNullChecks` 配置为`true`,可以有效的检测`null`或者`undefined`在代码中的隐式转化，避免很多常见问题，例如下面这些隐藏较深的在生产环境中会出现的问题

```js
let a:number
a=null
// error
a.toFixed(1)
```

也可以使我们程序编写更加严谨

```js
let ele = document.querySelector('div')
// 在获取元素的方法返回的类型可以会是 null ，也就是没获取到元素，所以最好是先进行必要的判断，再进行操作
if(ele) {
    ele.style.display = 'none'
}
```

#### 4.5 对象类型(Object)

内置对象类型：在`JavaScript`中，有许多的内置对象，比如：Object、Array、Date...，我们可以通过对象的构造函数或者类进行标注，object 类型是指除了原始类型（string、number、boolean）以外的其它类型

```js
let a:Object = {}
// 数组这里标注的格式有点不太一样，后面的数组标注会详细讲解
let arr:Array<number> = [1,2,3]
let d1:Date = new Date()
```

**自定义对象类型**

另外有一种情况，许多时候，我们可能需要自定义结构的对象，这个时候，我们可以：

* 字面量标注
* 接口
* 定义类 或者 构造函数

字面量标注:

优点：方便、直接。缺点：不利于复用定义的结构和维护

```js
let a: {username: string; age: number} = {
  username: 'zMouse',
  age: 35
};
// ok
a.username;
a.age;
// error
a.gender;
```

接口：

优点：复用性高。缺点：接口只能作为类型标注使用，不能作为具体值，它只是一个抽象的结构定义，并不是实体，没有具体的功能实现，进过tsc 编译之后，会被去掉

```js
// 这里使用了 interface 关键字，在后面的接口章节中会详细讲解
interface Person {
    username: string;
    age: number;
};
let a: Person = {
    username: 'zMouse',
    age: 35
};
// ok
a.username;
a.age;
// error
a.gender;
```

类和构造函数

```js
// 类的具体使用，也会在后面的章节中讲解
class Person {
    constructor(public username: string, public age: number) {
    }
}
// ok
a.username;
a.age;
// error
a.gender;
```

优点：功能相对强大，定义实体的同时也定义了对应的类型，经过tsc编译还是会被保存。

缺点：复杂，比如只想约束某个函数接收的参数结构，没有必要去定一个类，使用接口会更加简单

```js
interface AjaxOptions {
    url: string;
    method: string;
}

function ajax(options: AjaxOptions) {}

ajax({
    url: '',
    method: 'get'
});
```

**扩展**

包装对象：这里说的包装对象其实就是 `JS`中的`String、Number、Boolean`，我们知道`string`类型和`String`类型并不一样，在`Ts`中也是一样的

```js
let a: string;
a = '1';
// error String有的，string不一定有（对象有的，基础类型不一定有）
a = new String('1');

let b: String;
b = new String('2');
// ok 和上面正好相反
b = '2';
```

#### 4.6 数组类型(Array)

Ts 中数组存储的类型是必须一致的，所以在标注数组类型的时候，同时也要标注数组中存储的数据类型

**使用泛型标注**

```js
// <number> 表示数组中存储的数据类型，泛型具体概念后面会专门讲 
let arr1:Array<number> = []
// ok 
arr1.push(100)
// error
arr1.push('kkb')
```

**简单标注**

```js
let arr2: string[] = []
// ok 
arr2.push('kkb')
// error
arr2.push(1)
```

#### 4.7 元组

元组类似数组，但是存储的元素类型不必相同，但是需要注意：

* 初始化数据格式以及对应位置标注类型必须一致
* 越界数据（新增数据）必须是元组标注中的类型之一，（标注越界数据可以不用对应顺序--联合类型）

```js
// 元组（Tuple）

export {} // 确保跟其它示例没有成员冲突

let data1:[string,number] = ['kkb',100]
// ok 新增的数据类型必须是标准类型之一
data1.push(100)
// ok 新增的数据类型必须是标准类型之一
data1.push('100')
// error 必须是定义类型之一
data.push(true)
// ----------------------------
const tuple: [number, string] = [18, 'zce']
const [age, name] = tuple
// age  =18 ; name='zce'

// ---------------------
const entries: [string, number][] = Object.entries({
  foo: 123,
  bar: 456
})
const [key, value] = entries[0]
// key => foo, value => 123
```

#### 4.8 枚举(Enum)

枚举的作用组织收集一些关联数据的方式，通过枚举我们可以给一组有关联意义的数据赋予一些友好的名字

```js
enum HTTP_CODE {
    OK=200,
    NOT_FOUND=404,
    METHOD_NOT_ALLOWED
}
// 200 
console.log(HTTP_CODE['OK'])
// 405 
console.log(HTTP_CODE['METHOD_NOT_ALLOWED'])
// error 枚举值为只读，不可修改
HTTP_CODE['OK'] = 1
```

注意事项：

* key 不能是数字
* value 可以是数字，称为 数字类型枚举，也可以是字符串，称为字符串类型枚举，但不能是其它值，默认为数字：0
* 枚举值可以省略，如果省略，则：
  * 第一个枚举值默认为：0
  * 非第一个枚举值为上一个数字枚举值 +1 
* 枚举值为只读（常量），初始化后不可修改

**字符串类型枚举**

枚举类型的值，也可以说字符串类型

```js
enum URLS {
    USER_REGISETER= '/user/register',
    USER_LOGIN = '/user/login',
    // 如果前一个枚举值类型为字符串，则后续枚举值必须手动赋值
    INDEX = 0
}
```

注意：如果前一个枚举值类型为字符串，则后续枚举项必须手动赋值

> 小技巧：枚举名称可以是大写，也可以是小写，推荐使用全大写（通常使用全大写的命名方式来标注值为常量）

```js
// 枚举（Enum）

export {} // 确保跟其它示例没有成员冲突

// 用对象模拟枚举
// const PostStatus = {
//   Draft: 0,
//   Unpublished: 1,
//   Published: 2
// }

// 标准的数字枚举
// enum PostStatus {
//   Draft = 0,
//   Unpublished = 1,
//   Published = 2
// }

// 数字枚举，枚举值自动基于前一个值自增
// enum PostStatus {
//   Draft = 6,
//   Unpublished, // => 7
//   Published // => 8
// }

// 字符串枚举
// enum PostStatus {
//   Draft = 'aaa',
//   Unpublished = 'bbb',
//   Published = 'ccc'
// }

// 常量枚举，不会侵入编译结果,在编译中会自动清除，而上面的则会保留，若没有特殊要求，则推荐使用常量枚举
const enum PostStatus {
  Draft,
  Unpublished,
  Published
}

const post = {
  title: 'Hello TypeScript',
  content: 'TypeScript is a typed superset of JavaScript.',
  status: PostStatus.Draft // 3 // 1 // 0
}

// PostStatus[0] // => Draft
```

#### 4.9 无值类型

表示没有任何数据的类型，通常用于标注无返回值函数的返回值类型，函数默认返回值类型标注为`void`

```js
function fn():void {
    // 没有return 或者 return undefined
}
```

> 在 `strictNullChecks`为`false`的情况下，`undefined`和`null`都可以赋值给`void`，但是当`strictNullChecks`为`true`的情况下，只有`undefined`才可以赋值给`void`

#### 4.10 Never类型

当一个函数永远不可能执行`return `的时候，返回的就是`never`，与`void`不同，void 是执行了 return ，只是没有值，never 是不会执行 return ，比如抛出错误而导致函数终止执行

```js
function fn():never {
    throw new Error('error')
}
```

#### 4.11 函数类型

在 javascript 函数是非常重要的，在 TS 也是如此，同样的，函数也有自己的类型标注格式

* 参数
* 返回值

```
函数名称(参数1：类型，参数2：类型.....):返回值类型 
```

``` js
function add(x:number,y:number):number {
    return x+y
}
```

```js
// 函数类型

export {} // 确保跟其它示例没有成员冲突

function func1 (a: number, b: number = 10, ...rest: number[]): string {
  return 'func1'
}

func1(100, 200)

func1(100)

func1(100, 200, 300)

// -------------匿名函数----------------------------

const func2: (a: number, b: number) => string = function (a: number, b: number): string {
  return 'func2'
}

```

#### 4.12 任意类型(弱类型)

有的时候，我们并不确定这个值到底是什么类型或者不需要对该值进行类型检测，就可以标注为`any`类型，它可以允许成为任何值，这是一种不安全的做法，不推荐这么使用，它的出现很大的原因是用于兼容老项目

```js
let a:any
```

注意:

* 一个变量声明未赋值且未标注类型的情况下，默认为`any`类型
* 任何类型值都可以赋值给`any`类型
* `any`类型也可以赋值给任意类型
* `any`类型有任意属性和方法
* 标注为`any`类型，也就意味着放弃对该值的类型检测，同时放弃IDE 的智能提示

> 小技巧：当指定`noImplicitAny`配置为`true`，当**函数参数**出现隐含的`any`类型时会报错

```js
// 任意类型（弱类型）

export {} // 确保跟其它示例没有成员冲突

function stringify (value: any) {
  return JSON.stringify(value)
}
// 任意类型都可以赋值给any类型
stringify('string')

stringify(100)

stringify(true)
// any可以赋值给任意类型
let foo: any = 'string'
let b:number 
b = foo
// any类型有任意属性和方法
foo.bar()

// any 类型是不安全的
```

#### 4.13 未知类型

unknow , 3.0版本中新增，属于安全版的 `any`,但是与`any`不同的是

* unknow 仅能赋值给 `unkonw`、`any`
* unknow 没有任何属性和方法

#### 4.14 隐式类型推导(断)

)每次都显示标注类型会比较麻烦，TS 提供了一种更加方便的特性：类型推导(断)，TS 编译器会根据当前上下文自动的推导出对应的类型标注，这个过程发生在：

* 初始化变量
* 设置函数默认参数值
* 返回函数值

```js
// 自动推断 x 为 number
let x = 1
// 不能将类型`a`分配给类型`number`
x = 'a'

// 函数参数类型，函数返回值会根据对应的默认值和返回值进行自动推断
function fn(a=1) {return a*a}
// 会将a推断为number 函数返回值推断为 number
```

#### 4.15 类型断言

有的时候，我们可能标注一个更加精确的类型（缩小类型标注范围），比如：

```js
let img = document.querySelector('#img')
```

我们可以看到 <u>img</u> 的类型为 Element ,而 Element 类型其实只是元素类型的通用类型，如果我们去访问 src 这个属性是有问题的，我们需要把它的类型标注得更加精确：<u>HTMLImageElement</u>类型，这个时候，我们就可以使用类型断言，它类似于一种类型转换

```js
let img = <HTMLImageElement>document.querySelector('#img')
```

或者

```js
let img = document.querySelector('#img') as HTMLImageElement
```

例如：

```js
let img = document.querySelector('#img')
if(img) {
    (<HTMLImageElement>img).src
    // or
    (img as HTMLImageElement).src
}
```

或者当我们明确了该变量一定是存储某个类型的数据，那么我们就可以使用类型断言的方法给他进行限制

```js
// 类型断言

export {} // 确保跟其它示例没有成员冲突

// 假定这个 nums 来自一个明确的接口
const nums = [110, 120, 119, 112]
// 未断言的时候 res的类型 可能是 number 和 undefined
const res = nums.find(i => i > 0)
// 没断言就进行数值操作，会报错
// const square = res * res

// 断言有两种方法 
// 1. 推荐
const num1 = res as number
// 2.
const num2 = <number>res // JSX 下不能使用
```

> 注意：断言只是一种预判，并不会数据本身产生实际的作用，即：类型转换，但并非真的转换了。

#### 4.16 接口

TS 的核心之一就是对值（数据）所具有的结构进行类型检查，除了一些基本类型标注，针对**对象类型**的数据，我们还可以通过`interface（接口）`来进行标注

<u>接口</u>：对复杂的对象类型进行标注的一种方式，或者给其它代码定义契约（比如：类）

接口的基础语法定义结构特别简单

```js
interface Point {
    x:number;
    y:number;
}
```

上面的代码定义了一个类型，该类型包含两个数，一个 <u>number</u>类型的 x 和 一个<u>number</u>  类型的 y,接口中多个属性之间可以使用 逗号或者分号进行分隔

我们可以通过这个接口来给一个数据进行类型标注

```js
let p1:Point = {
    x:100,
    y:100
}
```

<span style="color:red;">注意：接口是一种类型 ，不是作为值使用</span>

```js
interface Point {
    x:number;
    y:number;
}
let p1 = Point // error
```

当然，接口的定义规则远远不止这些

* 可选属性

接口也可以定义可选的属性，通过`?`来进行标注，其中的 color？ 表示该属性是可选的，成员在协议上可有可无

```js
interface Point {
    x:number;
    y:number;
    color?: string;
}
```

* 只读属性

我们还可以通过`readonly` 来标注属性为只读，当我们标注了一个属性为只读，那么该属性除了初始化以外，是不能被再次赋值的

```js
interface Point {
    readonly x: number;
    readonlu y: number;
}
```

* 任意属性

有的时候，我们希望给一个接口添加任意属性，可以通过索引类型来实现，下面的 prop名称可以自定义，将其理解为形参即可

1. 属性类型索引：

```js
interface Point {
    x:number;
    y:number;
    [prop:number] : number
}
 let p1:Point = {
     x:100,
     y:100
 }
p1[0] = 100 // ok
p1.z = 100 // error
```

2. 字符串类型索引

```js
interface Poit {
    x:number;
    y:number;
    [prop:string]:number
}

 let p1:Point = {
     x:100,
     y:100
 }
p1.z = 100 // ok
p1[0] = 100 // error
```

<span style="color:red;" >注意：数字索引是字符串索引的子类型</span>

> 注意：索引签名参数类型必须是`string or number`之一，但两者可同时出现

```js
interface Point {
    [prop1:string]:string;
    [prop2:number]:string;
}
```

当同时存在数字类型索引和字符串类型索引的时候，数字类型的值类型必须是字符串类型的值类型或子类型

```js
interface Point1 {
    [prop1:string] :string;
    [prop2:number]: number // error
}

interface Point2 {
    [prop1:string]: Object;
    [prop2:number]: Date // ok
}
```

* 使用接口描述函数

我们还可以使用接口来描述一个函数

```js
interface IFunc {
    (a:string):string
}
// 给函数命名
interface IFly {
    fly():void
}
let fn:IFunc = function(a) {return '111'}
```

> 注意：如果使用接口来单独描述一个函数，是没有`key`的，也就是不用写函数名

* 接口合并

多个同名的接口合并成一个接口

```js
interface Box {
    height:number;
    width:number;
}
interface Box {
    scale:number;
}
let box:Box = {height:5,width:6,scale:10}
```

注意：

1. 如果合并的接口存在同名的非函数成员，则必须保证他们类型一致，否则编译报错
2. 接口中的同名函数则是采用重载

#### 4.17 联合类型

联合类型也可以称为多选类型，当我们希望标注一个变量为多个类型之一时可以选择联合类型标注，类型之间是 `或`的关系

```js
function css(el:ELement,attr:string,value:string | number) {
    // ... 
}
let box = document.querySelector('.box')
// doucument.querySelector 方法返回值就是一个联合类型 HtmlElement | null 
if(box) {
    // ts会提示有返回null的可能性，加上判断更严谨
    css(box,'width','100px')
    css(box,'opacity',1)
    css(box,'opacity',[1,2]) // error
}
```

#### 4.18 交叉类型

交叉类型也可以称为合并类型，可以把多种类型合并到一起称为一种新的类型，类型之间是**并且**的关系，例如对一个对象进行扩展

```js
interface o1 {x:number;y:string}
interface o2 {z:number}

let o: o1 & o2 = Object.assign({},{x:1,y:'2'},{z:100})
```

**小技巧**

> TS 在编译过程中只会转换语法（比如扩展运算符，箭头函数等语法进行转换，对应 API 是不会进行转换的（也没必要转换，而是引入一些扩展库进行处理的），如果我们的代码中使用了`target`中没有的`API`,则需要手动进行引入，默认情况下`TS`会根据`target`载入核心的类型库
>
> `target`为`es5`时：`["dom","es5","scripthost"]`
>
> `target`为`es6`时：`["dom","es6","dom.iterable","scripthost"]`
>
> 如果代码中使用了这些默认载入库以外的代码，则可以通过`lib`选项来进行设置
>
> <http://www.typescriptlang.org/docs/handbook/compiler-options.html> 

#### 4.19 字面量类型

有的时候，我们希望标注的不是某个类型，而是一个固定值，就可以使用字面量类型，配合联合类型会更有用

```js
function setPosition(ele:Element,direction:'left'|'top'|'right'|'bottom') {
    //...
}
// ok
box && setDirection(box,'bottom')
// error
box && setDirection(box,'hehe')
```

#### 4.20 类型别名

有的时候类型标注比较复杂，这个时候我们可以类型标注起一个相对简单的名字

```js
type dir = 'left' | 'top' | 'right' | 'bottom'
function setPosition(ele:ELement, direction:dir) {
    // ...
}
```

**使用类型别名定义函数类型**

这里需要注意一下，如果使用`type`来定义函数类型，和接口有点不太相同

```js
type callback = (a:string) => string
let fn:callback = function(a) {return '123'}

// 或者直接
let fn:(a:string) => string = function(a) {return '123'}
```

**interface 与 type 的区别**

interface：

* 只能描述`Object/class/function`的类型
* 同名`interface`自动合并，有利于扩展

type:

* 不能重名
* 能描述所有数据

#### 4.21 类(Class)

* 类的基本语法: 实例对象的属性要在类先声明好数据类型，并且要在构造函数中定义与赋值

```js
// 类（Class）

export {} // 确保跟其它示例没有成员冲突

class Person {
  name: string // = 'init name'
  age: number
  
  constructor (name: string, age: number) {
    this.name = name
    this.age = age
  }

  sayHi (msg: string): void {
    console.log(`I am ${this.name}, ${msg}`)
  }
}
```

* ts新增的类的访问修饰符

| 修饰符              | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| public(公共)        | 默认属性修饰符，可以在实例对象获取，也可以在自身方法与子类中获取 |
| private(私有)       | 被private 修饰的属性，类内部访问，外部不能访问，子类中也不行 |
| protected(受保护的) | 外部不能访问，允许类中访问和子类访问                         |
| readonly(只读属性)  | 写在访问修饰符的后面，该属性不能被修改                       |

```js

export {} // 确保跟其它示例没有成员冲突

class Person {
  public name: string // = 'init name'
  private age: number
  // 只读成员
  protected readonly gender: boolean
  
  constructor (name: string, age: number) {
    this.name = name
    this.age = age
    this.gender = true
  }

  sayHi (msg: string): void {
    console.log(`I am ${this.name}, ${msg}`)
    console.log(this.age)
  }
}

const tom = new Person('tom', 18)
console.log(tom.name)
// tom.gender = false
```

* 类和接口

```js
// 类与接口

export {} // 确保跟其它示例没有成员冲突

interface Eat {
  eat (food: string): void
}

interface Run {
  run (distance: number): void
}

class Person implements Eat, Run {
  eat (food: string): void {
    console.log(`优雅的进餐: ${food}`)
  }

  run (distance: number) {
    console.log(`直立行走: ${distance}`)
  }
}

class Animal implements Eat, Run {
  eat (food: string): void {
    console.log(`呼噜呼噜的吃: ${food}`)
  }

  run (distance: number) {
    console.log(`爬行: ${distance}`)
  }
}
```

* 抽象类

使用关键字`abstract`,将子类的方法抽象成一个公关类，而这个类成为抽象类之后，这个类不能在实例化对象，只能让子类继承 

```js
// 抽象类 成为抽象类之后，这个类不能在实例化对象，只能让子类继承
export {}

abstract class Animal {
    eat(foo:string):void {
        console.log('1111')
    }
    // 定义了抽象方法则子类一定要存在
    abstract run(distance:number) : void
}

class Dog extends Animal {
    run(distance:number):void {
        console.log('1312')
    }
}

const dog = new Dog()
dog.eat('123')
dog.run(4455134)
```

* 泛类

定义的时候不明确他的类型，使用的时候在定义类型，例如Array也是一个泛类，它的元素类型是可以让我们随意定义的，在如下面的`createArrf`方法也是一个泛类

```js
// 泛类
export {}

function createNumberArray(length:number,value:number) {
    const arr = Array<number>(length).fill(value)
    return arr 
}

function createStringArray(length:number,value:string) {
    const arr = Array<String>(length).fill(value)
    return arr 
}

// 泛类
function createArr<T>(length:number,value:T) {
    const arr = Array<T>(length).fill(value)
    return arr
}

const res = createArr<string>(3,'foo')
console.log(res)
```

### 五、typeScript 函数类型

#### 5.1 函数的标注

一个函数的标注包含：

* 参数
* 返回值

```js
// 普通标注法 
function fn(a:string):string[]
// 匿名函数赋值会根据变量的定义来推断自己的类型
let fn:(a:string) => string = function(a) {return ''}

// 别名标注
type callback = (a:string)=> string 
let fn:callback = function(a) {return ''}

// 接口标注
interface ICallBack {
    (a:string):string
}
let fn:ICallBack = function(a) {return ''}
```

#### 5.2 可选参数与默认参数

* 可选参数

通过参数名后面添加`?`来标注该参数是可选的

``` js
let div = document.querySelector('div')
function css(el:HTMLElement,attr:string,val?:any) {
    
}
// 设置
div && css(div,'width','100px')
div && css(div,'width')
```

* 默认参数

  我们还可以给参数设置默认值

  * 有默认值的参数也是可选的
  * 设置了默认值的参数可以根据自动推断类型

```js
function sort(items:Array<number>,order="desc") {}
sort([1,2,3])

// 也可以通过联合类型来限制取值
function sort(items:Array<number>,order:'desc'|'asc'="desc") {}
// ok 
sort([1,2,3])
sort([1,2,3],'asc')
// error
sort([1,2,3],'abc')
```

* 剩余参数

剩余参数是一个数组，所以标注的时候一定要注意

```js
interface IObj {
    [key:string] : any
}

function merge(target:IObj,...others:Array<IObj>) {
    console.log(others) // [{y: 2}, {z: 3}]
    return Object.assign(target,...others)
}

let newObj = merge({x: 1}, {y: 2}, {z: 3});
```

#### 5.3函数中的 **this**

无论是`JavaScript`还是`TypeScript`，函数中的`this`都是我们需要关心的，那函数中的`this`的类型该如何进行标注呢?

1. 普通函数

对于普通函数而已，`this`是会随着调用环境的变化而变化，使用默认情况下，普通函数中的 `this`会标注为`any`，但我们可以在函数的第一个参数位（实际不占参数位）上显示的标注 this 的类型

```js
interface T {
    a:number,
    fn:(x:number)=>void
}

let obj1 = {
    a:1,
    fn(x:number) {
        // any类型
        console.log(this)
    }
}

// 通过对this的标注，这样我们在访问不是this下的属性时会有错误提示，没有this标注，由于是any类型故不会有错误提示
let obj2:T = {
    a:1,
    fn(this:T,x:number) {
        // 通过第一个参数标注 this 的类型，它对实际参数不会有影响
        console.log(this)
    }
}
obj2.fn(1)
```

2. 箭头函数

箭头函数的`this`不能像普通函数那样进行标注，它的`this`标注类型取决于它所在的作用域`this`的标注类型

```js
interface T {
    a:number,
    fn:(x:number) => void
}
let obj2:T = {
    a:2,
    fn(this:T) {
        return () => {
            // T ,其实还是像我们以前查找this的规则一样，看最近的function函数的调用情况(this情况)
            console.log(this)
        }
    }
}
```

#### 5.4 函数重载

有的时候，同一个函数会接收不同类型的参数返回不同类型的返回值，我们可以使用函数重载来实现，通过下面的例子来体会一下函数重载

```js
function showOrHinde(ele:HTMLElement,attr:string,value:'block'|'none'|number) {
    // 
}
let div = document.querySelector('div')
if(div) {
    showOrHide(div,'display','none')
    showOrHide(div,'opacity',1)
    // error，这里是有问题的，虽然通过联合类型能够出来同时接收不同类型的参数，但是多个参数之间是一种组合的模式(或的关系)，而我们需要的是一种对应关系，如何解决呢？函数重载应运而生
    showOrHide(div,'display',1)
}
```

我们来看一下函数重载

```js
function showOrHide(ele: HTMLElement, attr: 'display', value: 'block'|'none'):any
function showOrHide(ele: HTMLElement, attr: 'opacity', value: number):any
// 一般函数具体实现方式的参数标注为any
function showOrHide(el: HTMLElement, attr: any, value: any) {
    // 
}
let div2 = document.querySelector('div');
if (div2 ) {
    // ok
    showOrHide(div2, 'display', 'block');
    showOrHide(div2, 'display', 'none');
    showOrHide(div2, 'opacity', 1);
    // error 因为 opacity 的组合接收一个数字
    showOrHide(div2, 'opacity', 'block');

}
```

重载函数类型只需要定义结构，不需要实体，类似接口，例如：下面的接收参数个数不一样，这就得需要含重载而不能使用联合类型

```js
interface PlainObject {
    [key: string]: string|number;
}

function css(ele: HTMLElement, attr: PlainObject);
function css(ele: HTMLElement, attr: string, value: string|number);
// 一般函数具体实现方式的参数标注为any
function css(ele: HTMLElement, attr: any, value?: any) {
    if (typeof attr === 'string' && value) {
        ele.style[attr] = value;
    }
    if (typeof attr === 'object') {
        for (let key in attr) {
            ele.style[attr] = attr[key];
        }
    }
}

let div = document.querySelector('div');
if (div) {
    css(div, 'width', '100px');
    css(div, {
        width: '100px'
    });

    // error，如果不使用重载，这里就会有问题了
    css(div, 'width');
}
```

#### 六、typescript 类

面向对象编程中一个重要的核心就是:`类`，当我们使用面向对象的方式进行编程的时候，通常会首先去分析具体要实现的功能，把特性相似的抽象成一个一个的类，然后通过这些类实例化出来的具体对象来完成具体的业务需求

#### 6.1 类的基础

在类的基础中，包含下面几个核心的知识点，也是`TypeScript`与 `ECMAScript2015+` 在类方面共有的一些特性

* `class`关键字
* 构造函数：`constructor`
* 成员属性定义
* 成员方法
* this关键字

出来以上的共同特性以外，在`TypeScript`中还有许多`ECMAScript`没有的，或当前还不支持的一些特性，比如：抽象
**class**

通过class 就可以描述和组织一个类的结构，语法

```js
// 通常类的名称我们会使用 大驼峰命名规则，也就单词首字母大写
class User {
    // 类的特性都定义在{}内部
}
```

#### 6.2 构造函数

通过 class 定义了一个类以后，我们可以通过`new`关键字来调用该类从而得到该类型的一个具体对象：也就是实例化

为什么类可以像函数用于去调用呢？其实我们只想的并不是这个类，而是类中包含的一个特殊函数：构造函数 -`constructor`

```js
class User {
    constructor() {
        console.log('实例化')
    }
}
let user1 = new User;
```

* 默认情况下，构造函数是一个空函数
* 构造函数会在类被实例化的时候调用
* 我们定义的构造函数会覆盖默认构造函数
* 如果在实例化（new）一个类的时候无需传入参数，则可以省略`()`
* 构造含`constructor`不允许有`return`和返回值类型标注的（因为要返回实例对象）

通常情况下，我们会把一个类实例化的时候的初始化相关代码写在构造函数中，比如对类成员的属性的初始化赋值

#### 6.3 成员属性与方法定义

在TS 中类的属性成员需要在开头定义类型先，例如下面的 id username，定义完才能通过this进行绑定到类内部

```js
class User {
  id: number;
  username: string;
  
  constructor(id: number, username: string) {
    this.id = id;
    this.username = username;
  }
	
	postArticle(title: string, content: string): void {
    console.log(`发表了一篇文章： ${title}`)
  }
}

let user1 = new User(1, 'zMouse');
let user2 = new User(2, 'MT');

```

this关键字:在类的内部，我们可以通过`this`关键字来访问类的成员属性和方法

```js
class User {
    id:number;
	username:string;	
	
    postArticle(title:string,content:string):void {
        // 在类的内部可以通过`this`来访问属性和方法
        console.log(`${this.username}发表了一篇文章：${title}`)
    }
}
```

构造函数参数属性

因为在构造函数中对类成员属性进行传参赋值初始化是一个比较常见的场景，所以`ts`提供了一个简化操作：给构造函数参数添加修饰符来总结生成成员属性，关键字 **public**，就是类的默认修饰符，表示该成员可以在任何地方进行读写操作

```js
// 未使用关键字时标注属性
class User {
    id:number
    username:string
    constructor(id:number,username:string) {
        this.id = id
        this.username = username
        console.log('这是构造函数，对类中成员属性进行初始化赋值')
    }
    postArticle(title:string,content:string) {
        console.log(`${this.username} 发表了一篇文章:${title}`)
    }
} 

// 使用关键字时标注属性
class User {
    /**
     * 但我们给构造函数参数设置了访问修饰符 public 那么ts会做如下两件事
     *  - 给当前类添加同名的成员属性
     *  - 在类的实例化的时候，会把传入的参数值赋值给对应的成员属性
     * 
     */
    constructor( public id:number,public username:string) {
        this.id = id
        this.username = username
    }
        postArticle(title:string,content:string) {
        console.log(`${this.username} 发表了一篇文章:${title}`)
    }
}
8
```

#### 6.4 继承

在`ts`中，也是通过`extends`关键字来实现类的继承

```js
class VIP extends User {
    
}
```

**super关键字**：在子类中，我们可以通过`super`来引用父类

* 如果子类没有重写构造函数，则会在默认的`constructor`中调用`super()`
* 如果子类有自己的构造函数，则需要在子类构造函数中显示的调用父类构造函数：`super(// 参数)`
* 在子类构造函数中只有在`super(//参数)`之后才能访问`this`
* 在子类中，可以通过`super`来访问父类的成员属性和方法
* 通过`super`访问父类的同时，会自动绑定上下文对象为当前子类`this`

```js
class VIP extends User {

    constructor(
    id: number,
     username: string,
     public score = 0
    ) {
        super(id, username);
    }

    postAttachment(file: string): void {
        console.log(`${this.username} 上传了一个附件： ${file}`)
    }
}

let vip1 = new VIP(1, 'Leo');
vip1.postArticle('标题', '内容');
vip1.postAttachment('1.png');
```

#### 6.5 方法的重写与重载

默认情况下，子类成员方法集成自父类，但是子类也可以对它们进行重写与重载

* 重写 :重新实现了父类的方法，参数的个数和参数的类型 

```js
class User {
    constructor(
    public id: number,
     public username:string
    ){
    }
    postArticle(title:string,content?:string) {
        console.log(`${this.username}发表了111一篇文章:${title}`)
    }
}

class VIP extends User {
    constructor(
    id:number,
     username:string,
     public score:number
    ) {
        super(id,username)
        this.score = 10
    }
    // postArticle重新实现了父类的方法，参数的个数和参数的类型需要一样，覆盖父类
    postArticle(title:string,content?:string) {
        this.score++
        console.log(`${this.username}发表了一篇文章：${title}，积分：${this.score}`)
    }
    postAttachment(file:string) {
        console.log(`${this.username}:上传了一个附件`)
    }
}

let vip = new VIP(1,'mt',0)
vip.postArticle('这么写好ts','hahh')
console.log(vip)
export {}
```

* 重载：

```js
class User {
    constructor(
    public id: number,
     public username:string
    ){

    }
    postArticle(title:string,content?:string) {
        console.log(`${this.username}发表了111一篇文章:${title}`)
    }
}

class VIP extends User {
    constructor(
    id:number,
     username:string,
     public score:number
    ) {
        super(id,username)
        this.score = 10
    }
    // 实现重载:写一套套的规则与传入不同的参数进行对应，还得有一个实现体
    postArticle(title:string,content:string):void;
    postArticle(title: string, content: string, file: string): void;
    postArticle(title:string,content:string,file?:string) {
        super.postArticle(title, content);
        if(file) {
            this.postAttachment(file)
        }
    }

    postAttachment(file:string) {
        console.log(`${this.username}:上传了一个附件`)
    }
}

let vip = new VIP(1,'mt',0)
vip.postArticle('这么写好ts','hahh','1.png')
console.log(vip)
export {}
```

#### 6.6 修饰符

有的时候，我们希望对类成员（属性、方法）进行一定的访问控制，来保证数据的安全，通过`类修饰符`可以做到这一点，目前	TypeScript 提供了四种修饰符

1. public ：共有、默认
2. protected：受保护的
3. private：私有
4. readonly：只读

* public 修饰符

这个是类成员的默认修饰符，它的访问级别为：自身、子类、类外

* protected 修饰符

它的访问级别：自身、子类

* private 修饰符

它的访问级别：自身

* readonly 修饰符

只读修饰符只能针对成员属性使用，且必须咋声明时或构造函数里被初始化，它的访问级别为：自身、子类、类外，它与前面几个修饰符可以同时出现，要在他们其后放置

```js
class User {
  
  constructor(
  	// 可以访问，但是一旦确定不能修改
  	public readonly id: number,
    // 可以访问，但是不能外部修改
    protected username: string,
    // 外部包括子类不能访问，也不可修改
    private password: string
  ) {
    // ...
  }
	// ...
}

let user1 = new User(1, 'zMouse', '123456');
```

#### 6.7 寄存器

有的时候，我们需要对类成员`属性`进行更加细腻的控制，就可以使用 `寄存器`来完成这个需求，通过`寄存器`，我们可以对类成员属性的访问进行拦截并加以控制，更好的控制成员属性的设置和访问边界，寄出去分为两种

1. getter：访问控制器，当访问指定成员属性时调用
2. setter：设置控制器，当设置指定成员属性时调用

```js
class VIP {
    constructor(id:number,username:string,password:string) {
    }

    set password(password:string) {
        if(password.length >=6) {
            console.log('大于6')
            this.password = password
        }
    }
    get password() {
        return '***'
    }
}

let vip = new VIP(1,'lyz','123')
console.log(vip.password) // 会去调用 password 的get寄存器
vip.password = '123123' // 会去调用 password 的 set寄存器
```

#### 6.8 静态成员

我们类的成员分为是类本身的和实例对象的，静态成员要用关键字`static标注`，当我们定义成员的时候，区分某成员是静态还是实例的： 

* 该成员属性或方法是类型的特征还是实例化对象的特征 
* 如果一个成员方法中没有使用或依赖 `this` ，那么该方法就是静态的 

```js
type IAllowFileTypeList = 'png'|'gif'|'jpg'|'jpeg'|'webp';

class VIP extends User {

    // static 必须在 readonly 之前
    static readonly ALLOW_FILE_TYPE_LIST: Array<IAllowFileTypeList> = ['png','gif','jpg','jpeg','webp'];

constructor(
    id: number,
    username: string,
    private _allowFileTypes: Array<IAllowFileTypeList>
) {
    super(id, username);
}

info(): void {
    // 类的静态成员都是使用 类名.静态成员 来访问
    // VIP 这种类型的用户允许上传的所有类型有哪一些
    console.log(VIP.ALLOW_FILE_TYPE_LIST);
    // 当前这个 vip 用户允许上传类型有哪一些
    console.log(this._allowFileTypes);
}
}

let vip1 = new VIP(1, 'zMouse', ['jpg','jpeg']);
// 类的静态成员都是使用 类名.静态成员 来访问
console.log(VIP.ALLOW_FILE_TYPE_LIST);
this.info();
```

- 类的静态成员是属于类的，所以不能通过实例对象（包括 this）来进行访问，而是直接通过类名访问（不管是类内还是类外）
- 静态成员也可以通过访问修饰符进行修饰
- 静态成员属性一般约定（非规定）全大写

#### 6.9 抽象类

有的时候，一个基类（父类）的一些方法无法确定具体的行为，而是由继承的子类去实现，看下面的例子：现在前端比较流行的组件化设计，比如我们需要每个子组件的都有组件的 render 渲染方法，而下面这么写，并未在子组件的render 方法拼错或者子组件内部并未render 方法而给我们报如何错误信息，这就会在生产环境下留下隐藏bug。由此我们出现了抽象来规范类的成员定义 ,我们希望子类应该有什么是有子类传给父类

```js
class Component {
    props:any
    state:any
    constructor(props:string) {
        console.log(props)
        this.props = props
    }
render(){
    // ....
} 
}

class MyComponent extends Component {
    constructor(props:string) {
        super(props)
    }

    // render() {
    //     // this.props.val
    //     return '<myComponent />'
    // }
}
let myComponent = new MyComponent('123')
myComponent.render()
```

抽象类使用`abstract`关键字来修饰，将上面改写为

```js
abstract class Component {
    props:any
    state:any
    constructor(props:any) {
        console.log(props)
        this.props = props
    }
	abstract render():string 
}

class MyComponent extends Component {
    constructor(props:any) {
        super(props)
    }

    render() {
        // this.props.val
        return '<myComponent />'
    }
}
let myComponent = new MyComponent({val:1})
myComponent.render()
```

使用抽象类有一个好处：约定了所有继承子类的所必须实现的方法，使类的设计更加规范

使用注意事项：

1. abstract 修饰的方法不能有方法体
2. 如果一个类有抽象方法，那么该类也必须为抽象的
3. 如果一个类是抽象的，那么就不能使用 new 进行实例化（因为抽象类表明该类有未实现的方法，所以不允许实例化）
4. 如果一个子类继承了一个抽象类，那么该子类就必须实现抽象类中的所有抽象方法，否则编译不通过，或者该类还得声明未抽象的

#### 6.9 类与接口

在前面我们已经学习了接口的使用，通过接口，我们可以为对象定义一种结构和契约。我们还可以把接口与类进行结合，通过接口，让类去强制符合某种契约，从某个方面来说，当一个抽象类中只有抽象的时候，它与接口没有太大区别了，这个时候，我们更推荐通过接口的方式来定义契约

接口与类的区别：

1. 抽象类编译后还是会产生实体代码，而接口不会
2. ts 只支持当继承，即一个子类只能继承一个父类，但是一个类可以实现多个接口
3. 接口不能有实现体，抽象类可以

**implements关键字**

我在一个类中使用接口并不是使用`extends`关键字，而是`implements`

* 与接口类似，如果一个类`implements`了一个接口，那么必须实现该接口中定义的契约
* 多个接口`,`分隔
* `implements`与`extends`可同时存在

```js
abstract class Component {
    props:any
    state:any
    constructor(props:any) {
        console.log(props)
        this.props = props
    }
    abstract render():string 
}

interface ILog {
    getInfo():string
}

interface IStorage {
    save(data:string):void
}

class MyComponent extends Component implements ILog,IStorage {
    constructor(props:IMyComponentProps) {
        super(props)
    }

    render() {
        return '<myComponent />'
    }
    getInfo() {
        console.log('sss')
        return '123'
    }
    save(data:string) {}
}

let myComponent = new MyComponent({val:1})
myComponent.render()
myComponent.getInfo()
```

接口也可以继承,概念与我们类的继承类似

```js
interface IStorage {
    save(data:string):void
}

interface ILog extends IStorage {
    getInfo():string
}
```

#### 6.10类与对象类型

当我们在	 TypeScript 定义了一个类的时候，其实同时定义两个不同的类型

* 类类型
* 对象类型

首先，对象类型好理解，就是我们的 new 出来的实例类型

那类类型是什么，我们知道 JavaScript 中的类，或者说是 TypeScript 中的类其实本质上还是一个函数，当然我们也称为构造函数，那么这个类或者这个构造函数本身也是有类型的，那么这个类型就是类的类型

```js
class Person {
    // 属于类的
    static type = '人';

// 属于实例的
name: string;
age: number;
gender: string;

// 类的构造函数也是属于类的
constructor( name: string, age: number, gender: '男'|'女' = '男' ) {
    this.name = name;
    this.age = age;
    this.gender = gender;
}

public eat(): void {
    // ...
}

}

let p1 = new Person('zMouse', 35, '男');
p1.eat();
Person.type;
```

上面例子中，有两个不同的数据

* `Person`类（构造函数）
* 通过`person`实例化出来的对象`p1`

对应的也有两种不同的类型

* 实例的类型（person）
* 构造函数的类型（typeof person）

用接口的方式描述如下

```js
// new Person后 得到的实例化对象被规范的接口
interface Person {
    name:string;
    age:string;
    gender:string;
    eat():void;
}
// Person 类接口
interface PersonConstructor {
    // new 表示它是一个构造函数
    new (name:string,age:number,gender:'男'|'女') : PersonInstance;
    type:string
}
```

在使用的时候要格外注意：

```js
function fn1(arg: Person /* 如果希望这里传入的Person的实例化对象*/ ) {
    arg.eat()
}
// 传入实例化对象类型
fn1(new Person('',1,'男'))

function fn2(arg:typeof Person /*如果希望传入的Person构造函数*/) {
    new arg('',1,'男')
}
// 传入类类型
fn(Person)
```

### 六、类型系统深入

#### 6.1 类型保护

我们通常在`JavaScript`中通过判断来处理一些逻辑，在`TypeScript`中这种条件语句块还有另外一个特性：根据判断逻辑的结果，缩小类型范围（有点类似断言），这种特性称为`类型`保护，触发条件：

1. 逻辑条件语句块：if、else、elseif
2. 特定的一些关键字：typeof、instanceof、in....

* typeof

我们知道 typeof 可以返回某个数据类型，在TS在 if 、 else 代码块中能够把 typeof 识别为类型保护，推断出适合的类型

```js
function fn(a:string|number) {
    // error: 不能保证a就是字符串
    a.substring(1)
    // ok
    if(typeof a==='string') {
        a.substring(1)
    }else {
        a.toFixed(1)
    }
}
```

* instanceof

与`typeof`类似的，`instanceof`也可以被 TS 识别为类型保护

```js
function fn(a:string|Array<any>) {
    if(a instanceof Array) {
        a.push(1)
    }else {
        a.getFullYear()
    }
}
```

* in

```js
interface IA {
    x:string;
    y:string;
}

interface IB {
    a:string;
    b:string;
}

function fn(arg:IA|IB) {
    if('x' in arg) {
        // ok
        arg.x
        //error 
        arg.a
    }else {
        //ok 
        arg.a
        //error
        arg.x
    }
}
```

* 字面量类型保护

如果类型为字面量类型，那么还可以通过改字面量类型的字面量值进行推断

```js
interface IA {
    type:'IA';
    x:string;
    y:string;
}
interface IB {
    type:'IB';
    a:string;
    b:string;
}

function fn(arg:IA|IB) {
    if(arg.type === 'IA') {
        // ok
        arg.x
        // error
        arg.a
    }else {
        // ok
        arg.a
        // error
        arg.x
    }
}
```

* 自定义类型保护

有的时候，以上的一些方法并不能满足一些特殊情况，则可以自定义类型保护规则

```js
function canEach(data:any):data is ELement[]|NodeList {
    return data.forEach !== undefined
}
function fn2(elements:Element[]|NodeList|Element) {
    if(canEach(element)) {
        elements.forEach((el:Element)=>{
            el.classList.add('box')
        })
    }else {
        elements.classList.add('box')
    }
}
```

`data is Element[]|NodeList` 是一种类型谓词，格式为:`xx i xx`,返回这种类型的函数就可以被`TS`识别为类型保护

#### 6.2 类型操作

TS 提供了一些方法来操作类型这种数据，但是需要注意的是，类型数据只能作为类型来使用，而不能作为程序中的数据，这是两种不同的数据，一个用在编译检测阶段，一个用于程序执行阶段

* typeof

在 TS 中，typeof 有两种作用：

1. 获取数据的类型
2. 捕获数据的类型

```js
let str = 'kkb'
// 如果是let，则是把`string`作为值给t ----> 获取数据类型
let t = typeof str
// 如果是type，则是把`string`作为类型给myType ---> 捕获数据类型
type MyType = typeof str

let str2:MyType = 'KKB' 
let str3:typeof str = 'kkb' // 直接获取str的类型来定义str3的类型
```

* keyof

获取类型的所有`key`的集合

```js
interface Person {
    name:string;
    age:number;
}
type personKeys = keyof Person;
// 等同：type personKeys = 'name' | 'age'

let p1 = {
    name:'lyz',
    age:20
}
function getPersonVal(k:personKeys) {
    return p1[k];
}
/**
等同：
function getPersonVal(k:'name'|'age') {
	return p1[k];
}
*/
getPersonVal('name') // 正确
getPersonVal('gender') // 错误
```

in

针对类型进行操作的话，内部使用的`for....in`对类型进行遍历

```js
interface Person {
    name:string;
    age:string;
}
type personKeys = keyof Person;
type newPerson = {
    [k in personKeys]:number
    /*
    	等同 [k in 'name'|'age'] : number
    	也可以写成
    	[k in keyof Person]:number
    */
}
```

注意：in 后面的类型必须是`sting`或者`number`或者`symbol`

#### 6.3 类型兼容

TS 的类型系统是基于结构子类型的，它与名义类型（如:java）不同（名义类型的数据类型兼容或等价性是通过明确的声明或类型的名称来决定的）。这种基于结构子类型的类型系统是基于组成结构的，只要具有相同类型的成员，则两种类型即为兼容的

```js
class Person {
 	name: string;
 	age: number; 
}
class Cat {
	 name: string;
	 age: number; 
}
function fn(p: Person) {
	 p.name; 
}
let xiaohua = new Cat();
// ok，因为 Cat 类型的结构与 Person 类型的结构相似，所以它们是兼容的
fn(xiaohua);
```

为了解决这个问题，我们一般都会选用接口来 implements 给类加上特殊的标记

```js
interface Ifly {
    fly():void
}

class Person implements Ifly {
    name: string;
	age: number; 
	fly():void {
    return 
	}
}

class Cat {
    name: string;
	age: number; 
}
function fn(p: Person) {
    p.name;
}
let xiaohua = new Cat();
// error: 由于Perosn上扩展了`Ifly`
fn(xiaohua)
```

### 七、泛型

、为什么使用泛型：许多时候，标注的具体类型并不能确定，比如一个函数的参数类型

```js
function getVal(obj,k) {
    return obj[k]
}
```

上面的函数，我们想实现的是获取一个对象指定的 k 所对应的值，那么四级使用的时候，obj 的类型是不确定的，自然 k 的取值范围也是不确定的，它需要我们在具体调用的时候才能确定，这个时候这种定义过程不确定类型的需求就可以通过泛型来解决

#### 7.1 泛型的使用 - 函数

所谓的泛型，就是给可变的类型定义变量

```js
function getVal<T>(obj:T,k:keyof T) {
    return obj[k]
}

let obj1 = {
    x:1,
    y:2
}

let obj2 = {
    username:'zs',
    age:20
}

getVal<typeof obj1>(obj1,'x')
getVal<typeof obj2>(obj2,'username')
```

#### 7.2 泛型的使用-类

```js
class Component<T1,T2> {
    props:T1;
    state:T2;
    constructor(props:T1) {
        this.props = props
    }
}

interface IMyComponentProps {
    val:number
}
interface IMyComponentState {
     x: number; 
}

class MyComponent extends Component<IMyComponentProps,IMyComponentState> {
    constructor(props:IMyComponentProps){
        super(props)
    }
    render() {
       this.props.val
        return '<myComponents />'
    }
}

let myComponent = new MyComponent({val: 1});
myComponent.render()
```

#### 7.3 泛型的使用 - 接口

我们还可以在接口中使用泛型，后端提供了一些接口，用于返回一些数据，依据这些返回的数据格式定义如下接口，下面的例子是根据用户获取不同的数据返回不同的结果。

```js
interface IResponseData<T> {
    code:number,
    message?:string,
    data:T
}

// 用户接口
interface IRespnseUserData {
    id:number,
    username:string,
    email:string
}

// 文章接口
interface IResponseArticleData {
    id:number,
    title:string,
    author:IRespnseUserData
}

async function getData<U>(url:string) {
    let response = await fetch(url)
    let data:Promise<IResponseData<U>> = await response.json()
    return data
}

(async function() {
    let userData = await getData<IRespnseUserData>('/user')
    userData.code
    let articleData = await getData<IResponseArticleData>('/article')
    articleData.data.author.email
})()
```

### 八、TS 的模块系统

虽然早期的时候，TS 有一套组件的模块系统实现，但是随着更新，以及`JavaScript`模块化的日趋成熟，TS 对 ESM 模块系统的支持也越来越完善

模块：

无论是 `JavaScript`还是 `TypeScript`都是以一个文件作为模块最小单位

* 任何一个包含了顶级`import`或者`export`的文件都被当成一个模块
* 相反的一个文件不带有顶级的`import`或者`export`，那么它的内容就是全局可见的，整个项目可见的

```js
// a.ts
let a1 = 100
let a2 = 200
// b.ts
// ok 100
console.log(a1)
// error 
let a2 = 300
```

> 不推荐使用全局模式，因为它会容易造成代码命名冲突（全局变量污染）

文件模块：任何一个包含了顶级`import`或者`export`的文件都会当做一个模块，在`TS`中也称为外部模块

模块语法：TS 与 ESM 语法类似

导出模块内部数据：使用`export`导出模块内部数据（变量、函数、类、类型别名、接口....）

导入外部模块数据：使用`import`导入外部模块数据

#### 8.1 模块编译

TS  编译器也能够根据相应的编译参数，把代码编译成指定的模块系统使用的代码，通过编译文件的 **module**选项：

在TS 编译选项中，module 选项是用来指定生成那个模块系统使用的代码，可设置的值有：`none`、`commonjs`、`amd`、`umd`、`es6/es2015/esnext`、`System`

* target === "es3" or "es5" ： 默认使用`commonjs`
* 其他情况，默认`esm`

#### 8.2 模块导出默认值的问题

如果一个模块没有默认导出

```js
// m1.js
export let obj = {
    x:1
}
```

则在引入该模块的时候，需要使用下列一些方式来导入

```js
// main.js
// error :提示 m1 模块没有默认导出
import v from './m1'

// 可以简单的使用如下方法
import {obj} from './m1'
console.log(obj.x)
// or
import * as m1 from './m1'
console.log(m1.obj.x)
```

#### 8.3 加载非`TS`文件

有的时候，我们需要引入一些 js 的模块，比如导入一些第三方的使用的 js 而非 ts 编写的模块，默认情况下 `tsc`是不对非ts模块文件进行处理的，但我们可以通过`allowJs`选项开启该选项

```josn
{
    "compilerOptions": {
        "outDir": "./dist",
        "target":"ES6",
        "watch": true,
        "module": "commonJS",
        "allowJs": true
    },
    "include": ["./src/**/*"]
}
```

#### 8.4 非`ESM`模块中的默认值问题

在`ESM`中模块可以设置默认导出值

```js
export default 'kkb'
```

但是在`commonJS`、`AMD`中时没有默认值设置的，它们导出的是一个对象（`exports`）

```js
module.exports.obj = {
    x:100
}
```

在 TS 中导入这种模块的时候会出现`模块没有默认导出的错误提示`

简单的一些做法

```js
import * as m from './m1.js'
```

通过配置选项解决

* `allowSyntheticDefaultImports`：设置为`true`,允许从没有设置默认导出的模块中默认导入，虽然这个配置可以解决编译过程中的检测问题，但是编译后的具体要运行代码还是有问题，我们可以通过配置 `esModuleInterop`为true来解决
* `esModuleInterop`:设置为`true`,则在编译的同时生成一个`__importDefault`函数，用来处理具体的`default`默认导出

```json
{
    "compilerOptions": {
        "outDir": "./dist",
        "target":"ES6",
        "module": "commonjs",
        "allowJs": true,
        "allowSyntheticDefaultImports": true,
        "esModuleInterop": true
    },
    "include": ["./src/**/*"]
}
```

> 注意：以上的设置只有当`module`不为`esm`规范的情况下有效，因为esm都有默认的导入导出模块，我们不需要处理

#### 8.5以模块的方式加载 JSON 格式的文件

TS 2.9+ 版本添加了一个新的编译选项：`resolveJsonModule`,它允许我们把一个`JSON`文件作为模块进行加载

* resolveJsonModule ：设置为`true`，可以吧`json`文件作为一个模块进行解析
```json
{
    "compilerOptions": {
        "outDir": "./dist",
        // 编译成es6代码规范
        "target":"ES6",
        // 编译成commonjs模块规范
        "module": "commonjs",
        // 允许加载JS文件
        "allowJs": true,
        // 处理非esm默认导出数据的规范
        "allowSyntheticDefaultImports": true,
        // 处理非esm默认导出数据的规范
        "esModuleInterop": true,
        // 在这配置json
        "resolveJsonModule": true
    },
    "include": ["./src/**/*"]
}
```

data.json
```json
{
    "name": "zMouse",
    "age": 35,
    "gender": "男"
}
```
ts文件
```js
import * as userData from './data.json'
console.log(userData.name)
```

### 九、模块解析策略

模块解析是指：编译器在查找导入模块内容时，所遵循的流程

相对与非相对模块导入

相对导入：是以`/`、`./`、`../`开头的引用

```js
// 导入根目录下的 m1 模块文件
import m1 from './m1'
// 导入当前目录下的 mods 目录下的m2模块文件
import m2 from './mods/m2'
// 导入商机目录下的m3模块文件
import m3 from '../m3'
```

非相对导入：所有的其他形式的导入被当作非相对的

```js
import m1 from 'm1'
```

模块解析策略：为了兼容不同的模块系统（`commonJS`、`ESM`）、`TypeScript`支持两种不同的模块解析策略：`Node`、`Classic`,当`--module`选项为：`amd`、`System`、`ES2015`的时候，默认为`Classic`,其他情况为`Node`

我们还可以通过`moduleResolution`选项来手动指定解析策略

```json
// tsconfig.json
{
    ...,
    "moduleResoltion":"node"
}
```

#### 9.1 classic 模块解析策略

该策略是`TypeScript`以前的默认解析策略，它已经被新的`Node`策略所取代 ，现在使用该策略主要是为了向后兼容

* 相对导入：从引入该模块的文件目录下寻找引入的文件，例如这里的 b 文件会去 m1文件查找，无就报错

```js
// /src/m1/a.js
import b from './b'
```

* 非相对导入：从引入该模块的文件目录开始依次向上级目录变量查找，直到根目录为止，例如这的 b.js  文件会以 m1目录查找，在往src目录下进行查找，否则继续往上寻找

```js
// /src/m1/a.ts
import b from 'b'
```

#### 9.2 node 模块解析策略

这部分详细看我的node篇章有写require的查找规则

该解析策略是参照了 node.js 的模块解析机制

* 相对导入

```js
// node.js
// /src/m1/a.js
import b from './b'
```

在`classic`中，模块只会按照单个的文件进行查找，但是在 node.js 中，会首先按照单个文件进行查找，如果不存在，则会按照目录进行查找

1. /src/m1/b.js
2. /src/m1/b/package.json中'main'中指定的文件
3. /src/m1/b/index.js

* 非相对导入

```js
// node.js
// /src/m1/a.js
import b from 'b'
```

对于非相对导入模块，解析是很特殊的，node.js 会在这一个特殊文件夹`node_modules`里查找，并且在查找过程中从当前目录的`node_modules`目录下逐级向上级文件夹进行查找

1. /src/m1/node_modules/b.js
2. /src/m1/node_modules/b/package.json中'main'中指定的文件
3. /src/m1/node_modules/b/index.js
4. /src/node_modules/b.js
5. /src/node_modules/b/package.json中'main'中指定的文件
6. /src/node_modules/b/index.js
7. /node_modules/b.js
8. /node_modules/b/package.json中'main'中指定的文件
9. /node_modules/b/index.js

ts现在使用了与`node.js`类似的模块解析策略，但是ts增加了其他几个源文件扩展名（`.ts`,`.tsx`,`.d.ts`），同时 TS 在 package.json 里使用字段 types来表示main的意义

小结：ts都是采用 import 导入和 export 导出

### 十、typescript 命名空间

在 TS 中，export 与 import 称为 外部模块，ts 中还支持一种内部模块`namespace`,它的主要作用只是单纯的在文件内部（模块内容）隔离作用域，同名的命名空间会合并，在命名空间中能够导出对象，例如下面的 export

```js
namespace k1 {
    let a = 10;
}
namespace k1 {
    let b =20
    export var obj2 = {
        b
    }
}

namespace k2 {
    let c = 100
    console.log(k1.obj2)
}
```

### 十一、 装饰器

装饰器 - Decorators 在 TypeScript 中是一种可以在不修改类代码的基础上通过添加标注的方式来对类型进行扩展的一种方式

* 减少代码量
* 提高代码扩展性，可读性和维护性

> 在 TS 中，装饰器只能在类中使用

#### 11.1装饰器语法

装饰器的使用极其的简单

* 装饰器本质就是一个函数
* 通过特定语法在特定的位置调用装饰器即可对数据（类、方法、甚至参数等）进行扩展

启动装饰器特性：在tsconfig.json中的"compilerOptions"节点下添加`experimentalDecorators:true`

```JS
// 装饰器语法
function log(target:Function,name:string,descriptor:PropertyDescriptor) {
    /**
     * target：被装饰的方法所属的类
     * name: 被装饰的方法的名称
     * descriptor: 描述符
     */
    console.log(target,name,descriptor)
    let fn = descriptor.value
    descriptor.value = function(a:number,b:number) {
        let result = fn(a,b)
        console.log('日志',{
            name,a,b,result
        })
        return result
    }

}   
// 原始类
class M {
    @log
    static add(a:number,b:number) {
        return a+b
    }
    @log
    static sub(a:number,b:number) {
        return a-b
    }
}

let v1 = M.add(1,2)
console.log(v1)
let v2 = M.sub(2,3)
console.log(v2)
```

#### 11.2装饰器分类

装饰器是一个函数，它可以通过`@装饰器函数`这种特殊的语法附加 类、方法、访问符、属性、参数上，对它们进行包装，然后返回一个包装后的目标对象，（类、方法、访问符、属性、参数），**装饰器工作在类的构建阶段，而不是使用阶段**

```js
function 装饰器1() {}
...

@装饰器1
class MyClass {
  
  @装饰器2
  a: number;
  
  @装饰器3
  static property1: number;
  
  @装饰器4
  get b() { 
    return 1; 
  }
  
  @装饰器5
  static get c() {
    return 2;
  }
  
  @装饰器6
  public method1(@装饰器5 x: number) {
    //
  }
  
  @装饰器7
  public static method2() {}
}
```

* 类装饰器

目标：应用于类的构造函数

参数：第一个参数（也只有一个参数）：类的构造器函数作为其唯一的参数

* 方法装饰器

目标：应用于类的方法上

参数：

第一个参数：静态方法：类的构造器。实例方法：类的原型对象

第二个参数：方法名称

第三个参数：方法描述符对象

* 属性装饰器

目标：应用于类的属性上

参数：

第一个参数：静态方法：类的构造函数。实例方法：类的原型对象

第二个参数：属性名称

* 访问器装饰器

目标：应用于类的访问器上（getter、setter）上

参数：

第一个参数：静态方法：类的构造函数。实例方法：类的原型对象

第二个参数：属性名称

第三个参数：方法描述符对象

* 参数装饰器

目标：应用在参数上

参数：

第一个参数：静态方法：类的构造函数。实例方法：类的原型对象。

第二个参数：方法名称

第三个参数：参数在含参数列表中的索引

**装饰器执行顺序**

1. 实例装饰器：属性=>访问符=>参数=>方法
2. 静态装饰器：属性=>访问符=>参数=>方法
3. 类装饰器：类

```js
function d1(target:Function) {
    // target 是其修饰的目标 这里是类
    console.log(typeof target)
}
function d11(target:Function) {
    // target 是其修饰的目标 这里是类
    console.log(typeof target, target, '1111111');
}


function d2(target:any,name:string) {
      // target 是其修饰的目标 这里是属性
      //  name 是其修饰的属性名
    console.log(typeof target,name)
}

function d3(target:any,name:string,descriptor:PropertyDescriptor) {
    console.log(typeof target ,name,descriptor,'------d3')
}

function d4(target:any,name:string,descriptor:PropertyDescriptor) {
    console.log(typeof target ,name,descriptor,'------d4')
}

function d5(target:any,name:string,index:number) {
  // name : 当前参数所在的方法
  console.log(typeof target, name, index,'-----d5');
}

@d1 // 类装饰器
@d11 // 类装饰器
class MyClass {
    @d2 // 静态属性装饰器
    static property1: number;
    @d2 // 实例属性装饰器
    a: number;
    @d3 //访问器装饰器
    get b() { 
        return 1; 
    }
    @d3  //静态访问器装饰器
    static get c() {
        return 2;
    }
    @d4 // 实例方法装饰器
    public method1(@d5 x: number,@d5 y: number) {}
    @d4 // 静态方法装饰器
    public static method2() {}

}
```

#### 11.3 装饰器工厂

如果我们需要给装饰器执行过程中传入一些参数的时候，就可以使用装饰器工厂来实现

```js
function log(type:string) {
    return function (target: Function, name: string, descriptor: PropertyDescriptor) {

        let value = descriptor.value;
        descriptor.value = function(x: number, y: number) {
            let result = value(x, y);
    
            console.log({
                type,
                name,
                x,
                y,
                result
            });
            return result;
        }
    
    }
}

class M {
    @log('log')
    static add(x: number, y: number) {
        return x + y;
    }

    @log('storage')
    static sub(x: number, y: number) {
        return x - y;
    }
}

let v1 = M.add(1, 2);
console.log(v1);
let v2 = M.sub(1, 2);
console.log(v2);
export default {}
```

#### 11.4 元数据

在`装饰器`函数中，我们可以拿到类、方法、访问符、属性、参数的基本信息，如它们的名称，描述符等，但是我们想获得更加信息就需要通过另外的方法来进行：元数据

什么是元数据：用来描述数据的数据，在我们的程序中，对象、类等都属数据，它们描述了某种数据，另外还有一种数据，它可以用来描述对象、类，这些用来描述数据的数据就是 元数据

> 比如一首歌曲本身就是一组数据，同时还有一组用来描述歌曲的歌手、格式、时长的数据，那么这组数据就是歌曲数据的元数据 

描述元数据我们一般使用`reflect-metadata`库：<https://www.npmjs.com/package/reflect-metadata> 

```bash
npm i reflect-metadata
```

定义元数据

我们可以为类、方法等数据定义元数据

* 元数据会被附加到指定的类、方法等数据之上，但是又不会影响类、方法本身的代码，是一种映射关系
* 设置元数据：`Reflect.defineMetadata(metadataKey,metadataValue,target,propertyKey)`
  1. metadataKey：设置元数据的key
  2. metadataValue：设置元数据的 值
  3. target：元数据附加的目标
  4. propertyKey：对应的 property key，例如类中的方法名

设置元数据调用方式：

* 通过`Reflect.defineMetadata`方法调用来添加元数据
* 通过`@Reflect.metadata`装饰器来添加元数据

获取元数据调用方式：

* `Reflect.getMetadata(metadataKey,target,propertKey)`

参数的含义与 defineMetadata 对应

```js
import 'reflect-metadata';

@Reflect.metadata('n',1)
class A {
    @Reflect.metadata('n',2)
    public static method1() {
        
    }
    @Reflect.metadata('n',4)
    public method2() {

    }
}

let obj = new A

// 也可用以下的方法来定义元数据
// Reflect.defineMetadata('n',1,A)
// Reflect.defineMetadata('n',2,A,'method1')
// Reflect.defineMetadata('n',3,obj)
// Reflect.defineMetadata('n',4,obj,'method2')

console.log(Reflect.getMetadata('n',A))
console.log(Reflect.getMetadata('n',A,'method1'))
console.log(Reflect.getMetadata('n',obj))
console.log(Reflect.getMetadata('n',obj,'method2'))

export default {}
```

使用元数据的 log 装饰器

```js
import 'reflect-metadata'
function log(type?: string) {
    return function (target: any, name: string, descriptor: PropertyDescriptor) {
        let value = descriptor.value;
        descriptor.value = function(x: number, y: number) {
            let result = value(x, y);
            let _type = type;

            if(!_type) {
                if(typeof target ==='function') {
                    _type = Reflect.getMetadata('type',target)
                }else {
                    _type = Reflect.getMetadata('type',target.constructor)
                }
            }
    
            console.log({
                type: _type,
                name,
                x,
                y,
                result
            });
    
            return result;
        }
    
    }
}

@Reflect.metadata('type','storage')
class M {
    @log()
    static add(x: number, y: number) {
        return x + y;
    }

    @log('log')
    static sub(x: number, y: number) {
        return x - y;
    }
}

let v1 = M.add(1, 2);
console.log(v1);
let v2 = M.sub(1, 2);
console.log(v2);

export default {}
```

#### 11.5 使用`emitDecoratorMetadata`

在tsconfig.json 中有一个配置`emitDecoratorMetadata`，开启该特性，ts 会在编译之后自动给类、方法、访问符、属性、参数添加如下几个元数据

* design:type：被装饰目标的类型
  * 成员属性：属性的标注类型
  * 成员方法：Function 类型
* design:paramtypes：被装饰上的形参标注
  * 成员方法：方法形参列表的标注类型
  * 类：构造函数形参列表的标注类型
* design:returntype : 被装饰方法的返回值
  * 成员方法：函数返回值的标注类型

```js
import 'reflect-metadata'

function f() {
    return function(target:any,name:string,descriptor:PropertyDescriptor) {
        console.log(descriptor.value.length)
        console.log( Reflect.getMetadata('design:type', target, name) );
        console.log( Reflect.getMetadata('design:paramtypes', target, name) );
        console.log( Reflect.getMetadata('design:returntype', target, name) );
        let _t = Reflect.getMetadata('design:paramtypes', target, name)[0];
        
        let value = descriptor.value;
        if (_t === Number) {
            value(100);
        }
        if (_t === String) {
            value('开课吧')
        }
        if (_t === Date) {
            value(new Date)
        }
    }
}

class B {
    name: string;

    constructor() {

    }

    @f()
    method2(x?: Date):string {
        return ''
    }
}

let b = new B();
b.method2();
b.method2();
```

### 十二、typescript的配置选项说明
```js
{
    "compilerOptions": {
        "outDir": "./dist",
        // 编译成es6代码规范
        "target":"ES6",
        // 编译成commonjs模块规范
        "module": "commonjs",
        // 开启额外库的加载
        "lib":[xxxx],
        // 定义要编译成的导入或导出模块解析规范
        "moduleResolution":"classic | node",
       // 开启允许加载json文件
         "resolveJsonModule":true,
       // 开启自动监听模式
         "watch":true,
        // 允许加载JS文件
        "allowJs": true,
        // 处理非esm默认导出数据的规范
        "allowSyntheticDefaultImports": true,
        // 处理非esm默认导出数据的规范
        "esModuleInterop": true,
        // 在这配置json
        "resolveJsonModule": true,
        // 开启装饰器
        "experimentalDecorators": true,
        // 开启元数据
        "emitDecoratorMetadata": true,
        // 开启避免 null和 undefined类型隐式转带来的错误
        "strictNullChecks":true,
        // 开启主要避免对函数参数的any类型隐式转换带来的错误
        "noImplicitAny":true
    },
    // 需要给ts处理的文件
    "include": ["./src/**/*"]
}
```

 ### 十三、 类型声明

类型声明主要用于项目引入一些外部库的情况下，这些库可能没有ts的声明文件，这时我们可以自己进行类型声明，假设`lodash`模块没有ts文件声明，我们要使用他的 `cameCase`方法，这时我们要进行类型声明，**关键字 declare**

```js
import { camelCase } from 'lodash'
declare function camelCase (input: string): string
const res = camelCase('hello typed')
```

但目前绝大多数的npm上的资源包已经携带自己的ts声明文件了，我们使用ts开发的时候，只要npm下来就可以