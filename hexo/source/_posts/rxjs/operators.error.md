---
title: rxjs中的操作符 - 错误处理
---

## 前言
>    RxJS作为目前前端最热门的库之一，提供强大的功能性方法来处理事件，还有能力利用你之前掌握的语言知识，如果你掌握了响应式编程，那它会很好理解，否则我们将会面临命令式编程转换为响应式编程的困难。虽然如此，但它绝对值得你去学习！

>    操作符是RX最重要的内容，我们将学习操作符的组合类型

<!--more-->

## 操作符

### catch / catchError

>  catch中需要返回一个observable  catchError(project : function): Observable

> 捕获 observable 中的错误

```ts
// RxJS v6+
import { throwError, of } from 'rxjs';
import { catchError } from 'rxjs/operators';
// 发出错误
const source = throwError('This is an error!');
// 优雅地处理错误，并返回带有错误信息的 observable
const example = source.pipe(catchError(val => of(`I caught: ${val}`)));
// 输出: 'I caught: This is an error'
const subscribe = example.subscribe(val => console.log(val));
```

### retry

> 如果发生错误，以指定次数重试 observable 序列。retry(number: number): Observable

```ts
// RxJS v6+
import { interval, of, throwError } from 'rxjs';
import { mergeMap, retry } from 'rxjs/operators';

// 每1秒发出值
const source = interval(1000);
const example = source.pipe(
  mergeMap(val => {
    // 抛出错误以进行演示
    if (val > 5) {
      return throwError('Error!');
    }
    return of(val);
  }),
  // 出错的话可以重试2次
  retry(2)
);
/*
  输出:
  0..1..2..3..4..5..
  0..1..2..3..4..5..
  0..1..2..3..4..5..
  "Error!: Retried 2 times then quit!"
*/
const subscribe = example.subscribe({
  next: val => console.log(val),
  error: val => console.log(`${val}: Retried 2 times then quit!`)
});

```

### retryWhen

> 当发生错误时，基于自定义的标准来重试 observable 序列.retryWhen(receives: (errors: Observable) => Observable, the: scheduler): Observable

```ts
// RxJS v6+
import { timer, interval } from 'rxjs';
import { map, tap, retryWhen, delayWhen } from 'rxjs/operators';

// 每1秒发出值
const source = interval(1000);
const example = source.pipe(
  map(val => {
    if (val > 5) {
      // 错误将由 retryWhen 接收
      throw val;
    }
    return val;
  }),
  retryWhen(errors =>
    errors.pipe(
      // 输出错误信息
      tap(val => console.log(`Value ${val} was too high!`)),
      // 5秒后重启
      delayWhen(val => timer(val * 1000))
    )
  )
);
/*
  输出:
  0
  1
  2
  3
  4
  5
  "Value 6 was too high!"
  --等待5秒后然后重复此过程
*/
const subscribe = example.subscribe(val => console.log(val));

```

