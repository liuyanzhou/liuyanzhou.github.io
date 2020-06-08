---
title: Vue脚手架使用
date: 2020-04-10 22:22:04
img: /medias/imges/vue/vue.jpg
categories: Vue
top: true
summary: Vue脚手架使用
tags: 
 - Vue
---

## Vue脚手架

> 还在为构造`vue`项目时被命令行那丑陋的面孔所支配吗？那么你有必要阅读这篇文章，它将打开你`vue`项目构造大门😀
>
> Vue 脚手架用于快速生成 Vue 项目基础架构，其官网地址为：https://cli.vuejs.org/zh/ 

安装 3.x 版本的 Vue 脚手架： 

```ba
npm install -g @vue/cli
```

### 1.基于命令行交互式的方式，创建新版 vue 项目

* 运行

  ```bash
  vue create my-project[项目存放文件夹名]
  ```

* 配置

  ![配置解析](/medias/imges/vue/vue-cli/vue-cli-07.png )

* 运行项目

  ```bash
  cd '到你的项目创建的目录'
  # 执行
  npm run serve
  ```

### 2. 基于 图形化界面 的方式，创建 新版 vue 项目 

* 运行

  ```bash
  vue ui
  ```


![第一步](/medias/imges/vue/vue-cli/vue-cli-2-1.png )

![第二步](/medias/imges/vue/vue-cli/vue-cli-2-2.png )

![第三步](/medias/imges/vue/vue-cli/vue-cli-2-3.png )

![第四步](/medias/imges/vue/vue-cli/vue-cli-2-4.png )

### 3.Vue 脚手架生成的项目结构分析 

![项目结构](/medias/imges/vue/vue-cli/vue-cli-2-6.png )

### 4.Vue 脚手架的自定义配置 

#### 4.1通过 package.json 配置项目 

```json
 // 必须是符合规范的json语法
 "vue": {
 "devServer": {
 "port": "8888",
 "open" : true
	 }
 },
```

> 注意：不推荐使用这种配置方式。因为 package.json 主要用来管理包的配置信息；为了方便维护，推荐将 vue 脚 手架相关的配置，单独定义到 vue.config.js 配置文件中。

#### 4.2通过单独的配置文件配置项目 

* 在项目的跟目录创建文件 vue.config.js 

* 在该文件中进行相关配置，从而覆盖默认配置 

  ```js
   // vue.config.js
   module.exports = {
       devServer: {
           // 指定端口
           port: 8888
       }
   }
  ```

### 5.Element-UI 基于图形化界面自动安装 

* 运行 vue ui 命令，打开图形化界面 
* 通过 Vue 项目管理器，进入具体的项目配置面板 
* 点击 插件 -> 添加插件，进入插件查询面板 
* 搜索 vue-cli-plugin-element 并安装 
* 配置插件，实现按需导入，从而减少打包后项目的体积 } }

### 寄语

> 撸起袖子，加油干！！！😎