---
title: Vuex-kkb
date: 2020-08-25 17:03:31
categories: Vue
top: false
summary:  Vuex
tags: 
 - Vue
---

### Vuex

### 一、为什么要用Vuex?

组件通信的类型:

* 父子通信
* 跨级通信
* 兄弟通信
* 路由视图级通信

![通信类型](/medias/imges/vue/vuex/vue-components.jpg)

### 二、通信解决方案

* `props / $emit` (父子通信) -----> 常规
* `$refs / ref` (父子通信) 

实现跨级通信（传递数据）：Home组件包裹着 Hello组件 ，Hello组件包裹着 Word组件，通过 ref 的属性绑定获取到组件实例，在调用组件挂载的方法，接收参数。例如：Home组件获取Hello组件出传递的数据

1. 在Home组件的Hello 组件标签上绑定 ref 属性，例如`ref="helloRef"`
2. 在Hello组件挂载给 Home组件调用的方法，并将Home需要的数据作为函数返回值
3. 在Home组件上方法内部通过ref获取到 Hello组件实例，并通过调用获取到传递过来的参数

```html
// Home.vue
<template>
  <div class="home">
    <div>
      Home <button @click="fnHome">home按钮</button>
    </div>
    <Hello ref="helloRef"/>
  </div>
</template>

<script>
import Hello from '@/components/Hello.vue'
export default {
  name: 'Home',
  components: {
    Hello
  },
  provide:{
    val:100
  },
  methods:{
    fnHome(e) {
      console.log('home---',e)
      // 通过 ref 与 $refs 方式进行组件间通信
       console.log(this.$refs.helloRef.helloFn())
    }
  }
}
</script>

-----------------------hello.vue----------------------------------------------------
<template>
  <div>
   <div>
      hello 组件 
   </div>
  </div>
</template>

<script>
export default {
  name:'hello',
  },
  methods:{
    helloFn() {
      return 'hello传去的值'
    }
  }
}
</script>
```

* `$children / $parent` （父子通信)

组件通过`this.$children` 可以获取到自身上所有的子组件实例，是一个数组形式。而组件通过`this.$parent`可以获取到它挂载的父组件实例，获取到实例就可以获取到对应的组件实例对象上的属性和方法，从而进行通信

当然，兄弟组件之间通信完全可以通过共同的祖辈搭建桥梁，例如这里的 `child2`传递信息给`child2`

```html
<!--父辈或祖辈在 child1 进行事件监听-->
<script>
	export default {
        name:'child1',
        created(){
            this.$parant.$on('msg-from-child2',function(msg){
                console.log('child2传递过来的值:',msg)
            })
        }
    }
</script>

<!--父辈或祖辈在 child2 事件分发-->
<template>
	<button @click="sendMsg"> 点击触发 </button>
</template>
<script>
	export default {
        name:'child2',
        methods:{
           sendMsg() {
               this.$parent.$emit('msg-from-child2','child1我来了')
           } 
        }
    }
</script>
```

> 注意：事件的**监听和分发**都是同一个组件来进行管理的，不能说监听挂载在一个组件上，分发又是另一个组件来触发。像这里的`$parent`（child1 与 child2 的共同父辈）就是事件的统一管理者。

* `$attrs / $listeners`  （父子通信，跨级通信）

实现跨级通信（传递数据）：Home组件包裹着 Hello组件 ，Hello组件包裹着 Word组件，想要Home组件的值传递给 Word组件

1. 先在 Hello 组件上 以属性的方式进行数据绑定，由于属于非props属性，则会在Hello组件的根标签上显示，例如：`msg="kkb"`
2. 在 Hello 组件内我们可以通过 `this.$attrs`进行上一步的数据获取，而我们要实现跨级数据传递，则需要使用`v-bind="$attrs"`将传过来的数据进行映射到 Word组件上，
3. 在Word的内部同理我们可以通过`this.$attrs`进行数据获取

注意：可以直接使用`v-bind="$attrs" 或 v-on="$listeners"`进行对传入非props的属性或方法进行展开到对应的组件身上

```html
// Home.vue
<template>
    <div class="home">
        <div>
            Home
        </div>
        <Hello msg="kkb"   />
    </div>
</template>

<script>
    import Hello from '@/components/Hello.vue'
    export default {
        name: 'Home',
        components: {
            Hello
        }
    }
</script>

<!-------Hello.vue----------->
<template>
    <div>
        <div>
            hello 组件
        </div>
        <Word v-bind="$attrs"  /> 
    </div>
</template>

<script>
    import Word from '@/components/Word'
    export default {
        name:'hello',
        components:{
            Word,
        },
    }
</script>

<!---Word.vue-->
<template>
    <div>
        <div>
            Word 组件 <button @click="wordClcik">Word按钮</button>
        </div>
    </div>
</template>

<script>
    export default {
        name:'Word',
        methods:{
            wordClcik() {
                // 获取到传递过来的数据
                 console.log(this.$attrs)
            }
        }
    }
</script>
```

使用 $listeners 进行传递事件，在往事件内传入参数实现数据传递，场景：Home组件接收Word组件传递过来的值

1. 在Home 组件上通过`v-on:函数名字="事件处理函数"`进行绑定，例如:`v-on:fnHome="fnHome" `
2. 在Hello 组件上在通过`v-on:函数名字="$listeners.上一步的事件名"`,例如：` <Word  v-on:fnHome="$listeners.fnHome" /> `
3. 在Word 组件上通过 `this.$listeners.函数名字(需要传入的数据)`进行调用Hello组件传递过来的方法，而在Hello组件上定义的方法的第一个参数就可以接收到 Word组件传递过去的参数

```html
<!--Home.vue-->
<template>
    <div class="home">
        <div>
            Home 
        </div>
        <Hello v-on:fnHome="fnHome"   />
    </div>
</template>

<script>
    import Hello from '@/components/Hello.vue'
    export default {
        name: 'Home',
        components: {
            Hello
        },
        methods:{
            fnHome(e) {
                console.log('home---',e)
            }
        }
    }
</script>

<!--Hello.vue-->
<template>
    <div>
        <div>
            hello 组件 
        </div>
        <Word v-on:fnHome="$listeners.fnHome" /> 
    </div>
</template>

<script>
    import Word from '@/components/Word'
    export default {
        name:'hello',
        components:{
            Word
        },
        created() {
            console.log(this.$listeners)
        }
    }
</script>

<!--Word.vue-->
<template>
    <div>
        <div>
            Word 组件 <button @click="wordClcik">Word按钮</button>
        </div>
    </div>
</template>

<script>
    import eventBus from '@/eventBus.js'
    export default {
        name:'Word',
        created() {
            console.log(this.$listeners)
        },
        methods:{
            wordClcik() {
                this.$listeners.fnHome('123')
            }
        }
    }
</script>
```

* `provide / inject` （父子通信，跨级通信）

实现跨级通信（传递数据）：Home组件包裹着 Hello组件 ，Hello组件包裹着 Word组件，实现Home直接往Word组件上传值

1. 在祖级或父级上通过`provide`将需要传入的数组进行注入，例如这里在Home组件进行数据注入
2. 在Word组件的 inject  节点进行接收，并且之后可以通过 this.xxx(inject上定义接收的数据)进行访问

```html
<!--Home.vue-->
<template>
    <div class="home">
        <div>
            Home <button @click="fnHome">home按钮</button>
        </div>
        <Hello ref="helloRef" msg="kkb" v-on:fnHome="fnHome"   />
    </div>
</template>

<script>
    import Hello from '@/components/Hello.vue'
    export default {
        name: 'Home',
        components: {
            Hello
        },
        provide:{
            val:100
        },
    }
</script>
<!--Hello.vue -->
<template>
    <div>
        <div>
            hello 组件
        </div>
        <Word /> 
    </div>
</template>

<script>
    import Word from '@/components/Word'
    export default {
        name:'hello',
        data() {
            return {
                title:'hello-title'
            }
        },
        components:{
            Word,
        }
    }
</script>

<!--Word.vue-->
<template>
    <div>
        <div>
            Word 组件 
        </div>
    </div>
</template>

<script>
    export default {
        name:'Word',
        inject:['val'],
       // 或 自定义名字和设置默认参数
        inject:{
          val:{ // val可以直接定义名字
              from:'val', // 来着哪里
              default:'val11111' // 默认值
          }  
        },
        created() {
            console.log(this.val) // 访问
        }
    }
</script>
```

* `eventBus`（父子通信、跨级通信、兄弟通信）

Home包裹着Hello，Hello包裹着Word以及Lyz组件。

eventBus（事件总线）实际就是借助事件机制来进行通信，这里我们就直接使用Vue本身具有事件机制特点来实现**Word往Lyz组件数据传递**，当然这个方法不只是限制于兄弟组件必须在同一个组件上，也可以是父级兄弟组件与父级子组件进行通信，简而言之，只要出现在同一个视图就可以利用这套方法进行组件间的相互通信

1. 在src目录下创建`eventBus.js`文件

```js
/*
* Vue
*   事件
*       $emit
*       $on
* */

import Vue from 'vue';

export default new Vue();
```

1. 在Word组件进行` eventBus.$emit(自定义事件名,传递的数据)`事件注册
2. 在Lyz组件上进行` eventBus.$on(自定义事件名,回调函数) `事件监听

```html
<!--Hmoe.vue-->
<template>
    <div class="home">
        <div>
            Home
        </div>
        <Hello    />
    </div>
</template>

<!--Hello-->
<template>
    <div>
        <div>
            hello 组件 <button @click="fnClick">hello按钮</button>
        </div>
        <Word /> 
        <hr>
        <Lyz />
    </div>
</template>

<!--Word.vue-->
<template>
    <div>
        <div>
            Word 组件 <button @click="wordClcik">Word按钮</button>
        </div>
    </div>
</template>
<script>
    import eventBus from '@/eventBus.js'
    export default {
        name:'Word',
        methods:{
            wordClcik() {
                // 进行事件注册
                eventBus.$emit('word-data','10000')
            }
        }
    }
</script>

<!--Lyz.vue-->
<template>
    <div>
        <h3>lyz</h3>
    </div>
</template>

<script>
import eventBus from '@/eventBus'
export default {
    name:'Lyz',
    created() {
        // 事件监听 e为传递过来的参数
        eventBus.$on('word-data',e=>{
            console.log('lyz监听执行了',e)
        })
    }
}
</script>
```

* `vuex` （父子通信、跨级通信、兄弟通信、路由视图级别通信）
* `localStorage/sessionStorage` 等基于浏览器客户端存储 （父子通信、跨级通信、兄弟通信、路由视图级别通信）

小结：上面已经有详细的介绍的组件通信方法，它们有一个共同特点就是，需要通信的组件必须是同一个视图呈现的，否则通信就会出错，简而言之这些通信不能做跨页面视图的通信，这就是我们为什么要使用Vuex的原因。

### 三、Vuex是什么？

Vuex是一个专为 Vue.js 应用程序开发的 **状态管理模式**，类似redux

![状态管理模式](/medias/imges/vue/vuex/flow.png)

这种状态管理模式包含;

* State : 状态数据源
* View ： 使用状态数据源的视图
* Actions： 修改更新数据源的操作

这种模式遵循的是 <u>单向数据流模式</u>

### 四、Vuex的工作流程

![Vuex的工作流程](/medias/imges/vue/vuex/vuex-cl.png)

* state : 存储应用状态数据 (React中的state)
* Vue Component ： 消费 State
* Actions : 提交修改 State 的动作 (包括异步行为) （React中的action）
* Mutations ： 唯一更改 State 的位置 (React中的 Reducer)

### 五、安装使用

* 安装

```bash
npm i vuex
// or
yarn add vuex
```

* 引入

1. 通过`<script>`引入

```html
<script src="vue.js"></script>
<script src="vuex.js"></script>
```

2. 通过 `import`引入

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
```

通过Import方式引入，需要手动安装(手动调用`Vue.use(Vuex)`)

* 创建Store并注入到Vue实例中去

**Store**，就是仓库，我们前面提到的 state 就存储在 store中，同时提交动作、修改状态的方法也都由`store`提供和管理

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
let store = new Vuex.store({
    state:{},
    getters:{},
    mutations:{},
    actions:{}
})
export default store

// 在Main.js 将store注入到Vue实例子中去
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
```

> 必须在 `Vue.use(Vuex)` 之后创建 `store` ，通过将 store 注入 Vue实例对象之后，我们就可以在组件实例中使用`this.$store`来访问 store 对象了

### 六、核心概念

*  state
* getters
* mutations
* actions

#### 6.1 state

存储应用状态数据的对象，我们可以通过`this.$store.state.xxx`来访问对应的值

例如：

```js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)

// Vue.Store 创建数据存储仓库，这个仓库提供了各种api来对数据进行操作
export default new Vuex.Store({
    state: {
        title: 'kkb',
        content: 'vuex学习',
    },
    // 类似组件中的 computed
    getters: {
    },
    mutations: {
    },
    actions: {},
    modules: {}
})
```

```html
<template>
    <div class="home">
        <h2>{{title}}</h2>
        <div>{{content}}</div>
    </div>
</template>

<script>
    export default {
        name: 'home',
        data() {
            return {
                title: '',
                content: ''
            }
        },
        created() {
            this.title = this.$store.state.title
            this.content = this.$store.state.content
        }
    }
</script>
```

当然这种直接赋值的方法，在store的state中的数据改变时，并不会更新视图，这时我们可以通过`computed`计算属性进行数据监听，可以改造为

```html
<template>
    <div class="home">
        <h2>{{title}}</h2>
        <div>{{content}}</div>
    </div>
</template>

<script>
    export default {
        name: 'home',
        computed: {
            title() {
                return this.$store.state.title
            },
            content() {
                return this.$store.state.content
            }
        }
    }
</script>
```

当一个组件需要获取多个状态的时候，将这些状态都声明为计算属性会有些重复和冗余，为了解决这个问题，我们可以使用`mapState`（Vuex给我们提供辅助函数）帮助我们生成计算属性，让我们少写点代码，其实就是我们把 store 的 state 通过`mapState`函数映射到组件的`computed`上，而映射方式可分为数组直接映射，对象映射，**其实mapState 函数返回的是一个对象**，使用时要通过vuex导入

```js
import {mapState} from 'vuex'
```

* 数组直接映射

```html
<template>
    <div class="home">
        <h2>{{title}}</h2>
        <div>{{content}}</div>
    </div>
</template>

<script>
    import {mapState} from 'vuex'

    export default {
        name: 'home',
        computed: mapState([
            'title','content'
        ])
    }
</script>
```

* 对象映射

```html
<template>
    <div class="home">
        <h1>{{title}}</h1>
        <h2>{{subTitle}}</h2>
        <div>{{content}}</div>
    </div>
</template>

<script>
    import {mapState} from 'vuex'

    export default {
        name: 'home',
        data() {
            return {title: 'Vuex'}
        },
        computed: mapState({
            subTitle: 'title',
            content: ({content}) => content.length <= 12 ? content : content.substring(0, 12) + '......'
        })
    }
</script>
```

也可以使用扩展运算符将组件原本的计算属性进入融合

```html
<template>
    <div class="home">
        <h1>{{title}}</h1>
        <h2>{{subTitle}}</h2>
        <div>{{content}}</div>
    </div>
</template>

<script>
    import {mapState} from 'vuex'

    export default {
        name: 'home',
        data() {
            return {title: 'Vuex'}
        },
        computed:{
            result() {
                return '不知道写什么'
            },
            ...mapState({
            subTitle: 'title',
            content: ({content}) => content.length <= 12 ? content : content.substring(0, 12) + '......'
        })
        }
    }
</script>
```

模块化开发的时

```js
// ....
computed:{
    // 第一个参数 user 是模块名，第二个参数是获取user模块地下的数据 
    ...mapState('user',['info'])
    // 也可
    ...mapState('user',{
        user:state=>state.info
    })
}
// ....
```

#### 6.2 getters

有时候我们需要从 store 中的state 中派生出有些数据，类似组件的`computed`，`store`也提供了一个`getters`对象来处理派生数据

* getters函数 ： 与组件属性一样，我们是通过定义一个函数的形式来返回派生数据的，`getters`函数接收两个参数
  * 第一个参数： `state`对象
  * 第二个参数：`getters`对象（自身）
* 通过属性访问：this.$store.getters.xxx ，这种方式会缓存结果 ，例如下面的`showUsers属性`
* 通过方法访问：通过闭包函数的形式返回一个函数，来实现给 `getters` 函数传参，需要注意的是这种方式不支持结果缓存，例如下面的`showSortUsers属性`
* vuex也提供了getters的辅助函数`mapGetters`，与`mapState`函数类似，通常映射到组件的conputed 上

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
let maxId = 7

// Vue.Store 创建数据存储仓库，这个仓库提供了各种api来对数据进行操作
export default new Vuex.Store({
    state: {
        title: 'kkb',
        content: 'vuex学习',
        userGender: '',
        users: [
            { id: 1, username: 'baogege', gender: '男', checked: false },
            { id: 2, username: 'mt', gender: '男', checked: false },
            { id: 3, username: 'haigege', gender: '男', checked: false },
            { id: 4, username: 'zMouse', gender: '男', checked: false },
            { id: 5, username: 'reci', gender: '女', checked: false },
            { id: 6, username: 'lisi', gender: '女', checked: false }
        ]
    },
    // 类似组件中的 computed
    getters: {
        showUsers({ users, userGender }, e) {
            return '' === userGender ? users : users.filter(user => user.gender === userGender)
        },
        showSortUsers({ users, userGender }) {
            return (sort = 'asc') => {
                return [...users].sort((a, b) => {
                    return 'asc' === sort ? a.id - b.id : b.id - a.id
                })
            }
        }
    },
    mutations: {
    },
    actions: {},
    modules: {}
})
```

```html
<template>
    <div class="home">
        Home ---{{t}} 
        <h1>{{t}}</h1>
        <h1>{{c}}</h1>
        <hr>
        <hr>
        <ul>
            <li v-for="item in showUsers">
                {{item.id}} - {{item.username}}
            </li>
        </ul>

        <hr>
        <ul>
            <li v-for="item in showSortUsers('desc')">
                {{item.id}} - {{item.username}}
            </li>
        </ul> 
    </div>
</template>

<script>
    // @ is an alias to /src
    import {mapGetters} from 'vuex'
    export default {
        name: 'Home',
        data(){
            return {
                t:'',
                c:'',
                s:'123'
            }
        },
        created() {
            console.log(this.$store.state)
            this.t = this.$store.state.title
            this.c = this.$store.state.content
        },
        computed: {
            users() {
                return this.$store.getters.showUsers
            },
            sortUser() {
                return this.$store.getters.showSortUsers('desc')
            }
        }
        // 映射
        // computed: mapGetters(['showUsers','showSortUsers'])
    }
</script>
```

#### 6.3 mutations

更改 Vuex 的 state 中的数据唯一方法是提交 mutation (类似 redux 中的 actiob + reducer)，Vuex中的 mutation 非常类似于事件 ： 每个 mutations 都有一个字符串的事件类型 (type)和一个回调函数(handler)

* 提交

```js
this.$store.commit(type, payload)
// or
this.$store.commit({
    type: ...,
    payload: ...
})
```

type: 要提交的 mutation 回调函数名称（在store的mutations上定义的函数），type为固定的 key

payload：（载荷）提交的额外数据，任意格式

* mutations 函数介绍

mutations 中的函数被`commit`执行的时候（也就是mutations中定义函数时，接收两个形参），接收两个参数

1. 第一个参数：`state`对象
2. 第二个参数：`commit`提交的`payload`

在 mutations 函数中，我们就可以通过 state 对象进行状态数据的修改

* vuex也提供了 mutations 的辅助函数`mapMutations`，与`mapState`函数类似，但其一般把 mutations 节点上的函数映射为组件 methods节点上的函数，这样我们就可以直接通过`this.mutations上的方法`调用
* **mutations函数处理的业务逻辑必须是同步的，因为commit方法没有返回值，也不支持promise**

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
let maxId = 7

// Vue.Store 创建数据存储仓库，这个仓库提供了各种api来对数据进行操作
export default new Vuex.Store({
    state: {
        title: 'kkb',
        content: 'vuex学习',
        userGender: '',
        users: [
            { id: 1, username: 'baogege', gender: '男', checked: false },
            { id: 2, username: 'mt', gender: '男', checked: false },
            { id: 3, username: 'haigege', gender: '男', checked: false },
            { id: 4, username: 'zMouse', gender: '男', checked: false },
            { id: 5, username: 'reci', gender: '女', checked: false },
            { id: 6, username: 'lisi', gender: '女', checked: false }
        ]
    },
    // 类似组件中的 computed
    getters: {
        showUsers({ users, userGender }, e) {
            return '' === userGender ? users : users.filter(user => user.gender === userGender)
        },
        showSortUsers({ users, userGender }) {
            return (sort = 'asc') => {
                return [...users].sort((a, b) => {
                    return 'asc' === sort ? a.id - b.id : b.id - a.id
                })
            }
        }
    },
    mutations: {
        addNewUser(state, { payload }) {
            console.log(payload)
            state.users.push({
                id: ++maxId,
                username: payload,
                gender: '男',
                checked: false
            })
        }
    },
    actions: {},
    modules: {}
})
```

```html
<template>
    <div class="home">
        Home ---{{t}} 
        <h1>{{t}}</h1>
        <h1>{{c}}</h1>

        <hr>
        <div>
            <input type="text" ref="newUserName">
            <button @click="addNewUser">添加新用户</button>
        </div>
        <hr>
        <ul>
            <li v-for="item in users">
                {{item.id}} - {{item.username}}
            </li>
        </ul>

        <hr>
        <ul>
            <li v-for="item in sortUser">
                {{item.id}} - {{item.username}}
            </li>
        </ul>
    </div>
</template>

<script>
    export default {
        name: 'Home',
        data(){
            return {
                t:'',
                c:'',
                s:'123'
            }
        },
        created() {
            console.log('created')
            console.log(this.$store.state)
            this.t = this.$store.state.title
            this.c = this.$store.state.content
        },
        methods:{
            addNewUser() {
                console.log(this.$refs.newUserName.value)
                this.$store.commit({
                    type:'addNewUser',
                    payload:this.$refs.newUserName.value
                })
                this.$refs.newUserName.value =''
            }
        },
        computed: {
            users() {
                return this.$store.getters.showUsers
            },
            sortUser() {
                return this.$store.getters.showSortUsers('desc')
            }
        }
    }
</script>
```

#### 6.4 actions

action 中的函数与 mutations 中的函数类似，但是它主要用来进行异步任务的处理，然后通过提交mutations 函数来修改 state

> 注意：actions 中的函数 不要直接修改 state，而要通过 mutations函数去，让数据流控制得统一完整

* 提交

```js
this.$store.dispatch(type, payload)
// or
this.$store.dispatch({
    type: ...,
    payload: ...
})
```

actions 任务需要通过 dispatch 方法来提交（派发），与 commit 类似

dispatch 方法有返回值，且一定返回一个 promise 对象

* actions 函数

actions 中得函数执行的过程中也接收两个参数

1. 第一个参数： store 对象
2. 第二个参数：dispatch 提交的 payload

我们可以通过 第一个参数 store对象来调用`mutations`函数进行对state数据的更改

* vuex 也提供了actions的辅助函数，与`mapMutations`函数类似，其一般把 actions 节点上的函数映射为组件 methods节点上的函数，这样我们就可以直接通过`this.actions上的方法`调用

store.js

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
let maxId = 7

// Vue.Store 创建数据存储仓库，这个仓库提供了各种api来对数据进行操作
export default new Vuex.Store({
    state: {
        title: 'kkb',
        content: 'vuex学习',
        userGender: '',
        users: [
            { id: 1, username: 'baogege', gender: '男', checked: false },
            { id: 2, username: 'mt', gender: '男', checked: false },
            { id: 3, username: 'haigege', gender: '男', checked: false },
            { id: 4, username: 'zMouse', gender: '男', checked: false },
            { id: 5, username: 'reci', gender: '女', checked: false },
            { id: 6, username: 'lisi', gender: '女', checked: false }
        ]
    },
    // 类似组件中的 computed
    getters: {
        showUsers({ users, userGender }) {
            return '' === userGender ? users : users.filter(user => user.gender === userGender)
        },
        showSortUsers({ users, userGender }) {
            return (sort = 'asc') => {
                return [...users].sort((a, b) => {
                    return 'asc' === sort ? a.id - b.id : b.id - a.id
                })
            }
        }
    },
    mutations: {
        addNewUser(state, payload) {
            console.log('mutations', payload)
            state.users.push({
                id: ++maxId,
                username: payload,
                gender: '男',
                checked: false
            })
        }
    },
    actions: {
        addNewUser({ state, commit }, { payload }) {
            console.log('store', payload)
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    if (state.users.find(user => user.username === payload)) {
                        reject('已经存在该用户')
                    } else {
                        commit('addNewUser', payload)
                        resolve('添加成功')
                    }

                }, 1000)
            })
        }
    },
    modules: {}
})
```

Home.vue

```html
<template>
    <div class="home">
        Home ---{{t}} 
        <h1>{{t}}</h1>
        <h1>{{c}}</h1>
        <hr>
        <div>
            <input type="text" ref="newUserName">
            <button @click="addNewUser">添加新用户</button>
        </div>
        <hr>
        <ul>
            <li v-for="item in users">
                {{item.id}} - {{item.username}}
            </li>
        </ul>
        <hr>
        <ul>
            <li v-for="item in sortUser">
                {{item.id}} - {{item.username}}
            </li>
        </ul>
    </div>
</template>

<script>
    import {mapGetters} from 'vuex'
    export default {
        name: 'Home',
        data(){
            return {
                t:'',
                c:'',
                s:'123'
            }
        },
        created() {
            console.log('created')
            console.log(this.$store.state)
            this.t = this.$store.state.title
            this.c = this.$store.state.content
        },
        methods:{
            async  addNewUser() {
                try {
                    // 调用
                    await this.$store.dispatch({
                        type:'addNewUser',
                        payload:this.$refs.newUserName.value
                    })
                }catch(e) {
                    console.log(e)
                }
                this.$refs.newUserName.value =''
            }
        },
        computed: {
            users() {
                return this.$store.getters.showUsers
            },
            sortUser() {
                return this.$store.getters.showSortUsers('desc')
            }
        }
    }
</script>
```

### 七、Vuex模块化

这个更多的是基于一种代码组织结构上的辅助，让我们代码管理更加有结构，清晰

https://vuex.vuejs.org/zh/guide/modules.html

https://vuex.vuejs.org/zh/guide/structure.html

例如：我们将 上面例子在 store.js上关于用户的信息拆分出来,

1. 要在store.js 的modules节点上将users注入，并取别名
2. 在user.js 建议开启命名密闭空间，防止命名污染，`    namespaced: true`
3. 这样拆分为模块管理之后，我们的调用的方法也要做相对应的改变

store.js

```js
import Vue from 'vue'
import Vuex from 'vuex'
// 导入users 
import users from './users'
Vue.use(Vuex)
// Vue.Store 创建数据存储仓库，这个仓库提供了各种api来对数据进行操作
export default new Vuex.Store({
    state: {
        title: 'kkb',
        content: 'vuex学习',
    },
    // 类似组件中的 computed
    getters: {
    },
    mutations: {
    },
    actions: {
    },
    modules: {
        // 注入
        U: users
    }
})
```

user.js

```js
let maxId = 7
export default {
    namespaced: true,
    state: {
        userGender: '',
        users: [
            { id: 1, username: 'baogege', gender: '男', checked: false },
            { id: 2, username: 'mt', gender: '男', checked: false },
            { id: 3, username: 'haigege', gender: '男', checked: false },
            { id: 4, username: 'zMouse', gender: '男', checked: false },
            { id: 5, username: 'reci', gender: '女', checked: false },
            { id: 6, username: 'lisi', gender: '女', checked: false }
        ]
    },
    // 类似组件中的 computed
    getters: {
        showUsers({ users, userGender }, obj) {
            console.log('obj', obj)
            return '' === userGender ? users : users.filter(user => user.gender === userGender)
        },
        showSortUsers({ users, userGender }) {
            return (sort = 'asc') => {
                return [...users].sort((a, b) => {
                    return 'asc' === sort ? a.id - b.id : b.id - a.id
                })
            }
        }
    },
    mutations: {
        addNewUser(state, payload) {
            console.log('mutations', payload)
            state.users.push({
                id: ++maxId,
                username: payload,
                gender: '男',
                checked: false
            })
        }
    },
    actions: {
        addNewUser({ state, commit }, { payload }) {
            console.log('store', payload)
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    if (state.users.find(user => user.username === payload)) {
                        reject('已经存在该用户')
                    } else {
                        commit('addNewUser', payload)
                        resolve('添加成功')
                    }
                }, 1000)
            })
        }
    },
}
```

Home.vue 相对应的改变

```html
<template>
    <div class="home">
        Home ---{{t}} 
        <h1>{{t}}</h1>
        <h1>{{c}}</h1>
        <hr>
        <div>
            <input type="text" ref="newUserName">
            <button @click="postAddNewUser">添加新用户</button>
        </div>
        <hr>
        <ul>
            <li v-for="item in users">
                {{item.id}} - {{item.username}}
            </li>
        </ul>

        <hr>
        <ul>
            <li v-for="item in sortUser">
                {{item.id}} - {{item.username}}
            </li>
        </ul>
    </div>
</template>

<script>
    import {mapActions} from 'vuex'
    export default {
        name: 'Home',
        data(){
            return {
                t:'',
                c:'',
                s:'123'
            }
        },
        created() {
            console.log('created')
            console.log(this.$store.state)
            this.t = this.$store.state.title
            this.c = this.$store.state.content
        },
        methods:{
            // actions 上的方法需要映射调用，U是模块名
            ...mapActions('U',['addNewUser']),
            async  postAddNewUser() {
            try {
            // 映射后可以直接this.xxx调用
            await this.addNewUser({
            type:'addNewUser',
            payload:this.$refs.newUserName.value
        })
    }catch(e) {
        console.log(e)
    }
    this.$refs.newUserName.value =''
    }
    },
        computed: {
            users() {
                // getters 调用的变化
                return this.$store.getters['U/showUsers']
            },
                sortUser() {
                    // getters 调用的变化
                    return this.$store.getters['U/showSortUsers']('desc')
                }
        }
    }
</script>

```

改变：

1. getters 函数的调用要加上模块别名，改为`this.$store.getters['U/showUsers']`, -> `this.getters[U/xxx]`
2. action 函数的调用要借助于辅助函数`mapActions`进行映射到组件上的 methods节点上，之后我们就可以直接通过`this.xxx`直接调用
3. state 的映射 通过`mapState()`，在上文有讲模块化开发时，如何进行映射。
4. 其他改变我们可以直接从 vuedevTools工具上查看

### 八、严格模式

严格模式下，无论何时发生了状态变更且不是由`mutation`函数引擎的，将会抛出错误，这能保证所有的状态变更都能被调试工具跟踪到，开启严格模式`strict:true`

```js
const store = new Vuex.Stroe({
    // ... 
    strict:true
})
```

### 九、插件

Vuex 的 stroe 接受 `plugins`选项，这个选项暴露出每次`mutation`的钩子，`Vuex`插件就是一个函数，它接受store 作为唯一参数

```js
const myPlugin = store => {
    // 当store 初始化后调用
}
```

注册插件：

```js
// 导入插件文件
import persist from './plugins/persist'
const store = new vuex.Store({
    // ... 
    plugins:[persist]
})
```

范例：实现登录状态持久化，stroe/plugins/persist.js

```js
export default store => {
    // 初始化时从 localStorage 获取数据
    if(localStorage) {
        const user = JSON.parse(localStorage.getItem('user'))
        if(user) {
            store.commit('user/login')
            store.commit('user/setUsername',user.username)
        }
    }
    // 用户状态发生变化时缓存
    // store.subscribe() 这个api 在每次commit提交mutation时会被触发
    store.subscribe((mutation,state)=>{
        if(mutation.type === 'user/login') {
            localStorage.setItem('user',user)
        }else if(mutation.type === 'user/logout') {
            localStorage.removeItem('user')
        }
    })
}
```

