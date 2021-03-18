---
title: REACT 井字棋游戏教程学习（一）搭建开发环境，运行项目
date: 2020-11-7
---

# 开始

> 利用闲暇时间再次开始学习react！

<!--more-->

## 创建

> 打开教程<https://react.docschina.org/tutorial/tutorial.html>后，根据步骤创建一个新的my-app项目
> 然后删除src中的源文件，并不是将src目录删除，而是将src目录清空

## 内容部分

> 创建好项目之后就正式开始了
> 1.创建好index.js，和index.css文件之后 将React 和 ReactDOM包引入index.js
> 2.从<https://codepen.io/gaearon/pen/oWWQNa?editors=0100>拷贝代码到index.css
> 3.从

``` js,javascript
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
```

> 在根目录下运行npm start;然后在浏览器访问<http://localhost:3000>。这样你就可以在浏览器中看见一个空的井字棋的棋盘了