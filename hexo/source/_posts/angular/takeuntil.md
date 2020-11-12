---
title: angular中的rxjs操作符takeUntil
---

## 介绍

> 用于自动取消订阅可观察对象，监听第二个Observable,如果其输出值并完成，则takeUntil取消观察对象的订阅，否则将传递所有值,若Observable不停的产生新值，而没有取消订阅，极易发生内存泄漏!

