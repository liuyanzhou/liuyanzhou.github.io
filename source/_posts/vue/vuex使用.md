---
title: vuex使用
date: 2020-04-13 18:20:50
img: /medias/imges/vue/vue.jpg
categories: Vuex
top: true
summary: Vuex使用
tags: 
 - Vue
---


###Vuex使用

### 1.什么是vuex

>[官方](https://vuex.vuejs.org/zh/)介绍：Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。Vuex 也集成到 Vue 的官方调试工具 [devtools extension](https://github.com/vuejs/vue-devtools)，提供了诸如零配置的 time-travel 调试、状态快照导入导出等高级调试功能 ，
>
>**说白了就是：**vuex是一个大的容器，对数据集中处理

 ### 2.vuex什么时候使用

* 多个视图依赖于同一状态 
* 来自不同视图的行为需要变更同一状态
* 构建一个中大型单页应用 

### 3.state

* 安装

  ```bash
  yarn add vuex
  ```

* 使用

  1.在src目录下建立`store`目录，且创建`index.js`

  ```js
  import Vue from 'vue'
  import Vuex from 'vuex'
  
  // Vuex 通过 store 选项，提供了一种机制将状态从根组件“注入”到每一个子组件中（需调用 Vue.use(Vuex)）,为了注入每个子组件
  Vue.use(Vuex) 
  
  const store = new Vuex.Store({
      state: {
          count: 10
      },
      mutations: {
          add(state) {
              state.count ++ 
          }
      }
  })
  
  export default  store
  ```

* 将vuex注入根组件[在src目录下的`main.js`]

  ```js
  // 导入
  import store from './store/'
  
  new Vue({
     // 通过在根实例中注册 store 选项，该 store 实例会注入到根组件下的所有子组件中，且子组件能通过 this.$store 访问到
    store,
    render: h => h(App),
  }).$mount('#app')
  ```

* 更新组件状态

  ```js
  const Counter = {
    template: `<div>{{ count }}</div>`,
    computed: {
      count () {
        return this.$store.state.count
      }
    }
  }
  ```

  > 这样我们更新状态的时候借助于`computed`属性来return值，过于繁琐，官方提供了`mapState`来简化

  ```js
  const Counter = {
    template: `<div>{{ count }}</div>`,
    computed: {
        // 当当前组件的data值于vuex的值相同时，谁在computed节点后面就会覆盖前面
        ...mapState({
            // 相当于 count: state => state.count
            count:'count'
        })
    }
  }
  ```

### 4.Getter

> `getter`相当于组件的`computed`属性，监听`vuex`的state值变化，一旦变化便会更新依赖该值的组件

* 设置

  ```js
  const store = new Vuex.Store({
      state: {
          thing:[
              {id:1,nema:'睡觉',done:true},
              {id:2,nema:'吃放',done:false},
              {id:3,nema:'打豆豆',done:false},
              {id:4,nema:'说话',done:false}
          ]
      },
      getters:{
          number(state) {
              return state.thing.filter(item => item.done === false).length
          },
          //Getter 也可以接受其他 getter 作为第二个参数：
          doneTodosCount: (state, getters) => {
          return getters.number.length
        }
      }
  })
  ```

* 使用

  ```js
  import { mapGetters } from "vuex";
  
  export default {
    computed: {
        // 和`mapState使用一样`
      ...mapGetters({
        number: "number"
      })
    }
  };
  ```

### 5.Mutation

> 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件 [method]
>
> vuex是不允许直接更改`data`中的数据，必须要以相应的 type 调用 **store.commit** 方法，而且`mutation `中的代码只能是`同步代码`，不能出现异步代码，因为这样在Vue-devtool  调试工具工追踪不到数据变化，虽然界面效果不改变，但官方不推荐这么做

```js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
      // 第一个参数是state 第二个参数是payload[组件commit该函数时提交的参数]
    increment (state,payload) {
      // 变更状态
      state.count++
       console.log(payload)
    }
  }
})
```

#### 在组件中更改

* 1.使用`this.$store.commit`来更改

  ```js
  store.commit({
    // 类型
    type: 'add',
    // 传参
    amount: 10
  })
  ```

  当使用上面对象风格的提交方式，整个对象都作为载荷传给 mutation 函数，add函数要这样写

  ```js
  mutations: {
    add (state, payload) {
      state.count += payload.amount
    }
  }
  ```

* 2.使用mapMutations来更改data更方便

  ```js
  import { mapMutations } from 'vuex'
  
  export default {
    // ...
    methods: {
      ...mapMutations({
        add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
      })
    }
  }
  ```


### 6.Action

> Action里可以写异步函数的处理逻辑，并且在vue的调式工具能够追踪到。所以当要写异步逻辑时，那么就写道这个节点下！！！而它修改store的逻辑是，通过`mutations`来修改data

* 使用

  ```js
  const store = new Vuex.Store({
      state: {
          count: 10
      },
      mutations: {
          // step是接受actions传过来的参数
          async(state,step) {
              state.count += step 
          }
      },
      actions: {
            // 可以将context看做就是vue的实例对象,step组件传递过来的参数
          ayncs(context,step) {
      		// 异步
              setTimeout(() => {     
                  context.commit('async',step)
              }, 2000)
          },
          actionA() {....}, 
              // dispatch 可以调用同节点下的action方法，commit去触发mutation属性下的节点方法，更改state状态
          actionB ({ dispatch, commit }) {
                return dispatch('actionA').then(() => {
                      commit('someOtherMutation')
               })
      }
  })
  ```

* 组件中调用

  * 1.使用`store.dispatch `触发

    ```JS
    <button @click="AddAsync">...+1</button>
    
    methods:{
      AddAsync(){
        this.$store.dispatch('ayncs',5)
      }
    }
    ```

  * 2.使用 `mapActions` 辅助函数将组件的 methods 映射为 `store.dispatch` 调用（需要先在根节点注入 `store`） 

    ```js
    import { mapActions } from 'vuex'
    
    export default {
      methods: {
        ...mapActions({
          AddAsync: 'ayncs' // 将 `this.add()` 映射为 `this.$store.dispatch('ayncs')`,如何要传参就必须使用第一种方法
        })
      }
    }
    ```

### 7.Module

> 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。 为了解决以上问题，Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割： 

#### 1.未开启`namespaced: true `模块化

* 在store下新建modules文件夹，并创建`bar.js`

  ```js
  export default {
    state: {
      a: 123
    },
    mutations: {
    },
    actions: {},
    getters: {
      abc (state) {
        return state.a + '...'
      }
    }
  }
  ```

* 在去store下导入

  ```js
  import foo from './modules/bar'
  
  const store = new Vuex.Store({
      modules:{
          bar
      }
  })
  ```

* 访问值`state`

  ```html
   <p>这是modules的bar: {{ $store.state.bar.a }}</p>
  ```

* 访问action、mutation 和 getter 

  > 默认情况下，模块内部的 action、mutation 和 getter 是注册在**全局命名空间**的——这样使得多个模块能够对同一 mutation 或 action 作出响应。 

  ```js
  import {  mapGetters } from "vuex";
  
  export default {
    computed: {
      ...mapGetters({
        number: "number",
        test: "test"
      })
    }
  };
  ```

  ```html
   <p>这是modules的test:{{test}}</p>
  ```

  
#### 2.加上模块化
2.当加上`namespaced: true `

> 官方说:如果希望你的模块具有更高的封装度和复用性，你可以通过添加 `namespaced: true` 的方式使其成为带命名空间的模块。当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名 

  ```js
    export default {
     namespaced: true,
     state: {
         a: '123-bar'
     },
     mutations: {},
     actions: {},
     getters: {
         test(state) {
             return state.a + '123-ss'
         }
     }
    }
  ```

* 当注册上`namespaced: true `，命名空间的 getter 和 action 会收到局部化的 `getter`，`dispatch` 和 `commit` ,打破了`vuex`默认得命名得方式

![vuex](/medias/imges/vue/vuex/vuex.png )

​	访问就变成

```html
 <p>这是modules的test:{{$store.getters['bar/test']}}</p> 
```

#### 3.访问访问全局内容

如果你希望使用全局 state 和 getter，`rootState` 和 `rootGetters` 会作为第三和第四参数传入 getter，也会通过 `context` 对象的属性传入 action。

若需要在全局命名空间内分发 action 或提交 mutation，将 `{ root: true }` 作为第三参数传给 `dispatch` 或 `commit` 即可。

> 所谓的在全局命名空间内分发是指：将这个模块当成根模块，这样调用时候可以按根模块下函数被命名的名字调用

```js
modules: {
  foo: {
    namespaced: true,

    getters: {
      // 在这个模块的 getter 中，`getters` 被局部化了
      // 你可以使用 getter 的第四个参数来调用 `rootGetters`
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
      },
      someOtherGetter: state => { ... }
    },

    actions: {
      // 在这个模块中， dispatch 和 commit 也被局部化了
      // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
      someAction ({ dispatch, commit, getters, rootGetters }) {
        getters.someGetter // -> 'foo/someGetter'
        rootGetters.someGetter // -> 'someGetter'

        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
      },
      someOtherAction (ctx, payload) { ... }
    }
  }
}
```

#### 4.带命名空间的绑定函数

当使用 `mapState`, `mapGetters`, `mapActions` 和 `mapMutations` 这些函数来绑定带命名空间的模块时，写起来可能比较繁琐： 

```js
computed: {
  ...mapState({
    a: state => state.some.nested.module.a,
    b: state => state.some.nested.module.b
  })
},
methods: {
  ...mapActions([
    'some/nested/module/foo', // -> this['some/nested/module/foo']()
    'some/nested/module/bar' // -> this['some/nested/module/bar']()
  ])
}
```

对于这种情况，你可以将模块的空间名称字符串作为第一个参数传递给上述函数，这样所有绑定都会自动将该模块作为上下文。于是上面的例子可以简化为： 

```js
computed: {
  ...mapState('some/nested/module', {
    a: state => state.a,
    b: state => state.b
  })
},
methods: {
  ...mapActions('some/nested/module', [
    'foo', // -> this.foo()
    'bar' // -> this.bar()
  ])
}
```



### 小结

* 辅助函数小结

  |   辅助函数   | 在组件中的使用位置 |                             映射                             |
  | :----------: | :----------------: | :----------------------------------------------------------: |
  |   mapState   |      computed      |              相当于 count: state => state.count              |
  |  mapGetters  |      computed      |       使用对象展开运算符将 getter 混入 computed 对象中       |
  | mapMutations |      methods       | 同步，将 `this.add()` 映射为 `this.$store.commit('increment')` |
  |  mapActions  |      methods       | 异步，将 `this.add()` 映射为 `this.$store.dispatch('ayncs')  |

* `getter`、`action`和`mutation`参数总结

  |  方法名  |                  方法意义                  |      参数      |                           参数意义                           |
  | :------: | :----------------------------------------: | :------------: | :----------------------------------------------------------: |
  |  getter  |          当于组件的`computed`属性          | state, getters | state`当模块的state状态`，getters`获取同个节点的getters方法` |
  | mutation |  当于组件的`methods`属性,写的代码是同步的  | state,payload  |      state`与getter一样`，payload`组件使用时传入的参数`      |
  |  action  | 当于组件的`methods`属性，,写的代码是异步的 |  context,...   | context`相当于store实例，为了调用commit方法`,...是组件使用时传入的参数 |

### 8.项目结构

Vuex 并不限制你的代码结构。但是，它规定了一些需要遵守的规则：

1. 应用层级的状态应该集中到单个 store 对象中。
2. 提交 **mutation** 是更改状态的唯一方法，并且这个过程是同步的。
3. 异步逻辑都应该封装到 **action** 里面。

只要你遵守以上规则，如何组织代码随你便。如果你的 store 文件太大，只需将 action、mutation 和 getter 分割到单独的文件。

对于大型应用，我们会希望把 Vuex 相关代码分割到模块中。下面是项目结构示例：

>```sh
>├── index.html
>├── main.js
>├── api
>│   └── ... # 抽取出API请求
>├── components
>│   ├── App.vue
>│   └── ...
>└── store
>    ├── index.js          # 我们组装模块并导出 store 的地方
>    ├── actions.js        # 根级别的 action
>    ├── mutations.js      # 根级别的 mutation
>    └── modules
>        ├── cart.js       # 购物车模块
>        └── products.js   # 产品模块
>```

### 9.插件、严格模式、表单处理、测试、热重载

> 小编推荐去官网查看这部分（并没有什么好说的这部分）:
>
> [官网](https://vuex.vuejs.org/zh/guide/plugins.html)



### 寄语

> 撸起袖子，加油干!!!💪