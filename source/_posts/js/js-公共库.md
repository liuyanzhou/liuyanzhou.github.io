---
title: js-公共库
date: 2021-07-27 23:43:29
categories: JS
top: false
summary: js-公共库
tags: 
 - JS
---

### JS-公共库封装

公共属性或方法提前声明

```js
let class2type={},
    toString = class2type.toString, // ===  Object.prototype.toString
    hasOwn = class2type.hasOwnProperty, // === Object.prototype.hasOwnProperty
    fnToString=hasOwn.toString, // === Function.prototype.toString
    ObjectFunctionString=fnToString.call(Object), // function Object() { [native code] }"
    getProto = Object.getPrototypeOf;
```

#### 检测是否为函数

```js
const isFunction = function isFunction(obj){
    // obj.nodeType => 排除部分浏览器 typeof Object()的值为function 
    // obj.item => 排除document.getElementsByTagName("div")
    return typeof obj === "funtion" && typeof obj.nodeType !== "number" && typeof obj.item !== "funtion";
}
```

#### 检测是否为window对象

```js
const isWindow = function isWindow(obj) {
    // 利用的是 window内部还有一个属性window指针回指自己
   return obj !== null && obj === obj.window;
}
```

#### 检测数据类型的统一处理

```js
const toType = function toType(type) {
    if(obj == null) return obj+"";
    let type = typeof obj;
    if(/(object|function)/i.test(type)){
        // 这里直接调用的是Object.prototype.toString.call
        type=toString.call(obj);
        let [,$1="object"] = type.match(/^\[object (\w+)\]$/) || [];
        return $1.toLowerCase();
    }
    return type;
}
```

#### 检测是否为一个纯粹的对象

> 纯粹的对象是指`obj.__proto__ === Object.prototype`对象的原型指向的是`Object` ，或者是`Object.create(null)`创建的空对象

```js
const isPlainObject = function isPlainObject(obj) {
    let proto,Ctor;
    // 当obj不存在 或者 toString.call的值都不是[object Object]说明其原型链直接指向不是Object
    if(!obj || toString.call(obj) !== "[object Object]") return false;
    // 获取原型指向
    proto = getProto(obj);
    // 不存在的话，说明是一个空对象，没有原型链指向，例如`Object.create()`创建的
    if(!proto) return true;
    // 获取原型上的构造函数
    Ctor = hasOwn.call(proto,"constructor")  && proto.constructor;
    // 判断这个构造函数toString之后是不是Object.prototype.toString.call(Object)的值
    return typeof Ctor === "function" && fnToString.call(Ctor) === ObjectFunctionString;
}
```

#### 检测是否是空对象

```js
const isEmptyObject = function isEmptyObject(obj) {
    if(!obj || !/(object|function)/i.test(typeof obj)) return false;
    let keys = Object.keys(obj);
    if(typeof Symbol !== 'undefined') keys = keys.concat(Object.getOwnPropertySymbols(obj))
    return keys.length === 0;
}
```

#### 检测是否为一个有效数字

> 【支持】：数字&数字字符串&构造函数创建的`Number`实例

```js
const isNumberic = function isNumberic(obj) {
    let type = toType(obj);
    return (type === "number" || type === "string") && isNaN(obj);
}
```

#### 检测是否为数组或者类数组

```js
const isArrayLike = function isArrayLike(obj){
    let length = !!obj  && "length" in obj && obj.length,
        type = toType(obj);
    // function函数也是有length->其代表的是参数个数
    // window也是有length数组->其代表ifram的嵌套个数
    if(isFunction(obj) || isWindow(obj)) return false;
    // length === 0 表示的是空的类数组 || 后面的判断都是在判断类数组集合
    return type === "array" || length === 0 || typeof length === "number" && length >0 && (length - 1) in obj;
}
```

#### 函数防抖处理

> 防抖就是防止帕金森，一直点触发最开始的一次或最后的一次

```js
const debounce = function debounce(func,wait,immediate) {
    // 参数处理
    if(typeof func !== "function") throw new TypeError('func muse be an function')
    if(typeof wait === "boolean") {
        immediate = wait;
        wait=300;
    }
    if(typeof wait !=="number") wait = 300;
    if(typeof immediate !== "boolean") immediate = false;
    let timer;
    return function proxy(...params){
        let runNow = !timer && immediate, 
            self = this,
            result;
        if(timer) {
            clearTimeout(timer);
            timer=null;
        }
        timer=setTimeout(()=>{
            // 这里的清除是为了配合Immediate
            if(timer) {
                clearTimeout(timer);
                timer = null;
            }
            if(!immediat) result= func.call(self,...params);
        },wait)
        if(runNow) result = func.call(self,...params);
        return result;
    }
}
```

#### 函数节流处理

> 所谓节流就像水龙头控制水流的速率，对应事件就是说控制其一段时间的触发频率

```js
const throttle = function throttle(func,wait) {
    if(typeof func !== "function") throw new TypeError('func must be function');
    if(typeof wait !== "number") wait = 300;
    let timer,
        previous=0;
    return function proxy(...params) {
        let now = +new Date(),
            remaining= wait -(now - previous),
            self = this,
            result;
        // 第一次执行函数会走remaining,之后是等定时器执行完则会重新进入逻辑，如果有定时器我们我们这里设置了一次，只有当前定时器执行完则才能设置新的定时器
        if(remaining<=0) {
            if(timer) {
                clearTimeout(timer);
                timer=null;
            }
            result = func.call(self,...params);
            previous=now;
        }else if(!timer) {
            timer = setTimeout(()=>{
                if(timer) {
                    clearTimeout(timer);
                    timer=null;
                }
                result = func.call(self,...params);
                previous=+new Date();
            },remaining)
        }
        return result;
    }
}
```

#### 迭代数组/类数组/对象

> 【支持】当`callback`函数返回值是`true`时，断开迭代

```js
const each = function each(obj,callback) {
    if(obj == null || typeof obj !== "object") throw new TypeError('obj must be an array/like-array/object');
    if(typeof callback !== "function") callback = function() {}
    if(isArrayLike(obj)){
        let i =0,
            length=obj.length,
            item;
        for(;i<length;i++) {
            item = obj[i];
            if(callback.call(item,item,i) === false) break;
        }
    }else {
        let keys = Object.keys(obj),
            i=0,
            length = keys.length,
            key,
            item;
        if(typeof Symbol !== 'undefined') keys = keys.comcat(Object.getOwnPropertySymbols(obj));
        for(;i<length;i++){
            key=keys[i];
            item=obj[key];
            if(callback.call(item,item,key) === false) break;
        }
    }
}
```

#### 实现数组/纯粹对象的深浅合并

> 【支持】深浅合并

```js
const merge = function merge() {
    let options,
        target=arguments[0] || {},
        i=1,
        length=arguments.length,
        deep=false,
        treated=arguments[length-1];
    if(typeof target === "boolean") {
        deep = target;
        target=arguments[i];
        i++;
    }
    if(Array.isArray(treated) && treated.treated) {
        length--;
    }else {
        treated=[];
        treated.treated=true;
    }
    if(typeof target !== "object" && !isFunction(target)) target={};
    for(;i<length;i++) {
        options=arguments[i];
        if(options == null) continue;
        if(treated.includes(options)) return options;
        treated.push(options);
        each(options,function(copy,name){
            let copyIsArray = Array.isArray(copy),
                copyIsObject = isPlainObject(copy),
                // 这里的clone是指将要进行合并后值存放的地方，也就是target上的
                clone = target[name];
            if(deep && copy && (copyIsArray || copyIsObject)) {
                if(copyIsArray && !Array.isArray(clone)) clone=[];
                if(copyIsObject && !isPlainObject(clone)) clone={};
                target[name] = merge(deep,clone,copy,treated)
            }else {
				target[name] = copy;
            }
        })
    }
    return target;
}
```

#### 深浅克隆

```js
// 实现数组/对象的深浅克隆
const clone = function clone() {
    let target = arguments[0],
        deep = false,
        type,
        isArray,
        isObject,
        result,
        Ctor,
        treated = arguments[arguments.length - 1];
    !Array.isArray(treated) || !treated.treated ? (treated = [], treated.treated = true) : null;
    if (typeof target === "boolean") {
        deep = target;
        target = arguments[1];
    }
    if (treated.indexOf(target) > -1) return target;
    treated.push(target);
    type = toType(target);
    isArray = Array.isArray(target);
    isObject = isPlainObject(target);
    if (target == null) return target;
    Ctor = target.constructor;
    if (/^(regexp|date)$/i.test(type)) return new Ctor(target);
    if (/^(error)$/i.test(type)) return new Ctor(target.message);
    if (/^(function|generatorfunction)$/i.test(type)) {
        return function proxy() {
            var args = Array.from(arguments);
            return target.apply(this, args);
        };
    }
    if (!isArray && !isObject) return target;
    result = new Ctor();
    each(target, function (copy, name) {
        if (deep) {
            result[name] = clone(deep, copy, treated);
            return;
        }
        result[name] = copy;
    });
    return result;
};
```

