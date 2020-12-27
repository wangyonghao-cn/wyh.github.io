---
title: rxjs中的操作符 - 创建类
---

## 前言
>    RxJS作为目前前端最热门的库之一，提供强大的功能性方法来处理事件，还有能力利用你之前掌握的语言知识，如果你掌握了响应式编程，那它会很好理解，否则我们将会面临命令式编程转换为响应式编程的困难。虽然如此，但它绝对值得你去学习！

>  操作符是RX最重要的内容，这节我们将学习创建类操作符！

<!--more-->

## 操作符

### create
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
### empty
> 立即完成的 observable (empty(scheduler: Scheduler): Observable)

```ts
// RxJS v6+
import { empty } from 'rxjs';

// 输出: 'Complete!'
const subscribe = empty().subscribe({
  next: () => console.log('Next'),
  complete: () => console.log('Complete!')
});
```
### from
> from(ish: ObservableInput, mapFn: function, thisArg: any, scheduler: Scheduler): Observable将数组、promise 或迭代器转换成 observable 

```ts
// RxJS v6+
import { from } from 'rxjs';

// 将数组作为值的序列发出
const arraySource = from([1, 2, 3, 4, 5]);
// 输出: 1,2,3,4,5
const subscribe = arraySource.subscribe(val => console.log(val));

// 发出 promise 的结果
const promiseSource = from(new Promise(resolve => resolve('Hello World!')));
// 输出: 'Hello World'
const subscribe = promiseSource.subscribe(val => console.log(val));

// 使用 js 的集合
const map = new Map();
map.set(1, 'Hi');
map.set(2, 'Bye');

const mapSource = from(map);
// 输出: [1, 'Hi'], [2, 'Bye']
const subscribe = mapSource.subscribe(val => console.log(val));

// 将字符串作为字符序列发出
const source = from('Hello World');
// 输出: 'H','e','l','l','o',' ','W','o','r','l','d'
const subscribe = source.subscribe(val => console.log(val));
```

### formEvent
> 将事件转换成 observable 序列  fromEvent(target: EventTargetLike, eventName: string, selector: function): Observable

```ts
// RxJS v6+
import { fromEvent } from 'rxjs';
import { map } from 'rxjs/operators';

// 创建发出点击事件的 observable
const source = fromEvent(document, 'click');
// 映射成给定的事件时间戳
const example = source.pipe(map(event => `Event time: ${event.timeStamp}`));
// 输出 (示例中的数字以运行时为准): 'Event time: 7276.390000000001'
const subscribe = example.subscribe(val => console.log(val));
```

### fromPromise
> 创建由 promise 转换而来的 observable，并发出 promise 的结果.fromPromise(promise: Promise, scheduler: Scheduler): Observable

```ts
import { of } from 'rxjs/observable/of';
import { fromPromise } from 'rxjs/observable/fromPromise';
import { mergeMap, catchError } from 'rxjs/operators';

// 基于输入来决定是 resolve 还是 reject 的示例 promise
const myPromise = willReject => {
  return new Promise((resolve, reject) => {
    if (willReject) {
      reject('Rejected!');
    }
    resolve('Resolved!');
  });
};
// 先发出 true，然后是 false
const source = of(true, false);
const example = source.pipe(
  mergeMap(val =>
    fromPromise(myPromise(val)).pipe(
      // 捕获并优雅地处理 reject 的结果
      catchError(error => of(`Error: ${error}`))
    )
  )
);
// 输出: 'Error: Rejected!', 'Resolved!'
const subscribe = example.subscribe(val => console.log(val));
```
### interval
> 基于给定时间间隔发出数字序列.interval(period: number, scheduler: Scheduler): Observable

```ts
// RxJS v6+
import { interval } from 'rxjs';

// 每1秒发出数字序列中的值
const source = interval(1000);
// 数字: 0,1,2,3,4,5....
const subscribe = source.subscribe(val => console.log(val));
```

### of
> 按顺序发出任意数量的值  of(...values, scheduler: Scheduler): Observable

```ts
// RxJS v6+
import { of } from 'rxjs';
// 出任意类型值
const source = of({ name: 'Brian' }, [1, 2, 3], function hello() {
  return 'Hello';
});
// 输出: {name: 'Brian}, [1,2,3], function hello() { return 'Hello' }
const subscribe = source.subscribe(val => console.log(val));
```

### range
> 依次发出给定区间内的数字。  range(start: number, count: number, scheduler: Scheduler): Observable

```ts
// RxJS v6+
import { range } from 'rxjs';

// 依次发出1-10
const source = range(1, 10);
// 输出: 1,2,3,4,5,6,7,8,9,10
const example = source.subscribe(val => console.log(val));
```

### throwError
> 在订阅上发出错误 throw(error: any, scheduler: Scheduler): Observable
```ts
// RxJS v6+
import { throwError } from 'rxjs';

// 在订阅上使用指定值来发出错误
const source = throwError('This is an error!');
// 输出: 'Error: This is an error!'
const subscribe = source.subscribe({
  next: val => console.log(val),
  complete: () => console.log('Complete!'),
  error: val => console.log(`Error: ${val}`)
});
```

### timer
> 给定持续时间后，再按照指定间隔时间依次发出数字  timer(initialDelay: number | Date, period: number, scheduler: Scheduler): Observable

```ts
// RxJS v6+
import { timer } from 'rxjs';

// 1秒后发出0，然后结束，因为没有提供第二个参数
const source = timer(1000);
// 输出: 0
const subscribe = source.subscribe(val => console.log(val));

/*
  timer 接收第二个参数，它决定了发出序列值的频率，在本例中我们在1秒发出第一个值，
  然后每2秒发出序列值
*/
const source = timer(1000, 2000);
// 输出: 0,1,2,3,4,5......
const subscribe = source.subscribe(val => console.log(val));
```
