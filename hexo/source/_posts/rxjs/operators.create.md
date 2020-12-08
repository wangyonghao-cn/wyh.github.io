---
title: rxjs中的操作符 - (创建类)
---

## 前言
>    RxJS作为目前前端最热门的库之一，提供强大的功能性方法来处理事件，还有能力利用你之前掌握的语言知识，如果你掌握了响应式编程，那它会很好理解，否则我们将会面临命令式编程转换为响应式编程的困难。虽然如此，但它绝对值得你去学习！

>  操作符是RX最重要的内容，这节我们将学习创建类操作符！

<!--more-->

## 操作符

#### create
> 使用给定的函数来创建 observable (create(subscribe: function))

```ts
const sub = Rx.Observable.create((o) => {
	let a = 0
	setInterval(() => {
		a++;
		o.next(a)
	}, 2000)
})
sub.subscribe(n => console.log(n))
// 输出 1 2 3 4
```
#### empty
> 立即完成的 observable (empty(scheduler: Scheduler): Observable)

> 未完待续。。。
