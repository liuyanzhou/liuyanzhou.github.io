---
title: js面向对象
date: 2020-06-25 19:47:23
categories: JS
top: false
summary: js面向对象
tags: 
 - JS
---

> 本章是对小编对`JS面向对象思想`的个人理解，但也是经过学习后，总结出来的

## 一、面向过程(POP)& 面向对象(OOP)

> 这里我用一个造汽车的例子来讲述一下这两者的思想概念

* 面向过程：它是一种亲力亲为的一个编程思想，要满足一个需求就要一步步的写下去，并不需要考虑代码是否冗余。

> 例如：我们要造一个汽车，我们就得先做好设计图，在着就生产出轮子，车架，车灯等等一个个部件，最后在按设计图造出来一部车子，如果还要造另一部，就得重新之前的操作。所有的事情都要**亲力亲为**

* 面向对象：它是将事务划分成一个个对象，然后在由个个对象分工与合作去实现需求,类似将事务交给子公司，之后再总公司协调与配合下完成需求。 但他需要一些编程功底，因为要实现它就要学会**封装、继承、多态**

  特点：

  * 抽取（抽象）对象共用的属性和行为组织(封装)成一个类(模板) 
  * 对类进行实例化, 获取类的对象 
> 例如：我们要造一个汽车，我们将设计稿，车轮，车架，车灯都统统交给子公司去完成，之后都运到总公司去配置成一个想要的车子，如果还要造另一部，就在根据设计图，将车架构搭起来就行了，而不要去知道车轮是怎么做的，车架怎么做的。

* 两者的优缺点

| 思想名   | 优点                                                         | 缺点                         |
| -------- | ------------------------------------------------------------ | ---------------------------- |
| 面向过程 | 性能比面向对象高，适合跟硬件联系很紧密的东西，例如单片机就采用的面向过程编程 | 不易维护、不易复用、不易扩展 |
| 面向对象 | 易维护、易复用、易扩展，由于面向对象有封装、继承、多态性的特性，可以设计出低耦合的系统，使系统 更加灵活、更加易于维护 | 性能比面向过程低             |

> 注意：两者并无优劣，只是不同编程场景下那种更适合

## 二、构造函数

> 在 ES6之前 ，对象不是基于类创建的，而是用一种称为构建函数的特殊函数来定义对象和它们的特征 

### 2.1构造函数概念

构造函数是一种特殊的函数，主要用来初始化对象，即为对象成员变量赋初始值，它总与 **new** 一起使用。我 

们可以把对象中一些公共的属性和方法抽取出来，然后封装到这个函数里面。 

 在 JS 中，使用构造函数时要注意以下两点： 

* 构造函数用于创建某一类对象，其首**字母要大写** 
* 构造函数要和 new 一起使用才有意义 

  **代码演示**

```js
function Star(uname, age) {
    this.uname = uname;
    this.age = age;
    this.sing = function() {
        console.log('我会唱歌');

    }
}

var ldh = new Star('刘德华', 18); // 实例化一个对象
ldh.sing(); // 我会唱歌
```

而在实例化`new`这个过程中new 关键字帮我们做了四件事

* 在内存中创建一个新的空对象。  

* 让 this 指向这个新的对象。  

* 执行构造函数里面的代码，给这个新对象添加属性和方法。  

* 返回这个新对象（所以构造函数里面不需要 return ）。 

  ![new](/medias/imges/js/faceObj/obj.png)

### 2.2 静态成员 & 实例成员

* 实例成员

实例成员就是构造函数内部通过this添加的成员 如下列代码中uname age sing 就是实例成员,实例成员只能通过实例化的对象来访问

```js
function Star(uname, age) {
    this.uname = uname;
    this.age = age;
    this.sing = function() {
        console.log('我会唱歌');
    }
}
var ldh = new Star('刘德华', 18);
console.log(ldh.uname);//实例成员只能通过实例化的对象来访问
```

* 静态成员

静态成员 在构造函数本身上添加的成员  如下列代码中 sex 就是静态成员,静态成员只能通过构造函数来访问

```js
function Star(uname, age) {
    this.uname = uname;
    this.age = age;
    this.sing = function() {
        console.log('我会唱歌');
    }
}
Star.sex = '男';
var ldh = new Star('刘德华', 18);
console.log(Star.sex);//静态成员只能通过构造函数来访问
```

### 2.3 构造函数的缺点

> 由于构造函数中的方法是属于复杂类型，则在对象实例化的时候会对同一个方法在内存重新开辟一个存储空间，而不是复用同一个方法。导致内存空间的浪费。由此我们需要通过**原型**来解决实例化对象对同一个方法不能复用的问题

![构造函数的缺点](/medias/imges/js/faceObj/img1.png)

## 三、原型

### 3.1构造函数的原型[prototype ]

> 构造函数原型相当于是为每一个实例化对象提供一个公共共享空间，在这里存放着共同的属性或方法，方便给实例化对象复用属性或方法，降低内存资源损耗。我们可以把那些不变的方法，直接定义在 prototype 对象上，这样所有对象的实例就可以共享这些方法。简而言之，构造函数原型也是一个对象，作用是为了实现资源在所有对象中**共享**

**代码演示**

```js
function Star(uname, age) {
    this.uname = uname;
    this.age = age;
}
// 我们将公共的函数写到原型 prototype中 实现代码复用
Star.prototype.sing = function() {
    console.log('我会唱歌');
}
console.log(Star.prototype)
var star1 = new Star('star1', 18);
var star2 = new Star('star2', 19);
console.log(star1.sing === star2.sing); // true 证明了实例化多个对象时，都是复用一个函数
star1.sing();
star2.sing();
```

### 3.2对象原型[__proto__]

> 对象都会有一个属性 `__proto__ `指向构造函数的 prototype 原型对象，之所以我们对象可以使用构造函数 prototype 原型对象的属性和方法，就是因为有 `__proto__ `属性，它会构成一条原型链。
> `__proto__`对象原型和原型对象 prototype 是等价的，因为它们是引用数据类型。
> `__proto__`对象原型的意义就在于为对象的查找机制提供一个方向，或者说一条路线，但是它是一个非标准属性，因此实际开发中，不可以使用这个属性，它只是内部指向原型对象 prototype。

**构造函数&实例对象&原型三者的关系**

```js
function Star(uname, age) {
    this.uname = uname;
    this.age = age;
}
Star.prototype.sing = function() {
    console.log('我会唱歌');
}
var star1 = new Star('star1', 18);
var star2 = new Star('star2', 19);
star1.sing();
console.log(star1); // 对象身上的 __proto__ 属性指向我们构造函数的原型对象 prototype
console.log(star1.__proto__ === Star.prototype); // true
```

> 方法的查找规则: 首先先看ldh 对象身上是否有 sing 方法,如果有就执行这个对象上的sing
>  如果么有sing 这个方法,因为有__proto__ 的存在,就去构造函数原型对象prototype身上去查找sing这个方法

**关系图**

![关系图](/medias/imges/js/faceObj/img4.png)

> 1.构造函数的prototype属性指向了构造函数原型对象
> 2.实例对象是由构造函数创建的,实例对象的__proto__属性指向了构造函数的原型对象（prototype）
> 3.构造函数的原型对象的constructor属性指向了构造函数,实例对象的原型的constructor属性也指向了构造函数（用于区分实例化对象是由那个构造函数创建出来的）

### 3.3constructor构造函数

> 对象原型（ __proto__）和构造函数（prototype）原型对象里面都有一个属性 constructor 属性 ，constructor 我们称为构造函数，因为它指回构造函数本身。
> constructor 主要用于记录该对象引用于哪个构造函数，它可以让原型对象重新指向原来的构造函数。
> 一般情况下，对象的方法都在构造函数的原型对象中设置。如果有多个对象的方法，我们可以给原型对象采取对象形式赋值，但是这样就会覆盖构造函数原型对象原来的内容，这样修改后的原型对象 constructor  就不再指向当前构造函数了。此时，我们可以在修改后的原型对象中，添加一个 constructor 指向原来的构造函数。

```js
function Star(uname, age) {
    this.uname = uname;
    this.age = age;
}
// 很多情况下,我们需要手动的利用constructor 这个属性指回 原来的构造函数
Star.prototype = {
    // 如果我们修改了原来的原型对象,给原型对象赋值的是一个对象,则必须手动的利用constructor指回原来的构造函数
    constructor: Star, // 手动设置指回原来的构造函数
    sing: function() {
        console.log('我会唱歌');
    },
    movie: function() {
        console.log('我会演电影');
    }
}
var zxy = new Star('张学友', 19);
console.log(zxy)
```

![设置上](/medias/imges/js/faceObj/img8.png)

![未设置上](/medias/imges/js/faceObj/img9.png)

### 3.4原型链

>   每一个实例对象又有一个__proto__属性，指向的构造函数的原型对象，构造函数的原型对象也是一个对象，也有__proto__属性，这样一层一层往上找就形成了原型链，最终查找到最顶层还没有该属性或方法，则方法null对象。

![原型链图](/medias/imges/js/faceObj/img5.png)

### 3.5原型链查找机制

> 当访问一个对象的属性（包括方法）时，首先查找这个对象自身有没有该属性。
> 如果没有就查找它的原型（也就是 __proto__指向的 prototype 原型对象）。
> 如果还没有就查找原型对象的原型（Object的原型对象）。
> 依此类推一直找到 Object 为止（null）。
> __proto__对象原型的意义就在于为对象成员查找机制提供一个方向，或者说一条路线。

### 3.6原型对象中this指向

> 构造函数中的this和原型对象的this,都指向我们new出来的实例对象

```js
function Star(uname, age) {
    this.uname = uname;
    this.age = age;
}
var that;
Star.prototype.sing = function() {
    console.log('我会唱歌');
    that = this;
}
var ldh = new Star('刘德华', 18);
// 1. 在构造函数中,里面this指向的是对象实例 ldh
console.log(that === ldh);// true
// 2.原型对象函数里面的this 指向的是 实例对象 ldh
```

### 3.7通过原型为数组扩展内置方法

```js
Array.prototype.sum = function() {
    var sum = 0;
    for (var i = 0; i < this.length; i++) {
        sum += this[i];
    }
    return sum;
};
//此时数组对象中已经存在sum()方法了  可以始终 数组.sum()进行数据的求
var arr = [1, 2, 3];
console.log(arr.sum()); // 6
```

> 注意：数组和字符串内置对象不能给原型对象覆盖操作 Array.prototype = {} ，只能是 Array.prototype.xxx = function(){} 的方式。 

## 四、继承

> 继承是指当我们一个类和另一个写好的类中功能是相同的，而我们要扩展新的功能，那么最快捷的方法就是继承写好了的类的属性和方法。但ES6之前并没有给我们提供 extends 继承。我们可以通过构造函数+原型对象模拟实现继承，被称为组合继承。 

### 4.1 call()

> call方法可以改变函数运行时的`this`指向问题，从而实现继承

- call()可以调用函数
- call()可以修改this的指向,使用call()的时候 参数一是修改后的this指向,参数2,参数3..使用逗号隔开连接

```js
// call 方法
function fn(x, y) {
    console.log('我想喝手磨咖啡');
    console.log(this);
    console.log(x + y);
}
var o = {
    name: 'andy'
};
// 1. call() 可以调用函数
fn.call(); // 此时的this 是 window
// 2. call() 可以改变这个函数的this指向 此时这个函数的this 就指向了o这个对象
fn.call(o, 1, 2);   // 此时的this 是 对象o 
```

### 4.2子构造函数继承父构造函数中的属性

1. 先定义一个父构造函数
2. 再定义一个子构造函数
3. 子构造函数继承父构造函数的属性(使用call方法)

```js
// 1. 父构造函数
function Father(uname, age) {
    // this 指向父构造函数的对象实例
    this.uname = uname;
    this.age = age;
}
// 2 .子构造函数 
function Son(uname, age, score) {
    // this 指向子构造函数的对象实例
    // 3.使用call方式实现子继承父的属性
    Father.call(this, uname, age);
    this.score = score;
}
var son = new Son('刘德华', 18, 100);
console.log(son); // Son {uname: "刘德华", age: 18, score: 100}
```

### 4.3 借用原型对象继承方法

1. 先定义一个父构造函数
2. 再定义一个子构造函数
3. 子构造函数继承父构造函数的属性(使用call方法)

```js
// 1. 父构造函数
function Father(uname, age) {
    // this 指向父构造函数的对象实例
    this.uname = uname;
    this.age = age;
}
Father.prototype.money = function() {
    console.log(100000);
};
// 2 .子构造函数 
function Son(uname, age, score) {
    // this 指向子构造函数的对象实例
    Father.call(this, uname, age);
    this.score = score;
}
// Son.prototype = Father.prototype;  这样直接赋值会有问题,如果子原型对象上添加上一个方法，那么父原型对象也会出现该方法，这是因为对象是复杂数据类型，我们这样修改只是传址的方式上修改，将子原型对象的指针指向了父原型对象上
// 下面这种创建了一个新的对象，那么在内存中便开辟了一个位置，在将子原型对象指向这个区域，采用这种继承方法，相当于我们在中间实例化出来一个对象来隔断对 Father 的原型对象赋值
Son.prototype = new Father(); 
// 如果利用对象的形式修改了原型对象,别忘了利用constructor 指回原来的构造函数
Son.prototype.constructor = Son;
// 这个是子构造函数专门的方法
Son.prototype.exam = function() {
    console.log('孩子要考试');

}
var son = new Son('刘德华', 18, 100);
var father = new Father()
console.log(son);
console.log(father)
```

![运行后的子原型实例对象](/medias/imges/js/faceObj/img12.png)

![运行后的子原型实例对象](/medias/imges/js/faceObj/img13.png)

> 由两图分析可知，这种方法确实实现了修改子原型对象并不污染到父原型对象

### 4.4 使用原型对象+call继承

> 我们可以利用call与原型对象来实现继承父类的实例属性与方法和原型属性与方法

* call + prototype 

```js
function Animal(name) {
    this.name = name;
    this.eat = '吃肉';
}
Animal.prototype.address = { location: '山里' }

function Tiger(name) {
    this.name = name;
    this.age = 10;
    Animal.call(this); // 继承实例方法
}
Tiger.prototype.__proto__ = Animal.prototype; // 继承原型上方法
let tiger = new Tiger();
console.log(tiger.eat) // 吃肉
console.log(tiger.address) // { location: '山里' }
console.log(tiger.constructor) // Tiger
```

* call + Object.create()

```js
function Animal(name) {
    this.name = name;
    this.eat = '吃肉';
}
Animal.prototype.address = { location: '山里' }

function Tiger(name) {
    this.name = name;
    this.age = 10;
    Animal.call(this)
}
Tiger.prototype = Object.create(Animal.prototype)
let tiger = new Tiger();
console.log(tiger.eat); // 吃肉
console.log(tiger.address); // { location: '山里' } 
console.log(tiger.constructor) // [Function: Animal]
```

> Object.create会我们原型对象的`constructor`属性指向，一般我们调用之后都会手动改回`constructor`属性的指向
>
> `Tiger.prototype.constructor = Tiger`

我们手动实现 `Object.create()方法`，并完成 constructor 回指问题

```js
function create(parentPrototype) {
    let Fn = function() {}
    Fn.prototype = parentPrototype; // 当前函数的原型 只有父类的原型
    let fn = new Fn();
    fn.constructor = Tiger;
    return fn // 当实例可以拿到 animal.prototype
}
```

## 五、ES6类

> 在 ES6 中新增加了类的概念，可以使用 class 关键字声明一个类，之后以这个类来实例化对象 
>
> 类抽象了对象的公共部分，它泛指某一大类（class） 
>
> 对象特指某一个，通过类实例化一个具体的对象 

### 5.1创建类的语法

```js
//步骤1 使用class关键字
class name {
    // class body
}     
//步骤2使用定义的类创建实例  注意new关键字
var xx = new name();     
```

**代码演示**

```js
// 1. 创建类 class  创建一个 明星类
class Star {
    // 类的共有属性放到 constructor 里面
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
}
// 2. 利用类创建对象 new
var ldh = new Star('刘德华', 18);
console.log(ldh); // Star {uname: "刘德华", age: 18}
```

### 5.2类创建添加属性和方法

```js
// 1. 创建类 class  创建一个类
class Star {
    // 类的共有属性放到 constructor 里面 constructor是 构造器或者构造函数
    constructor(uname, age) {
        this.uname = uname;
        this.age = age;
    }//------------------------------------------->注意,方法与方法之间不需要添加逗号
    sing(song) {
        console.log(this.uname + '唱' + song);
    }
}
// 2. 利用类创建对象 new
var ldh = new Star('刘德华', 18);
console.log(ldh); // Star {uname: "刘德华", age: 18}
ldh.sing('冰雨'); // 刘德华唱冰雨
```

> 注意：
>
> 1. 通过class 关键字创建类, 类名我们还是习惯性定义首字母大写
> 2. 类里面有个constructor 函数,可以接受传递过来的参数,同时**返回实例对象**
> 3. constructor 函数 只要 new 生成实例时,就会自动调用这个函数, 如果我们不写这个函数,类也会自动生成这个函数
> 4. 多个函数方法之间不需要添加逗号分隔
> 5. 生成实例 new 不能省略
> 6. 语法规范, 创建类 类名后面不要加小括号,生成实例 类名后面加小括号, 构造函数不需要加function

### 5.3类的继承

#### 5.3.1语法

语法：

```js
// 父类
class Father{   
} 

// 子类继承父类
class  Son  extends Father {  
}       
```

**代码演示**

```js
class Father {
    constructor(surname) {
        this.surname= surname;
    }
    say() {
        console.log('你的姓是' + this.surname);
    }
}

class Son extends Father{  // 这样子类就继承了父类的属性和方法
}
var damao= new Son('刘');
damao.say();      //结果为 你的姓是刘
```

> `extends`关键字让子类继承了父类的 实例属性方法、原型属性方法、父类的静态属性与方法

```js
class Animal {
    static flag() {
            return 123
        } // es7 支持静态属性 es6 值支持静态方法
    constructor(name) {
        this.name = name;
        this.eat = '吃肉'
    }
    say() { // 原型上的方法 
        console.log(this); // es6 规范里 如果单独调用原型上的方法 this是不存在的
    }
}
class Tiger extends Animal { // 实例+原型
    constructor(name) {
        super(name); // super 相当于 Animal.call(this)
    }
}

let tiger = new Tiger('王老虎');
console.log(tiger.name); // 王老虎
console.log(Tiger.flag()); // 静态方法可以被继承 123
```



#### 5.3.2继承的super使用

super的作用：

* 可以调用父类的普通函数
* 可以调用父类的构造函数，并传值

> 可以这么理解，super就是父类的一个构造出来的对象，你可以用它来调用父类的所有的方法,但不能通过它直接获取到父类的属性
>
> 注意: 子类在构造函数中使用super, 必须放到 this 前面 (必须先调用父类的构造方法,在使用子类构造方法)

调用父类的构造函数

```js
//定义了父类
class Father {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }
    sum() {
        console.log(this.x + this.y); // 这里的this是指 Father
    }
}
//子元素继承父类
class Son extends Father {
    constructor(x, y) {
        super(x, y); //使用super调用了父类中的构造函数,之后把 x y 传入到父类的构造函数中去
    }
}
var son = new Son(1, 2);
son.sum(); //结果为3
```

调用父类的普通函数

```js
// super 关键字调用父类普通函数
class Father {
    say() {
        return '我是爸爸';
    }
}
class Son extends Father {
    say() {
        console.log(super.say() + '的儿子');
        // super.say() 就是调用父类中的普通函数 say()
    }
}
var son = new Son();
son.say(); // 结果： 我是爸爸的儿子
```

如果子类想要继承父类的方法,同时在自己内部扩展自己的方法,利用super 调用父类的构造函数,super 必须在子类this之前调用

```js
// 父类有加法方法
class Father {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }
    sum() {
        console.log(this.x + this.y);
    }
}
// 子类继承父类加法方法 同时 扩展减法方法
class Son extends Father {
    constructor(x, y) {
        // 利用super 调用父类的构造函数 super 必须在子类this之前调用,放到this之后会报错
        super(x, y);
        this.x = x;
        this.y = y;
    }
    subtract() {
        console.log(this.x - this.y);
    }
}
var son = new Son(5, 3);
son.subtract(); //2
son.sum();//8
```

####5.3.3 类的方法查找规则

> 1. 继承中,如果实例化子类输出一个方法,先看子类有没有这个方法,如果有就先执行子类的
> 2. 继承中,如果子类里面没有,就去查找父类有没有这个方法,如果有,就执行父类的这个方法(就近原则)

#### 5.3.4类中的this指向

1. constructor中的this指向的是new出来的实例对象 
2. 自定义的方法,一般也指向的new出来的实例对象
3. 绑定事件之后this指向的就是触发事件的事件源

```html
<body>
    <button>点击</button>
</body>

<script>
var that;
var _that;
class Star {
    constructor(uname, age) {
        // constructor 里面的this 指向的是 创建的实例对象
        that = this;
        console.log(this); // Star {} 

        this.uname = uname;
        this.age = age;
        // this.sing();
        this.btn = document.querySelector('button');
        this.btn.onclick = this.sing;
    }
    sing() {
        // 这个sing方法里面的this 指向的是 btn 这个按钮,因为这个按钮调用了这个函数
        console.log(this); // <button>点击<button>

        console.log(that.uname); // that里面存储的是constructor里面的this
    }
    dance() {
        // 这个dance里面的this 指向的是实例对象 ldh 因为ldh 调用了这个函数
        _that = this;
        console.log(this);

    }
}

var ldh = new Star('刘德华');
console.log(that === ldh); // true
ldh.dance();
console.log(_that === ldh); // true
</script>
```

#### 5.3.5使用class类需要注意的问题

*  在 ES6 中类没有变量提升，所以必须先定义类，才能通过类实例化对象.
* 类里面的共有属性和方法一定要加this使用 
* 类里面的this指向问题. 【constructor 里面的this指向实例对象, 方法里面的this 指向这个方法的调用者 】
* **在es6 规范里 如果单独调用原型上的方法 this是不存在的**

```js
class Animal {
    static flag() {
            return 123
        } // es7 支持静态属性 es6 值支持静态方法
    constructor(name) {
        this.name = name;
        this.eat = '吃肉'
    }
    say() { // 原型上的方法 
        console.log(this); // es6 规范里 如果单独调用原型上的方法 this是不存在的
    }
} 
let animal = new Animal();
let say = animal.say
say(); // undefined
```

### 5.4类的方法分类

> 类中的方法可以分为：静态方法，原型方法，实例方法

* 静态方法[只能让类直接调用]

```js 
class Example{
    static sum(a, b) {
        console.log(a+b);
    }
}
Example.sum(1, 2); // 3
```

* 原型方法 [实例化对象调用，可以和其他实例化对象进行共享]

```js
class Example {
    sum(a, b) {
        console.log(a + b);
    }
}
let exam = new Example();
exam.sum(1, 2); // 3
```

* 实例方法 [实例化对象new之后就会被绑定，但它不能与其他实例化出来的对象共享]

```js
class Example {
    constructor() {
        this.sum = (a, b) => {
            console.log(a + b);
        }
    }
}
let num = new Example()
num.sum(1, 2)
```

### 5.5class的本质

经过上面的学习，小编在此来总结一下class类到底是什么，

> 正如上面所说，class类是在ES6之后提出的，在之前我们都是在使用构造函数来创建对象，由于构造函数的原型，继承写起来比较繁琐，才出现了es6的类的概念，它简化了我们构造函数的继承的一些弊端，像我们子类继承父类的时候使用组合继承若没有使用一个新的`xxx.prototype = new xxx()`来隔断子类和父类的原型污染问题的话，子类添加的方法父类也会被添加上，但到了es6之后我们使用`extend`关键字就能避免这个问题，所以可以说class类是构造函数的**语法糖**。学好前端，小编正在努力！！！💪







