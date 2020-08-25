---
title: vue-router
date: 2020-08-24 12:13:10
categories: Vue
top: false
summary:  Vue基础
tags:  Vue
---

### vue-router

> 使用vue-router 的时候，需要重点注意：当从一个路由切换到另一个路由的时候，两个路由名是一样的，相当于自己切换自己，这个可以用`router.push()`能够测试出来，但在守卫路由钩子上小编测试不出来，但它们都是不会在进行切换的。这一点在开发的时候要注意，但也符合我们人的思想，本身就是那个页面在进行切换就没必要，这时我们就在判断一下来源，若跳转同源的话就不让跳转。

路由渲染的匹配规则是：遍历路由对象的 routes 节点下的所有路由规则节点，直到匹配到符合规则的路由进行获取与渲染

### 一、路由

当应用变得复杂以后，我们就需要通过一种便捷、高效的方式来管理应用，最常见的就是通过路由

路由：把 url 与应用中的对应的组件进行关联，通过不同的 url 访问不同的组件

* vue-router 安装

```bash
npm i vue-router 
// or 
yarn add vue-router 
```

* 注册路由

通过 `Vue.use()`方法，把 vue-router 注册到 Vue实例中去

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
```

* 创建路由对象

通过 `vue-router` 提供的 Router 构造函数(类) 创建路由对象，路由对象包含了当前使用的模式(hash、history)、路由信息(url 与 组件对应的关系) 等信息

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from './views/Home.vue'
import About from './views/About.vue'

Vue.use(VueRouter) // 调用 Vue.use 进行路由注册
const router = new VueRouter({
    mode:'history', // 路由模式
    // 路由规则
    routes:[
        {
            path:'/',
            component:Home
        },
        {
            path:'/about',
            component:About
        }
    ]
})
// 导出路由
export default router  
// --------------main.js---------------
import router from './router'
new Vue({
    ....,
    router  //件路由注入到 vue 实例中去
})
```

### 二、router-view/link 组件

配置路由信息以后，我们还需要在页面（组件）中指定路由对应的组件出现的位置，当前访问 url 与 某个路由信息匹配的时候，该组件就会出现在 `router-view`组件所在的位置，而且`router-link`组件相当于我们的`a`标签，并且它会被vue解析成一个被阻止默认行为的a标签

```html
// App.vue
<template>
    <div id="app">
        <div id="nav">
            <router-link to="/">Home</router-link> |
            <router-link to="/about">About</router-link>
        </div>
        <hr>
        <router-view/>
    </div>
</template>
```

### 三、异步请求

在实际的应用开发中，与后端交互，进行异步请求是很常见的需求，我们这里使用 axios 来进行前后端的数据交互

* 安装axios

```bash
npm i axios
```

* 发送请求

```html
// home.vue
<template>
    <div class="home">
        Home
</div>
</template>

<script>
        import axios from 'axios';
export default {
    name: 'home',
    data() {
        return {
            items: []
        }
    },
    created() {
        axios({
            url: 'http://localhost:7777/items'
        }).then(res => {
            this.items = res.data;
        });
    }
}
</script>
```

* 跨域的解决

vue-cli 提供一个内置基于`node`的 webserver 我们可以使用它提供的 proxy（代理） 服务器来进行跨域请求代理

首先要配置 **vue.config.js**文件：在项目的根目录下创建一个`vue.config.js`的文件，通过`npm run serve` 重新启动服务的时候，会自动读取该文件，这种方法只能用于开发阶段，上线后这个代理服务器将不再存在，因为上线的代码我们不需要vue-cli ，那个时候后端可以使用其他手段解决

跨域请求代理配置

```js
// vue.config.js
module.exports = {
    devServer: {
        proxy: {
            // '/api' 请求是以`/api`开头的会被自动代理转发
            '/api': {
                // 代理转发的目标服务器地址
                target: 'http://localhost:7777',
                // 重写请求路径。这里是将 'http://localhost:7777/api/items' -> 'http://localhost:7777/items'
                pathRewrite: {
                    '^/api': ''
                }
            }
        }
    }
}
```

项目中使用

```html
// home.vue
<script>
    ...
    created() {
        axios({
            url: '/api/items'
        }).then(res => {
            this.items = res.data;
        });
    }
    ...
</script>
```

### 四、vue-devTools

为了更方便的在开发过程中对 vue 程序进行调试，出来传统的浏览器自带的 debug 工具以外，我们还可以通过一些专门为 vue通过的扩展插件来进行调试

参考：https://github.com/vuejs/vue-devtools

Vue.js-devtools，[点击下载](./assets/Vue.js-devtools_v3.1.6.crx)

### 五、动态路由

有的时候，我们需要把满足某种规划的路由全部匹配到同一个组件，比如不同的商品的url

```html
/item/1
/item/2
/item/3
...
```

我们不肯为每一个商品都定义一个独立的组件，而是把它们都映射到同一个组件，同时 url 后面的部分为动态变化的部分，我们会在设计路由的时候进行特殊处理

```js
...
{
    path: '/item/:itemId',
    name: 'item',
    component: Item
}
    ...
```

其中`:itemId`表示匹配的 url 中的动态部分内容，如上面的 1，2,3等，同时该值将被赋值给路由的变量 `itemId`,这个值会在路由对象上面($route.params)

```html
// home.vue
<template>
    <div class="home">
        <h2>商品列表</h2>
        <ul class="item-list">
            <li class="head">
                <span>名称</span>
                <span>价格</span>
                <span>操作</span>
            </li>
            <li v-for="item of items" :key="item.id">
                <span>
                    <router-link :to='{name: "item", params:{itemId: item.id}}'>{{item.name}}</router-link>
                </span>
                <span>{{item.price|RMB}}</span>
                <span>
                    <button>添加到购物车</button>
                </span>
            </li>
        </ul>
    </div>
</template>

<script>
    import axios from 'axios';
    import {RMB} from '@/filters/RMB';
    export default {
        name: 'home',
        data() {
            return {
                items: []
            }
        },
        filters: {
            RMB
        },
        created() {
            axios({
                url: '/api/items'
            }).then(res => {
                this.items = res.data;
            });
        }
    }
</script>
<style>
    ul {
        margin: 0;
        padding: 0;
    }

    li {
        list-style: none;
    }

    .item-list li {
        padding: 10px;
        display: flex;
        justify-content: space-between;
        height: 30px;
        line-height: 30px;
        border-bottom: 1px dotted #333;
    }
    .item-list li.head {
        font-weight: bold;
    }
    .item-list li span {
        min-width: 200px;
    }
</style>
```

### 六、路由对象

vue-router 会在组件中添加(注入)两个属性

* `$router` : 该对象其实就是 new VueRouter(....) 得到的路由对象，通过该对象,外面就可以访问全局路由信息，调用路由下的方法，比如：`go`、`back`、`push`等
* `$route`：通过该对象可以访问与当前路由匹配的路由信息，在`$route.params`可以获取到动态路由有关的信息

```html
// item.vue
<template>
    <div>
        <template v-if="item">
            <h2>商品详情 - {{item.name}}</h2>
            <dt>ID</dt>
            <dd>{{item.id}}</dd>
            <dt>名称</dt>
            <dd>{{item.name}}</dd>
            <dt>价格</dt>
            <dd>{{item.price|RMB}}</dd>
        </template>
        <template v-else>
            <h2>没有该商品信息</h2>
        </template>
    </div>
</template>
<script>
    import axios from 'axios';
    import {RMB} from '@/filters/RMB';
    export default {
        name: 'item',
        data() {
            return {
                item: null
            }
        },
        filters: {
            RMB
        },
        created() {
            let itemId = Number(this.$route.params.itemId); // 得到动态路由传来的数据
            if (itemId) {
                axios({
                    url: `/api/item/${itemId}`
                }).then(res => {
                    this.item = res.data;
                }).catch(err=>{});
            }
        }
    }
</script>
```

### 七、queryString

有的时候，我们可能也会用到queryString，例如：我们在排序的时候，默认是从高到底，之后我们选择了低到高，并且想给好友分享，而在分享给好友后，好友也只会看到高到低的排序顺序，这是因为：当路由切换使用的是同一个组件的时候，则会复用该组件，而不是销毁重建，这说明我们排序操作属于组件内部操作，它并没有销毁重建只是数据进行更新，并且我们分享给好友的地址也并无特殊标识，所以好友访问网页的时候是默认的创建高到低排序顺序的组件。为了解决这里我们组件和分享给好友展示的数据不一样的问题，我们可以采用 queryString 给路由添加参数的方式来区分当前呈现的信息。说白了就是往路由上添加`xxx/?name=xxx`（？号后面的参数）

流程：

* 使用 ` this.$router.push({name:'路由名称',query:{}(要携带过去的参数))`
* 通过watch监听`$route`当前路由变化，触发重新渲染
* 通过`this.$route.query.xxx`获取到编程式导航携带过来的参数

如果切换的路由复用了组件，这个时候，我们可以使用 <u>watch</u> 监听 <u>$route</u>

```js
watch: {
    $route(to, from) {
        console.log('$route');
    }
}
```

* to : 改变之后的 $route 对象
* from： 改变之前的 $route 对象

编程式导航

使用` this.$router.push({name:'路由名称',query:{}(要携带过去的参数))`进行不是点击链接形式的导航(跳转)

实例：

```html
<template>

    <div>
        <h2>商品列表</h2>

        <select :value="sort" @change="goto">
            <option value="desc">从高到低</option>
            <option value="asc">从低到高</option>
        </select>

        <ul class="item-list">
            <li class="head">
                <span>名称</span>
                <span>价格</span>
                <span>操作</span>
            </li>
            <li v-for="item of items" :key="item.id">
                <span>
                    <router-link :to='{name: "Item", params:{itemId: item.id}}'>{{item.name}}</router-link>
                </span>
                <span>{{item.price|RMB}}</span>
                <span>
                    <button>添加到购物车</button>
                </span>
            </li>
        </ul>
    </div>
</template>

<script>
    import RMB from '@/filters/RMB.js'
    // 如果路由变化使用是同一个组件的话，那么这个时候这个组件是不会销毁重新渲染的，而是会复用
    export default {
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
        created() {
            this.getItem()
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
            $route() {
                this.getItem()
            }
        }
    }
</script>

<style>
    ul {
        margin: 0;
        padding: 0;
    }

    li {
        list-style: none;
    }

    .item-list li {
        padding: 10px;
        display: flex;
        justify-content: space-between;
        height: 30px;
        line-height: 30px;
        border-bottom: 1px dotted #333;
    }
    .item-list li.head {
        font-weight: bold;
    }
    .item-list li span {
        min-width: 200px;
    }
</style>
```

### 七、路由守卫

当导航发生改变的时候，vue-router 会在多个不同的地方调用指定的函数，也就是与路由有关的生命周期函数，也称为**路由守卫**，路由守卫可分为：

* 组件内守卫
* 路由独享守卫
* 全局守卫

路由守卫参数

* to：即将要进入的目标路由对象（$route）
* from : 当前导航正要离开的路由对象 （$route）
* next：路由确认回调函数，类似 Promise 中的 resolve 函数，一定要确保调用 next函数，因为后续的导航行为将依赖next 方法的回调参数
  * netx() : 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 `confirmed`(确认的)
  * next(false)： 中断当前的导航，如果浏览器的 URL 改变了(可能是用户手动或者浏览器后退按钮)，那么URL 地址会重置到`from`路由对应的地址
  * next('/') 或者 next({path:'/'}) : 跳转到一个不同的地址，当前的导航被中断，然后进行一个新的导航

#### 7.1 组件内守卫

 定义在组件内的与路由有关的生命周期函数（守卫）

* beforeRouteEnter

当路由解析完成，并指定的组件渲染之前（组件`beforeCreate`之前）进行调用，这里不能通过`this`访问组件实例，需要通过`next`回调进行调用

```js
beforeRouteEnter(to,from,next) {
    // 这里不能使用组件实例子 (this）因为当守卫执行时，组件实例还未被创建，但next中可以
    next(vm=>{
        // 而这里的vm这是当前组件实例对象，我们可以这里对其进行操作，例如挂载数据
        .... 
    })
}
```

* beforeRouteUpdate

在当前路由改变，但是该组件被复用时调用

```js
beforeRouteUpdate(to,from,next) {
    // 可以访问组件实例 this
}
```

* beforeRouteLeave

导航离开该组件的对应路由时调用

```js
beforeRouteLeave(to,from,next) {
    // 可以访问组件实力 this
}
```

#### 7.2 路由独享守卫

可以在路由配置上直接定义`beforeEnter`守卫，相对来说，应用不多

```js
const router = new VueRouter({
    routes:[
        {
            path:'/foo',
            component:Foo,
            beforeEnter:(to,from,next) => {
                //....
            }
        }
    ]
})
```

#### 7.3 全局守卫

全局守卫是注册在 router 对象(new VueRouter(....))上的，有下面几个钩子函数

* beforeEach

当一个导航触发时，全局前置守卫按照创建顺序调用

```js
router.beforeEach((to,from,next) => {
    //....
})
```

* beforeResolve

在所有组件内守卫和异步路由组件被解析之后被调用

```js
router.beforeResolve((to,fron,next)=>{
    // ....
})
```

* afterEach

导航被确认后调用

```js
router.afterEach((to,from)=>{
    //....
})
```

> 因为导航已经被确认，所以没有 **next**

### 八、嵌套路由

一些比较复杂的应用会有多层嵌套的路由和组件组成，比如：在应用增加一个用户个人中心，用户个人中心又是由多个页面组成，如

![嵌套路由](/medias/imges/vue/basic/vue-router-usercenter.png )

**添加路由与子路由**

在添加子路由path时：如果当前这个路由是 /user 的子路由，比如，访问的地址我们想设计成：/user/profile，就不要以 / 开头 ，直接填写 /user/ 后面的内容 `path:'profile '`即可

```js
...
import User from './views/User.vue'
import Profile from './views/User/Profile'
import Cart from './views/User/Cart.vue'
    ...

        ...
    {
        path: '/user',
        component: User,
        children: [
            {
                path: '',
                name: 'user',
                component: Profile
            },
            {
                path: 'cart',
                name: 'user-cart',
                component: Cart
            }
        ]
    }
        ...
```

children 属性：一个路由中的`children`表示嵌套的子路由

* 子路由的`path`:如果以`/`开头表示根路径，不再基于父级路径，否则基于父级`path`
* 如果一个子路由的`path`为空，表示为默认子路由
* 如果一个路由有默认子路由，则父级`name`属性需要设置给这个默认子路由，父级不需要 name节点

**添加子路由视图：**

有了子路由以后，页面还不能呈现路由对应的组件，我们还需要在视图组件设置`router-view`

```html
// User.vue
<template>
    <div>
        <h3>用户中心</h3>
        <ul class="left">
            <router-link exact tag="li" :to="{name: 'user'}">基本信息</router-link>
            <router-link tag="li" :to="{name: 'user-cart'}">我的购物车</router-link>
        </ul>
        <div class="right">
            <router-view></router-view>
        </div>
    </div>
</template>

<style scoped>
    .left {
        float: left;
        width: 200px;
    }
    .left li {
        line-height: 30px;
        cursor: pointer;
    }
</style>
```

### 九、重定向与别名

#### 9.1 重定向

有的时候，我们会根据某个需求对用户请求的页面进行重新定位

案例：现有一小说网站，提供了男生频道 和 女生频道 的两个入口，用户首次进入页面的时候，会出现选择，并记住用户的选择，以后该用户进入网站直接根据记录的选择进入对应频道

组件

```html
// BookChoose.vue
<template>
    <div>
        <router-link :to="{name: 'book-boy'}">男生</router-link>
        <span> | </span>
        <router-link :to="{name: 'book-girl'}">女生</router-link>
    </div>
</template>
```

```html
// BookBoy.vue
<template>
    <div>
        BookBoy
    </div>
</template>

<script>
    export default {
        name: 'BookBoy',
        created() {
            localStorage.setItem('book-type', 'book-boy');
        }
    }
</script>
```

```html
// BookGirl.vue
<template>
    <div>
        BookGirl
    </div>
</template>

<script>
    export default {
        name: 'BookGirl',
        created() {
            localStorage.setItem('book-type', 'book-girl');
        }
    }
</script>
```

路由配置

可以直接返回一个对象，`redirect:{name:'路由名字'}`，也可以写成一个函数的形式处理一些逻辑，例如下面的形式

```js
{
  path: '/book',
  name: 'book',
  // redirect: { name: 'book-choose' }
  redirect: to => {
    let type = localStorage.getItem('book-type')
    return { name: type || 'book-choose' }
  }
},
{
  path: '/book-choose',
  name: 'book-choose',
  component: BookChoose
},
{
  path: '/book-boy',
  name: 'book-boy',
  component: BookBoy
},
{
  path: '/book-girl',
  name: 'book-girl',
  component: BookGirl
}
```

#### 9.2 别名

重定向，是从一个路由切换到另外一个路由，而别名是不同的路由显示同一个页面，比如：`/user`是用户中心的路由，`/member`，我们也可以给这个页面定义另一个路由，虽然在某些时候，重定向与别名有类似的效果，但是，别名不存在跳转，浏览器地址栏上显示的 URL 并不会切换，别名使用**`alias:xxx`**属性

```js
{ 
    path: '/user',
    alias: '/member'
    component: User, 
}
```

#### 9.3 404页面

在路由对象规则`routes`节点上的最后一个位置填写

```js
{
    path: '*',
    component: NotFound
}
```

### 十、路由组件传参

我们通过把路由直接映射（绑定）的组件成为`路由组件`,也至于路由组件才能直接调用路由有关对象`$router`、`$route`

当我们一个组件即希望作为路由组件使用，又可能作为功能组件（某个页面中的一部分）去使用，这个时候传参会出现一些问题，因为作为功能性组件时，参数可以通过props接收，而作为路由组件时，参数可以通过 $route.params.xxx节点来接收，两者接收方式不同，会出现两难的问题。这时我们可以通过路由配置来进行将两种方式进行融合。

案例：我们对 item.vue 组件进行改造，但我们在 home.vue 的商品列表上移入移出，出现商品信息提示框

![路由组件传参](/medias/imges/vue/basic/item-tip-layer.png )

```html
// Home.vue
<template>
	...
  <li v-for="item of items" :key="item.id">
    <span>
      <router-link @mouseover.native="mouseover(item.id, $event)" @mouseout.native="mouseout(item.id, $event)" :to='{name: "item", params:{itemId: item.id}}'>{{item.name}}</router-link>
    </span>
    <span>{{item.price|RMB}}</span>
    <span>
      <button>添加到购物车</button>
    </span>
  </li>
	...
	<div class="tip" :style="{left: tip.left, top: tip.top}" v-show="tip.isShow">
    <Item :itemId="tip.itemId"></Item>
  </div>
	...
</template>
<script>
...
export default {
  ...,
  data() {
    return {
      items: [],
      tip: {
        itemId: 0,
        isShow: false,
        left: 0,
        top: 0
      }
    }
  },
  ...
  methods: {
    ...,
    mouseover(itemId, e) {
      let pos = e.target.getBoundingClientRect();
      this.tip.itemId = itemId;
      this.tip.left = pos.left + pos.width + 10 + 'px';
      this.tip.top = pos.top + 'px';
      this.tip.isShow = true;
    },
    mouseout(itemId, e) {
      this.tip.isShow = false;
    }
  }
}
</script>
<style>
...
.tip {
  position: fixed;
  left: 0;
  top: 0;
  border: 1px solid #000;
  background: #fff;
  padding: 10px;
}
</style>
```

因为用来的 `Item.vue`组件通过`this.$route.params.itemId`来接收 timeId 的，但是作为功能性组件`itemId`需要通过`prop` 来传入，这个时候，我们需要对`Item.vue`组件进行改造

```html
<template>
    <div>
        <template v-if="item">
            <h2>商品详情 - {{item.name}}</h2>
            <dt>ID</dt>
            <dd>{{item.id}}</dd>
            <dt>名称</dt>
            <dd>{{item.name}}</dd>
            <dt>价格</dt>
            <dd>{{item.price|RMB}}</dd>
        </template>
        <template v-else>
            <h2>没有该商品信息</h2>
        </template>
    </div>
</template>
<script>
import axios from 'axios';
import {RMB} from '@/filters/RMB';
export default {
    name: 'item',
    props: ['itemId'],
    data() {
        return {
            item: null
        }
    },
    filters: {
        RMB
    },
    watch: {
        itemId() {
            this.getItem();
        }
    },
    created() {
        // let itemId = Number(this.$route.params.itemId);
        this.getItem();
    },
    methods: {
        getItem() {
            if (this.itemId) {
                axios({
                    url: `/api/item/${this.itemId}`
                }).then(res => {
                    this.item = res.data;
                }).catch(err=>{});
            }
        }
    }
}
</script>
```

但是这个时候，我们的`Item.vue`可以接收来之 props 的参数，却不可以处理来自路由的 params 参数了。 为了能让 Item.vue 组件既能接收 props 传递的值，也能接收 route.params传递过来的参数，需要对路由 也进行一些改造，也就是在路由上添加`props`属性

```js
{
  path: '/item/:itemId',
  name: 'item',
  component: Item,
  props: true // 默认处理
}
```

props的设置有三种方式

* 默认处理

当 props 设置为true ，那么 route.params 中的数据（route上的路由参数）自动就会被设置为组件属性与组件原有的 props进行合并

* 对象模式

我们也可以有选择的返回 props，但这样路由组件（对应的功能组件）接受的参数只有对象上的值，所以我们在使用这种模式的时候，一定要将我们需要的值写在对象上

```js
{
  path: '/item/:itemId',
  name: 'item',
  component: Item,
  props: {a: 1, b: 2}
}
```

* 回调函数模式

也可以使用回调函数模式，处理一些复杂传参的问题，这里的 r === $route（当前路由对象）

```js
{
  path: '/item/:itemId',
  name: 'item',
  component: Item,
  props: r => ({ itemId: Number(r.params.itemId) })
}
```

**不管哪种方式，记住一定要显示的组件上props上进行接收对应的参数名**

### 十一、路由动效

利用 `transition`组件，我们还可以给路由导航加上动效，而在根组件上加，则是为所有路由添加动效

```html
// App.vue
<template>
    ...
    <transition name="fade">
        <router-view/>
    </transition>
    ...
</template>
<style>
    .fade-enter-active {
        transition: opacity .5s;
    }
    .fade-leave-active {
        transition: none;
    }
    .fade-enter, .fade-leave-to {
        opacity: 0;
    }
</style>
```

### 十二、滚动行为

前端路由并没有重载整个浏览器，只是通过 DOM 进行了局部更新，所以，有的时候，浏览器的行为状态会被保持，比如：滚动条，当我们在一个页面组件中滚动滚动条，然后跳转到另一个页面组件，滚动条会保持在上一个页面组件位置上，我们其实更希望滚动条能会到页面顶部，就像重载了整个页面一样，那么我们需要在创建路由对象时添加`scrollBehavior`节点配置

```js
const router = new VueRouter({
  routes: [...],
  scrollBehavior: () => ({ y: 0 })
});
```

而正对后退/前进行为，会提供一个`savedPosition`参数，通过该参数返回历史记录中的滚动值,可以将上面进行改造

```js
scrollBehavior (to, from, savedPosition) {
  // console.log(savedPosition)
  if (savedPosition) {
    return savedPosition
  } else {
    return { x: 0, y: 0 }
  }
}
```

### 十三、路由元信息

定义路由的时候可以配置`meta`字段，这就像我们的 data-xxx 自定义属性一样，给路由添加自定义属性，这样我们就可以通过获取`meta`定义的值来进行**验证路由**

* 将涉及到的路由全加上`meta`属性，则在使用时需要在`meta`上接收对应的参数

```js
const router = new Router({
    routes: [
        {
            path: '/user',
            component: User,
            meta: { requiresAuth: true }
            children: [
                {
                    path: '',
                    name: 'user',
                    component: Profile,
                    meta: { requiresAuth: true }
                },
                {
                    path: 'cart',
                    name: 'user-cart',
                    component: Cart,
                    meta: { requiresAuth: true }
                }
            ]
        }
    ]
})

const isLogin = true;

router.beforeEach((to, from, next) => {
    NProgress.start()

    if (to.meta.requiresAuth && !isLogin) {
        next({
            name: 'login',
        })
    } else {
        next()
    }

})
```

* 或者只在父路由加上`meta`属性，子路由不加，则在使用时需要在`matched`上接收对应的参数

```js
const router = new Router({
    routes: [
        {
            path: '/user',
            component: User,
            meta: { requiresAuth: true }
            children: [
            {
            path: '',
            name: 'user',
            component: Profile
        },
        {
            path: 'cart',
            name: 'user-cart',
            component: Cart
        }
    ]
}
                          ]
                          })

const isLogin = true;

router.beforeEach((to, from, next) => {
    NProgress.start()

    if (to.matched.some(record => record.meta.requiresAuth) && !isLogin) {
        next({
            name: 'login',
        })
    } else {
        next()
    }

})
```

### 十四、路由懒加载

当使用 vue-cli 打包构建应用时，JavaScript 包会变得非常大，这是因为打包将我们所有的路由信息整合到一个 JS 文件上，这会导致我们上线应用首屏加载慢的问题。非常影响页面加载。如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就更加高效，运用的技术即为：路由懒加载，其实也就将组件通过 `import(对应的组件路径)`方式导入

```js
import Vue from 'vue';
import VueRouter from 'vue-router';
import NProgress from 'nprogress'
import 'nprogress/nprogress.css';
// import Home from '@/views/Home';
// import About from '@/views/About';
// import Item from '@/views/Item'
const Home =
    import ('@/views/Home');
const About =
    import ('@/views/About');
const Item =
    import ('@/views/Item');
import User from '@/views/User'

import Cart from '@/views/User/Cart'
import Profile from '@/views/User/Profile'

import Book from '@/views/Book'
import BookBoy from '@/views/Book/Boy'
import BookGril from '@/views/Book/Gril'

import BookChoose from '@/views/BookChoose'

import NotFound from '@/views/NotFound'

import Login from '@/views/Login'

// 调用 Vue.use 进行插件注册
Vue.use(VueRouter);

// 创建具体的路由对象，这个路由对象就包含了当前应用中的路由信息，比如：路由使用的模式，路由的url与组件的对应关系
const router = new VueRouter({
    // 路由模式
    mode: 'history',
    routes: [{
            path: '/',
            name: 'Home',
            component: () => Home
        },
        {
            path: '/item/:itemId(\\d+)',
            name: 'Item',
            component: () => Item,     
            props: (r) => {
                console.log(r)
                return { itemId: Number(r.params.itemId) }
            }
        },
        {
            path: '/about',
            name: 'About',
            component: () => About
        },
        {
            path: '/User',
            // name: 'User',
            alias: '/member',
            component: User,
            meta: { requiresAuth: true },
            children: [{
                    // 如果当前这个路由 是以 /User 子路由，比如 访问的地址我们想设计成： /User/Profile 就不要以/ 开头 ，直接填写 /User/ 后面的内容
                    path: '', // path为空 则为默认子路由，直接绑定到父级path上
                    name: 'UserProfile',
                    component: Profile
                },
                {
                    path: 'Cart',
                    name: 'UserCart',
                    component: Cart
                },
            ]
        },
        {
            path: '/Book',
            name: 'Book',
            // redirect: { name: 'BookChoose' },
            redirect(to) {
                // return { name: 'BookChoose' }
                let bookType = localStorage.getItem('book-type')
                return bookType ? { name: bookType } : {
                    name: 'BookChoose'
                }
            },
            component: Book,
            children: [{
                    path: 'Boy',
                    name: 'BookBoy',
                    component: BookBoy
                },
                {
                    path: 'Gril',
                    name: 'BookGril',
                    component: BookGril
                }
            ]
        },
        {
            path: '/BookChoose',
            name: 'BookChoose',
            component: BookChoose
        },
        {
            path: '/login',
            name: 'Login',
            component: Login
        },
        {
            path: '*',
            name: 'NotFound',
            component: NotFound
        }
    ]
});

let isLogin = false
router.beforeEach((to, from, next) => {
    console.log(to)
    NProgress.start()
    if (to.matched.some(record => record.meta.requiresAuth) && !isLogin) {
        console.log(from)
        if (from.name == 'Login') {
             NProgress.done()
             return
        }

        try {
            console.log('222')
            next({
                name: 'Login',
            })
        } catch (e) {
            console.log(e)
        }

    } else {
        next()
    }
})

router.afterEach((to, from) => {
    NProgress.done()
})

export default router;
```



