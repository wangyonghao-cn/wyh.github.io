---
title: rxjs中的各类subject(ts)
---

## 前言

> 开发ng2时基本上避不开rxjs处理数据流，但随着越来越复杂的需求，subject的类型也在日益发展
> 今天我们就来介绍一下subject的扩展类

<!--more-->

### BehaviorSubject

> BehaviorSubject必须要设置一个默认值，并会缓存最后发送的一个值，当有新的观察着订阅时，会立即接收到这个最新的值
> 代码如下：

```ts
let sub: BehaviorSubject<number> = new BehaviorSubject<number>(0);
sub.subscribe(n => `A:${console.log(n)}`);
sub.next(100);
sub.subscribe(n => `B:${console.log(n)}`);
sub.next(200);
sub.next(300);
sub.subscribe(n => `C:${console.log(n)}`);
```
>输出如下：

```ts
// A:0
// A:100
// B:100
// A:200
// B:200
// A:300
// B:300
// C:300
```

### ReplaySubject

> ReplaySubject会暂存所有其发出去的值，无论何时订阅，订阅者都能获取所有的数据流
> 代码如下：

```ts
let sub: ReplaySubject<number> = new ReplaySubject<number>();
sub.next(100);
sub.next(200);
sub.subscribe(n => `A:${console.log(n)}`);
sub.next(300);
sub.subscribe(n => `B:${console.log(n)}`);
sub.next(400);
```
>输出如下：

```ts
// A:100
// A:200
// A:300
// B:100
// B:200
// B:300
// A:400
// B:400
```

### AsyncSubject

> AsyncSubject与BehaviorSubject类似，不同的是它只会暂存complete时的数据，而订阅者和后来的订阅者也只能拿到此时的值
> 代码如下：

```ts
let sub: AsyncSubject<number> = new AsyncSubject<number>();
sub.next(100);
sub.next(200);
sub.subscribe(n => `A:${console.log(n)}`);
sub.next(300);
sub.complete();
sub.subscribe(n => `B:${console.log(n)}`);
sub.next(400);
```
>输出如下：

```ts
// A:300
// B:300
```
