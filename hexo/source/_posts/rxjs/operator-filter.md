---
title: rxjs中的操作符 - 过滤
---

## 前言
>    RxJS作为目前前端最热门的库之一，提供强大的功能性方法来处理事件，还有能力利用你之前掌握的语言知识，如果你掌握了响应式编程，那它会很好理解，否则我们将会面临命令式编程转换为响应式编程的困难。虽然如此，但它绝对值得你去学习！

>    操作符是RX最重要的内容，我们将学习操作符的过滤类型

<!--more-->

## 操作符

### debounce
> 根据一个选择器函数，舍弃掉在两次输出之间小于指定时间的发出值。debounce(durationSelector: function): Observable

```ts
// 示例一 基于 timer 的 debounce
// RxJS v6+
import { of, timer } from 'rxjs';
import { debounce } from 'rxjs/operators';

// 发出四个字符串
const example = of('WAIT', 'ONE', 'SECOND', 'Last will display');
/*
  只有在最后一次发送后再经过一秒钟，才会发出值，并抛弃在此之前的所有其他值
*/
const debouncedExample = example.pipe(debounce(() => timer(1000)));
/*
    在这个示例中，所有的值都将被忽略，除了最后一个
    输出: 'Last will display'
*/
const subscribe = debouncedExample.subscribe(val => console.log(val));
```

```ts
// 示例 2: 基于 interval 的 dobounce

// RxJS v6+
import { interval, timer } from 'rxjs';
import { debounce } from 'rxjs/operators';

// 每1秒发出值, 示例: 0...1...2
const interval$ = interval(1000);
// 每1秒都将 debounce 的时间增加200毫秒
const debouncedInterval = interval$.pipe(debounce(val => timer(val * 200)));
/*
  5秒后，debounce 的时间会大于 interval 的时间，之后所有的值都将被丢弃
  输出: 0...1...2...3...4......(debounce 的时间大于1秒后则不再发出值)
*/
const subscribe = debouncedInterval.subscribe(val =>
  console.log(`Example Two: ${val}`)
)
```
### debounceTime

> 舍弃掉在两次输出之间小于指定时间的发出值; debounceTime(dueTime: number, scheduler: Scheduler): Observable

```ts
// RxJS v6+
import { fromEvent, timer } from 'rxjs';
import { debounceTime, map } from 'rxjs/operators';

const input = document.getElementById('example');

// 对于每次键盘敲击，都将映射成当前输入值
const example = fromEvent(input, 'keyup').pipe(map(i => i.currentTarget.value));

// 在两次键盘敲击之间等待0.5秒方才发出当前值，
// 并丢弃这0.5秒内的所有其他值
const debouncedInput = example.pipe(debounceTime(500));

// 输出值
const subscribe = debouncedInput.subscribe(val => {
  console.log(`Debounced Input: ${val}`);
});

```
