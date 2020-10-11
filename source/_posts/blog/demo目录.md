---
title: demo目录
date: 2020-08-16 23:43:43
categories: blog
top: true
summary: demo目录
tags: 
 - blog
---

### Demo目录

### 01-vue

基于es6的class思想进行vue封装，里面涉及了es6提供的新属性，例如 proxy的认知，node节点的判断

* vue-defineproperty 是分使用类分离并采用 defineproperty监听对象数据变化
* vue-proxy 采用 类模块划分并采用 proxy 监听对象数据变化 

### 02-mvc

基于mvc思想构建项目

### 03-长连接

* new-lx 轮询方式获取最新数据
* news-see see模块进行服务器单工传递数据
* news-websocket 采用ws模块实现原生的webscoket全双工传递数据
* news-sockettest 采用 scoket.io 模块实现长连接
* news-scoketnews 用socket.io 模块实现新闻列表demo

### 04-qqPhoto

qq多图片上传demo，对上传图片实现预览和上传功能，这里能够清晰了解 formData 对象的作用和promise依次上传的实现

### 05-axios

* fetch 了解fetch 请求的方式和传参格式
* jwt 服务器token鉴权机制

* myaxios 封装的一个简单的axios库，这里能够清晰了解 axios是如何实现的，

###06-vuex+通信

* app 里面是出了 vuex 的组件通信方式demo
* app2 里面是 vuex 的 通信方式  

### 07-美食杰

这是一个 使用 mongose  +  vue + vue-router + vuex 完成的项目 ，能够学习到vue的平常开发细节

* meishijie-back  后端数据
* mongodb-mock-data --> mongose 数据库的数据 
* meishijie-fe --> 自己完成的项目
* meishijie-fe-finish --> 老师源码

### 08-typescript

这是使用typescript学习的案例，里面又介绍typescript的装饰器语法和用ts的装饰器的功能来开发一个api库

* koa-ts ------> 自己封装的api库
* koa-ts-controllers-demo ---> 使用 koa-ts-controller的库来写一个api库
* 装饰器 ----> 简单介绍了装饰器的语法

### 09-jwt

这里是鉴权的知识例子，可以认识到 jsonwebtoken 的鉴权与机制

### 10-trello

* backend ------> 后端模块，技术栈：`typeScript`、`koa`、`sequelize`、`mysql`来构建

* forntend -----> 前端模块，技术栈：`vue`、`vuex`

  有卡片拖拽实例参考，以及卡片碰撞如何保留状态（通过数据库的两个卡片的order值足够大，作为基础）

* template -----> 前端静态页面

### 11-canvas

这里是用canvas做的一些粒子，比如粒子时钟，在这里可以了解到canvas如何制作动画。

### 12-echarts

这里介绍了echarts的常用图表绘制方法，和echarts的一些高级应用案例，并且有echarts结合地图绘制的大屏数据可视化案例

### 13-黑马电商可视化

这里使用了`echarts`的各种图形的绘制，以及使用`websocket`进行多端同步一端的操作。从这里我们可以认识更多的`websocket`使用，这里是使用websocket进行多端通信，调用注册好的事件。而在前端项目中，可以了解到绘制echarts图表的步骤：`initChart(初始化图表)`、`getData(获取数据)`、`updateChart(更新图表数据)`、`screenAdapter(自适应)`

* koa_server --------> 后端项目
  * service/web_scoket.service.js 是后端的websocket代码
* vue_fornt ----------> 前端项目
  * src/utils/socket_service.js  是与后端的webscoket进行交互的代码