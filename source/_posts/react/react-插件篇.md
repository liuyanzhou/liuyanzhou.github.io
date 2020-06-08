---
title: react-插件篇
date: 2020-04-28 22:14:20
categories: react
top: true
summary: react插件
tags: 
 - react
---

### 一.qrcode.react 

> 二维码生成器

* [npm](https://www.npmjs.com/package/qrcode.react)

* 安装

  ```bash
  npm install qrcode.react
  ```

* 使用

  ```js
  var React = require('react');
  var QRCode = require('qrcode.react');
   
  // value -> 是微信官方生成的二维码地址
  React.render(
    <QRCode value="http://facebook.github.io/react/" />,
    mountNode
  );
  ```

  