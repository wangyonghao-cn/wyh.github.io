---
title: 自研脚本tool.js
---

# 前言

> 闲着无聊自己写了一些工具方法，有兴趣的也可以用用看，提些建议，好用的话可以给个小星星哈，地址在这<https://github.com/wangyonghao-cn/tool.js>

<!--more-->

## 脚本介绍

### 引入
> 使用script标签引入此tool.js文件，会将对象$$挂载在window上，直接调用window上的$$对象上的方法 如：window.$$.deepClone(a) 或者 $$.deepClone(a);

### 方法

deepClone: 深拷贝对象或数组
```js
  var a = {a: 1, b: 2, c: {m: 2}}
  var b = $$.deepClone(a);
  console.log(b);
```
 
lightClone: 浅拷贝对象或数组

```js
  var a = {a: 1, b: 2, c: {m: 2}}
  var b = $$.lightCopy(a);
  console.log(b);
```

formateDate：格式化日期 格式化年月日 格式支持yyyy-mm-dd yyyymmdd yyyy/mm/dd

```js
  var a = '20180909';
  var b = $$.formateDate(a);
  console.log(b); '2018/09/09';
```

> 持续更新中....