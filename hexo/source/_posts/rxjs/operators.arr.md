---
title: rxjs中的操作符 - 组合类
---

## 前言
>    RxJS作为目前前端最热门的库之一，提供强大的功能性方法来处理事件，还有能力利用你之前掌握的语言知识，如果你掌握了响应式编程，那它会很好理解，否则我们将会面临命令式编程转换为响应式编程的困难。虽然如此，但它绝对值得你去学习！

>    操作符是RX最重要的内容，我们将学习操作符的组合类型

<!--more-->

## 操作符

### combineLatest
> 当任意 observable 发出值时，发出每个 observable 的最新值(combineLatest(observables: ...Observable, project: function): Observable)

> 当有多个长期活动的 observables 且它们依靠彼此来进行一些计算或决定时，此操作符是最适合的，请注意传入的observable都最少发出一个值时才会发出初始值

```ts
// RxJS v6+
import { timer, combineLatest } from 'rxjs';

// timerOne 在1秒时发出第一个值，然后每4秒发送一次
const timerOne = timer(1000, 4000);
// timerTwo 在2秒时发出第一个值，然后每4秒发送一次
const timerTwo = timer(2000, 4000);
// timerThree 在3秒时发出第一个值，然后每4秒发送一次
const timerThree = timer(3000, 4000);

// 当一个 timer 发出值时，将每个 timer 的最新值作为一个数组发出
const combined = combineLatest(timerOne, timerTwo, timerThree);

const subscribe = combined.subscribe(latestValues => {
  // 从 timerValOne、timerValTwo 和 timerValThree 中获取最新发出的值
    const [timerValOne, timerValTwo, timerValThree] = latestValues;
  /*
      示例:
    timerOne first tick: 'Timer One Latest: 1, Timer Two Latest:0, Timer Three Latest: 0
    timerTwo first tick: 'Timer One Latest: 1, Timer Two Latest:1, Timer Three Latest: 0
    timerThree first tick: 'Timer One Latest: 1, Timer Two Latest:1, Timer Three Latest: 1
  */
    console.log(
      `Timer One Latest: ${timerValOne},
     Timer Two Latest: ${timerValTwo},
     Timer Three Latest: ${timerValThree}`
    );
  }
);

```
> 它还可以接收一个处理函数，subscribe将接收其return的值

```ts
// RxJS v6+
import { timer, combineLatest } from 'rxjs';

// timerOne 在1秒时发出第一个值，然后每4秒发送一次
const timerOne = timer(1000, 4000);
// timerTwo 在2秒时发出第一个值，然后每4秒发送一次
const timerTwo = timer(2000, 4000);
// timerThree 在3秒时发出第一个值，然后每4秒发送一次
const timerThree = timer(3000, 4000);

// combineLatest 还接收一个可选的 projection 函数
const combinedProject = combineLatest(
  timerOne,
  timerTwo,
  timerThree,
  (one, two, three) => {
    return `Timer One (Proj) Latest: ${one}, 
              Timer Two (Proj) Latest: ${two}, 
              Timer Three (Proj) Latest: ${three}`;
  }
);
// 输出值
const subscribe = combinedProject.subscribe(latestValuesProject =>
  console.log(latestValuesProject)
);
```
> 示例二： 监听两个按钮的click，并计算点击次数

```ts
// RxJS v6+ 示例2
import { fromEvent, combineLatest } from 'rxjs';
import { mapTo, startWith, scan, tap, map } from 'rxjs/operators';

// 用来设置 HTML 的辅助函数
const setHtml = id => val => (document.getElementById(id).innerHTML = val);

const addOneClick$ = id =>
  fromEvent(document.getElementById(id), 'click').pipe(
    // 将每次点击映射成1
    mapTo(1),
    startWith(0),
    // 追踪运行中的总数
    scan((acc, curr) => acc + curr),
    // 为适当的元素设置 HTML
    tap(setHtml(`${id}Total`))
  );

const combineTotal$ = combineLatest(addOneClick$('red'), addOneClick$('black'))
  .pipe(map(([val1, val2]) => val1 + val2))
  .subscribe(setHtml('total'));
```
```html
<div>
  <button id='red'>Red</button>
  <button id='black'>Black</button>
</div>
<div>Red: <span id="redTotal"></span> </div>
<div>Black: <span id="blackTotal"></span> </div>
<div>Total: <span id="total"></span> </div>
```

### combineAll
> 当源 observable 完成时，对收集的 observables 使用 combineLatest 策略； (combineAll(project: function): Observable)

```ts
// RxJS v6+
import { take, map, combineAll } from 'rxjs/operators';
import { interval } from 'rxjs';

// 每秒发出值，并只取前2个
const source = interval(1000).pipe(take(2));
// 将 source 发出的每个值映射成取前5个值的 interval observable
const example = source.pipe(
  map(val => interval(1000).pipe(map(i => `Result (${val}): ${i}`), take(5)))
);
/*
  soure 中的2个值会被映射成2个(内部的) interval observables，
  这2个内部 observables 每秒使用 combineLatest 策略来 combineAll，
  每当任意一个内部 observable 发出值，就会发出每个内部 observable 的最新值。
*/
const combined = example.pipe(combineAll());
/*
  输出:
  ["Result (0): 0", "Result (1): 0"]
  ["Result (0): 1", "Result (1): 0"]
  ["Result (0): 1", "Result (1): 1"]
  ["Result (0): 2", "Result (1): 1"]
  ["Result (0): 2", "Result (1): 2"]
  ["Result (0): 3", "Result (1): 2"]
  ["Result (0): 3", "Result (1): 3"]
  ["Result (0): 4", "Result (1): 3"]
  ["Result (0): 4", "Result (1): 4"]
*/
const subscribe = combined.subscribe(val => console.log(val));

```

### concat

> 按照顺序，前一个 observable 完成了再订阅下一个 observable 并发出值!(concat(observables: ...*): Observable)

> 示例 1: concat 2个基础的 observables

```ts
// RxJS v6+
import { concat } from 'rxjs/operators';
import { of } from 'rxjs';

// 发出 1,2,3
const sourceOne = of(1, 2, 3);
// 发出 4,5,6
const sourceTwo = of(4, 5, 6);
// 先发出 sourceOne 的值，当完成时订阅 sourceTwo
const example = sourceOne.pipe(concat(sourceTwo));
// 输出: 1,2,3,4,5,6
const subscribe = example.subscribe(val =>
  console.log('Example: Basic concat:', val)
);
```
> 示例 2: concat 作为静态方法
```ts
// RxJS v6+
import { of, concat } from 'rxjs';

// 发出 1,2,3
const sourceOne = of(1, 2, 3);
// 发出 4,5,6
const sourceTwo = of(4, 5, 6);

// 作为静态方法使用
const example = concat(sourceOne, sourceTwo);
// 输出: 1,2,3,4,5,6
const subscribe = example.subscribe(val => console.log(val));

```
> 示例 3: 使用延迟的 souce observable 进行 concat

```ts
// RxJS v6+
import { delay, concat } from 'rxjs/operators';
import { of } from 'rxjs';

// 发出 1,2,3
const sourceOne = of(1, 2, 3);
// 发出 4,5,6
const sourceTwo = of(4, 5, 6);

// 延迟3秒，然后发出
const sourceThree = sourceOne.pipe(delay(3000));
// sourceTwo 要等待 sourceOne 完成才能订阅
const example = sourceThree.pipe(concat(sourceTwo));
// 输出: 1,2,3,4,5,6
const subscribe = example.subscribe(val =>
  console.log('Example: Delayed source one:', val)
);
```
> 示例 4: 使用不完成的 source observable 进行 concat

```ts
// RxJS v6+
import { interval, of, concat } from 'rxjs';

// 当 source 永远不完成时，随后的 observables 永远不会运行
const source = concat(interval(1000), of('This', 'Never', 'Runs'));
// 输出: 0,1,2,3,4....
const subscribe = source.subscribe(val =>
  console.log(
    'Example: Source never completes, second observable never runs:',
    val
  )
// 输出: 0,1,2,3,4....
const subscribe = source.subscribe(val => console.log('Example: Source never completes, second observable never runs:', val));
```
### concatAll

> 收集 observables，当前一个完成时订阅下一个(concatAll(): Observable)

```ts
// RxJS v6+
import { map, concatAll } from 'rxjs/operators';
import { of, interval } from 'rxjs';

// 每2秒发出值
const source = interval(2000);
const example = source.pipe(
  // 为了演示，增加10并作为 observable 返回
  map(val => of(val + 10)),
  // 合并内部 observables 的值
  concatAll()
);
// 输出: 'Example with Basic Observable 10', 'Example with Basic Observable 11'...
const subscribe = example.subscribe(val =>
  console.log('Example with Basic Observable:', val)
);
```
### forkJoin
> 当所有 observables 完成时，发出每个 observable 的最新值(forkJoin(...args, selector : function)), 如果内部 observable 不完成的话，forkJoin 永远不会发出值

> 示例 1: Observables 再不同的时间间隔后完成

```ts
// RxJS v6+
import { delay, take } from 'rxjs/operators';
import { forkJoin, of, interval } from 'rxjs';

const myPromise = val =>
  new Promise(resolve =>
    setTimeout(() => resolve(`Promise Resolved: ${val}`), 5000)
  );

/*
  当所有 observables 完成时，将每个 observable 
  的最新值作为数组发出
*/
const example = forkJoin(
  // 立即发出 'Hello'
  of('Hello'),
  // 1秒后发出 'World'
  of('World').pipe(delay(1000)),
  // 1秒后发出0
  interval(1000).pipe(take(1)),
  // 以1秒的时间间隔发出0和1
  interval(1000).pipe(take(2)),
  // 5秒后解析 'Promise Resolved' 的 promise
  myPromise('RESULT')
);
//输出: ["Hello", "World", 0, 1, "Promise Resolved: RESULT"]
const subscribe = example.subscribe(val => console.log(val));
```
> 示例 2: 发起任意多个请求

```ts
// RxJS v6+
import { mergeMap } from 'rxjs/operators';
import { forkJoin, of } from 'rxjs';

const myPromise = val =>
  new Promise(resolve =>
    setTimeout(() => resolve(`Promise Resolved: ${val}`), 5000)
  );

const source = of([1, 2, 3, 4, 5]);
// 发出数组的全部5个结果
const example = source.pipe(mergeMap(q => forkJoin(...q.map(myPromise))));
/*
  输出:
  [
   "Promise Resolved: 1",
   "Promise Resolved: 2",
   "Promise Resolved: 3",
   "Promise Resolved: 4",
   "Promise Resolved: 5"
  ]
*/
const subscribe = example.subscribe(val => console.log(val));
```
> 示例 3: 在外部处理错误

```ts
// RxJS v6+
import { delay, catchError } from 'rxjs/operators';
import { forkJoin, of, throwError } from 'rxjs';

/*
  当所有 observables 完成时，将每个 observable 
  的最新值作为数组发出
*/
const example = forkJoin(
  // 立即发出 'Hello'
  of('Hello'),
  // 1秒后发出 'World'
  of('World').pipe(delay(1000)),
  // 抛出错误
  throwError('This will error')
).pipe(catchError(error => of(error)));
// 输出: 'This will Error'
const subscribe = example.subscribe(val => console.log(val));
```
> 某个错误得到其他成功的

```ts
// RxJS v6+
import { delay, catchError } from 'rxjs/operators';
import { forkJoin, of, throwError } from 'rxjs';

/*
  当所有 observables 完成时，将每个 observable 
  的最新值作为数组发出
*/
const example = forkJoin(
  // 立即发出 'Hello'
  of('Hello'),
  // 1秒后发出 'World'
  of('World').pipe(delay(1000)),
  // 抛出错误
  throwError('This will error').pipe(catchError(error => of(error)))
);
// 输出: ["Hello", "World", "This will error"]
const subscribe = example.subscribe(val => console.log(val));
```

### merge （已弃用）
> 将多个 observables 转换成单个 observable(merge(input: Observable): Observable),无顺序限制

### mergeAll
> 收集并订阅所有的 observables(mergeAll(concurrent: number): Observable) ，mergeAll 操作符接收一个可选参数
  以决定在同一时间有多少个内部 observables 可以被订阅。其余的 observables 会先暂存以等待订阅。

```ts
// RxJS v6+
import { map, mergeAll } from 'rxjs/operators';
import { of } from 'rxjs';

const myPromise = val =>
  new Promise(resolve => setTimeout(() => resolve(`Result: ${val}`), 2000));
// 发出 1,2,3
const source = of(1, 2, 3);

const example = source.pipe(
  // 将每个值映射成 promise
  map(val => myPromise(val)),
  // 发出 source 的结果
  mergeAll(2)
);

/*
  输出:
  "Result: 1"
  "Result: 2"
  "Result: 3"
*/
const subscribe = example.subscribe(val => console.log(val));
```
### pairwise

> 将前一个值和当前值作为数组发出(pairwise(): Observable<Array>)
```ts
// RxJS v6+
import { pairwise, take } from 'rxjs/operators';
import { interval } from 'rxjs';

// 返回: [0,1], [1,2], [2,3], [3,4], [4,5]
interval(1000)
  .pipe(
    pairwise(),
    take(5)
  )
  .subscribe(console.log);
```
### race (已弃用)
> 使用首先发出值的 observable(race(): Observable)

```ts
// RxJS v6+
import { mapTo } from 'rxjs/operators';
import { interval } from 'rxjs/observable/interval';
import { race } from 'rxjs/observable/race';

// 接收第一个发出值的 observable
const example = race(
  // 每1.5秒发出值
  interval(1500),
  // 每1秒发出值
  interval(1000).pipe(mapTo('1s won!')),
  // 每2秒发出值
  interval(2000),
  // 每2.5秒发出值
  interval(2500)
);
// 输出: "1s won!"..."1s won!"...etc
const subscribe = example.subscribe(val => console.log(val));
```
### startWith
> 发出给定的第一个值(startWith(an: Values): Observable)

```ts
// RxJS v6+
import { startWith } from 'rxjs/operators';
import { of } from 'rxjs';

// 发出 (1,2,3)
const source = of(1, 2, 3);
// 从0开始
const example = source.pipe(startWith(0));
// 输出: 0,1,2,3
const subscribe = example.subscribe(val => console.log(val));
```

### withLatestFrom 

> observable输出时同时输出另一个observable（withLatestFrom(other: Observable, project: Function): Observable）,需注意combinelatest与withLatestFrom一样，当所有observable都有值时才会发出值，与 combinelatest的区别是withLatestFrom只监听调用的observable

```ts
// RxJS v6+
import { withLatestFrom, map } from 'rxjs/operators';
import { interval } from 'rxjs';

// 每5秒发出值
const source = interval(5000);
// 每1秒发出值
const secondSource = interval(1000);
const example = source.pipe(
  withLatestFrom(secondSource),
  map(([first, second]) => {
    return `First Source (5s): ${first} Second Source (1s): ${second}`;
  })
);
/*
  输出:
  "First Source (5s): 0 Second Source (1s): 4"
  "First Source (5s): 1 Second Source (1s): 9"
  "First Source (5s): 2 Second Source (1s): 14"
  ...
*/
const subscribe = example.subscribe(val => console.log(val));
```
### zip（已弃用）
> zip(zip(observables: *): Observable) 操作符会订阅所有内部 observables，然后等待每个发出一个值。一旦发生这种情况，将发出具有相应索引的所有值。这会持续进行，直到至少一个内部 observable 完成。

```ts
// RxJS v6+
import { delay } from 'rxjs/operators';
import { of, zip } from 'rxjs';

const sourceOne = of('Hello');
const sourceTwo = of('World!');
const sourceThree = of('Goodbye');
const sourceFour = of('World!');
// 一直等到所有 observables 都发出一个值，才将所有值作为数组发出
const example = zip(
  sourceOne,
  sourceTwo.pipe(delay(1000)),
  sourceThree.pipe(delay(2000)),
  sourceFour.pipe(delay(3000))
);
// 输出: ["Hello", "World!", "Goodbye", "World!"]
const subscribe = example.subscribe(val => console.log(val));
```