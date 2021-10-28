---
title: node模块原理分析
date: 2020-12-07 08:36:44
categories: 原理分析
top: false
summary:  node模块原理分析
tags: 
 - 原理分析
---

### node模块原理分析

### 一、events模块

node中自己实现的发布订阅模块。订阅是将方法对应成一种`1:n`的关系。其中

* on：用来订阅
  * newListener：这个特殊，当监听了该事件之后，在每次on绑定新事件时，会先触发这个监听事件类型。参数是只有一个【type】为on的监听事件名
* once：订阅一次，当执行后自动删除订阅的事件
* off：移除对应的监听事件
* emit：用来执行监听事件的订阅回调

实现：

```js
function EventEmitter() {
    this._events = {} // 实例属性
}

EventEmitter.prototype.on = function(eventName, callback) {
    if (!this._events) this._events = {}

    // 如果绑定的事件不是newListener就要触发事件
    if (eventName !== 'newListener') {
        this.emit('newListener', eventName)
    }

    if (!this._events[eventName]) {
        this._events[eventName] = [callback]
    } else {
        this._events[eventName].push(callback)
    }
}

EventEmitter.prototype.emit = function(eventName, ...args) {
    if (!this._events) this._events = {}
    if (this._events[eventName]) {
        this._events[eventName].forEach(fn => fn(...args))
    }
}

EventEmitter.prototype.off = function(eventName, callback) {
    if (!this._events) this._events = {};
    if (this._events[eventName]) {
        this._events[eventName] = this._events[eventName].filter(fn => callback !== fn && fn.l !== callback) // 都不一样才要留下
    }
}

EventEmitter.prototype.once = function(eventName, callback) {
    // AOP思想
    const once = (...args) => {
        callback(...args)
        this.off(eventName, once)
    }
    once.l = callback
    this.on(eventName, once)
}

module.exports = EventEmitter
```

**使用时，我们得继承 EventEmitter原型上的方法，也就是on、once、emit、off**

```js
const EventEmitter = require('./events'); // 事件触发器
const util = require('util');
function Girl() {}
util.inherits(Girl, EventEmitter)
let girl = new Girl();

girl.on('newListener', (type) => { // 每次绑定都会触发此函数 (先触发newListener在把结果放到数组中)
    console.log(type) 
})
girl.on('test', (...args) => {
    console.log('哭', ...args)
})
girl.on('test', (...args) => {
    console.log('吃', ...args)
})
girl.on('test', () => {
    console.log('逛街')
})
girl.emit('test', 1, 2, 3)
```

> 输出结果： test、test、test、哭 1 2 3、吃 1 2 3、逛街

### 二、Buffer【缓冲区】

javascript 语言自身只有字符串数据类型，没有二进制数据类型，但在处理像`TCP`或`文件流`时，必须使用到二进制数据。因此在Node.js中，定义了一个Buffer类，该类用来创建一个专门存放二级制数据的缓存区。

在Node.js中，Buffer类是随着 Node 内核一起发布的核心库，Buffer库为Node.jsz带来了一种存储原始数据的方法，可以让Node.js处理二进制数据，每单需要在 Node.js中处理 `i/o`操作中移动的数据时，就有可能使用`Buffer`库。原始数据存储在 Buffer类的实例中。一个Buffer类似一个整数数组，但它对应于V8堆内存之外的一块原始内存。

**buffer与字符编码**

 buffer 可以和字符串任意转化通过`toString`，不支持`GBK`

```js
const buf = Buffer.from('runoob', 'ascii');

// 输出 72756e6f6f62
console.log(buf.toString('hex'));

// 输出 cnVub29i
console.log(buf.toString('base64'));
```

node.js目前支持的字符编码包括:

| 编码    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| ascll   | 仅支持7位ASCII数据，如果没有设置去高位的话，这种编码是非常快的 |
| utf8    | 多字节编码的 Unicode字符，许多我也和其他文档格式都使用`UTF8`，它的大小为1-4个字节，规定一个汉字为`3个字节` |
| base64  | Base64编码，4 * 6的数据表示方式【4个字节】                   |
| hex     | 将每个字节编码为两个16进制字符                               |
| utf16le | 2 或 4 个字节，小字节序编码的 Unicode 字符。支持代理对（U+10000 至 U+10FFFF） |
| ucs2    | utf16le 的别名                                               |
| latin1  | 一种把 Buffer 编码成一字节编码的字符串的方式                 |
| binary  | latin1 的别名。                                              |

> 前面四者掌握，其他了解即可

**两个特殊方法：toString | parseInt**

* 把任意进制转为为10进制 -----> `parseInt(进制数据，要转化的进制名)`

```js
console.log(parseInt('11111111'),2) // 255
```

* 把任意进制转化为任意进制------> `toString(要转化为的进制名)`

```js
console.log((0x16).toString(2)) // 10110
```

> * 二进制以 `0b`开头
> * 十六进制以`0x`开头
> * 八进制以`0`开头

**针对二进制的操作符**

| 操作符 | 描述                                                     |
| ------ | -------------------------------------------------------- |
| \|     | 有一个是1 则为1 ，`console.log(0b100 | 0b010) `，得到110 |
| &      | 同是1为1。`console.log(0b100 & 0b010)`,得到 110          |
| <<     | `console.log(1<<3)`;往左移动三位，得到00001000           |

#### 创建Buffer

| 方法                           | 描述                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| Buffer.alloc(size,[fill])      | 返回一个指定大小的Buffer实例，如果没有设置`fill`，则默认填满 0 |
| Buffer.allocUnsafe(size)       | 返回一个指定大小的 Buffer 实例，但是它不会被初始化，使用它可能包含敏感的数据 |
| Buffer.from(array)             | 返回一个被 array 的值初始化的新的Buffer实例（传入的array的元素只能是数字，不然就会自动被0覆盖） |
| Buffer.from(buffer)            | 复制传入的 Buffer实例的数据，并返回一个新的 Buffer实例       |
| Buffer.from(string,[encoding]) | 返回一个被 string的值初始化的新的Buffer实例，默认是`UTF-8`   |

```js
// 创建一个长度为 10、且用 0 填充的 Buffer。
const buf1 = Buffer.alloc(10);

// 创建一个长度为 10、且用 0x1 填充的 Buffer。 
const buf2 = Buffer.alloc(10, 1);

// 创建一个长度为 10、且未初始化的 Buffer。
// 这个方法比调用 Buffer.alloc() 更快，
// 但返回的 Buffer 实例可能包含旧数据，
// 因此需要使用 fill() 或 write() 重写。
const buf3 = Buffer.allocUnsafe(10);

// 创建一个包含 [0x1, 0x2, 0x3] 的 Buffer。
const buf4 = Buffer.from([1, 2, 3]);

// 创建一个包含 UTF-8 字节 [0x74, 0xc3, 0xa9, 0x73, 0x74] 的 Buffer。
const buf5 = Buffer.from('tést');

// 创建一个包含 Latin-1 字节 [0x74, 0xe9, 0x73, 0x74] 的 Buffer。
const buf6 = Buffer.from('tést', 'latin1');
```

#### Base64

* Base64是网络上最常见的用于传输`8Bit`字节码的编码方式之一
* Base64 就是一种基于 64个可打印字符来表示二进制数据的方法
* Base64 要求把每三个`8bit`的字节转换为`4个6Bit`的字节（38=>46=>24）,然后把`6bit`在添加两位高位`00`,组成4个bit的字节。这就让base64的一个字节最大不超过64的原因。最大为63

我们现在编码都是使用了`utf8`，那我们如何将一个`utf8`编码的汉字转化为 `base64`编码呢？

流程：

* 先创建buffer
* 再将buffer对应的16进制转化为二进制
* 在将二进制转化为 十进制
* 在找到十进制在str对应的表示数据，在将其他进行拼接，即可得到`base64`编码

```js
console.log(Buffer.from('珠')); //<Buffer e7 8f a0>
console.log((0xe7).toString(2)) // 11100111
console.log((0x8f).toString(2)) // 10001111
console.log((0xa0).toString(2)) // 10100000

// 11100111 10001111 10100000 3*8 => 4*6
// 00111001 00111000 00111110 00100000 最多是63
console.log(parseInt(0b00111001)) // 57
console.log(parseInt(0b00111000)) // 56
console.log(parseInt(0b00111110)) // 62
console.log(parseInt(0b00100000)) // 32

let str = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
str += str.toLowerCase();
str += '0123456789+/'; // 64
console.log(str[57] + str[56] + str[62] + str[32]) //54+g
```

封装方法：

```js
const CHARTS = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/';

function transfer(str) {
    let buf = Buffer.from(str);
    let result = '';
    for (let b of buf) {
        result += b.toString(2);
    }
    return result.match(/(\d{6})/g).map(val => parseInt(val, 2)).map(val => CHARTS[val]).join('');
}
let r = transfer('珠');
console.log(r); //54+g
```

#### 第三方解码包 iconv-lite

```bash
npm i iconv-lite
```

将二进制数据转化为`gbk`

```JS
const fs = require('fs');
const path = require('path')
const iconvLite = require('iconv-lite') // [解码用的]

let r = fs.readFileSync(path.resolve(__dirname, '1.txt'))
r = iconvLite.decode(r, 'gbk')
console.log(r)
```

#### Buffer常用的方法

下面的`buf`是实例，Buffer是类

##### 1.copy()

```js
buf.copy(targetBuffer,targetStart,sourceStart,sourceEnd) => 没有返回值
```

> 作用：将buf拷贝到目标buffer上，它是Buffer原型上的方法，下面是其实现的原理

```js
/**
 * targetBuffer 拷贝到的目标buffer
 * targetStart 拷贝到目标buffer的位置(可理解目标源的索引)
 * sourceStart 拷贝源的buffer的位置(可理解拷贝源的索引)
 * sourceEnd   要拷贝拷贝源数据的几个(可理解为拷贝源的长度)
 */
Buffer.prototype.copy = function(targetBuffer, targetStart, sourceStart = 0, sourceEnd = this.length) {
    for (let i = sourceStart; i < sourceEnd; i++) {
        targetBuffer[targetStart++] = this[i]; // 将自身的buffer中的内容拷贝到了目标buffer上
    }
}
```

调用：

```js
let buf4 = Buffer.from('前端')
let buf5 = Buffer.from('架构')
let buf = Buffer.alloc(12);
buf4.copy(buf, 0)
buf5.copy(buf, 6, 0, 6)
console.log(buf) // <Buffer e5 89 8d e7 ab af e6 9e b6 e6 9e 84>
```

##### 2.slice

```js
// startIndex 截取的开始索引【被包含】，endIndex 截取的结束索引【不被包含】
buf.slice(startIndex，endIndex) => 返回的是 buf  
```

> 作用：对buffer的地址截取，是引用关系。

注意：slice操作buffer是获取到了该buffer内存的地址，是引用关系，和数组的`slice`很像

例如：buf7是截取了buf6的第一个索引在内存中的引用关系。之后buf7对其赋值时会改变buf6的值

```js
let buf6 = Buffer.from([1, 2, 3]);
let buf7 = buf6.slice(0, 1); // 在内存中截出来某一个位置
buf7[0] = 1000
console.log(buf7) // <Buffer e8>
console.log(buf6) // <Buffer e8 02 03>
```

##### 3.indexOf

```js
// findTarget 是要查找的字符
buf.indexOf(findTarget)  => 返回的是一个 findTarget 在 buf的索引位置
```

> 作用：查找 findTarget 在buf的那个位置。

```js
// indexOf 字符串中的indexOf同理 返回的索引是字节的索引
let buf8 = Buffer.from('前端架构师培训')
let index = buf8.indexOf('师')
console.log('index的值为:', index) // index的值为: 12
```

##### 4.split

基于slice方法，封装split方法

```js
Buffer.prototype.split = function(sep) {
    let arr = [];
    let offset = 0;
    let current = 0;
    let len = Buffer.from(sep).length;

    while (-1 != (current = this.indexOf(sep, offset))) {
        arr.push(this.slice(offset, current));
        offset = current + len
    }
    arr.push(this.slice(offset));
    return arr;
}
```

> 作用：和字符串的`split`方法一样，这里是将buffer进行切割

```js
let buf8 = Buffer.from('前端架构师培训')
let result = buf8.split('师')
console.log(result) // [ <Buffer e5 89 8d e7 ab af e6 9e b6 e6 9e 84>,
                    //  <Buffer e5 9f b9 e8 ae ad> ]
console.log(result.toString()) // 前端架构,培训
```

##### 5.concat()

```js
Buffer.concat([buf1,buf2],[length]) => 返回值是 拼接后的新buffer
```

> 作用：将buf数组的buffer实例进行拼接组成一个新的buffer，相当于合并。它是Buffer的静态方法，下面是实现原理

```js
/**
 * bufferList 要进行拼接的buffer数组
 * length 规定拼接后的新buffer的长度
 */
Buffer.concat = function(bufferList, length) {
    if (typeof length == 'undefined') {
        length = 0;
        length = bufferList.reduce((total, curVal) => {
            return total += curVal.length
        }, 0)
    }
    let newBuffer = Buffer.alloc(length);
    let offset = 0;
    bufferList.forEach(buffer => {
        buffer.copy(newBuffer, offset);
        offset += buffer.length
    })
    return newBuffer
}
```

调用：

```js
let buf4 = Buffer.from('前端')
let buf5 = Buffer.from('架构')
let concatBuffer = Buffer.concat([buf4, buf5], 6)
console.log(concatBuffer.toString()) // 前端
```
##### 6.isBuffer

```js
Buffer.isBuffer(target)
```

> 作用：判断 target 是不是 Buffer类型，它是Buffer的静态方法，内部就是使用`instance Buffer`来判断target是不是Buffer产生的实例对象

```js
let buf = Buffer.from('1') 
console.log(buf) // <Buffer 31>
console.log(Buffer.isBuffer(buf)) // true
```

### 三、fs模块

fs是node提供给我们操作文件的模块，里面的`readFile/readFileSync....`，这些api都是同步和异步相互成套配对。他们只适合对小文件（在node中规定小于64k的文件）进行读取与写入操作，不适合对大文件的操作。这是我们就要使用node底层给我们提供的api `fs.open,fs.read,fs.write`.

>不适合大文件的操作原因：因为这些api都是一步将文件读取到内存中，在一步写入到硬盘文件内。当我们读取的文件大小远大于内存大小时，会造成内存溢出现象。当我们读取的文件占大量的内存时，会造成内存淹没的现象。而`fs.open,fs.read,fs.wirte`这些api可以让我们规定读取文件的`开始位置`和`结束位置`，那样我们就可以开辟一个小空间`来边读边消费`，实现用小内存来读取大文件并不会出现乱码问题。

* 打开文件

```js
fs.open(文件路径，文件操作符，callback(err,fd){})
```

* 读取文件

```js
fs.read(文件描述符，要读入的buffer，从buffer那个位置开始写入【索引】，写入几个【长度】，从文件那个位置读取【索引】，callback(err,bytestRead[读取到的真实数据个数]){})
```

* 写入文件

```js
fs.write(文件描述符，要写入文件的buffer，从buffer那个位置读取【索引】，读取buffer几个数据【长度】，写入那个位置【索引】，callback(err,wirtten[写入文件的真实数据个数]){})
```

嵌套应用案例

```js
const fs = require('fs');
const path = require('path');

let buffer = Buffer.alloc(3);
fs.open(path.resolve(__dirname, 'note.md'), 'r', function(err, fd) {
    // fd可以用来描述我要对这个文件做什么操作
    // 1.文件描述符 2.读取到那个buffer中 3,.从buffer的那个位置开始写入 4.写入几个 5.读取文件的位置
    fs.read(fd, buffer, 0, 3, 0, function(err, bytesRead) {
        // 将读取到的结果消费掉
        fs.open(path.resolve(__dirname, 'copy.md'), 'w', function(err, wfd) {
            // 从buffer的第0个位置读取3个 写入到文件的第0个位置
            fs.write(wfd, buffer, 0, 3, 0, function(err, written) {
                fs.close(fd, () => {})
                fs.close(wfd, () => {})
            })
        })
    })
})

fs.read()
```

异步迭代方式

```js
const fs = require('fs');
const path = require('path');

let BUFFER_SIZE = 3;
let buffer = Buffer.alloc(BUFFER_SIZE);
let readOffset = 0;
let wirteOffset = 0;
fs.open(path.resolve(__dirname, 'note.md'), 'r', function(err, fd) {
    fs.open(path.resolve(__dirname, 'copy.md'), 'w', function(err, wfd) {
        function next() {
            fs.read(fd, buffer, 0, BUFFER_SIZE, readOffset, function(err, bytesRead) {
                if (bytesRead > 0) { // 读取内容
                    fs.write(wfd, buffer, 0, bytesRead, wirteOffset, function(err, written) {
                        readOffset += bytesRead
                        wirteOffset += written
                        next()
                    })
                } else {
                    // 关闭操作
                    fs.close(fd, () => {})
                    fs.close(wfd, () => {})
                }

            })
        }
        next()
    })
})
```

> 上面的代码出现的问题：1.嵌套 2.读写本可以分开操作，现在耦合在一起了 
>
> 解决方法：用发布订阅来解耦 

#### 文件可读流

> 基于 `fs.open`、` fs.read`、` fs.close` ，实现的

```js
const fs = require('fs');
const path = require('path');

let rs = fs.createReadStream(path.resolve(__dirname, 'note.md'), {
    flags: 'r', // 操作文件标识
    encoding: null, // 编码 null 为二进制
    autoClose: true,  // 读取完毕自动触发close事件
    start: 0,  // 读取文件的开始位置 [所以]
    // end: 100, // 从索引0的位置读取到索引3的位置
    highWaterMark: 3 // 每次读取3个字节

})

// rs是可读流
// 当fs.open() api调用并将文件状态为打开状态，触发open事件
rs.on('open', function(fd) {
    console.log(fd)
})
let arr = []
// 当fs.read() api读取数据后触发该data事件
rs.on('data', function(chunk) { // 当用户监听data事件时内部会不停的将数据发射出来
    console.log(chunk)
    arr.push(chunk)
})
// 当fs.read() api从文件中读取不到了数据就触发end事件
rs.on('end', function() {
    console.log(Buffer.concat(arr).toString())
})
// 当fs.close() api调用 将操作文件描述符销毁了 触发该close事件
rs.on('close', function() { // close事件需要等待读取完毕后才会触发
    console.log('close')
})

// 文件流 和 流 是两个概念  文件流是基于流来实现的 (文件流采用open和close事件)
// on('data') on('end') 都是流的方法 =》 可读流标识就是拥有 data 和 end方法
```

> 文件流和流是两个概念，文件流是基于流来实现的，它内部自己独有的事件有`open`、`close`，而流的特征事件有`data`、`end` ，所以我们以后可以将`data`、`end`作为流的标识

文件可读流原理：

```js
const EventEmitter = require('events');
const fs = require('fs');
class ReadStream extends EventEmitter {
    constructor(path, options = {}) {
        super();
        this.path = path;
        this.flags = options.flags || 'r';
        this.encoding = options.encoding || null;
        this.autoClose = options.autoClose || true;
        this.start = options.start || 0;
        this.end = options.end || undefined;
        this.highWaterMark = options.highWaterMark || 64 * 1024;
        this.offset= 0 ;
        this.flowing = false; // 表示默认不是流动模式
        // 1. 对默认值进行操作
        // 2. 打开文件
        this.open(); // fs.open() 异步没法立即获取fd
        this.on('newListener', (type) => { // 当绑定的事件不是newListener 会触发回调方法
            if (type === 'data') { //只有用户监听了data的事件后，才去开启读取文件
                this.flowing = true; // 标识流是读取文件的状态，简称是流动模式
                this._read(); // 真正读取的方法
            }
        })
    }
    pause(){
        this.flowing = false;
    }
    resume(){
        if(!this.flowing){ // 不是流动模式在恢复成流动模式
            this.flowing = true;
            this._read();// 继续重新读取
        }
    }
    destroy(err) {
        if (err) {
            this.emit('error', err)
        }
        if(typeof this.fd === "number" && this.autoClose){
            fs.close(this.fd,()=>{
                this.emit('close');
            })
        }
    }
    _read(){ 
        // fs.read调用
        if(typeof this.fd !=='number'){ 
            return this.once('open',()=>this._read())
        }
        // 要写入多少个字节数据 
        let howMouchToRead = this.end? Math.min(this.highWaterMark,this.end - this.offset + 1):this.highWaterMark;
        let buffer = Buffer.alloc(howMouchToRead); // 每次读取的个数

        fs.read(this.fd,buffer,0,howMouchToRead,this.offset,(err,byteRead)=>{
            if(err) return this.destroy(err);
            //真实的读取到的个数
            if(byteRead > 0){
                this.emit('data',buffer.slice(0,byteRead));
                this.offset +=  byteRead; 
                if(this.flowing){ // 如果是流动模式就继续下一轮的读取
                    this._read();
                }
            }else{
                this.emit('end');// 文件读取完毕后才触发
                this.destroy();// 触发close事件
            }
        });
    }
    open() {
        fs.open(this.path, this.flags, (err, fd) => {
            if (err) {
                return this.destroy(err);
            }
            this.fd = fd;
            this.emit('open', fd);
        })
    }
}

module.exports = ReadStream
```

使用：

```js
const fs = require('fs');
const path = require('path');
// fs.open fs.read fs.close 
// 文件流可读流 是基于stream模块中Readable接口
const ReadStream = require('./ReadStream');
let rs = new ReadStream(path.resolve(__dirname,'note.md'),{
    flags:'r',
    encoding:null,
    autoClose:true,
    start:0,
    highWaterMark:3, // 每次读取3个字节
    end:6 // 从索引0的位置读取到索引3的位置
});
// rs 是可读流对象
rs.on('open',function (fd) { // 内部会emit
    console.log(fd);
});
let arr = []
rs.on('data',function (chunk) { // 当用户监听data事件时内部会不停的将数据发射出来
     console.log(chunk);
    //  rs.pause(); // 暂停读取
     arr.push(chunk);
})
rs.on('end',function () {
    console.log(Buffer.concat(arr).toString())
})
rs.on('close',function () { // close 事件需要等待读取完毕后才会触发 fs.close
    console.log('close')
})

```

> 文件可读流需要掌握的方法：
>
> 这里的callback是表示对于on绑定的事件数组的所有callback
>
> * on('data',callbacks) 文件读取一次就触发一次callbacks【多次触发】
> * on('end',callbacks)  文件全部读取完毕后触发callbacks【只触发一次】
> * on('open',callbacks) 文件是打开状态就触发callbacks [只触发一次]
> * on('close',callbacks) 文件全部读取完毕或读取文件失败并且要关闭文件时触发callbacks [只触发一次]
> * rs.pause() 暂停文件可读流的读取
> * rs.resume() 恢复文件可读流的读取

#### 文件可读流

> 基于：` fs.open`、`  fs.write`、`fs.close`方法实现的

```js
// 我希望写入10个数 (0-9) 只用一个字节的内存

const fs = require('fs');
const path = require('path');

let ws = fs.createWriteStream(path.resolve(__dirname, 'copy.md'), {
    flags: 'w',
    encoding: 'utf8',
    mode: 0o666,
    emitClose: true,
    start: 0,
    highWaterMark: 1, // 我期望使用多少字节完成写入操作，如果超出或等于这个值后  返回false 可以用于判断，告知用户不要在写入了，在写入只能放到内存中，占用内存
})

let index = 0;

function write() {
    let writting = true;
    while (index < 10) { // 真正写入到文件后 操作highWaterMark的数量会减少直到为0
        writting = ws.write(index++ + ''); // write方法只能使用 string 或buffer类型
        if (!writting) {
            break;
        }
    }
    if (index == 10) {
        ws.end('ok'); // 常出现报错 write after end 不能在关闭后继续写入 end 相当于 write + close
        // 触发了end就不会在触发drain事件了
    }
}
// 当写入的个数达到或者超过这个预期后被消费掉，会触发drain事件
ws.on('drain', () => { 
    console.log('drain')
    write()

})
// 当文件写入完毕后调用 fs.close()会触发该事件
ws.on('close', () => {
    console.log('close')
})

write()

// writeStream要掌握的方法有:
// write ws.end() on('drain')
```

文件可写流原理：

```js
const EventEmitter = require('events');
const fs = require('fs');

class WriteStream extends EventEmitter {
    constructor(path, options = {}) {
        super();
        this.path = path;
        this.flags = options.flags || 'w';
        this.encoding = options.encoding || 0x666;
        this.emitClose = options.emitClose || true;
        this.start = options.start || 0;
        this.highWaterMark = options.highWaterMark || 16 * 1024;

        this.writing = false; // 默认不是正在写入，第一次调用write的时候需要执行fs.write方法
        this.len = 0; // 此长度指向写入的个数，写入后需要进行减少
        this.needDrain = false; // 是否触发drain事件
        this.offset = 0;
        this.cache = [];

        this.open(); // 保证打开文件
    }
    destroy(err) {
        if (err) {
            this.emit('error', err);
        }
    }
    open() {
        fs.open(this.path, this.flags, this.mode, (err, fd) => {
            if (err) return this.destroy(err)
            this.fd = fd;
            this.emit('open', fd);
        })
    }
    end() {
        if (typeof this.fd === 'number' && this.emitClose) {
            fs.close(this.fd, () => {
                this.needDrain = false
                this.emit('close');
            })
        }
    }
    clearBuffer() {
        // 清空缓存
        let data = this.cache.shift();
        if (data) {
            this._write(data.chunk, data.encoding, data.clearBuffer);
        } else {
            this.writing = false; // 后续的第一次操作进行往文件写入
            if (this.needDrain) {
                this.needDrain = false; // 更新needDrain
                this.emit('drain');
            }
        }
    }
    write(chunk, encoding = this.encoding, cb = () => {}) {
        chunk = Buffer.isBuffer(chunk) ? chunk : Buffer.from(chunk); // 统一成buffer
        this.len += chunk.length;
        let result = this.len < this.highWaterMark;
        this.needDrain = !result; // 超过预期或者达到预期需要触发

        const clearBuffer = () => {
            this.clearBuffer();
            cb();
        }

        if (this.writing) {
            // 将写入的内容缓存起来
            this.cache.push({
                chunk,
                encoding,
                clearBuffer
            });
        } else {
            this.writing = true; // 正在写入
            this._write(chunk, encoding, clearBuffer);
        }
        return result;
    }
    _write(chunk, encoding, cb) {
        // 绑定事件 监听open
        if (typeof this.fd !== 'number') {
            return this.once('open', () => this._write(chunk, encoding, cb));
        }
        fs.write(this.fd, chunk, 0, chunk.length, this.offset, (err, written) => {
            this.offset += written; // 每次更改偏移量 减少缓存数量
            this.len -= written;
            cb(); // 当前写入后 要清空缓存
        })
    }
}

module.exports = WriteStream
```

调用：

```js
// 我希望写入10个数 （0-9） 只用一个字节的内存

const fs = require('fs');
const path = require('path');
const WriteStream = require('./WriteStream');
let ws = new WriteStream(path.resolve(__dirname, 'copy.md'), {
    flags: 'w',
    encoding: 'utf8',
    mode: 0o666,
    emitClose: true,
    start: 0,
    highWaterMark: 5, // 我期望使用多少个字节完成写入操作，如果超出后write的返回值会变成false， 返回false 可以用于判断，告知用户不要在写入了，再写入只能放到内存中，占用内存
});
let index = 0;
// 循环操作时同步的，会批量写入十次，第一次调用fs.write, 后面的操作都放到了内存中
// 读和写的搭配流程 先读取=》调用write方法写入，如果放下超过预期，暂停读取 =》 等待写入完毕后=》触发drain事件=》在恢复读取，周而复始。 实现边读边写的功能


function write() {
    let writting = true;
    while (index < 10) { // 真正写入到文件后会减少数量
        writting = ws.write(index++ + ''); // write方法只能使用string 或buffer;
        if (!writting) {
            break;
        }
    }
    if(index == 10){
        //ws.end('ok'); // write after end 不能在关闭后继续写入 end = write + close
        // 触发了end 就不会再触发drain事件了
    }
}
ws.on('drain',()=>{ // 当写入的个数达到或者超过预期后被消费掉后，会触发drain事件
    console.log('drain');
    write();
});

ws.on('close',()=>{
    console.log('close')
})
write();
// write() end()  on('drain')  
```

> 文件可写流需要掌握的方法
>
> * ws.write() 调用写入文件的方法
> * ws.end(endString) 关闭要写入的文件的操作。象征着文件可写流结束。调用end方法相当于 触发write和close方法，它会在关闭前将`endString`写入到文件的尾部。并且调用end后`drain`事件将不会再次触发
> * on('close',callbacks) 是要触发了 `ws.end()` 事件后`callbacks`才会被触发
> * on('drain',callbacks) 当写入文件的个数达到或等于了预期的个数就会触发`callbacks`方法

#### pipe（管道）

pipe是管道的意思，基于文件可读流和可写流的特点，实现读一点写一点，极大有效的减少了内存使用。

> 依赖 `rs.data()`、`rs.end()`、`rs.pause()`、`rs.resume()`、`ws.write()`、`ws.end()`

```js
rs.on('data', (chunk) => {
    let r = ws.write(chunk);
    if (!r) {
        rs.pause()
    }
})

rs.on('end', function() {
    console.log('end')
    ws.end(); // 关闭可写流
})

ws.on('drain', function() {
    rs.resume()
})
```

内置于`ReadStream`

```js
const EventEmitter = require('events');
const fs = require('fs');
// on('data') on('end') pause() resume()
class ReadStream extends EventEmitter {
    constructor(path, options = {}) {
        super();
        this.path = path;
        this.flags = options.flags || 'r';
        this.encoding = options.encoding || null;
        this.autoClose = options.autoClose || true;
        this.start = options.start || 0;
        this.end = options.end || undefined;
        this.highWaterMark = options.highWaterMark || 64 * 1024
        this.offset = 0;
        this.flowing = false; // 表示默认不是流动的
        // 1.对默认值进行操作
        // 2.打开文件
        this.open();
        this.on('newListener', (type) => { // 当绑定的事件不是newListener 会触发回调方法
            if (type === 'data') {
                this.flowing = true; // 流动模式
                this._read(); // 真正读取的方法
            }
        })
    }
    pause() {
        this.flowing = false;
    }
    resume() {
        if (!this.flowing) { // 不是流动模式在恢复成流动模式
            this.flowing = true;
            this._read(); // 继续重新读取
        }
    }
    // ------------------------------------------------------------------
    pipe(ws) {
        this.on('data', (chunk) => {
            let r = ws.write(chunk)
            if (!r) {
                this.pause()
            }
        })
        ws.on('drain', () => {
            this.resume()
        })
        this.on('end', () => {
            ws.end()
        })
    }
  // ------------------------------------------------------------------

    destroy(err) {
        if (err) {
            this.emit('error', err)
        }
        if (typeof this.fd === 'number' && this.autoClose) {
            fs.close(this.fd, () => {
                this.emit('close')
            })
        }
    }
    _read() { // 第一次不会立刻得到 this.fd(文件操作符)
        // // fs.read调用
        if (typeof this.fd !== 'number') {
            return this.once('open', () => this._read()) // 为了_read内得到this.fd(文件操作符)
        }
        // 1234567890
        // 123 456 789 0 没有传递end 每次读3个 直到读取完毕即可
        // 123 45 如果传递了end=4
        let howMuchToRead = this.end ? Math.min(this.highWaterMark, this.end - this.offset + 1) : this.highWaterMark;
        let buffer = Buffer.alloc(howMuchToRead); // 每次读到的个数

        fs.read(this.fd, buffer, 0, howMuchToRead, this.offset, (err, byteRead) => {
            if (err) return this.destroy(err);
            if (byteRead > 0) {
                this.emit('data', buffer.slice(0, byteRead));
                this.offset += byteRead;
                if (this.flowing) {
                    this._read(); // 如果是流动模式就继续下一轮读取
                }

            } else {
                this.emit('end'); // 文件读取完毕后才触发
                this.destroy(); // 触发close事件
            }
        })
    }
    open() {
        fs.open(this.path, this.flags, (err, fd) => {
            if (err) {
                return this.destroy(err);
            }
            this.fd = fd;
            this.emit('open', fd);
        })
    }
}

module.exports = ReadStream
```

使用：

```js
const ReadStream = require('./ReadStream');
const WriteStream = require('./WriteStream');
const path = require('path');

const rs = new ReadStream(path.resolve(__dirname, 'note.md'), {
    highWaterMark: 4 // 64*1024
});

const ws = new WriteStream(path.resolve(__dirname, 'copy.md'), {
    highWaterMark: 10
})

// 边读边写入 为了解决大文件的读写操作
rs.pipe(ws);
```

### 四、Http

什么是http？应用层

> 通常的网络是在`TCP/IP`协议族的基础上来运作的，HTTP是一个子集。

TCP/IP协议族(HTTP应用层协议 在传输层的基础上增加了一些自己的内容)

> 协议简单来说就是通信的规则，例如：通信时谁先发起请求，怎样结束，如何进行通讯。把互联网相关的协议统称起来称为`TCP/IP`

协议分层(OSI协议分层)

> (物、数)，网，传，(会，表，应)
>
> * 应用层 HTTP,FTP,DNS （与其他计算机进行通讯的一个应用服务，向用户提供应用服务时的通信活动）
> * 传输层 TCP (可靠)，UDP数据传输（HTTP->TCP DNS->UDP）
> * 网络层 IP 选择传输路线 （通过ip地址和mac地址）使用ARP协议凭借mac地址进行通信
> * 链路层 网络连接硬件部分

![http](/medias/imges/theory/http.png)

HTTP特点

> * http 是不保存状态的协议，使用`cookie`来管理状态（登录先给你cookie 我可以看一下你有没有cookie）
> * 为了防止每次请求都会造成无谓的`tcp`链接建立和断开，所以采用保持链接的方式`keep-alive`
> * 以前发送请求后需要等待并受到响应，才能发下一个，下载都是管线化的方式（js css可以并发请求）

HTTP缺点：

> * 通信采用明文
> * 不验证通信方的身份
> * 无法验证内容的完整性（内容可能被篡改）
>
> > 这些可以通过 SSL (安全套阶层) 建立安全通信线路 HTTPS (超文本传输安全协议)

HTTP方法（get post 简单请求）Resful 风格

> * GET：获取资源 
> * POST：传输实体文本，请求体中
> * PUT：传输文件
> * HEAD：获取报文头
> * DELETE：删除文件、
> * OPTIONS：询问支持的方法  跨域 默认发送的是`get/post`不能发送options请求，当我们在`get/post`加上了自定义请求头，那就会发送options请求，处理简单的`get/post`其他请求都为复杂请求，都会发送options

**REST API**：Resful风格，根据路径和不同的方法就能确定对资源进行什么操作

HTTP状态码（发请求 命令行 curl命令）

> * curl 命令行工具
> * 1xx 信息性状态码 websocket upgrade
> * 2xx 成功状态码 200(成功) 204(没有响应体)  206(范围请求 暂停进行下载)  
> * 3xx  301(永久重定向) 302(临时) 重定向  304 缓存 (服务端缓存策略) 307
> * 4xx  400 401 用户没有登录权限  403 用户登录了没权限 404 没找到 405 方法不存在
> * 5xx 服务端异常 502 503

#### 实例：手写mini-http-server

* 配置目录

> ---bin
>
> ​	-----config.js
>
> ​	-----www
>
> ---node_module
>
> ---src
>
> ​	-----eje.js
>
> ​	-----server.js
>
> ​	------template.html
>
> package.json

* 下载第三包

```bash
npm i chalk commander mime ejs 
```

* 配置package.json

```json
{
    "name": "zf-hs",
     "bin": "/bin/www",
}
```

* config.js 【commander配置项】

```js
// 自定义需要显示到的命令行中的命令
const config = { // 给自己来维护参数的
    'port': {
        option: '-p,--port <n>', // <n> 表示的是一个值
        descriptor: 'set your server port',
        default: 8080,
        usage: 'zf-hs --port <n>'
    },
    'directory': {
        option: '-d,--directory <n>',
        descriptor: 'set your server start directory',
        default: process.cwd(),
        usage: 'zf-hs --directory <n>'
    },
    'cache': {
        option: '-c,-cache <n>',
        descriptor: 'set your server cache',
        default: 'no-cache',
        usage: 'zf-hs --cache <n>'
    }
}

module.exports = config;
```

* www

```js
#! /usr/bin/env node

const program = require('commander');
const chalk = require('chalk');
const config = require('./config');
const Server = require('../src/server')
program.name('zf-hs'); // 配置使用的名字

// 写服务 
// 1.端口号 2.能指定目录 3.文件是否需要缓存 4.....
const defaultConfig = {};
const usageList = [];
// [[key,value],[key,value]]
Object.entries(config).forEach(([key, value]) => {
    defaultConfig[key] = value.default;
    usageList.push(value.usage);
    program.option(value.option, value.descriptor)
});
program.on('--help', function() {
    console.log('Examples:')
    usageList.forEach(line => {
        console.log(`  ${chalk.green(line)} \r`);
    })
})

// 根据用户的参数和默认值做出一个配置项
program.parse(process.argv); // 解析用户执行时的参数

function mergeOptions(defaultConfig, newConfig) {
    const options = {};
    for (let key in defaultConfig) {
        if (!(key in newConfig)) {
            options[key] = defaultConfig[key];
        } else {
            // 可以校验newConfig 是否符合预期
            options[key] = newConfig[key];
        }
    }
    return options;
}

let options = mergeOptions(defaultConfig, program);

// 获取用户的参数来创建一个服务并启动
let server = new Server(options);
server.start();
```

* src/server.js

```js
const http = require('http');
const url = require('url');
const path = require('path');
const fs = require('fs').promises;
const chalk = require('chalk');
const mime = require('mime');
const ejs = require('./ejs');
const { createReadStream, createWriteStream } = require('fs');

class Server {
    constructor(options) {
        this.port = options.port;
        this.directory = options.directory;
        this.cache = options.cache;
    }
    async handleRequest(req, res) {
        let { pathname } = url.parse(req.url);
        pathname = decodeURIComponent(pathname); // decode 是解码
        // 列出所有的文件夹
        let requestUrl = path.join(this.directory, pathname); // 路径带/的不要用resolve会回到根路径
        try {
            const statObj = await fs.stat(requestUrl);
            if (statObj.isDirectory()) {
                // 目录 文件目录
                let dirs = await fs.readdir(requestUrl);
                let content = await ejs.renderFile(path.resolve(__dirname, 'template.html'), {
                    dirs: dirs.map(dir => ({
                        name: dir,
                        pathname: path.join(pathname, dir)
                    }))
                });
                res.setHeader('Content-Type', 'text/html;charset=utf-8');

                res.end(content)
            } else {
                // 文件 读取文件
                this.sendFile(requestUrl, req, res, statObj);
            }
        } catch (e) {
            console.log(e)
            this.sendError(e, req, res);
        }
    }
    sendError(err, req, res) {
        res.statusCode = 404;
        res.end('Not Fount');
    }
    sendFile(filePath, req, res, stat) {
        res.setHeader('Content-Type', mime.getType(filePath) + ';charset=utf-8');
        createReadStream(filePath).pipe(res); // res是一个可写流 会有 res.write() res.end()
    }
    start() {
        const server = http.createServer(this.handleRequest.bind(this));
        server.listen(this.port, () => {
            console.log(`${chalk.yellow('Starting up http-server ,servering')}`);
            console.log(`  http://127.0.0.1:${chalk.green(this.port)}`)
        })
    }
}

module.exports = Server
```

* src/template.html

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
    <% dirs.forEach(item=>{ %>
        <li>
            <a href="<%=item.pathname%>">
                <%=item.name%>
            </a>
        </li>
        <% }) %>
</body>

</html>
```

* ejs.js

> ejs的原理就是：正则匹配，模板字符串的拼接，利用new Function + with语法将其包裹成一个函数执行，后得到一个拼接后的模板字符串

```js
const fs = require('fs').promises
let ejs = {
    async renderFile(templateUrl, data) {
        let content = await fs.readFile(templateUrl, 'utf8');
        let head = `let html='';\r\n with(obj){\r\n`;
        head += 'html+=`'
        content = content.replace(/<%=(.+?)%>/g, function() {
            return '${' + arguments[1] + '}'
        })
        let body = content.replace(/<%(.+?)%>/g, function() {
            return '`\r\n' + arguments[1] + '\r\n html+=`'
        })
        let tail = '`}\r\n return html'
        let templateStr = head + body + tail;
        // 可以隔离作用域 会创建一个和全局平行的作用域
        let fn = new Function('obj', templateStr);
        return fn(data);
        // with + new Function()
    }
}

module.exports = ejs
```

#### 缓存设置(catch)

我们浏览器自身有缓存机制的，它在访问某些文件时，会判断是走缓存呢还是往服务器再次发送请求获取呢？这都得靠我们服务器来设置文件第一次返回时得`响应报文`，缓存类型可分为：`强制缓存`、`协商缓存（对比缓存）`

* 强制缓存

> 强制缓存只对我们引用资源有效，对于我们直接访问的资源是无效的（url路径访问的资源）。其存在的风险就是：服务器更改了文件，客户端用的还是老的文件。

```js
// 设置的响应头：
res.setHeader('Cache-Control','max-age=30'); // 30s内不在访问服务器
res.setHeader('Expires',new Date(Date.now()+3*1000).toUTCString()); // 兼容老版本，作用与上面是一样的
```

服务器设置代码:

```js
// 服务端主要的功能就是告诉浏览器要不要使用它的缓存 希望引用的资源没有变化的情况下 就使用默认的浏览器的缓存就好了
const http = require('http');
const path = require('path');
const fs = require('fs');
const url = require('url');
const mime = require('mime');

const server = http.createServer((req, res) => {
    let { pathname } = url.parse(req.url);
    let filePath = path.join(__dirname, pathname);
    console.log(filePath);
    // 这个都会缓存(强制缓存只对我们的引用资源生效)
    // 直接访问的资源不会走强制缓存
    // 强制缓存的风险就是  服务器更改了文件 客户端用的还是老的文件 服务器根据文件类型来设置缓存时间
    
    res.setHeader('Cache-Control', 'max-age=30');
    res.setHeader('Expires', new Date(Date.now() + 3 * 1000).toUTCString()); // 访问后到第几点别找我了
    // 优化:我希望超过缓存的时间后 文件没有变化(对比、协商)  继续走缓存

    fs.stat(filePath, (err, statObj) => {
        if (err) {
            res.statusCode = 404;
            res.end('not Found')
        } else {
            if (statObj.isFile()) {
                res.setHeader('Content-Type', mime.getType(filePath) + ';charset=utf-8');
                fs.createReadStream(filePath).pipe(res);
            } else {
                res.statusCode = 404;
                res.end('not Found')
            }
        }
    })
})

server.listen(3000, () => {
    console.log('3000服务器开启')
})
```

* 协商缓存(对比缓存)

> 何为协商缓存？当文件第一次访问服务器，我们在响应头上设置一个标识，当下一次在访问服务器时，我们对比一下这个标识有没有改变，改变则返回最新文件，若不变，则返回状态码`304`，浏览器得到该状态码则会从自己的缓存中获取文件内容。注意：**首页【直接访问的资源】它不走强制缓存但一定会走协商缓存。**

```js
// no-cache 表示将文件缓存了但还是会访问服务器。
// no-store 表示不将文件缓存，每次都是往服务器获取文件。
res.setHeader('Cache-Control', 'no-cache'); // 这是为了每次都像服务器发请求 (因为浏览器有默认的强制缓存时间)
// 设置标识 请求头对应的header为 `if-modified-since`
res.setHeader('Last-Modified',可以是文件的修改时间或其他做标识[ctime])
```

服务器代码：

```js
// 服务端主要的功能就是告诉浏览器要不要使用它的缓存
const http = require('http');
const path = require('path');
const fs = require('fs');
const url = require('url');
const mime = require('mime');

const server = http.createServer((req, res) => {
    let { pathname } = url.parse(req.url);
    let filePath = path.join(__dirname, pathname);
    res.setHeader('Cache-Control', 'no-cache'); // 这是为了每次都像服务器发请求 (因为浏览器有默认的强制缓存时间)

    fs.stat(filePath, (err, statObj) => {
        if (err) {
            res.statusCode = 404;
            res.end('not Found')
        } else {
            if (statObj.isFile()) {
                res.setHeader('Content-Type', mime.getType(filePath) + ';charset=utf-8');
                let ctime = statObj.ctime.toUTCString();
                // 服务端给你应该Last-Modified 客户端下次请求时会携带一个if-modified-since 
                if (req.headers['if-modified-since'] == ctime) {
                    // 让浏览器去找缓存
                    res.statusCode = 304;
                    res.end()
                } else {
                    res.setHeader('Last-Modified', ctime); // 这里设置
                    fs.createReadStream(filePath).pipe(res);
                }

            } else {
                res.statusCode = 404;
                res.end('not Found')
            }
        }
    })
})

server.listen(3000, () => {
    console.log('3000服务器开启')
})
```

优化：`last-modified`的方式有一个问题：当文件内容并没有被修改，修改时间变了，那么文件在一次访问服务器时，并重新读取文件内容，浏览器并不走缓存。优化方法：`Etag`来做更为确切的标识，例如读取文件后生成一个md5戳。

> 在响应头上设置`Etag`，其在请求头上对应`if-none-match`

```js
// 服务端主要的功能就是告诉浏览器要不要使用它的缓存
const http = require('http');
const path = require('path');
const fs = require('fs');
const url = require('url');
const mime = require('mime');
const crypto = require('crypto');

const server = http.createServer((req, res) => {
    let { pathname } = url.parse(req.url);
    let filePath = path.join(__dirname, pathname);
    res.setHeader('Cache-Control', 'no-cache'); // 这是为了每次都像服务器发请求 (因为浏览器有默认的强制缓存时间)

    fs.stat(filePath, (err, statObj) => {
        if (err) {
            res.statusCode = 404;
            res.end('not Found')
        } else {
            if (statObj.isFile()) {
                res.setHeader('Content-Type', mime.getType(filePath) + ';charset=utf-8');
                let ctime = statObj.ctime.toUTCString();

                let etag = crypto.createHash('md5').update(fs.readFileSync(filePath)).digest('base64');

                // 服务端给你应该Last-Modified 客户端下次请求时会携带一个if-modified-since 
                if (req.headers['if-none-match'] == etag) {
                    // 让浏览器去找缓存
                    res.statusCode = 304;
                    res.end()
                } else {
                    res.setHeader('Etag', etag); // 这里设置
                    fs.createReadStream(filePath).pipe(res);
                }

            } else {
                res.statusCode = 404;
                res.end('not Found')
            }
        }
    })
})

server.listen(3000, () => {
    console.log('3000服务器开启')
})
```

> 这会存在一个问题：当文件很大时，我们并没有必要去读取完文件在去进行摘要，这浪费性能很大。一般情况下，我们会生成一个容易的`etag`，自定义规则。

* 强制缓存+协商缓存

> 场景：强制缓存是在`某段时间内不在请求访问`，协商缓存是`每次请求服务器要判断一下，是否返回304`，将两者结合，可以实现更为合理的浏览器的缓存机制。例如：`index.css`，我们设置`Catch-Control`为`max-age=5`，并设置合理的`Last-Modified`与`Etag`，那么`index.css`  5s 内不再访问服务器，5s后在访问服务器时，在判断一下`Last-Modified`或`Etag`是否改变【这里的判断灵活规定，可判断其一或两者都判断】，改变则返回新的文件，不变则返回`304`,让浏览器走自己的缓存。

服务器代码：**优化http-server的代码，加入缓存机制**

```js
const http = require('http');
const url = require('url');
const path = require('path');
const fs = require('fs').promises;
const chalk = require('chalk');
const mime = require('mime');
const ejs = require('./ejs');
const { createReadStream, createWriteStream, readFileSync } = require('fs');
const crypto = require('crypto');

class Server {
    constructor(options) {
        this.port = options.port;
        this.directory = options.directory;
        this.cache = options.cache;
    }
    async handleRequest(req, res) {
        let { pathname } = url.parse(req.url);
        pathname = decodeURIComponent(pathname); // decode 是解码
        // 列出所有的文件夹
        let requestUrl = path.join(this.directory, pathname); // 路径带/的不要用resolve会回到根路径
        try {
            const statObj = await fs.stat(requestUrl);
            if (statObj.isDirectory()) {
                // 目录 文件目录
                let dirs = await fs.readdir(requestUrl);
                let content = await ejs.renderFile(path.resolve(__dirname, 'template.html'), {
                    dirs: dirs.map(dir => ({
                        name: dir,
                        pathname: path.join(pathname, dir)
                    }))
                });
                res.setHeader('Content-Type', 'text/html;charset=utf-8');

                res.end(content)
            } else {
                // 文件 读取文件
                this.sendFile(requestUrl, req, res, statObj);
            }
        } catch (e) {
            console.log(e)
            this.sendError(e, req, res);
        }
    }

    sendError(err, req, res) {
        res.statusCode = 404;
        res.end('Not Fount');
    }
    cacheFile(filePath, req, res, stat) {
        // 设置强制缓存
        res.setHeader('Cache-Control', 'max-age=10');
        // 得到上一次修改时间
        const LastModified = stat.ctime.toUTCString();
        // 摘要签名做标识
        const Etag = crypto.createHash('md5').update(readFileSync(filePath)).digest('base64');
        // 设置上一次修改的时间
        res.setHeader('Last-Modified', LastModified);
        //设置Etag签名标识
        res.setHeader('Etag', Etag);
        // last-modified对应的请求头
        let ifModifiedSince = req.headers['if-modified-since'];
        // etag对应的请求头
        let ifNoneMatch = req.headers['if-none-match'];
        if (LastModified !== ifModifiedSince) {
            return false
        }
        if (Etag !== ifNoneMatch) {
            return false
        }
        return true
    }
    sendFile(filePath, req, res, stat) {
        // sendFile中可以设置缓存   
        if (this.cacheFile(filePath, req, res, stat)) {
            res.statusCode = 304;
            return res.end();
        }

        res.setHeader('Content-Type', mime.getType(filePath) + ';charset=utf-8');
        createReadStream(filePath).pipe(res); // res是一个可写流 会有 res.write() res.end()
    }
    start() {
        const server = http.createServer(this.handleRequest.bind(this));
        server.listen(this.port, () => {
            console.log(`${chalk.yellow('Starting up http-server ,servering')}`);
            console.log(`  http://127.0.0.1:${chalk.green(this.port)}`)
        })
    }
}

module.exports = Server
```

#### crypto

> crypto 是node一个核心库，提供给我们一些摘要算法`(md5)`，加盐算法`(sha1,sha256)`，统称为摘要算法。他们都不是加密算法，因为加密算法是要能被解密的。

摘要算法的特点：

* 内容摘要后不能被分解，只能是被暴力破解（撞库）
* 我们不能根据摘要的结果反推摘要前的内容，而只要内容有一点变化，摘要的内容就会完全不一样（雪崩效应）
* 相同的值摘要的结果相同
* **所有的摘要出来的结果长度都相同**

> 下面的代码`.update`可以链式调用

```js
const crypto = require('crypto')
// md5
let r = crypto.createHash('md5').update('123').update('456').digest('base64')
console.log(r)  // 4QrcOUm6Wau+VuBX8g+IPg==
console.log(crypto.createHash('md5').update('123456').digest('base64')); // 4QrcOUm6Wau+VuBX8g+IPg==

// sha1 / sha256 ----> 加盐算法
let r1 = crypto.createHmac('sha1', 'test1').update('123').digest('base64')
let r2 = crypto.createHmac('sha1', 'test2').update('123').digest('base64')

console.log(r1) // n4ZSmUyGBpgMoT8tiGTYnhdHaF0=
console.log(r2) // iRyPagbJv2UxKZJn7K8rTLASF+o=
```

### 四、cookie session jwt 

> http是无状态的，每次访问服务器它都不会记住我们是谁，我们是否来过。这让我们做鉴权业务很伤脑筋，早起我们采用的是`cookie`来解决http的无状态问题，之后由于cookie是明文并存储与浏览器的，这安全性不好，为了解决cookie的安全性问题，出现了session【session基于cookie】，session + cookie 实现了一定的安全机制，服务器只发送session的标识到cookie上，这样每次请求我们都携带上cookie去服务器上获取到session的存储的值，实现鉴权业务。session的缺点是：它要在服务器上开辟一个session空间，如果我们不定时清除，很可能造成内存泄漏的问题。现在做鉴权业务最典型的就是采用`jwt`，它并不会像session一样要专门开辟一个空间来存储，它只需要将请求携带上来的`token`进行摘要对比，即可。

#### cookie

简单来说，cookie就是一个头字段。最为简单设置`cookie`的方法如下：

cookie设置的主要字段有:

| 字段名    | 描述                                             |
| --------- | ------------------------------------------------ |
| key/value | cookie名与值                                     |
| domain    | 限制cookie能被访问的域名范围                     |
| path      | 限制cookie能被访问的路径范围                     |
| max-age   | cookie能存活多久，单位是`秒(s)`                  |
| exipres   | cookie能存活多少，单位是`毫秒(ms)`，确切的时间点 |
| httpOnly  | 设置cookie是否能被前端`js`代码所修改，`布尔值`   |

```js
const http = require('http');

// 设置cookie 参数
// key/value/domain/path/maxAge/expires/httpOnly

// domain 限制域名 默认是当前的域名
// path 限制设置cookie 的路径 (基本用不到) 减少对cookie传入
// maxAge (多少秒) / expires 确切的时间点 () cache
// cookie 可以设置 httpOnly 不能通过代码去更改cookie字段
const server = http.createServer((req, res) => {
    // cookie 就是一个header 默认cookie 会话结束时销毁 如果增加失效时间，可以延迟销毁
    if (req.url === '/read') {
        res.end(req.headers['cookie'])
    } else if (req.url === '/write') {
        res.setHeader('Set-Cookie', ['name=zf; max-age=10', 'age=10; path="/read"; httpOnly=true']);
        res.end('write ok ')
    } else {
        res.end('not found');
    }
})

server.listen(3000)
```

函数封装设置 cookie 与 获取cookie，并保证cookie不能被修改。下面给cookie设置了`siged`属性即可实现

```js
const http = require('http');
const queryString = require('querystring');
const crypto = require('crypto');
const key = 'zf';

// --------------加盐算法摘要签名--------------------
function signed(value) { 
    return crypto.createHmac('sha256', key).update(value.toString()).digest('base64');
}

const server = http.createServer((req, res) => {
	// 获取cookie函数
    req.getCookie = function(key, options = {}) {
        let cookieObj = queryString.parse(req.headers['cookie'], '; ')
        if (options.signed) {
            let [value, sign] = cookieObj[key].split('.');
            // ------------这里验证签名-------------------------------------------------
            if (signed(value) == sign) { // 校验签名  
                return value;
            } else {
                res.setHeader('Content-Type', 'text/html;charset=utf-8')
                return '被修改了'; // 校验失败 说明cookie失效，可能被用户修改了
            }
        } else {
            if (cookieObj[key]) {
                return cookieObj[key].split('.')[0]
            } else {
                return ''
            }
        }

        return cookieObj[key];
    }
	
    // 设置cookie函数
    let cookies = [];
    res.setCookie = function(key, value, options = {}) {
        let optArgs = [];
        if (options.maxAge) {
            optArgs.push(`max-age=${options.maxAge}`);
        }
        if (options.path) {
            optArgs.push(`path=${options.path}`);
        }
        if (options.httpOnly) {
            optArgs.push(`httpOnly=${options.httpOnly}`);
        }
		
        if (options.signed) {
            value = value + '.' + signed(value);
        }

        // cookie 的分隔符 `; `：name:zf; max-age=10; 
        cookies.push(`${key}=${value}; ${optArgs.join('; ')}`);
        res.setHeader('Set-Cookie', cookies);
    }

    if (req.url === '/read') {
        // res.end(req.headers['cookie'])
        let value = req.getCookie('age', { signed: true });
        res.end(value);
    } else if (req.url === '/write') {
        // res.setHeader('Set-Cookie', ['name=zf; max-age=10', 'age=10; path="/read"; httpOnly=true']);
        // 使用封装函数
        res.setCookie('name', 'zf', { maxAge: 10 });
        res.setCookie('age', 10, { httpOnly: true, signed: true });
        res.end('write ok ')
    } else {
        res.end('not found');
    }
})

server.listen(3000)
```

#### session

session 是基于cookie的，它在服务器上开辟了一个新的内存【可以理解用一个`{}`对象来存储】，之后将`key`设置到`cookie`上，每次请求都会将cookie鞋带上，服务器拿着这个`key`去这个对象找对应的值。

> 缺点就是服务器要定期清除`session`，防止内存泄漏

```js
const http = require('http');
const queryString = require('querystring');
const crypto = require('crypto');
const uuid = require('uuid');
const key = 'zf';

function signed(value) { // 加盐算法
    return crypto.createHmac('sha256', key).update(value.toString()).digest('base64');
}

// session存放地
const session = {};
const CardName = 'connect.sid'; // 分发给浏览器cookie的名字

const server = http.createServer((req, res) => {

    req.getCookie = function(key, options = {}) {
        let cookieObj = queryString.parse(req.headers['cookie'], '; ')
        if (options.signed) {
            let [value, sign] = cookieObj[key].split('.');
            if (signed(value) == sign) { // 校验签名  
                return value;
            } else {
                res.setHeader('Content-Type', 'text/html;charset=utf-8')
                return '被修改了'; // 校验失败 说明cookie失效，可能被用户修改了
            }
        } else {
            if (cookieObj[key]) {
                return cookieObj[key].split('.')[0]
            } else {
                return ''
            }
        }

        return cookieObj[key];
    }

    let cookies = [];
    res.setCookie = function(key, value, options = {}) {
        let optArgs = [];
        if (options.maxAge) {
            optArgs.push(`max-age=${options.maxAge}`);
        }
        if (options.path) {
            optArgs.push(`path=${options.path}`);
        }
        if (options.httpOnly) {
            optArgs.push(`httpOnly=${options.httpOnly}`);
        }

        if (options.signed) {
            value = value + '.' + signed(value);
        }

        // cookie 的分隔符 `; `：name:zf; max-age=10; 
        cookies.push(`${key}=${value}; ${optArgs.join('; ')}`);
        res.setHeader('Set-Cookie', cookies);
    }

    if (req.url === '/cut') {
        let cardId = req.getCookie(CardName);

        if (cardId && session[cardId]) { // 服务器一旦重重启session就被清空了
            session[cardId].mny -= 20;
            res.end(session[cardId].mny + 'money')
        } else { // 第一次来生成一个 uuid
            let cardId = uuid.v4();
            session[cardId] = { mny: 100 };
            res.setCookie(CardName, cardId, { httpOnly: true });
            res.end('100 Money')
        }

    } else {
        res.end();
    }
})

server.listen(3000)
```

#### jwt

JSON Web Token (JWT) 是目前最流行的跨域身份验证解决方案

解决问题：session 不支持分布式架构，无法支持横向扩展，只能通过数据库来保存会话数据实现共享。如果持久层失败会出现认证失败。

优点：服务器不保存任何回话数据，即服务器变为无状态，使其更容易扩展。

> * 可扩展性好，应用程序分布式部署的情况下，session需要做多机数据共享，通常可以存在数据库或redis里面，而jwt不需要
> * 无状态jwt不在服务端存储如何状态。jwt的载荷中可以存储一些常用信息，用于交换信息 ，有效地使用JWT，可以降低服务器查询数据库的次数。

缺点：由于用Base64转码，这会让我们token变大，产生额外的服务器流量。

> * 安全性：由于jwt的payload是使用base64编码的，并没有加密，因此jwt中不能存储敏感数据，而session的信息存在服务端的，相对来说更安全。
> * 性能：jwt太长。由于是无状态使用JWT，所有的数据都被放在JWT里，如果还要进行一些数据交换，那载荷会更大，进过编码之后导致jwt非常长，cookie的限制大小一般在`4K`,cookie很可能放不下，使用jwt一般放在local storage 里面。并且用户在系统中每一次http请求都会把jwt携带到header里面，http请求的header可能比body大。而sessionId只是很短的一个字符串，因此使用jwt的http请求比使用session的开销大得多。
> * 时限：无法废弃，通过jwt的验证机制可以看出来，一旦签发一个jwt，在到期之前就会始终有效，无法中途废弃。例如你在payload中存储了一些信息，当信息需要更新时，则重新签发一个JWT，但是由于旧的JWT还没有过期，拿着这个旧的JWT依旧可以登录，那登录后服务端从JWT中拿到信息就是过时的。为了解决这个逻辑，我们就需要在服务端部署额外的逻辑。例如设置一个黑名单，一旦签发了新的jwt，那么旧的就要加入黑名单（比如存到redis里面），避免被再次使用
> * 续签问题：如果你使用jwt做会话管理，传统的cookie续签方案一般都是框架自带的，session有效期30分钟，30分钟内如果有访问，有效期被刷新至30分钟，一样的道理，要改变jwt的有效时间就要签发新的jwt。最简单的一种方式是每次请求刷新jwt，即每个http请求都返回一个新的jwt。这个方法不仅暴力不优雅，而且每次请求都会做jwt的加密解密，会带来性能问题。另外一种方法是在redis中单独为每个jwt设置过期时间，每次访问时刷新jwt的过期时间。当然我们可以判断这个token是否还有效，没有效就提示用户重新去获取新的token。

* Header 头部

```js
{"alg":"HS256","typ":"JWT"}
// algorithm => HMAC SHA256
// type => JWT 
```

* Payload 负载、载荷

```js
JWT 规定了7个官方字段
iss (ussuer): 签发人
exp (expiration time): 过期时间
sub (subject): 主题
aud (audience): 受众
nbf (Not Before): 生效时间
iat (Issued At): 签发时间
jti (JWT ID): 编号
```

* Signature 签名

对前两部分的签名，防止数据篡改

```js
HMACSHA256(
	base64UrlEncode(header) + '.' + base64UrlEncode(payload),
    secret
)
```

JWT作为一个令牌（token），有些场合可能会放到 URL (比如 api.example.com/?token=xxx)。Base64 有三个字符`+`、`/`和`=`，在URL里面有特殊含义，所以要被替换掉：`=`被省略、`+`替换`-`，`/`替换成`_`，这就是`Base64URL`算法。

* 使用方法：将token放入到HTTP请求的头信息`Authorization`字段里面

```
Authorization: Bearer <token>
```

通过url传输

```
http://www.xxx/com/pwa?token=xxxx
```

如果是post请求也可以放在请求体中

原理实现：

```js
// 会走options请求吗 
// json web token (jwt 发给客户端的一个令牌)
const http = require('http');
const url = require('url');
const path = require('path');
const jwt = require('jwt-simple');
const querystring = require('querystring')
const secret = 'zfpx';


const jwt1 = {
    // 签名
    sign(content, secret) {
        return this.base64UrlEscape(require('crypto').createHmac('sha256', secret).update(content).digest('base64'))
    },
    // base64URL编码
    base64UrlEscape(content) {
        return content.replace(/\+/g, '-').replace(/\=/g, '').replace(/\//g, '_');
    },
    // base64URL反解
    base64UrlUnEscape(str) { // 字符串转成base64 有自己的规则 通过下面这些规则转码回去
        str += new Array(5 - str.length % 4).join('=');
        return str.replace(/\-/g, '+').replace(/_/g, '/');

    },
    // base64编码
    toBase64(content) {
        return this.base64UrlEscape(Buffer.from(JSON.stringify(content)).toString('base64'))
    },
    // 解码
    encode(info, secret) {
        let header = this.toBase64({ typ: 'JWT', alg: 'HS256' });
        let content = this.toBase64(info); // 用户的私密信息不要放在token里 这里放一些用户的唯一标识就可以了
        let sign = this.sign(header + '.' + content, secret)
        return header + '.' + content + '.' + sign
    },
    // 编码
    decode(token, secret) {
        let [header, content, sign] = token.split('.')
        let newSign = this.sign(header + '.' + content, secret);

        if (sign == newSign) {
            // console.log(Buffer.from(this.base64UrlUnEscape(content), 'base64').toString())
            return JSON.parse(Buffer.from(this.base64UrlUnEscape(content), 'base64').toString());
        } else {
            throw new Error('token error');
        }
    }
}

const server = http.createServer((req, res) => {
    if (req.url === '/login') {
        let arr = [];
        req.on('data', function(chunk) {
            arr.push(chunk);
        })
        req.on('end', function() {
            let result;
            let content = Buffer.concat(arr).toString();
            if (req.headers['content-type'] === 'application/x-www-form-urlencoded') {
                result = querystring.parse(content);
            } else if (req.headers['content-type'] === 'application/json') {
                result = JSON.parse(content);
            }

            if ('admin' === result.username && 'admin' === result.password) {
                res.end(JSON.stringify({
                    message: '登录成功',
                    token: jwt1.encode({ exp: new Date(Date.now() + 30 * 1000), username: 'admin' }, secret)
                }))
            }

        })
    } else if (req.url == '/validate') {
        let auth = req.headers['authorization'];
        if (auth) {
            let [, token] = auth.split(' ');
            try {
                let payload = jwt1.decode(token, secret);
                let exp = new Date(payload.exp).getTime()
                if (exp < new Date().getTime()) {
                    res.end('token exp');
                    // 返回一个新的token 或者让用户重新登录
                } else {
                    res.end('login ok ')
                }
            } catch (e) {
                console.log(e)
                res.end('token error')
            }
        }
    }
})


server.listen(3000);
```





