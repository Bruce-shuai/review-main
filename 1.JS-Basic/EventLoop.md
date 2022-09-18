## 什么是事件循环

首先`JavaScript`是单线程，单线程以为着就要排队。前一个任务执行结束，后一个任务才会执行。如果前一个任务执行时间很久，那么后一个任务必须一直等待。为此，JavaScript设计者在广义上将任务分为同步任务和异步任务。首先所有同步任务都在主线程上执行，形成一个**执行栈。**主线程外，还存在一个**任务队列**，只要异步任务有了运行结果，就在任务队列中放置一个事件；一旦执行栈中所有任务执行完毕，系统就会去任务队列中取出对应的回调函数进行执行。主线程不断重复以上操作。

即：主线程不断从消息队列中获取消息，执行消息，这个过程成为事件循环

除了广义上的同步任务和异步任务，我们还可以将任务细分为宏任务和微任务。



### 同步任务 异步任务

单线程即意味着所有任务都需要排队，前一个任务结束，才会执行后一个任务。如果前一个任务耗时很长，后一个任务就必须一直等着。为此，JavaScript 设计者在广义上将所有任务分成两种：
**同步任务（synchronous）**
**异步任务（asynchronous）**
二者运行机制如下：

1. 所有同步任务都在**主线程(渲染进程中的主线程)**上执行，形成一个**执行栈**；
2. 主线程外，还存在一个**任务队列**，只要异步任务有了运行结果，就在任务队列中放置一个事件；
3. 一旦执行栈中所有同步任务执行完毕，系统(Event Loop循环机制)就会取出任务队列中事件对应的回调函数进入执行栈，开始执行；
4. 主线程不断重复上面的第三步。

### 宏任务、微任务

除了广义上的定义，我们将任务进行更精细的定义，

​	任务队列的任务又分为宏任务和微任务，宏任务一次只取一个任务执行，执行完后去取微任务队列的任务执行至空。

* `macrotask`：
  * script（整体代码）
  * 事件回调（DOM）
  * XHR回调
  * `setTimeout`
  * `setInterval`
  * `setImmediate` (nodejs里会着重研究)
  * I / O (nodejs里会着重研究)
  * UI render
* **micro-task（微任务）** 大概包括：
  - process.nextTick (nodejs里会着重研究)
  - Promise.then
  - async / await （等价于 Promise.then）
  - MutationObserver（HTML5 新特性，监控某个节点）

## 浏览器中的事件循环

`JavaScript`代码的执行过程中，除了依靠函数调用栈来搞定函数的执行顺序以外，还依靠任务队列（task queue）来搞定另外一些代码的执行。整个执行过程，我们称为事件循环过程。一个线程中，事件循环是唯一的，但是**任务队列**可以拥有多个。任务队列又分为 macro-task（宏任务）和 micro-task（微任务），在最新标准中，他们分别被称为 tasks 和 jobs。

总体结论就是：

- 执行宏任务
- 然后执行宏任务产生的微任务
- 若微任务在执行过程中产生了新的微任务，则继续执行微任务
- 微任务执行完毕，再回到宏任务中进行下一轮循环
- **浏览器只保证`requestAnimationFrame`的回调在重绘之前执行，没有确定的时间，何时重绘由浏览器决定**
- process.nextTick的优先级高于Promise.then。可以理解为在宏任务结束，微任务开始之前执行。


## async / await 执行顺序

我们知道 `async` 会隐式返回一个 Promise 作为结果的函数，那么可以简单理解为：await 后面的函数在执行完毕后，await 会产生一个微任务（Promise.then 是微任务）。但是我们要注意微任务产生的时机，它是执行完 await 后，直接跳出 async 函数，执行其他代码（此处就是协程的运作，A暂停执行，控制权交给B）。其他代码执行完毕后，再回到 async 函数去执行剩下的代码，然后把 await 后面的代码注册到微任务队列中。例如：

```js
console.log('script start')

async function async1() {
	await async2()
	console.log('async1 end')
}

async function async2() {
	console.log('async2 end')
}
async1()

setTimeout(function() {
	console.log('setTimeout')
}, 0)

new Promise(resolve => {
	console.log('Promise')
	resolve()
}).then(function() {
	console.log('promise1')   // 没写返回值意味着下一个then无参数
}).then(function() {
	console.log('promise2')
})

console.log('script end')

// 执行结果：
/**
 * script start
 * async2 end
 * Promise
 * script end
 * async1 end
 * promise1
 * promise2
 * setTimeout
 */
```

分析这段代码：

1. 执行代码，输出 `script start`
2. 执行 `async1()`，调用了 `async2()`，然后输出 `async2 end`，此时会保留 async1 的上下文，然后跳出 async1
3. 遇到 `setTimeout`，产生一个宏任务
4. 执行 Promise，输出 Promise，遇到 then，产生第一个微任务，继续执行代码，输出 `script end`
5. 当前宏任务执行完毕，开始执行当前宏任务产生的微任务，输出 `promise1`，然后又遇到 then，产生一个新的微任务
6. 执行微任务，输出 `promise2`，此时微任务队列已清空，执行权交还给 async1
7. 执行 await 后的代码，输出 `async1 end`
8. 所有微任务队列均已执行完毕，开始执行下一个宏任务，打印 `setTimeout`

---

同步代码（宏任务） > **`process.nextTick > Promise（微任务）`**> setTimeout(fn)、setInterval(fn)（宏任务）> setImmediate（宏任务）> setTimeout(fn, time)、setInterval(fn, time)，其中time>0



## node 中的事件循环

同样是使用 V8 引擎的 Node.js 也同样有事件循环。事件循环是 Node 处理非阻塞 I / O 操作的机制，Node 中实现事件循环依赖的是 `libuv` 引擎。由于 Node 11 之后，事件循环的一些原理发生了改变，这里就以新的标准去讲，最后再列上变化点让大家了解前因后果。

### 宏任务和微任务

node 中也分为宏任务和微任务，其中，

macro-task（宏任务）包括：

- setTimeout
- setInterval
- setImmediate 
- script（整体代码）
- I / O 操作

micro-task（微任务）包括：

- process.nextTick（与普通微任务有区别，在微任务队列执行之前执行）
- Promise.then 回调

### node 事件循环整体理解

> node.js 本身就是靠异步事件执行机制占据后端一席之地的。所以重点在事件循环机制

```
   ┌───────────────────────────┐
┌─>│           timers          │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘
```
1. timers：本阶段执行已被setTimeout() 和 setInterval()调度的回调函数，由这两个函数启动的回调函数
2. pending callbacks: 本阶段执行某些系统操作(非nodejs)的回调函数
3. idle、prepare: 仅系统内部使用(非nodejs)，只需要知道有这两个阶段就可以了。
4. poll：检索新的I/O事件，执行与I/O相关的回调。有可能nodejs会在这里执行一些阻塞逻辑 （**真正需要关心的阶段**）
5. check：setImmediate() 回调函数在这里执行setImmediate。setImmediate并不是立马执行而是当事件循环poll中没有新的事件处理时就会执行该部分 **先执行回调函数，再执行setImmediate**
6. close callbacks: 执行一些关闭的回调函数。如socket.on('close',...)