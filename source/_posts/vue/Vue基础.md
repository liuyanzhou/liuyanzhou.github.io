---
title: Vue基础
date: 2020-08-19 20:48:24
categories: Vue
top: false
summary:  Vue基础
tags: 
 - Vue
---

### Vue基础

vue.js 是一套用于构建用户界面的渐进式框架，所谓的渐进式是指它入门很快，，核心功能上手很容易，这也是vue受欢迎的重要一点，但项目深入开发会发现要接触和掌握的东西越来越多。

![渐进式](/medias/imges/vue/basic/progressive.jpeg)

我们可以通过script引入体验：

```html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

### 一、简单vue实例的参数说明

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    </head>
    <body>
        <div id="app"></div>
        <script>
            new Vue({
                el: '#app',
                template: `
                    <h1>{{title}}</h1>
                    `,
                data: {
                    title:'开课吧'
                }
            });
        </script>

    </body>
</html>
```

#### 1.1 template(模板)

type： String

组件的模板结构 (HTML)，模板内容会被 `vue`进行渲染，生成最终的 `HTML`内容，并替换占位(挂载)元素，**注意：每一个独立的模板有且只能有一个顶级的根节点 ，否则会报错**

```js
// 报错	
new Vue({
    el: '#app',
    template: `
		<h1>这是h1</h1>
		<h1>这是h2</h1>
	`
})
// 正确
new Vue({
    el: '#app',
    template: `
	<div>
		<h1>这是h1</h1>
		<h1>这是h2</h1>
	</div>
	`
})
```

#### 1.2 render

我们的神奇的模板语法是如何实现的？：在底层的实现上，Vue将模板编译成虚拟DOM渲染函数，结合响应系统，Vue能够智能地计算出最少需要重新渲染多个组件，并把DOM操作次数减到最少，我们可以通过`app.$options.render`打印出template 节点下被vue处理后的结果（虚拟DOM）

```js
// 输出vue替我们生成的渲染函数一看究极
console.log(app.$options.render)
```

它类似这个样子

```js
// 它长这个样子 
(function anonymous( ) { with(this){return _c('div',{attrs:{"id":"app"}},[_c('h2',{attrs: {"title":title}},[_v("\n "+_s(title)+"\n ")]),_v(" "),_c('input',{directives:[{name:"model",rawName:"v-model",value: (course),expression:"course"}],attrs:{"type":"text"},domProps:{"value": (course)},on:{"keydown":function($event) {if(!$event.type.indexOf('key')&&_k($event.keyCode,"enter",13,$event.key,"Enter" ))return null;return addCourse($event)},"input":function($event) {if($event.target.composing)return;course=$event.target.value}}}),_v(" "),_c('button',{on:{"click":addCourse}},[_v("新增课程")]),_v(" "),(courses.length == 0)?_c('p',[_v("没有任何课程信息")]):_e(),_v(" "),_c('ul',_l((courses),function(c){return _c('li',{class:{active: (selectedCourse === c)},on:{"click":function($event){selectedCourse = c}}}, [_v(_s(c))])}),0)])} })
```

既然`template`模板最终还是要`render`函数进行处理转化为虚拟DOM节点，那么我们就可以直接使用render 函数来替代template

render函数的处理结构（type ： (createElement:()=>VNode) => VNode；） 而createElement 是将我们写在`template`下的类似HTML结构转为`Vnode`（虚拟DOM）的方法

它发挥了`JavaScript`强大的编程能力，它跳过了template编译阶段直接创建`VNode`（虚拟dom对象），优先级高于`el`和`template`，并且性能会更高

> https://cn.vuejs.org/v2/guide/render-function.html

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="js/vue.js"></script>
        <style>
            .box {
                color: red;
            }
        </style>
    </head>

    <body>
        <div id="app"></div>
    </body>
    <script>
        new Vue({
            el: '#app',
            render(createElement) {
                //  createElement 产生Vnode
                return createElement('div', {
                    attrs: {
                        id: 'app'
                    }
                }, [
                    createElement('h1', 'lyz'),
                    createElement('h2', {
                        attrs: {
                            class: 'box'
                        }
                    }, 'hahah ')
                ])
            }
        })
    </script>
</html>
```

#### 1.3 el(挂载点)

type：String| Element

提供一个在页面上已存在的 DOM 元素作为Vue实例的挂载目标。可以是 CSS 选择器，也可以是一个 HTMLElement 实例

- 该选择只对 new 创建的实例有效
- 如果提供了` el` ，但没有提供`template`，则，el 的内容将作为 template
- 当这个组件挂载了，`el`就是该组件DOM对象

#### 1.4 data(数据)

组件内部使用的数据，`data`是一个对象，模板中可以直接访问里面的值

* `Vue`实例组件(根组件)的 data 是一个对象

* 可复用功能的组件的`data`必须是一个函数，且该函数必须返回一个对象(因为复用性，避免多个组件实例引用一个对象。换句话说，组件中使用的数据必须是一个对象，但是可复用组件的这个数据对象必须通过函数返回)

* data中的数据会直接被挂在到实例对象下，可以直接通过组件实例对象访问`app.data下的属性名`，也可以通过实例对象下的`app.$data.data下的属性名`进行访问

  > 组件实例对象下有很多的以`$/_`开头的属性，这些都是实例对象内置的一些属性和方法，所以我们定义data中的数据时，应该避免使用` $/_`开头

data的数据访问

vue使用了基于 html 的模板语法，使用声明式的方式把实例中的数据(`data`)与 DOM 进行绑定，data中的数据在模板中可以直接使用，通过`Mustache`（双大括号，大胡子）语法输出，但`{ {}}`只能用于内容输出，不能在属性或指令等上使用，即

```js
// {{表达式}}
new Vue({
    el: '#app',
    data: {
        title: 'vue 框架'
    },
    template: `<div>{{title}}</div>`
});
```

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
            {{title}}
        </div>

        <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

        <script>
            let app = new Vue({
                el: '#app',
                data: {
                    title: 'vue 框架'
                }
            });
        </script>
    </body>
</html>
```

数据到视图的更新

vue会把 data 中的数据挂载到实例属性下，同时对它们进行主动的监听拦截，当数据发送变化的时候，重新渲染模板，我们可以通过实例对象对数据进行修改

```js
app.title = 'lyz'
// or
app.$data.title = 'hello'
```

vue检测数据变化的注意事项：

在vue3 之前，数据的监听是通过`Object.defineProperty`方法来实现的，但该方法只能监听拦截单个数据，对于对象新增属性并未进行注册监听故无法进行拦截。但我们可以使用vue提供的方法为新增的属性进行注册监听，即通过`app.$set()`添加新数据

扩展：

通过`Object.defineProperty`无法对下面为对象新增的属性进行监听拦截

* 属性对象性质属性
* 通过数组方法新增，push、pop、shift、unshift、splice、sort、reverse
* 通过`[]`对数组新增的值
* 数组 length 属性

但vue对数组中的`push`、`pop`方法等方法进行重新包装，所以在vue中调用这些方法，也可以对数组修改进行监听拦截

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
            <p>title: {{title}}</p>
            <p>user.username: {{user.username}}</p>
            <p>user.gender: {{user.gender}}</p>
        </div>

        <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

        <script>
            let app = new Vue({
                el: '#app',
                data: {
                    title: 'vue 框架',
                    user: {
                        username: 'zMouse'
                    }
                }
            });
        </script>
    </body>
</html>
```

在模板中，我们使用了一个不存在的数据`user.gender`,如果我们通过`app.user.gender='男'`的方式来新增，是不会被 vue 拦截监听处理的，我们需要使用

```js
Vue.set(app.user,'gender','男')
// 实例对象.$set 是Vue.set 的别名
app.$set(app.user,'gender','男')
```

这样的方式，其实就是`set`方法给`app.user`添加`gender`属性的同时，对它进行了`defineProperty`数据监听。

#### 1.5 延迟挂载

当Vue实例没有 el选项的时候，它会处于一种未挂载的状态，我们可以使用`$mount()`方法手动进行挂载也可以进行延迟挂载到页面上去，但应用场景很少。要注意：el:挂载点不能是body和html，并且 当实例被挂载以后，实例对象上就会有一个 $el 的属性，这个属性就是挂载点元素

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="js/vue.js"></script>
</head>

<body>
    <div id="app">

    </div>
</body>
<script>
    let app = new Vue({
        template: `
            <div id="app">
                <div>lyz</div>     
            </div>
        `
    })
    setTimeout(() => {
            app.$mount('#app')
        }, 1000)
</script>

</html>
```

#### 1.6 对比react 和 vue的视图更新的方式(等我总结好react再来看是否正确，这里还未确认)

这里的对比当然不是react的真正的视图更新方式，但原理是一样的，我们这里是用例子进行模拟。

react：它将render方法放到setData(数据更新方法)中去，每一次数据更新就会渲染视图。

```html
    <!DOCTYPE html>
    <html lang="en">

        <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <meta http-equiv="X-UA-Compatible" content="ie=edge">
            <title>Document</title>
            <script src="js/vue.js"></script>
        </head>

        <body>
            <div id="app">

            </div>
        </body>
        <script>
            let app = document.querySelector('#app')

            function render() {
                console.log('渲染开始')
                app.innerHTML = obj.x + '/' + obj.y
            }
            let obj = {
                x: 1
            }
            render()
            // react 
             function setData(state) {
                 obj = Object.assign(obj, state)
                 render()
             }
             setTimeout(() => {
                 setData({
                     y: 2
                 })
             }, 2000)
        </script>

    </html>
```

vue：通过对象数据监听的方式进行视图更新

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="js/vue.js"></script>
</head>

<body>
    <div id="app">

    </div>
</body>
<script>
    let app = document.querySelector('#app')

    function render() {
        console.log('渲染开始')
        app.innerHTML = obj.x + '/' + obj.y
    }
    let obj = {
        x: 1
    }
    render()
    // VUE
    let $data = {
        x: 1
    }
    Object.defineProperty(obj, 'x', {
        set(newValue) {
            console.log('123')
                // $data.x = newValue
            $data.x = newValue
            render()
        },
        get() {
            return $data.x
        }
    })

    Object.defineProperty(obj, 'y', {
        set(newValue) {
            console.log('123')
                // $data.x = newValue
            $data.y = newValue
            render()
        },
        get() {
            return $data.y
        }
    })
    obj.y = 33
</script>

</html>
```

#### 1.7 $options属性

this.$options属性上挂载着组件上所有的属性和方法，包括data ，methods，以及自定义属性等.....。也就是export default 括号里面的所有东西都包括

```js
export default {
    img:'123',
    name:'Home',
    data() {
        return {
            items:[],
            sort:'asc'
        }
    }, 
    filters:{
        RMB
    },
    components:{
        Son
    },
    created() {
    this.getItem()
    },
    mounted() {
        console.log(this.$options)
        console.log(this.$options.img)
    },
    methods:{
        getItem() {
            this.sort = this.$route.query.sort || this.sort
             this.axios({
                url: '/api/items',
                params:{
                    sort:this.sort
                }
            }).then(res => {
               this.items = res.data;
            });
        },
        goto({target: {value}}) {
            this.$router.push({name:'Home',query:{sort:value}})
        }
    },
    watch:{
        $route(to,from) {
            console.log(to)
            this.getItem()
        }
    }
}
```

#### 1.8 Vue.$extend(option)

option 属于 `Object`，使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。可以通过它结合工厂函数，我们就可以利用`js`像操作`DOM`元素一样操作组件，例如：我们封装一个提示消息的组件

组件文件：`TMessage.vue`

```html
<template>
    <transition name="message-fade">
        <div :class="[
        'message',
        'message-' + type,
        center ? 'is-center' : ''
    ]"
             :style="{top: offset + 'px'}"
             v-if="!closed"
        >
            <p class="message-content">提示信息：{{message}}</p>
            <i class="icon icon-close"></i>
        </div>
    </transition>
</template>

<script>
    export default {
        name: 'TMessage',

        data() {
            return {
                message: '这是默认信息',
                type: 'info',
                center: true,
                offset: 20,
                closed: false,
                duration: 1000,
                timer: null,
                onClose: null
            }
        },

        mounted() {
            this.timer = setTimeout(() => {
                if (!this.closed) {
                    this.close();
                }
            }, this.duration);
        },

        methods: {
            close() {
                this.closed = true;
                if (typeof this.onClose === 'function') {
                    this.onClose();
                }
            }
        }
    }
</script>
```

使用工厂函数：`TMessage.js`

```js
import Vue from 'vue';
// 上一步的组件文件导入
import TMessage from "./TMessage.vue";

const TMessageClass = Vue.extend(TMessage);

/**
 * 工厂函数
 *  创建一个TMessage组件对象
 *  管理TMessage组件对象队列
 */
let instances = [];
function Message(data) {

    data = data || {};
    if (typeof data === 'string') {
        data = {
            message: data
        }
    };

    data.onClose = function() {
        // console.log('onClose');
        Message.close(instance);
    };

    let instance = new TMessageClass({
        data
    });

    instance.$mount();  // $el
    document.body.appendChild(instance.$el);

    let offset = data.offset || 20;
    let offsetTop = offset;
    instances.forEach( item => {
        offsetTop += item.$el.offsetHeight + offset;
    } );

    instance.$el.style.top = offsetTop + 'px';

    instances.push(instance);
}

['info', 'success', 'error', 'warning'].forEach( type => {
    Message[type] = function(data) {
        if (typeof data === 'string') {
            data = {
                message: data
            }
        };
        data.type = type;
        return Message(data);
    };
} );

Message.close = function(instance) {
    /*
    * 获取当前这个instance的高度
    * 把这个instance后面的所有实例的top减去这个高度，再减去偏移
    * */
    let removeHeight = instance.$el.offsetHeight + instance.offset;
    let index = instances.findIndex( item => item === instance );
    instances = instances.filter( item => item !== instance );

    for (let i = index; i<instances.length; i++) {
        instances[i].$el.style.top = parseFloat(instances[i].$el.style.top) - removeHeight + 'px';
    }
};


export default Message;
```

使用：通常将其挂载到我们`VUE原型上`，供全局使用

```js
// 在项目的main.js中挂载全局
// ....代码省略
import TMessage from '@/components/TMessage/TMessage.js';
Vue.prototype.$message = TMessage;

// 在视图组件的调用方式 例如login组件
this.$message.error('用户名和密码不能为空')
```

#### 1.9 mixins 混入

当我们的程序代码出现很多相同冗余的代码，我们可以将其他提取出来，在利用Vue提供的`mixins`功能进行混入。例如我们需要在多个视图界面下的`components`注册相同组件，这时我们可以提取其中的组件注册部分代码到一个特定的文件下。

```js
// 冗余代码提取到mixins下的文件
// 引入组件
import CommonCard from '@/components/TopViews/CommonCarrd'

export default {
    components: {
        CommonCard
    }
}
```

混入组件代码： 按下面混入即可

```js
import CommonCard from '@/mixs/CommonCard'

export default { 
    // ... 
	mixins:[CommonCard]
    // ...
}
```

本大章小结：

* vue 帮助我们做的事情：把data与template进行结合，然后渲染得到最终结构，最后把这个结构(内容)添加到挂载点的位置
* vue渲染界面的过程：template => VDOM(虚拟dom) => html 

#### 1.10 VUE实例的声明周期

每个Vue实例在被创建到被销毁 都要经过一系列的初始化过程---例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等，称为Vue实例的[生命周期](https://cn.vuejs.org/v2/guide/instance.html#%E5%AE%9E%E4%BE%8B%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E9%92%A9%E5%AD%90)

生命周期钩子：在Vue实例的生命周期过程中会运行一些叫生命周期钩子的函数，这给用户在不同阶段添加自己代码的机会

范例：异步获取列表数据

```js
// 模拟异步数据调用接口
function getCourses() { 
    return new Promise(
        resolve => {
            setTimeout(() => { 
                resolve(['web全栈', 'web高级']) 
            }, 2000);
        }) 
}
const app = new Vue({ // created钩子中调用接口 
    async created() { 
        const courses = await getCourses()
        this.courses = courses 
    }, 
}
```

在由一道面试题开始浅讨生命周期

```
关于Vue的生命周期，下列哪项是不正确的？(B)[单选题] 
A、Vue 实例从创建到销毁的过程，就是生命周期。
B、页面首次加载会触发beforeCreate, created, beforeMount, mounted, beforeUpdate, updated。
C、created表示完成数据观测，属性和方法的运算，初始化事件，$el属性还没有显示出来。
D、DOM渲染在mounted中就已经完成了。

// 因为 beforeUpdate update 生命钩子只会在数据变化时才会被触发
```

```html
<!DOCTYPE html>
<html>
<head>
    <title>Vue源码剖析</title>
    <script src="vue.js"></script>
</head>

<body>
    <div id="demo">
        <h1>声明周期</h1>
        <p>{{foo}}</p>
    </div>
    <script>
        // 创建实例
        const app = new Vue({
            el: '#demo',
            data:{
                foo: 'foo'
            },
            beforeCreate(){console.log('beforeCreate')}, // beforeCreate 
            created(){console.log('created '+this.$el)}, // created undefined
            beforeMount(){console.log('beforeMount')}, // beforeCreate [Object HTMLDivElement]
            mounted(){
                setTimeout(() => {
                    this.foo = 'foooooo'
                }, 2000);
                console.log('mounted '+this.$el)},
            beforeUpdate(){console.log('beforeUpdate')}, // beforeUpdate 
            updated(){console.log('updated')}, // update 
        });
    </script>
</body>
</html>
```

结论：

* 生命周期三个阶段：初始化、更新、销毁
* 初始化：beforeCreate 、created 、beforeMount、mounted
* 更新：beforeUpdate、updated
* 销毁：beforeDestroy、destroyed

使用场景：

```js
{
    beforeCreate(){} // 执行时组件实例还未创建，通过用户插件开发中执行一些初始化任务
    created(){} // 组件初始化完毕，各种数据可以使用，常用于异步数据获取
    beforeMounted(){} // 未执行渲染、更新、dom未创建
    mounted() {} // 初始化结束、dom已创建，可用于获取访问数据和dom元素
    beforeUpdate(){} // 更新前，可用于获取更新前各种状态
    updated(){} // 更新后，所有状态已是最新
    beforeDestroy(){} // 销毁前，可用于一些定时器或订阅的取消
    destroyed(){} // 组件已销毁，可用于一些定时器或订阅的取消
}
```



### 二、Vue指令

表达式的值除了可以出现内容中，也可以使用在其他位置，比如：属性，当不能使用`{ {}}`语法，而是需要使用`指令`。

在vue中，指令是一个带有`v-`前缀的属性，与普通属性不一样的地方在于，指令的值是引号括起来的`表达式`,不同的指令有不同的作用，`vue`内置了一些常用的指令，我们还可以自定义属于自己的指令，它们按功能可以分为:

* 内容输出
* 循环
* 逻辑判断
* 属性绑定
* 事件
* 其它

#### 2.1 内容输出

通过`{ {}}`我们可以很方便的往模板中输出数据，但是这种方法会有一个问题，当页面加载渲染比较慢的时候，页面中就会出现`{ {}}`，`vue`提供了几个指令来解决这个问题

> 注意：指令中的表达式不需要使用`{ {}}`

* v-text

```html
<p v-text="title"></p>
```

> 弊端：`v-text`会填满整个`innerHTML`

* v-cloak

```html
<p v-cloak>{{title}}</p>
```

需要配置css 进行处理

```html
<style>
    [v-cloak] {
        display: none;
    }
</style>
```

* v-html

为了防止`XSS`攻击，默认情况下输出是不会作为`html`解析的，但我们通过`v-html`来让内容作为`html`进行解析

* v-once 

只渲染元素和组件一次，后期的更新不再渲染

```html
<p v-once>{{once}}</p>
```

* v-pre

忽略整个元素和它子元素内容的编译和解析，简单说就是原文输出

全部实例：

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="js/vue.js"></script>
        <style>
            [v-cloak] {
                display: none;
            }
        </style>
    </head>
    <body>
        <div id="app">
            <h1>hello {{title}}</h1>
            <h1 v-text=" 'hello'+ title"></h1>
            <h1 v-cloak>Hello12122, {{title}}</h1>
            <!-- <p>{{content}}</p>
			<p v-html="content"></p> -->
            <p v-pre>vue的解析语法{{title}}</p>
            <p v-once>{{once}}</p>
        </div>
    </body>
    <script>
        // 指令 - 后面的内容时表达式，而不是普通的字符串
        let app = new Vue({
            data: {
                myId: 'kkb',
                title: 'hello',
                content: '<style> body {display:none} </style>',
                once: '这是once'
            }
        })
        setTimeout(() => {
            app.$mount('#app')
        }, 1000)
    </script>
</html>
```

#### 2.2 逻辑判断

* v-show

根据表达式的值（布尔值），切换元素的显示和隐藏（display属性)

> 适合于状态切换比较频繁的情况

* v-if

更加表达式的值（布尔值），创建或销毁元素

> 适用于状态切换不频繁的情况

这里需要注意： v-if后面还要接着逻辑判断一定要**紧跟 `v-else` or `v-else-if`**

实例:

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="js/vue.js"></script>
    </head>

    <body>
        <div id="app">
            <div v-show="isLogin">这是隐式</div>
            <!-- v-if 和 v-else 以及 v-else-if 必须是连续的 -->
            <div v-if="isLogin">欢迎</div>
            <div v-else-if="!isLogin">不在话语</div>
            <div v-else>请登录</div>
        </div>
    </body>
    <script>
        let app = new Vue({
            el: '#app',
            data: {
                isLogin: true
            }
        })
    </script>

</html>
```

#### 2.3 循环与列表

* v-for 

根据数据循环渲染`v-for`指令所在的元素及其子元素，可以循环的数据结构有 : Array | Object | Number | String | lterable(2.6新增)

```html
<div v-for="(item, index) in items"></div>
<div v-for="(val, key) in object"></div>
<div v-for="(val, key, index) in object"></div>
```

> v-for 中也可以使用 of 语法，在vue中两者没有什么区别

:key

默认情况下，在渲染`DOM`过程中 使用 原地复用，这样一般情况下比较高效，但是对于循环列表，特别是依赖某种状态的列表，会有一些问题，它并不会将我们标签及子标签进行相互关联，那么我们需要将整个标签为一组看待时，我们可以通过`:key`属性，来给每个循环点添加一个唯一标识解决这个问题。**当然推荐每次使用 v-for 指令都添加 `:key`值。**

实例:

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="js/vue.js"></script>
    </head>

    <body>
        <div id="app">
            <ul>
                <li v-for="user of users" :key="user.name">
                    <input type="checkbox" name="" id=""> #{{user.name}} = {{user.age}}
                </li>
            </ul>

        </div>
    </body>
    <script>
        let app = new Vue({
            el: '#app',
            data: {
                users: [{
                    name: 'zs',
                    age: '10'
                }, {
                    name: 'ls',
                    age: '30'
                }, {
                    name: 'ww',
                    age: '20'
                },
                       ]
            }
        })
        setInterval(() => {
            app.users.sort((a, b) => {
                return Math.random() - .5;
            });
        }, 1000)
    </script>

</html>
```

#### 2.4 属性绑定

* v-bind 

绑定数据（表达式）到指定的属性上，`<div v-bind:参数="值/表达式" ></div>`，这里的参数就是指定的属性名称

```html
<div id="app">
    <div v-bind:id="'box1'"></div>
    <div v-bind:id="myId"></div>
	<!-- 要加其他标识要使用js的字符串拼接 -->
    <div v-bind:id="myId + '-1'"></div>
</div>

<script>
    new Vue({
        el: '#app',
        data: {
            myId: 'kaikeba'
        }
    })
</script>
```

缩写：有的一些常用指令会有对应的缩写，`v-bind`对应的缩写为：`:`

```html
<div :id="myId"></div>
```

对样式进行绑定有一些特殊的写法

**对style进行绑定**

原生写法：

```html
<div style="width:100px;height:100px;background:red"></div>
```

v-bind 写法

```html
<div :style="'width:100px;height:100px;background:red;'"></div>
```

对象写法

```html
<div :style="style1"></div>
....
<script>
    new Vue({
        el:'#app',
        data:{
            style1:{
                width:'100px',
                height:'100px',
                background:'green'
            }
        }
    })
</script>
```

数组写法

```html
<div :style="[style1,style2]"></div>

<script>
    new Vue({
        el:'#app',
        data:{
            style1:{
                width:'100px',
                height:'100px',
                background:'green'
            },
            style2:{
                border:'1px solid black'
            }
        }
    })
</script>
```

**对class进行绑定**

原生写法：

```html
<div class="box1 box2"></div>
```

v-bind 写法

```html
<div :class="'box1 box2'"></div>
```

数组写法

```html
<div :class="['box1 box2']"></div>
```

对象写法：使用对象写法可以根据值（boolean）动态添加对应的class ，true为添加，false不添加

```html
<div :class="{'box1': isActive, 'box2': isChecked}"></div>
```

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>

        <div id="app">
            <div :style="'width:' + style1.width"></div>
            <div :style="style1"></div>
            <div :style="{width, height:'100px'}"></div>
            <div :class="['box1', 'box2']"></div>
            <div :class="{'box1': isActive, 'box2': isChecked}"></div>
        </div>
        <script src="./js/vue.js"></script>
        <script>

            /**
         * class：对象写法
         *      key 就是要添加的class，对应的value，如果真表示添加，否则不添加
         */
            let app = new Vue({
                el: '#app',
                data: {
                    style1: {
                        width: '100px',
                        height: '100px',
                        background: 'green'
                    },
                    width: '100px',
                    isActive: true,
                    isChecked: false
                }
            });

        </script>

    </body>
</html>
```

#### 2.5 数据流

通过上面的总结预计归纳，我们可以知道当数据更新的时候，页面视图就会更新，但是页面视图中绑定的元素更新的时候，对应的数据是不会更新的，例如input值改变，title的值是不变的，我们把这种称谓**单向数据流** (数据---->视图)

```html
<input type="text" :value="title" /> {{title}}

<script>
    let app = new Vue({
        el:'#app',
        data:{
            title:'hello world'
        }
    })
</script>
```

* v-model

我们使用指令 `v-model`之后，数据`title`更新，视图的input 的value 就会更新，同时，当 input 中的 value 更新的时候，数据的 title 也会更新，这就是我们说的 **数据双向绑定** (与React中的受控组件类型)

```html
<input type="text" v-model="title" /> {{title}}

<script>
    let app = new Vue({
        el:'#app',
        data:{
            title:'hello world'
        }
    })
</script>
```

v-model 的使用场景

针对于一般元素，比如：div 、span、 p 、img 等，采用的是单向绑定:`v-bind:`，只需要把数据绑定到视图中就可以，但是对于表单这种交互性比较强的元素或组件，我们一般都会涉及到双向绑定，也就是说用户对视图元素的操作需要更新视图的时候我们就需要使用`v-model`，

要注意的是：v-model 在内部为不同的输入元素使用不同的属性和事件来处理数据，我们常见的使用双向绑定的元素有：

* `text`和`textarea`
* `checkbox`和`radio`
* `select`

**text和textarea**

v-model 使用它们的 `value`属性和`input`事件

```html
<div id="app">
    <input type="text" v-model="v1" />
    <textarea v-model="v2" cols="30" rows="10"></textarea>
</div>

<script>
    let app = new Vue({
        el: '#app',
        data: {
            v1: 'aaa',
            v2: 'bbb'
        }
    });
</script>
```

**checkbox 和 radio**

v-model 使用 它们的 `checked`属性和`change`事件

* 单选框绑定一个值

```html
<div id="app">
    <input type="radio" v-model="v3" value="男" /> 男
    <input type="radio" v-model="v3" value="女" /> 女
</div>
<script>
    let app = new Vue({
        el: '#app',
        data: {
            v3: '女',
        }
    });
</script>
```

* 多选框编订一个布尔值或数组

```html
<div id="app">
    <input type="checkbox" v-model="v4" /> 同意
    <hr/>
    <input type="checkbox" v-model="v5" value="足球" /> 足球
    <input type="checkbox" v-model="v5" value="音乐" /> 音乐
</div>

<script>
    let app = new Vue({
        el: '#app',
        data: {
            v4: true,
            v5: ['足球', '音乐']
        }
    });
</script>
```

**select**

v-model 使用 它的`value`属性和`change`事件

**单选绑定到值，多选绑定到数组**

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="js/vue.js"></script>
    </head>

    <body>
        <div id="app">
            <select v-model="balll1" multiple>
                <option value="足球">足球</option>
                <option value="篮球">篮球</option>
                <option value="羽毛球">羽毛球</option>
            </select>
            <select v-model="ball2" multiple>
                <option value="足球">足球</option>
                <option value="篮球">篮球</option>
                <option value="羽毛球">羽毛球</option>
            </select>
        </div>
    </body>
    <script>
        let app = new Vue({
            el: '#app',
            data: {
                v5: ['足球', '音乐'],
                ball1: '足球',
                ball2: ['足球', '羽毛球']
            }
        })
    </script>
</html>
```

#### 2.6 指令修饰符

一个指令可以包含的内容包括

* 指令名称
* 指令值
* 指令参数（只能一个）
* 指令修饰符（可以多个修饰）

```html
<组件 指令:参数.修饰符1.修饰符2="值" />
```

不同的指令有自己的指令修饰符，去官方查看，这里列举几个:

| 修饰符  | 描述                            |
| ------- | ------------------------------- |
| .lazy   | 取代 `input` 监听 `change` 事件 |
| .number | 输入字符串转为有效的数字        |
| .trim   | 输入首尾空格过滤                |

#### 2.7 自定义指令

我们除了使用官方提供的内置指令之外，我们也可以自己自定义指令来实现某些需求

* 注册指令：vue通过两种指令注册方式：1.全局指令 2.局部指令

全局指令：用实例创建

```js
Vue.directive('指令名称', {指令配置});
```

局部指令：创建指令注册到某个组件中去

```js
new Vue({
    el: '#app',
    directives: {
        '指令名称': {指令配置}
    }
});
```

> 在使用指令的时候，需要使用`v-指令名称`的方式来调用

* 指令的生命周期（钩子函数）

指令的运行方式很简单，它提供了一些指令生命周期钩子函数，我们只需要在不同的生命周期钩子函数中进行逻辑处理就可以了

| 钩子函数名       | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| bind             | 只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置 |
| inserted         | 被绑定元素插入父节点时调用(仅保证父节点存在，但不一定已被插入文档中) |
| update           | 所在组件更新的时候调用                                       |
| componentUpdated | 所在组件更新完成后调用                                       |
| unbind           | 只调用一次，指令与元素解绑时的调用                           |

不同的生命周期钩子函数在调用的时候同时会接收到传入的一些不同参数

* el : 指令所绑定的元素，可以用来直接操作`DOM`
* binding；一个对象，包括以下属性:
  * name : 指令名，不包括`v-`前缀
  * value：指令的绑定值（作为表达式解析后的结果）
  * expression：指令绑定的表达式（字符串）
  * arg：传给指令的参数，可传可不传，传即时参数名，不传则不存在
  * modifiers：传给指令的修饰符组成的对象，每个修饰符对应一个布尔值，并且都为true，这是因为修饰符存在都为true，不存在则不在对象中
  * oldValue：指令绑定的前一个值，仅在`updata`和`componentUpdated`钩子中可用，无论值是否改变都可用

案例1: 获取焦点

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
            <input type="text" v-focus>
        </div>

        <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

        <script>

            Vue.directive('focus', {
                inserted(el) {
                    el.focus();
                }
            });

            let app = new Vue({
                el: '#app'
            });
        </script>
    </body>
</html>
```

案例2：元素拖拽

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="js/vue.js"></script>
        <style>
            .box {
                position: absolute;
                width: 100px;
                height: 100px;
                top: 100px;
                left: 200px;
                background: red;
            }

            .box2 {
                position: absolute;
                width: 100px;
                height: 100px;
                top: 300px;
                left: 200px;
                background: green;
            }
        </style>
    </head>

    <body>
        <div id="app">
            <div class="box" v-drag="flag"></div>
            <div class="box2" v-drag:fn.limit="flag"></div>
            <button @click="fn">{{flag}}</button>
        </div>
    </body>
    <script>
        Vue.directive('drag', {
            bind(el, binding) {
                console.log(binding)
                el.flag = binding.value // false
                el.addEventListener('mousedown', (e) => {
                    el.x = e.clientX - el.offsetLeft
                    el.y = e.clientY - el.offsetTop
                    el.isUp = false
                })
                el.addEventListener('mousemove', (e) => {
                    if (!el.flag) return
                    if (!el.isUp) {
                        let l = e.clientX - el.x
                        let t = e.clientY - el.y

                        if (binding.modifiers.limit) {
                            l = l <= 0 ? 0 : l
                            t = t <= 0 ? 0 : t
                        }

                        el.style.left = l + 'px'
                        el.style.top = t + 'px'
                    }

                })
                el.addEventListener('mouseup', (e) => {

                    el.isUp = true
                })
            },
            componentUpdated(el, binding) {
                console.log(binding)
                console.log('componentUpdated生命钩子被触发')
                el.flag = binding.value
            }
        })

        let app = new Vue({
            el: '#app',
            data: {
                flag: false
            },
            methods: {
                fn() {
                    this.flag = !this.flag
                }
            }
        })
    </script>
</html>
```

### 三、事件

* 事件的绑定

在 vue 中，事件通过指令`v-on`进行绑定，`v-on`可缩写为`@`

```html
<组件 v-on:事件名称="表达式" />
<组件 @事件名称="表达式" />
```

* 组件的`methods`选项

在组件选项中，提供了一个`methods`选项，用来存放组件中使用的函数方法，且存放在`methods`中的函数方法之间可以通过组件实例（this）进行互相访问，而data数据来源却不可以使用 this获取到

* 通过内联方式绑定事件处理函数

```html
<组件 @事件名称="fn" />

<script>
    new Vue({
        ...,
        methods: {
        fn() {
        //...	
    }
            }
            })
</script>
```

注意：

1. 事件绑定函数中的`this`指向组件实例
2. 事件绑定函数的第一参数默认为`event`对象，而如果要传方法传参时，并且我们还需要事件对象，那么就得手动传入，并且事件对象的名字是固定死的`$event`，例如：

```html
// 事件绑定中直接调用函数（并不会立即执行，也是通过事件触发执行的）
<组件 @事件名称="fn('kaikeba', $event)" />

<script>
    new Vue({
        ...,
        methods: {
        fn(name, ev) {
        // ev 即为传入的事件对象
        //...	
    }
            }
            })
</script>
```

* 事件修饰符

在事件函数中，我们可以通过`ev.preventDefault()`、`ev.stopPropagation()`来阻止默认行为，阻止冒泡，但是vue中提供一些更加方便的方式来处理这些问题，这就是事件修饰符。这里也请去官网查看，因为太多了。

### 四、过滤器

过滤器是一个使用在`双大括号插值`和`v-bind`中，用于过滤输出内容的函数,说白了：过滤器就是对内容(数据)进行过滤(二次操作)的一种方式 

假设有一个用于把内容内容转为大写的过滤器`toUpperCase`

```js
{{content|toUpperCase}}
```

* `|` : 管道符，表示数据从左至右通过管道符进行传递
* 过滤器可以有多个，执行顺序从左至右，过滤器函数第一个参数的值就是其管道符前一个结果

注册过滤器：过滤器也分全局过滤器和局部过滤器

* 全局过滤器

```js
Vue.filter('过滤器名称',过滤器函数)
```

* 局部过滤器

```js
Vue.component('组件',{
    ...,
    filters:{
    '过滤器名称':过滤器函数
	}	
})
```

实例：

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="js/vue.js"></script>
    </head>

    <body>
        <div id="app">
            <ul>
                <li v-for="item of items">
                    <span>名称:{{item.name}}</span>
                    <span>-</span>
                    <span>价格:{{item.price | rmb('$')}}</span>
                </li>
            </ul>
        </div>
    </body>
    <script>
        // 过滤器就是对内容(数据)进行过滤(二次操作)的一种方式
        Vue.filter('rmb', function(price, flag = '￥') {
            return `${flag}` + (price / 100).toFixed(2)
        })
        let app = new Vue({
            el: '#app',
            data: {
                items: [{
                    id: 1,
                    name: 'iphone',
                    price: 888888
                }, {
                    id: 2,
                    name: 'ipad',
                    price: 666666
                }, {
                    id: 3,
                    name: 'imac',
                    price: 1888888
                }]
            }
        })
    </script>
</html>
```

### 五、computed 与 watch

#### 5.1 computed（计算属性） 

在实际的应用 中，我们会有一些原始数据，同时在应用中又会有一些数据是根据某些元素数据派生出来的，针对这样的一种情况，`vue`定义了一个专门用来处理这种派生数据的选项`computed`

直接看例子：这里我们需要点击按钮来筛选对应的数据，而他的数据正是从`users`派生出来的，按照常规的做法得定义事件，定义中间数组过滤等，但我们使用`computed`计算属性之后，它会根据监听的数据变化，而导致自身属性的变化，从而更新视图

```html
<div id="app">
    <label><input type="radio" v-model="gender" value="" /> 所有</label>
    <label><input type="radio" v-model="gender" value="男" /> 男</label>
    <label><input type="radio" v-model="gender" value="女" /> 女</label>
    <hr>
    <ul>
        <li v-for="user of showUsers">
            {{user.username}}
        </li>
    </ul>
</div>

<script>
    let app = new Vue({
        el: '#app',
        data: {
            gender: '',
            users: [
                {id: 1, username: 'baogege', gender: '男'},
                {id: 2, username: 'mt', gender: '男'},
                {id: 3, username: 'haigege', gender: '男'},
                {id: 4, username: 'zMouse', gender: '男'},
                {id: 5, username: 'reci', gender: '女'},
                {id: 6, username: 'lisi', gender: '女'}
            ]
        },
        computed: {
            showUsers() {
                return this.gender === '' ? [...this.users] : this.users.filter(user=>user.gender===this.gender);
            }
        }
    });
</script>
```

注意：

* 计算属性类似`getter`和`setter`,当访问某个计算属性的时候，就会调用`computed`中同名的函数，函数的返回值（其实也就是对于的get函数的返回值）将作为该计算属性的值
* 计算属性的值依赖计算函数中依赖的其他响应式数据（也就是data对象中的数据）
* 计算属性的值是可以缓存的，如果依赖的其他响应式数据没有发生变化，但多次访问该计算属性，得到的结果是最近一次变化产生的值（相对于调用方法得到结果在某些时候性能要好一些，因为调用方法形式它不管数据是否变化，都会执行一次方法里面的代码），例如下面的例子：

实例

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="js/vue.js"></script>
    </head>

    <body>
        <div id="app">
            <p v-if="showDate">{{getNow()}}</p>
            <button @click="showDate = !showDate">{{showDate}}</button>
            <p v-if="showDate">{{now}}</p>
        </div>
    </body>

    <script>
        let app = new Vue({
            el: '#app',
            data: {
                showDate: true,
            },
            methods: {
                getNow() {
                    return Date.now()
                }
            },
            computed: {
                now: {
                    get() {
                        // 这里没有如何其他依赖的data数据，模拟依赖的数据没有变化
                        return Date.now()
                    }
                }
            }
        })
    </script>
</html>
```

**计算属性的 getter 与 setter**

默认情况下，计算属性函数是一个`getter`函数，如果计算数学只有`get`，而不需要`set`函数处理，那么可以将计算属性进行简写

```js
computed: {
    now() {
        return Date.now();
    }
    // 等于
    now: {
        get() {
            return Date.now();
        }
    }
}
```

但是有的时候，这种派生数据具有`get`需求，也有`set`需求，例如

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="js/vue.js"></script>
    <style>
        .active {
            background: pink;
        }
    </style>
</head>

<body>
    <div id="app">
        <div>
            <button :class="{active:gender === '' }" @click="gender =''">全部</button>
            <button :class="{active:gender==='男'}" @click="gender ='男'">男生</button>
            <button :class="{active:gender==='女'}" @click="gender = '女'">女生</button>
        </div>

        <ul>
            <li v-for="user in showUsers">
                <input type="checkbox" v-model="user.checked"> {{user.username}}
            </li>
        </ul>
        <hr>
        <input type="checkbox" v-model="checkedAll"> 全选

    </div>
</body>

<script>
    let users = [{
        id: 1,
        username: 'baogege',
        gender: '男',
        checked: false
    }, {
        id: 2,
        username: 'mt',
        gender: '男',
        checked: true
    }, {
        id: 3,
        username: 'haigege',
        gender: '男',
        checked: false
    }, {
        id: 4,
        username: 'zMouse',
        gender: '男',
        checked: false
    }, {
        id: 5,
        username: 'reci',
        gender: '女',
        checked: true
    }, {
        id: 6,
        username: 'lisi',
        gender: '女',
        checked: false
    }];
    let app = new Vue({
        el: '#app',
        data: {
            gender: '',
            users: users
        },
        computed: {
            showUsers: {
                // get 方法定义该属性是如何得到值的
                get() {
                    if (this.gender == '') {
                        return this.users
                    } else {
                        return this.users.filter(item => item.gender === this.gender)
                    }
                }
            },
            // checkAll 属性：它的值其实是由users中每个数据的checked值来决定的
            checkedAll: {
                get() {
                    return this.users.every(user => user.checked)
                },
                set(newVal) {
                    this.users = this.users.map(user => {
                        return Object.assign(user, {
                            checked: newVal
                        })
                    })
                }
            }
        }
    })
</script>

</html>
```

在使用 `computed`计算属性的时候需要注意的几点

* computed 里面存放的是 **属性**，类似data ，而不是类似methods里面的方法，所以称为计算属性
* 它可以像data中的属性一样使用，结构类似(defineProperty)，有getter 和 setter
* 计算属性值变化是随着它监听的 `data`中的数据变化而变化的，只要它监听data中的任意的值变化后，自身就会被调用，重新运算获取值
* **它不能处理派生数据是通过异步方式得到的的数据，也不支持promise等使用，只能监听在初始化之后以及存在的值，这和它依赖return形式的返回值有关**，对于异步的数据，我们可以采用`watch`来监听 

#### 5.2 watch

有的时候，我们需要的派生数据是通过异步的方式得到的，这个时候，计算属性就不太好用（不能处理异步），但我们可以使用另外一个选项：`watch`，它的用法和 computed 差不多，但需要注意的是：

* 监听的数据是在 `data`中的，函数名要和 data 监听数据的名字一致
* 它是主动去监听数据变化，不依赖返回值。
* **它还可以监听`computed`上的计算属性的变化**

```js
{
    computed(){
        theme(){
            return this.xxxx
        }
    },
    watch:{
        theme(){
           // 响应theme计算属性一改变要触发的事情
        }
    }
}
```

实例：在文本框输入字符，一秒后根据返回的数据进行渲染

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="js/vue.js"></script>
    </head>

    <body>
        <div id="app">
            <input type="text" v-model.lazy='keyWord'>
            <hr>
            <ul>
                <li v-for="user of showUsers">
                    {{user.username}}
                </li>
            </ul>
        </div>
    </body>

    <script>
        let app = new Vue({
            el: '#app',
            data: {
                keyWord: '',
                users: [{
                    id: 1,
                    username: 'baogege',
                    gender: '男',
                    checked: false
                }, {
                    id: 2,
                    username: 'mt',
                    gender: '男',
                    checked: false
                }, {
                    id: 3,
                    username: 'haigege',
                    gender: '男',
                    checked: false
                }, {
                    id: 4,
                    username: 'zMouse',
                    gender: '男',
                    checked: false
                }, {
                    id: 5,
                    username: 'reci',
                    gender: '女',
                    checked: false
                }, {
                    id: 6,
                    username: 'lisi',
                    gender: '女',
                    checked: false
                }],
                showUsers: []
            },
            watch: {
                // computed 是依赖数据变化
                // watch 主动处理数据变化
                keyWord() {
                    setTimeout(() => {
                        this.showUsers = this.users.filter(user => user.username.includes(this.keyWord))
                    }, 1000)
                }
            }

        })
    </script>

</html>
```

* watch 的多层监听

对于多层数据的监听，可以使用 **字符串 + 点语法**

```js
watch: {
    'a.b.c': function() {
        //...
    }
}
```

* 深度监听

默认情况下，`watch`只对当前指定的值进行一层监听，如果需要对对象进行深层监听，则可以

```js
watch: {
    a: {
        // handler 处理函数名不可变
        handler() {
            console.log('a deep');
        },
            deep: true
    }
}
```

* immediate与deep一样，当watch监听的属性写成对象形式时，是其的一个参数配置，值为`true or false`,但为 true时，这代表页面一刷新，被其监听的数据就会立刻执行watch中定义的 handler 方法，而不需要监听的数据发生变化才触发。当然监听的数据变化时，它也会被执行。通俗的说：它与未配置`immediate`参数的监听方法多一次自动触发而已。例如：我们一进入页面就要监听路由的携带的参数是否有变化，而对其做相对应的数据请求。

```js
watch:{
    $route:{
     	// ....  
        },
     immediate:true
    }
},
```

#### 5.3 computed VS watch 小结

* 处理数据的场景不同，监听器适合一个数据影响多个数据，计算属性适合一个数据受多个数据影响
* 计算属性有缓存性，计算所得的值如果没有变化不会重复执行
* 监听器选项提供了更通用的方法，适合执行异步操作或较大开销操作的情况

### 六、组件

#### 6.1 组件概念

在vue中，我们可以通过`new Vue`来创建应该组件，不过通常它是作为整个应用的顶层根组件存在的，我们还可以通过另外的方式来注册一个更为通用的组件

Vue.component()

```js
Vue.component('组件名称',{组件选项})
```

* 组件名称遵循自定义组件命名规范： 全小写，连字符（虽然驼峰式一般也没问题）
* 组件选项与`new Vue`选项配置基本一致，（也有一些细节的不同，主要是data节点的返回形式不同）

全局组件与局部组件

通过 `Vue.component`注册的组件，我们成为**全局组件**，因为它可以在任意范围内使用，我们还可以定义**局部组件**

```js
new Vue({
    ...,
    components:{
    '组件名称':{组件选项}
        }
        })
```

在一个组件内部通过`components`选项注册的组件为**局部组件**，只能在当前`components`选项所在的组件内部使用

> 注意：局部注册的组件只能当前注册组件中使用，不能在它的子组件中使用

#### 6.2 组件data

在非`new Vue`创建的组件中，`data`必须为函数，函数的返回值必须是一个对象，作为组件的最终`data`

#### 6.3组件props

组件中内部私有数据存储中组件`data`中，而通过外部传入的数据，则通过`props`选项(节点)接收,需要注意几点：

* 如果传入的`props`值为一个表达式，则必须使用`v-bind`绑定，不然默认会是传入一个字符串值
* 组件中的`data`和`props`数据都可以通过组件实例进行直接访问，`this.xxx`的形式
* **data中的`key`与`props`中的keyz不能冲突**

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            a {
                padding: 5px 12px;
                margin: 5px;
                border: 1px solid #3ba9ff;
                text-decoration: none;
            }

            a.active {
                background: #3ba9ff;
                color: white;
            }
        </style>
        <script src="js/vue.js"></script>
    </head>

    <body>
        <div id="app">
            <ul>
                <li v-for="item in users" :key="item.id">
                    {{item.name}}
                </li>
            </ul>
            <!-- 如果属性没有使用 v-bind，那么传入的值就只有字符串 -->
            <k-pagination :pages="10" :page="3"></k-pagination>
            <k-pagination :pages="6" :page="3"></k-pagination>

        </div>
    </body>

    <script>
        Vue.component('k-pagination', {
            props: ['pages', 'page'], // 组件内部就可以通过this来直接访问proprs中的数据，类似data 中的数据一样
            template: `
                <div>
                <a href="">上一页</a>
                <a href="" v-for="p in pages" :class="{'active':p===page}">{{p}}</a>
                <a href="">下一页</a>
                        </div>
`
        })
        new Vue({
            el: '#app',
            data: {
                users: [{
                    id: 1,
                    name: '莫涛'
                }, {
                    id: 2,
                    name: '大海'
                }, {
                    id: 3,
                    name: '紫薯'
                }, {
                    id: 4,
                    name: '宝哥'
                }, {
                    id: 5,
                    name: '小姐姐'
                }, {
                    id: 6,
                    name: '央央'
                }, {
                    id: 7,
                    name: 'licas'
                }, ]
            }
        })
    </script>

</html>
```

#### 6.4 组件通信

* 父---->子

父组件将值传入到子组件，通过子组件的引用标签上属性绑定，并且在通过子组件的props节点接收参数，例如6.3的实例

* 子 ----> 父

vue中的数据默认是单向流动的，只能父到子可以直接传递，而子到父不能直接修改。原因在于父级数据不一定只是某个子组件在使用数据，那么如果一个子级内部随意去修改父级的数据就很容易出现数据错误。那么如果子级想修改数据，那么需要子级自己去通知父级，并且父级接收这个通知以后，去决定是否修改。这种通知方式：(自定义)事件机制

子组件去通知父组件通过触发`$emit()`方法即可，$emit:概念

Vue 为每个组件对象通过了一个内置方法`$emit`，它等同于自定义事件中的`new Event`,`trigger`等

```js
this.$emit('自定义事件名称',子组件传给父组件的事件数据)
```

* 事件数据就是触发父组件事件的通过携带传递的数据 
* 父级在绑定子组件的过程中，可以通过`@给子组件触发事件名称=自身定义的事件触发函数名`，给子组件绑定事件

实例：

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
            .pagination {
                margin: 20px 0;
            }
            .pagination a {
                padding: 5px 12px;
                border: 1px solid #3ba9ff;
                text-decoration: none;
                margin: 5px;
            }
            .pagination a.active {
                background: #3ba9ff;
                color: white;
            }
        </style>
    </head>
    <body>

        <div id="app">
            <ul>
                <li v-for="user of showUsers" :key="user.id">
                    {{user.name}}
                </li>
            </ul>
            <k-pagination :pages="uPages" :page="uPage" @changepage="changePage"></k-pagination>
        </div>

        <script src="./js/vue.js"></script>
        <script>
            Vue.component('k-pagination', {
                props: ['pages', 'page'],
                template: `
                    <div class="pagination">
                    <a href="">上一页</a>
                    <a
                    href=""
                    v-for="p of pages"
                    :class="{'active': p === page}"
                    @click.prevent="gotoPage(p)"
                    >
                    {{p}}
                                </a>
                    <a href="">下一页</a>
                                </div>
`,
                methods: {
                    gotoPage(p) {
                        console.log(p);
                        // this.page = p;
                        this.$emit('changepage', p);
                    }
                }
            });

            let app = new Vue({
                el: '#app',
                data: {
                    users: [
                        {id: 1, name: '莫涛'},
                        {id: 2, name: '大海'},
                        {id: 3, name: '紫薯'},
                        {id: 4, name: '宝哥'},
                        {id: 5, name: '小姐姐'},
                        {id: 6, name: '央央'},
                        {id: 7, name: 'licas'},
                    ],
                    uPage: 1,   // 当前和已有的数据没有太多关联，只会和用户的行为关联，所以它不算是派生的
                    prePage: 2, // 每页显示多少条
                    // uPages: 10  // 总的页数是根据已有的数据总条数，以及你每页想显示的数据条数来决定的所以，这个是派生的
                },
                computed: {
                    uPages() {
                        return Math.ceil(this.users.length / this.prePage);
                    },
                    showUsers() {
                        let start = (this.uPage - 1) * this.prePage;
                        return this.users.slice(start, start + this.prePage);
                    }
                },
                methods: {
                    changePage(page) {
                        console.log('子级修改了page', page);
                        this.uPage = page;
                    }
                }
            });
        </script>
    </body>
</html>
```

#### 6.5 组件双绑的实现（v-model）

虽然并不推荐在组件内部修改`props`，但是，有的时候确实希望组件内部状态变化的时候改变`props`对应的父组件的值，那么我们可以通过子组件触发事件，父级监听事件来达到这个目的，不过过程会比较繁琐，vue提供了一些操作来简化这个过程

**v-model**

v-model 是 vue 提供的一个用于实现数据双绑的指令，用来简化`props到data`，`data到props`的操作流程

* `props`指定要绑定的属性，默认是`value`
* `event`指定要绑定触发的事件，默认是 `input`事件

实现修改父组件数据的流程：v-model与父组件数据进行绑定，并且子组件的model节点上配置了父组件传入进来的数据点，以及需要触发事件，我们只需要在子组件通过`this.$emit(组件中的model节点定义的触发事件名，需要传到父组件的数据)`即可，这是因为我们在触发`model节点下定义的事件`，它会自动帮我们去修改父组件的值，即使父组件没有这个方法

**而这种方式我们不推荐使用，因为它隐藏了太多细节，会让我们在使用这个组件的时候，不是太明了，当出现了问题也不太容易进行排错，并且它只能同时绑定一个值 ，这种需求我们更推荐使用 `.sync`修饰符**

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            a {
                padding: 5px 12px;
                margin: 5px;
                border: 1px solid #3ba9ff;
                text-decoration: none;
            }

            a.active {
                background: #3ba9ff;
                color: white;
            }
        </style>
        <script src="js/vue.js"></script>
    </head>

    <body>
        <div id="app">
            <ul>
                <li v-for="item in showUsers" :key="item.id">
                    {{item.name}}
                </li>
            </ul>
            <!-- 如果属性没有使用 v-bind，那么传入的值就只有字符串 -->
            <k-pagination :pages="uPages" v-model="uPage"></k-pagination>
        </div>
    </body>
    <script>
        Vue.component('k-pagination', {
            props: ['pages', 'page'], // 组件内部就可以通过this来直接访问proprs中的数据，类似data 中的数据一样
            // model 选项是用来设定 v-model绑定的规则的 v-model只能同时绑定一个prop
            model: {
                // prop 告诉v-model绑定的prop是那个
                prop: 'page',
                // event 告诉v-model触发什么事件。自动去修改v-model 的绑定值
                event: 'changepage'
            },
            template: `
                <div>
                <a href="">上一页</a>
                <a 
                v-for="p in pages"
                :class="{'active':p===page}"   @click.prevent="goToPage(p)" >
                {{p}}
                        </a>
                <a href="">下一页</a>
                        </div>
                `,
            methods: {
                goToPage(p) {
                    console.log(this)
                    this.$emit('changepage', p)
                }
            }
        })
        new Vue({
            el: '#app',
            data: {
                users: [{
                    id: 1,
                    name: '莫涛'
                }, {
                    id: 2,
                    name: '大海'
                }, {
                    id: 3,
                    name: '紫薯'
                }, {
                    id: 4,
                    name: '宝哥'
                }, {
                    id: 5,
                    name: '小姐姐'
                }, {
                    id: 6,
                    name: '央央'
                }, {
                    id: 7,
                    name: 'licas'
                }, ],
                prePage: 2, // 每页显示多少条
                uPage: 1, // 当前和已有的数据没有太多关联，只会和用户的行为关联，所以它不算是派生的
                // uPages: 8 // 总的页数是根据已有的数据总条数，以及你每页想显示的数据条数来决定的，所以这个是派生的
            },
            computed: {
                uPages() {
                    return Math.ceil(this.users.length / this.prePage)
                },
                showUsers() {
                    let start = (this.uPage - 1) * this.prePage
                    return this.users.slice(start, start + this.prePage)
                }
            },
            methods: {}
        })
    </script>
</html>
```

另一种写法

```html
<!--父组件-->
<template>
	<div>
       <Stuff 
        v-model="info"
      ></Stuff>
    </div>
</template>

<script>
export default {
  name: 'create',
  components: {Stuff,Upload,UploadImg},
  data() {
        return {
            info:'123'
        }
    }
}
</script>

<!--子组件-->
<template>
  <div class="stuff">
    <div class="clearfix">
      {{value}}
      <div class="raw-item" v-for="(item,index) in value" :key="index" >
        <el-input placeholder="请输入内容" style="width: 200px" v-model="item.name" ></el-input>
        <el-input placeholder="请输入内容" style="width: 100px" v-model="item.specs"></el-input>
        <i class="delete-icon el-icon-close" v-show="value.length !== 1" @click="remove(index)"></i>
      </div>
    </div>
    <el-button  class="eaeaea" type="primary" size="medium" icon="el-icon-plus" @click="add">增加一项</el-button>
  </div>
</template>

<script>
// v-model 在组件上面双向绑定 value 发布事件input 
export default {
  props: {
    value: {
      type: Array,
      default: () => []
    }
  },
  methods:{
    remove(index){
      const newValue = this.value.filter((item, i) => {
        return i !== index;
      })
      // 触发input 事件去修改
      this.$emit('input', newValue)
    },
    add(){
      this.$emit('input', [
        ...this.value,
        { "name": "", "specs": "" }
      ])
    }
  }
}
</script>
```

上面实例中：v-model 直接传递绑定的值，我们的子组件内部可以通过 value 字段在 props 中直接接收，在通过 this.$emit('input',传递修改好的值) 去修改父组件上与 v-model 绑定的值

#### 6.6 .sync

通过`v-model`来进行双向绑定，会给状态维护带来一定的问题，因为修改比较隐蔽，同时只能处理一个prop 的绑定，那么在需要绑定多个prop时，我们可以通过`.sync`修饰符来达到这个目的，当要需要父组件的数据的时候，我们可以通过调用**`this.$emit('update:prop名称')`**的格式进行触发

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            a {
                padding: 5px 12px;
                margin: 5px;
                border: 1px solid #3ba9ff;
                text-decoration: none;
            }

            a.active {
                background: #3ba9ff;
                color: white;
            }
        </style>
        <script src="js/vue.js"></script>
    </head>

    <body>
        <div id="app">
            <ul>
                <li v-for="item in showUsers" :key="item.id">
                    {{item.name}}
                </li>
            </ul>
            <!-- 如果属性没有使用 v-bind，那么传入的值就只有字符串 -->
            <k-pagination :pages="uPages" :page.sync="uPage" @changepage="changePage"></k-pagination>
        </div>
    </body>

    <script>
        // v-model 我们不推荐事件 v-model,原因它隐藏了太多细节，会让我们在使用这个组件的时候，不是太明了，当出现了问题也不太容易进行拍错
        // 同时v-model 也绑定不了多个prop
        // 推荐使用 .sync 修饰符

        Vue.component('k-pagination', {
            props: ['pages', 'page'], // 组件内部就可以通过this来直接访问proprs中的数据，类似data 中的数据一样
            template: `
                    <div>
                    <a href="" @click.prevent="prev">上一页</a>
                    <a 
                    v-for="p in pages"
                    :class="{'active':p===page}"   @click.prevent="goToPage(p)" >
                    {{p}}
                            </a>
                    <a href="">下一页</a>
                            </div>
`,
            methods: {
                goToPage(p) {
                    console.log(this)
                    this.$emit('update:page', p)
                },
                prev() {
                    this.$emit('update:page', this.page - 1)
                }
            }
        })
        new Vue({
            el: '#app',
            data: {
                users: [{
                    id: 1,
                    name: '莫涛'
                }, {
                    id: 2,
                    name: '大海'
                }, {
                    id: 3,
                    name: '紫薯'
                }, {
                    id: 4,
                    name: '宝哥'
                }, {
                    id: 5,
                    name: '小姐姐'
                }, {
                    id: 6,
                    name: '央央'
                }, {
                    id: 7,
                    name: 'licas'
                }, ],
                prePage: 2, // 每页显示多少条
                uPage: 1, // 当前和已有的数据没有太多关联，只会和用户的行为关联，所以它不算是派生的
                // uPages: 8 // 总的页数是根据已有的数据总条数，以及你每页想显示的数据条数来决定的，所以这个是派生的
            },
            computed: {
                uPages() {
                    return Math.ceil(this.users.length / this.prePage)
                },
                showUsers() {
                    let start = (this.uPage - 1) * this.prePage
                    return this.users.slice(start, start + this.prePage)
                }
            },
            methods: {}
        })
    </script>

</html>
```

#### 6.7 插槽

默认情况下，组件模板解析后会替换整个组件内容，如果我们想要在组件引用被包含的内容，可以通过vue提供的内置组件`slot`来获取，默认情况下，组件包裹的内容会被插入到组件的`<slot></slot>`标签位置，当然我们也可以给插槽进行命名用于区分不同的插槽，也可以往插槽的传入父组件的数据

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            a {
                padding: 5px 12px;
                margin: 5px;
                border: 1px solid #3ba9ff;
                text-decoration: none;
            }

            a.active {
                background: #3ba9ff;
                color: white;
            }
        </style>
        <script src="js/vue.js"></script>
    </head>

    <body>
        <div id="app">
            <ul>
                <li v-for="item in showUsers" :key="item.id">
                    {{item.name}}
                </li>
            </ul>
            <!-- 如果属性没有使用 v-bind，那么传入的值就只有字符串 -->
            <k-pagination :pages="uPages" :page="uPage" @changepage="changePage">
                <span>一共有{{uPages}}页，当前第{{uPage}}页</span>
            </k-pagination>


        </div>
    </body>

    <script>
        Vue.component('k-pagination', {
            props: ['pages', 'page'], // 组件内部就可以通过this来直接访问proprs中的数据，类似data 中的数据一样
            template: `
                <div>
                <a href="">上一页</a>
                <a 
                v-for="p in pages"
                :class="{'active':p===page}"   @click.prevent="goToPage(p)" >
                {{p}}
                        </a>
                <a href="">下一页</a>
                <slot></slot> // 默认将组件包含的内容放入的位置
                        </div>
`,
            methods: {
                goToPage(p) {
                    console.log(this)
                    this.$emit('changepage', p)
                }
            }
        })
        new Vue({
            el: '#app',
            data: {
                users: [{
                    id: 1,
                    name: '莫涛'
                }, {
                    id: 2,
                    name: '大海'
                }, {
                    id: 3,
                    name: '紫薯'
                }, {
                    id: 4,
                    name: '宝哥'
                }, {
                    id: 5,
                    name: '小姐姐'
                }, {
                    id: 6,
                    name: '央央'
                }, {
                    id: 7,
                    name: 'licas'
                }, ],
                prePage: 2, // 每页显示多少条
                uPage: 1, // 当前和已有的数据没有太多关联，只会和用户的行为关联，所以它不算是派生的
                // uPages: 8 // 总的页数是根据已有的数据总条数，以及你每页想显示的数据条数来决定的，所以这个是派生的
            },
            computed: {
                uPages() {
                    return Math.ceil(this.users.length / this.prePage)
                },
                showUsers() {
                    let start = (this.uPage - 1) * this.prePage
                    return this.users.slice(start, start + this.prePage)
                }
            },
            methods: {
                changePage(p) {
                    this.uPage = p
                }
            }
        })
    </script>

</html>
```

* 具名插槽

通过在子组件上的`slot`标签上定义`name='插槽名'`属性，而在父组件包含的内容标签上使用`slot='插槽名`进行插槽绑定

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            a {
                padding: 5px 12px;
                margin: 5px;
                border: 1px solid #3ba9ff;
                text-decoration: none;
            }

            a.active {
                background: #3ba9ff;
                color: white;
            }
        </style>
        <script src="js/vue.js"></script>
    </head>

    <body>
        <div id="app">
            <ul>
                <li v-for="item in showUsers" :key="item.id">
                    {{item.name}}
                </li>
            </ul>
            <!-- 如果属性没有使用 v-bind，那么传入的值就只有字符串 -->
            <k-pagination :pages="uPages" :page="uPage" @changepage="changePage">
                <span slot="header">开课吧</span>
                <span slot="footer">一共有{{props.pages}}页，当前第{{props.page}}页</span>

            </k-pagination>


        </div>
    </body>

    <script>
        Vue.component('k-pagination', {
            props: ['pages', 'page'], // 组件内部就可以通过this来直接访问proprs中的数据，类似data 中的数据一样
            template: `
                <div>
                    <slot name="header"></slot>
                    <a href="">上一页</a>
                    <a 
                        v-for="p in pages"
                        :class="{'active':p===page}"   @click.prevent="goToPage(p)" >
                        {{p}}
                    </a>
                    <a href="">下一页</a>

                    <slot name="footer" :pages="pages" :page="page"></slot>
                 </div>
`,
            methods: {
                goToPage(p) {
                    console.log(this)
                    this.$emit('changepage', p)
                }
            }
        })
        new Vue({
            el: '#app',
            data: {
                users: [{
                    id: 1,
                    name: '莫涛'
                }, {
                    id: 2,
                    name: '大海'
                }, {
                    id: 3,
                    name: '紫薯'
                }, {
                    id: 4,
                    name: '宝哥'
                }, {
                    id: 5,
                    name: '小姐姐'
                }, {
                    id: 6,
                    name: '央央'
                }, {
                    id: 7,
                    name: 'licas'
                }, ],
                prePage: 2, // 每页显示多少条
                uPage: 1, // 当前和已有的数据没有太多关联，只会和用户的行为关联，所以它不算是派生的
                // uPages: 8 // 总的页数是根据已有的数据总条数，以及你每页想显示的数据条数来决定的，所以这个是派生的
            },
            computed: {
                uPages() {
                    return Math.ceil(this.users.length / this.prePage)
                },
                showUsers() {
                    let start = (this.uPage - 1) * this.prePage
                    return this.users.slice(start, start + this.prePage)
                }
            },
            methods: {
                changePage(p) {
                    this.uPage = p
                }
            }
        })
    </script>
</html>
```

* 给插槽传入数据(使用作用域插槽)

采用官方提供的`template`组件进行包裹，而且页面解析的时候，这个标签会被忽略解析。传参通过指令`v-slot`配置，例如`v-slot:footer="props" `: v-slot 指令，footer 插槽名(默认插槽名为default)，props 组件传入插槽的所有值集合。

> **再次强调：**props数据它是来着使用插槽的组件上，也就是下面例子的`k-pagination`组件，props会搜集`<slot>`组件除了`name`的所有属性

流程：在组件的slot标签上定义要传入的数据，例如`<slot :n="1"></slot>`,之后在使用`template`组件上接收传入的参数即可

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
            .pagination {
                margin: 20px 0;
            }

            .pagination a {
                padding: 5px 12px;
                border: 1px solid #3ba9ff;
                text-decoration: none;
                margin: 5px;
            }

            .pagination a.active {
                background: #3ba9ff;
                color: white;
            }
        </style>
    </head>

    <body>

        <div id="app">

            <ul>
                <li v-for="user of showUsers" :key="user.id">
                    {{user.name}}
                </li>
            </ul>

            <k-pagination :total="users.length" :pre-page="prePage" :page.sync="uPage">
                <span style="color:red" slot="header">开课吧友情赞助</span>

                <template v-slot:footer="props">
                    {{props}}
                    <span>一共有{{props.pages}}页，当前第{{props.page}}页</span>
                </template>
            </k-pagination>
        </div>

        <script src="./js/vue.js"></script>
        <script>
            /**
         * 组件->插槽
         */
            Vue.component('k-pagination', {
                props: ['total', 'page', 'prePage'],
                data() {
                    return {
                        pages: Math.ceil(this.total / this.prePage)
                    }
                },
                template: `
                <div class="pagination">
                    <slot name="header"></slot>
                    <a href="" @click.prevent="prev">上一页</a>
                    <a
                        href=""
                        v-for="p of pages"
                        :class="{'active': p === page}"
                        @click.prevent="gotoPage(p)"
                    >
                    {{p}}
                   </a>
                    <a href="">下一页</a>
                    <slot name="footer" :pages="pages" :page="page"></slot>
                </div>
`,
                methods: {
                    gotoPage(p) {
                        this.$emit('update:page', p);
                    },
                    prev() {
                        this.$emit('update:page', this.page - 1);
                    }
                }
            });

            let app = new Vue({
                el: '#app',
                data: {
                    users: [{
                        id: 1,
                        name: '莫涛'
                    }, {
                        id: 2,
                        name: '大海'
                    }, {
                        id: 3,
                        name: '紫薯'
                    }, {
                        id: 4,
                        name: '宝哥'
                    }, {
                        id: 5,
                        name: '小姐姐'
                    }, {
                        id: 6,
                        name: '央央'
                    }, {
                        id: 7,
                        name: 'licas'
                    }, ],
                    uPage: 1,
                    prePage: 2,
                },
                computed: {
                    showUsers() {
                        let start = (this.uPage - 1) * this.prePage;
                        return this.users.slice(start, start + this.prePage);
                    }
                }
            });
        </script>
    </body>
</html>
```

#### 6.8 props验证

组件的 `props`就是组件的参数，为了确保传入的数据在可控的合理范围内，我们需要对传入的props的值类型进行必要的验证

```js
Vue.component('my-component', {
    props: {
        // 基础的类型检查 (`null` 和 `undefined` 会通过任何类型验证)
        propA: Number,
        // 多个可能的类型
        propB: [String, Number],
        // 必填的字符串
        propC: {
            type: String,
            required: true
        },
        // 带有默认值的数字
        propD: {
            type: Number,
            default: 100
        },
        // 带有默认值的对象
        propE: {
            type: Object,
            // 对象或数组默认值必须从一个工厂函数获取
            default: function () {
                return { message: 'hello' }
            }
        },
        // 自定义验证函数
        propF: {
            validator: function (value) {
                // 这个值必须匹配下列字符串中的一个
                return ['success', 'warning', 'danger'].indexOf(value) !== -1
            }
        }
    }
})
```

* 非 prop 特性

一个非`prop`特性是指传向一个组件，但是该组件并没有相应的`props`定义接收的特性，这些`props`会被自动添加到组件上的根元素上面，在默认情况下，非`prop`特性的属性会覆盖组件根元素上同名的内容，而针对`style`和`class`有特殊的处理，它们会进行合并(标签上同样的样式还是会被非prop覆盖)

如果不希望组件的根元素进行继承特性，你可以在组件的选项中设置`inheritAttrs:false`，我们可以通过组件的`this.$attrs`来获取这些属性，例如下面的 c 在标签上的值还是 123

> 注意 `inheritAttrs: false` 选项**不会**影响 `style` 和 `class` 的绑定

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="js/vue.js"></script>
    </head>
    <body>
        <div id="app">
            <k-child a="1" b="2" c="3" style="background: yellow;color:red;"></k-child>
        </div>
    </body>
    <script>
        new Vue({
            el: '#app',
            components: {
                kChild: {
                    inheritAttrs: false,
                    props: ['a', 'b'],
                    template: `
<div c="123" style="color:green;">Child - a:{{a}} - b:{{b}} - {{this.$attrs}}</div>

                }
            }
        })
    </script>

</html>
```

#### 6.9 函数式组件

组件没有管理任何状态，也没有监听如何传递给它的状态（不watch外界属性），也没有生命周期方法时，可以将组件标记为`functional`，这意味着它无状态（没有响应式数据）也没有实例（没有`this`上下文)

```js
Vue.component('heading',{
    functional:true, // 标记函数式组件
    props:['level','title','icon'], 
    render(h,context) { // 上下嗯传参
        let children = []
        // 属性获取
        const {icon,title,level} = context.props
        if(icon) {
            children.push(h(
                'svg',
                {class:'icon'},
                [h('use',attrs:['xlink:href':'#icon-'+icon])]))
            // 子元素获取
            children = children.concat(context.children)
        }
        vnode= h(
            'h'+level,
            {attr:{title}},
            children
                )
        console.log(vnode)
        return vnode
    }
})
```

#### 6.10 组件的理解

vue组件化的理解：组件是Vue的精髓，Vue应用就是由一个个组件构成的。Vue的组件化涉及到的内容非常多，当面试时被问到：谈一下你对vue组件化的理解，这时候有可能无从下手，可以从以下几点进行阐述

**定义**:组件是 可复用 的Vue实例，准确讲它们是`VueComponent`的实例，继承自`Vue`

**优点**：组件可以添加代码的`复用性、可维护性、可测试性`

**使用场景**：什么时候使用组件？以下分类可作为参考

* 通用组件：实现最基本的功能，具有通用性，复用性，例如按钮组件、输入框组件、布局组件等
* 业务组件：它们完成具体业务，具有一定的复用性，例如登录组件、轮播图组件。
* 页面组件：组织应用各部分独立内容，需要时在不同页面间切换。例如：列表页、详情页组件

**如何使用组件**

* 定义：Vue.component()、components选项，sfc
* 分类：有状态组件（需要依赖data、props），functional（无状态组件，不需要data），abstract(抽象型组件，例如`transition`)
* 通信：props、emit() /  on() 、provide / inject 、 children / parent/ root / listeners
* 使用及优化：is、keep-alive 、异步组件

**组件本质**

vue中的组件经历如下过程：组件配置 -> VueComponent 实例   -> render() -> virtual Dom -> Dom ,所以组件的本质是产生虚拟DOM

### 七、Vue-cli与单文件组件

* vue-cli 脚手架，用于构建vue项目。
* 单文件是vue 提供的一套简便书写组件的文件形式。

对于两者的介绍，小编有一篇专门详细介绍该内容，可以移架查看。

### 八、生命周期

![生命周期](/medias/imges/vue/basic/lifecycle.png) 

组件生命周期指的是组件从创建到销毁的过程，在这个过程中的一些不同阶段，vue会调用指定的一些组件方法

基本生命周期函数有下面几个阶段

* 创建阶段
* 挂载阶段
* 更新阶段
* 卸载阶段

每一个阶段都对应着之前 与 之后两个函数

1. 创建阶段：

* beforeCreate()

初始化阶段，应用不多

* created()

在实例创建完成后被立即调用，该阶段完成了对`data`中的数据的`observer`，该阶段可以出来一些异步任务

2. 挂载阶段

* beforeMount()

在挂载开始之前被调用，应用不多

* mounted()

该阶段执行完了模板解析，以及挂载，同时组件更组件被赋给了`$el`属性，该阶段可以通过 DOM 操作对组件内部元素进行处理，【this.$el指向该组件的挂载点元素】

3. 更新阶段

* beforeUpdate()

数据更新时调用，但是还没有对视图进行重新渲染，这个时候，可以获取视图更新之后的状态

* updated()

由于数据的变更导致的视图重新渲染，可以通过DOM 操作来获取视图的最新状态

3. 卸载阶段

* beforeDestroy()

销毁之前调用，移除一些不必要的冗余数据，比如定时器

* destroyed()

Vue 实例销毁后调用

* errorCaptured()

当捕获一个来自子或子孙组件的错误时被调用，此钩子会接受三个参数：错误对象，发生错误的组件实例，包含错误来源信息的字符串。此钩子可以通过返回`return false`来阻止该错误继续往上传播

**小结：**

* **子组件的挂载优先于父组件**

```html
<template>
  <div id="app">
    <h1>hello world</h1>
    <Hi v-if="isShow" />
    <button @click="x++" >{{x+y}}</button>
    <button @click="isShow=!isShow">{{isShow}}</button>
  </div>
</template>

<script>
import Hi from './components/Hi.vue'

export default {
  name: 'App',
  data() {
    return {
      x:1,
      y:2,
      isShow:false
    }
  },
  components:{
    Hi
  },
  beforeCreate() {
    console.group('beforeCreate')
    console.log(this.$data)
    console.groupEnd('beforeCreate')
  },
  created() {
    console.group('created')
    console.log(this.$data)
    console.groupEnd('created')
  },
  beforeMount() {

    console.group('beforeMount')
    console.log(this.$el)
    console.groupEnd('beforeMount')
  },
  mounted() {
    console.group('mounted')
    console.log(this.$el)
    console.groupEnd('mounted')
  },
  beforeUpdate() {
    console.group('beforeUpdate')
    console.log(this.$el)
    console.groupEnd('beforeUpdate')
  },
  updated() {
    console.group('updated')
    console.log(this.$el)
    console.groupEnd('updated')
  },
  errorCaptured(err,vm,info) {
    console.log('子组件出错了')
    return false
  }
 }
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

### 九、ref与$refs

如果我们希望获取组件的节点，进行 DOM 相关操作，可以通过`ref`和`$refs`来完成

如果在普通的DOM元素上使用，引用指向的就是DOM元素；如果用在子组件上，引用就指向组件

* ref：给元素或组件添加`ref`属性，则该元素或组件实例对象将被添加到当前组件实例对象的`$refs`属性下面
* $refs ： 该属性是一个对象，存储了通过 `ref`绑定的元素对象或组件的实例对象

流程是：先通过`ref`给元素或组件进行绑定，在通过`this.$refs.ref的绑定名`进行对象获取

```html
<template>
    <div id="app">
        <h1>hello world</h1>
        <hr>
        <button @click="start">开启定时器Hi组件的</button>
        <Hi v-if="isShow" ref="hi" />
        <button @click="x++" >{{x+y}}</button>
        <button @click="isShow=!isShow">{{isShow}}</button>
        <hr>
        <input type="text" ref="myInput">
        <button @click="setFocus" >按钮</button>
    </div>
</template>

<script>
    import Hi from './components/Hi.vue'

    export default {
        name: 'App',
        data() {
            return {
                x:1,
                y:2,
                isShow:false
            }
        },
        components:{
            Hi
        },
        methods:{
            setFocus() {
                // let input =  document.querySelector('input')
                // console.log(input)
                // input.focus()
			// 获取元素对象
                this.$refs.myInput.focus()
            },
            start() {
                // 获取组件实例对
                console.log(this.$refs.hi)
                this.$refs.hi.start()
            }
        }
    }
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>
```

注意：

> * ref 是作为渲染结果被创建的，在初始渲染时不能访问它们
> * $refs 不是响应式的，不要试图用它在模板中做数据绑定
> * 当`v-for`用来元素或组件时，引用信息将是包含DOM节点或组件实例的数组

### 十、nextTick

当数据更新的时候，视图并不会立即更新，这个时候我们期望立马获取到视图更新后的数据，可以通过`nextTick`来进行操作，`nextTick`方法将在更新队列循环结束之后立即调用

```html
<template>
  <div id="app">
    <h1>hello world</h1>
    <hr>
    <Hi />
    <button @click="inc">{{v}}</button>
  </div>
</template>

<script>
import Hi from './components/Hi.vue'

export default {
  name: 'App',
  data() {
    return {
     v:1
    }
  },
  components:{
    Hi
  },
  methods:{
    inc(e) {
      this.v++
      // console.log(this.v,e.target.innerHTML)
      this.$nextTick(()=>{
          // 调用后获取到最新状态
           console.log(this.v,e.target.innerHTML)
      })
    },
  }
 }
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

```

### 十一、动态组件

有的时候，我们需要在多个不同的组件之间切换，虽然我们可以通过 `v-if`来处理，但是会表示麻烦，vue 提供了一个更方便的方式来处理这个情况，通过内置的`component`组件

#### component 组件

component 是 vue 内置的一个组件，它他提供了一个 `is`属性用来动态渲染不同的组件，is的值是注册组件节点下的组件名，但我们会发现，当组件切换的时候，都会触发组件的销毁和重建。首先性能不好，其次，会丢失组件状态。为了解决这个问题我们可以在套一个`keep-alive`组件

#### keep-alive组件

使用了`keep-alive`的组件会触发`activated`、`deactivated`两个生命周期函数

* activated: keep-alive 组件激活时调用
* deactivated : keep-alive 组件停用时调用

```html
<template>
<div id="app">
    <button @click="flag='InBox1'">收邮件</button>
    <button  @click="flag='PostMail'">发邮件</button>
    <button  @click="flag='RecycleBin'">回收站</button>
    <hr>
    <keep-alive>
        <component :is="flag" ></component>
    </keep-alive>

    </div>
</template>

<script>
    import InBox from './components/InBox.vue'
    import PostMail from './components/PostMail.vue'
    import RecycleBin from './components/RecycleBin.vue'
    export default {
        name: 'App',
        data() {
            return {
                flag:'InBox1'
            }
        },
        components:{
            InBox1:InBox,
            PostMail,
            RecycleBin
        }
    }
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>

```

### 十一、插件

Vue.js 的插件应该暴露一个`install`方法，这个方法的第一个参数是`vue`构造器，第二个可选的选项对象：

```js
MyPlugin.install = function(Vue,options) {
    // 1.添加全局方法或属性
    Vue.myGlobalMethod = function(){}
    // 2.添加全局资源
    Vue.directive('my-directive',{})
    // 3.注入组件选项
    Vue.mixin({
        created:function(){
            // 逻辑...
        }
    })
    // 4.添加实例方法
    Vue.prototype.$myMethod = function(methodOptions){}
}
```

范例：修改heading组件为插件

```js
// 插件需要实现install
const MyPlugin = {
  install(Vue, options) {
    // heading组件
    // <heading :level="1" :title="title" icon="cart">{{title}}</heading>
    // <h2 title=""></h2>
    Vue.component('heading', {
      props: {
        level: {
          type: String,
          required: true
        },
        title: {
          type: String,
          default: ''
        },
        icon: {
          type: String
        }
      },
      render(h) {
        // 子节点数组
        let children = []

        // icon属性处理逻辑
        if (this.icon) {
          // <svg class="icon"><use xlink:href="#icon-cart"/></svg>
          children.push(h(
            'svg',
            { class: 'icon' },
            [h('use', { attrs: { 'xlink:href': '#icon-' + this.icon } })]
          ))

        }

        // 拼接子节点
        children = children.concat(this.$slots.default)

        const vnode = h(
          'h' + this.level, // 参数1：tagname
          { attrs: { title: this.title } }, // 参数2：{。。。}
          children // 参数3：子节点VNode数组
        )
        console.log(vnode);
        // 返回createElement返回的VNode
        return vnode
      }
    })
  }
}

if (typeof window !== 'undefined' && window.Vue) {
  // 使用插件使用Vue.use()
  window.Vue.use(MyPlugin)
}
```

**之后直接引入界面即可使用插件提供的一些组件或方法**

### 十二、动画

在vue中给组件或元素添加动画的方式可以分很多种，但总体无非还是通过`css`和`javascript`来进行处理

* css 可以直接属性样式，通过``transition` `就可以做到
* js 可以通过获取DOM元素添加样式
* 而vue中为元素和组件添加了动画的生命周期

#### 12.1 动画的生命周期生效的情况

* 条件渲染 (使用`v-if`)
* 条件展示 (使用`v-show`)
* 使用动态组件
* 根组件渲染

#### 12.2 transition 组件

通过`transition`组件包裹的元素或组件，会在上面定义的几个场景中触发过渡，组件会自动给包裹的根DOM元素添加指定的class类名，如果要使用vue提供的动画生命周期函数，那么一定要 让元素或组件给`transtion`组件进行包裹

#### 12.3 过渡名

在给transtion包裹的元素或组件添加样式的时候，要在这几个过渡名写属性样式，否则动画无效

**进入动画过渡名**

**v-**是默认的，我们可以直接写定义，例如下面例子的`slide-fade`

| 过渡名         | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| v-enter        | 定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除 |
| v-enter-active | 调用进入过渡生效时的状态，在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡(动画)完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数 |
| v-enter-to     | 2.1.8版本以上定义进入过渡的结束状态。在元素被插入之后下一帧生效（与此同时`v-enter`被移除），自身在过渡完成之后移除，（可理解为在组件**进入**页面之后的动画最后一帧移除） |

**离开动画过渡名**

| 过渡名         | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| v-leave        | 定义离开过渡的开始状态。在离开过渡被触发时立即生效，下一帧被移除 |
| v-leave-active | 定义离开过渡生效时的状态，在整个离开过渡的过程中应用，在离开过渡被触发时立即生效，在过渡(动画)完成之后移除。这个类可以被用来定义离开过渡的时间，延迟和曲线函数 |
| v-leave-to     | 2.1.8版本及以上，定义离开过渡的结束状态。在离开过渡触发之后下一帧生效(与此同时`v-leave`被删除)，自身在过渡(动画)完成之后移除，可理解为在组件**离开**页面之后的动画最后一帧移除） |

![动画](/medias/imges/vue/basic/transition.png) 

```html
<template>
<div id="app">
    <h1>hello {{ 'world'| kkb1}} </h1>
    <button @click="flag='InBox'">收邮件</button>
    <button  @click="flag='PostMail'">发邮件</button>
    <button  @click="flag='RecycleBin'">回收站</button>
    <hr>
    <transition name="slide-fade">
        <component :is="flag" ></component>
    </transition>  
    </div>
</template>

<script>
    import InBox from './components/InBox.vue'
    import PostMail from './components/PostMail.vue'
    import RecycleBin from './components/RecycleBin.vue'
    export default {
        name: 'App',
        data() {
            return {
                flag:'InBox'
            }
        },
        components:{
            InBox,
            PostMail,
            RecycleBin
        }
    }
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
    .slide-fade-enter,.slide-fade-leave-to {
        opacity: 0;
    }
    .slide-fade-enter-to,slide-fade-leave {
        opacity: 1;
    }
    .slide-fade-enter-active,.slide-fade-leave-active {
        transition: all 2s ease;
    }
</style>

```

#### 12.4 结合css动画库

通过自定义过渡类名可以有效结合`Animate.css`这类动画库制作更精美的动画效果

引入animate.css

```html
<link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">
```

transition设置

```html
<transition enter-active-class="animated bounceIn" leave-active-class="animated bounceOut">
```

#### 12.5 JavaScipt 钩子

可以在`<transition>`属性中声明 JavaScrpt钩子，使用js实现动画

```html
<transition
 v-on:before-enter="beforEnter" // 动画开始前，设置初始状态
 v-on:enter="enter" // 执行动画
 v-on:after-enter="afterEnter" // 动画结束，清理工作
 v-on:enter-cancelled="enterCancelled" // 取消动画
 v-on:before-leave="beforeLeave" // 动画离场前，设置初始状态
 v-on:leave="leave"
 v-on:after-leave="afterLeave"
 v-on:leave-cancelled="leaveCancelled"
>
</transition>
```

范例：用JS实现消息动画

```js
Vue.component('message',{
    template:`
		<transition @before-enter="beforeEnter" @enter="enter">...</transition>
`,
    methods:{
        beforeEnter(el) {
            el.style.opacity=0 // 设置初始状态
        },
        enter(el,done) {
            document.body.offsetHeight // 触发回流激活动画
            el.style.opacity = 1 // 设置结束状态
        }
    }
})
```

**纯JS方案**

引入外包：

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"> </script>
```

```js
Vue.component('message',{
    template:`
		<transition 
		@before-enter="beforeEnter"
		@enter="enter"
		@before-leave="beforeLeave"
		@leave="leave"
		></transition>
`,
    methods:{
        beforeEnter(el) {
            el.style.opacity=0
        },
        enter(el,done) {
            Velocity(el,{opacity:1},{duration:500,complete:done})
        },
        beforeLeave(el) {
            el.style.opacity=1
        },
        leave(el,done) {
            Velocity(el,{opacity:0},{duration:500,complete:done})
        }
    }
})
```

#### 12.6 列表过渡

利用`transiton-group` 可以对v-for渲染的每个元素应用过渡，相当于它给每一个循环元素添加了`transition`组件

范例：给列表添加增加过渡

```html 
<transition-group>
	<div v-for="c in courses" :key="c.name">
        {{c.name}} - ￥{{c.price}}
        <button @click="addToCart(c)"></button>
    </div>
</transition-group>
```



### 十三、必会API盘点

#### 13.1 数据相关API

**Vue.set**

向响应式对象中添加一个属性，并确保这个新属性是响应式的，且触发视图更新

使用方法：`Vue.set(target,propertyName/index,value)`

范例：批量设置商品价格

```html
<template>
    <!--添加批量价格更新-->
    <p>
        <input v-model.number="price" />
        <button @click="batchUpdate">批量更新价格</button>
    </p>

    <div class="course-list" v-else>
        <div v-for="course-list" :key="c.name">
            <!--添加批量价格更新-->
            {{c.name}} - ￥{{c.price}}
        </div>
    </div>
</template>

<script>
    function getCourses(){
        return new Promise(resolve=>{
            setTimeout(()=>{
                resolve({name:'web全栈'},{name:'web高级'})
            },2000)
        })
    }

    const app = new Vue({
        data(){
            return {
                price:0 // 增加价格数据
            }
        },
        async created() {
            const courses = await getCourses()
            this.courses = courses
        },
        methods:{
            // 添加批量价格更新方法
            batchUpdate() {
                this.course.forEach(c=>{
                    c.price = this.price // no ok
                    Vue.set(c,'price',this.price) // ok
                })
            }
        }
    })
</script>
```

**Vue.delete**

删除对象的属性，如果对象是响应式的，确保删除能删除视图。

使用方法：`Vue.delete(target,property/index)`

#### 13.2 事件相关API

**vm.$on**

监听当前实例上的自定义事件。事件可以由`vm.$emit`触发，回调函数会接收所有传入事件触发函数的额外参数

```js
vm.$on('test',function(msg){
    console.log(msg)
})
```

**vm.$emit**

触发当前实例上的事件，附加参数都会传给监听器回调

```js
vm.$emit('test','hi')
```

典型应用：事件总线

通过在Vue原型上添加一个Vue实例作为事件总线，实现组件间相互通信，而且不受组件间关系的影响

```js
vm.prototype.$bus = new Vue();
```

> 这样做可以在任意组件中使用`this.$bus`访问到该Vue实例

注意：组件事件的派发者也就事件的监听者。通过`this.$bus`我们可以进行跨级别组件通信，但前提他们需要在同一个页面组价中

**vm.$once**

监听一个自定义事件，但是只触发一次，一旦触发之后，监听就会被移除

```js
vm.$once('test',function(msg){
    console.log(msg)
})
```

**vm.$off**

移除自定义事件监听器

* 如果没有提供参数，则移除所有的事件监听器
* 如果只提供了事件，则移除该事件所有的监听器
* 如果同时提供了事件与回调，则只移除这个回调的监听器

``` js
vm.$off() // 移除所有事件监听器
vm.$off('test') // 移除该事件所有的监听器
vm.$off('test',callback) // 只移除这个回调的监听器
```

#### 13.3 组件或元素引用

**ref和vm.$refs**

详细看上方阐述

### 扩展

#### 1. property 和 attribute

* property是指：js中的对象属性
* attribute是指：html标签的属性，比如`<input value='hello'>`的value属性

他们两者是毫无关系的，比如你直接使用js修改dom的属性值，也只是修改了property的值，而attribute的值是不变的。例如：

`<input value='hello'>`的value属性你通过`input.value='world'`修改你会发现标签上的value属性值并不会改变，而它的视图却改变了，若你要修改标签上的value值就得通过`input.setAttribute('value','xxxx')`来修改

