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
  * 动态类型：运行阶段才能够明确变量类型，而且变量的类型随时可以改变，可以说变量是没有数据类型的，变量存储的数据是有数据类型的

强类型和弱类型语言分布

![类型系统](/medias/imges/js/ts/leixin.png)

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
####3.2 注释类型

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

#### 4.1 快速上手

* 安装

```bash
yarn add typescript -D
```

编译有两个方式：

* 直接编译

```js
yarn tsc '文件路径'
```

* 使用配置文件编译(推荐)

```bash
// 初始化配置文件
yarn tsc --init 
```

配置文件关注的属性

| 属性名    | 描述                                                         | 例子                             |
| --------- | ------------------------------------------------------------ | -------------------------------- |
| target    | 编译 TS文件的语法为几版本的Js                                | "target": "es5",   编译为ES5版本 |
| lib       | 标准库文件声明，默认是ES5标准库，我们可以添加，以适应JS新增方法 | "lib": ["ES2015","DOM","ES2017"] |
| sourceMap | 代码映射，方便调试                                           | "sourceMap": true,               |
| outDir    | 编译后的代码输出路径                                         | "outDir": "./dist",              |
| rootDir   | 编译的根路径                                                 | "rootDir": "./src",              |
| strict    | 是否采用严格模式，严格模式需要给每个参数都标注类型，而非严格模式则不需要，建议开启 | "strict": true,                  |

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
// 原始数据类型

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

#### 4.4 对象类型(Object)

```js
// Object 类型

export {} // 确保跟其它示例没有成员冲突

// object 类型是指除了原始类型以外的其它类型
const foo: object = function () {} // [] // {}

// 如果需要明确限制对象类型，则应该使用这种类型对象字面量的语法，或者是「接口」,这里只能有foo，bar属性
const obj: { foo: number, bar: string } = { foo: 123, bar: 'string' }

// 接口的概念后续介绍
```

#### 4.5 数组类型(Array)

```js
// 数组类型

export {} // 确保跟其它示例没有成员冲突

// 数组类型的两种表示方式

const arr1: Array<number> = [1, 2, 3]

const arr2: number[] = [1, 2, 3]

// 案例 -----------------------

// 如果是 JS，需要判断是不是每个成员都是数字
// 使用 TS，类型有保障，不用添加类型判断
function sum (...args: number[]) {
  return args.reduce((prev, current) => prev + current, 0)
}

sum(1, 2, 3) // => 6
```

#### 4.6 元组

元组规定了数组的个数已经对应的数据类型，不能超过也不能少于

```js
// 元组（Tuple）

export {} // 确保跟其它示例没有成员冲突

const tuple: [number, string] = [18, 'zce']

// const age = tuple[0]
// const name = tuple[1]

const [age, name] = tuple

// ---------------------

const entries: [string, number][] = Object.entries({
  foo: 123,
  bar: 456
})


const [key, value] = entries[0]
// key => foo, value => 123
```

#### 4.7 枚举(Enum)

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

#### 4.8 函数类型

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

#### 4.9 任意类型(弱类型)

可以允许成为任何值，这是一种不安全的做法，不推荐这么使用，它的出现很大的原因是用于兼容老项目

```js
// 任意类型（弱类型）

export {} // 确保跟其它示例没有成员冲突

function stringify (value: any) {
  return JSON.stringify(value)
}

stringify('string')

stringify(100)

stringify(true)

let foo: any = 'string'

foo = 100

foo.bar()

// any 类型是不安全的
```

#### 4.10 隐式类型推断

```js
// 隐式类型推断

export {} // 确保跟其它示例没有成员冲突
// 已经为number类型
let age = 18 // number
// 在赋值字符串则会报错
// age = 'string'

// 定义时没赋值则为 any类型
let foo

foo = 100

foo = 'string'

// 建议为每个变量添加明确的类型标注
```

#### 4.11 类型断言

当我们明确了该变量一定是存储某个类型的数据，那么我们就可以使用类型断言的方法给他进行限制

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

#### 4.12 接口

* 接口基本语法

接口可以理解为一种协议，如果被某个协议限制了，那么就必须遵循这个协议的所有规定

```js
// 接口

export {} // 确保跟其它示例没有成员冲突

interface Post {
  title: string
  content: string
}

function printPost (post: Post) {
  console.log(post.title)
  console.log(post.content)
}

// prinitPost 的实参对象一定要有两个属性 title 和 content，类型与接口 Post对应
printPost({
  title: 'Hello TypeScript',
  content: 'A javascript superset'
})
```

* 接口的可选成员、只读成员、动态成员

```js
// 可选成员、只读成员、动态成员

export {} // 确保跟其它示例没有成员冲突

// -------------------------------------------

interface Post {
  title: string
  content: string
 // 可选成员：这个成员在协议上可有可无 
  subtitle?: string
  // 只读成员：这个成员值提供读权限，若修改值是不被允许的
  readonly summary: string
}

const hello: Post = {
  title: 'Hello TypeScript',
  content: 'A javascript superset',
  summary: 'A javascript'
}

// hello.summary = 'other'

// ----------------------------------
// 动态成员：可以增加定义的数据类型的多个数据
interface Cache {
  [prop: string]: string
}

const cache: Cache = {}

cache.foo = 'value1'
cache.bar = 'value2'
```

#### 4.13 类(Class)

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

 ### 五、 类型声明

类型声明主要用于项目引入一些外部库的情况下，这些库可能没有ts的声明文件，这时我们可以自己进行类型声明，假设`lodash`模块没有ts文件声明，我们要使用他的 `cameCase`方法，这时我们要进行类型声明，**关键字 declare**

```js
import { camelCase } from 'lodash'
declare function camelCase (input: string): string
const res = camelCase('hello typed')
```

但目前绝大多数的npm上的资源包已经携带自己的ts声明文件了，我们使用ts开发的时候，只要npm下来就可以