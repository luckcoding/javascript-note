<a id="线程与进程"></a>

## 线程与进程

* 进程是指一个内存中运行的应用程序，保存了上下文；
* 线程是进程中的一个执行流程。

---

<a id="rAF与rIC"></a>

## rAF与rIC

> 参考: [requestIdleCallback和requestAnimationFrame详解](https://www.jianshu.com/p/2771cb695c81)

### 浏览器每帧的任务

* 处理用户的交互
* JS 解析执行
* 帧开始。窗口尺寸变更，页面滚去等的处理
* requestAnimationFrame(rAF)
* 布局
* 绘制

### requestIdleCallback

浏览器空闲时执行，属于低优先级任务。不建议在里面操作DOM或者Promise操作

```js
var taskId = requestIdleCallack(function (idle) {
    idle.didTimeout // 任务是否超时，boolean
    idle.timeRemaining() // 当前帧剩余时间
}, {
    timeout: 0, // 如果超过这个事件，强制执行
})
cancelIdleCallback(taskId) // 取消任务
```

### requestAnimationFrame

执行时间间隔为屏幕刷新一次所需的时间。

> `setTimeout`回调函数的执行时间不固定，这是由Event Loop决定的，所以会导致卡顿

---

<a id="web-worker"></a>

## web worker

Web Worker 创建多线程环境，worker 线程在后台执行不影响主线程。好处是，处理计算密集型或高延迟的任务不会阻塞主线程。

* 同源限制
* DOM限制。无法读取主线程所在网页的DOM、window、document等对象，但可以读取主线程 navigator、location对象
* 脚本限制。不能执行 alert、 confirm等方法，可使用 XMLHttpRequest
* 文件限制。不能打开本机文件系统

```js
// 主线程
var worker = new Worker('work.js')
worker.postMessage({}) // 发送
worker.onmessage = function (e) {
    console.log(e.data) // 接受
}
worker.onerror(function (e) {}) // 监听错误
worker.terminate() // 关闭子线程

// 子线程
this.addEventListener('message', function (e) { // 接受
    this.postMessage('call') // 发送
})
this.close() // 子线程关闭自己
```

---

<a id="Event-Loop"></a>

## Event-Loop（事件循环）

单线程运行不阻塞的机制，即异步原理。

### 进程与线程

> https://www.cnblogs.com/jokezl/p/10469503.html

**进程**是指一个内存中运行的应用程序，保存了上下文；**线程**是进程中的一个执行流程。JS是单线程，可节省内存、节约上下文切换时间、且没有锁的问题。

### 执行栈

执行栈是一个存储函数调用的栈结构，遵循先进后出的原则。当执行JS代码时，首先会执行一个main函数，然后执行代码，根据先进后出的原则，后执行的函数会先弹出栈。

### 浏览器中的 Event Loop

当执行异步代码时，会被**挂起**并在需要执行的时候**加入到Task（有多种Task）队列**中，一旦执行栈为空，Event Loop就会从Task队列中拿到所需要执行的代码放入执行栈中执行，所以本质上JS中的异步还是同步行为。

不同的任务源会被分配到不同的Task队列中，任务源分为**微任务（jobs）**和**宏任务（task）**。

* 微任务：`promise`、`Object.observe`
* 宏任务：script、`setTimeout`、`setInterval`、`requestAnimationFarme`、UI rendering。

1. 首先执行全局script同步代码，这些同步代码有一些是同步语句，有一些是异步语句（如setTimeout）
2. 全局script代码执行完毕后，执行栈会被清空
3. 从微任务队列中取出队首的回调任务，放入执行栈中执行，执行完后微任务长度减1，直到长度为0。**如果执行微任务过程中又产生了新的微任务，那么加入到队列末尾继续等待被执行完毕**。
4. 所有微任务执行完毕后，取出队首的宏任务，放入执行栈中执行，直到宏任务执行完毕。
5. 重复3-4
6. ...

#### 

> [带你彻底弄懂Event Loop](https://segmentfault.com/a/1190000016278115?utm_source=tag-newest)

#### Nodejs 中的 Event Loop

nodejs 的 Event Loop中，宏任务队列的回调任务有**6个阶段**

1. `timers`执行`setTimeout`和`setinterval`预定的`callback`
2. `I/O callbacks`fs等
3. `idle, prepare`node内部调用
4. `poll` 获取新的I/O事件，node可能会阻塞
5. `check` 执行`setImmediate()`的`callbacks` 
6. `close callbacks` 执行 `xx.on('close', ...)` 这些 `callbacks`

* 宏任务：主要有`times`、`I/O callbacks`、`setImmediate`、`close callbacks` 4个
* 微任务：`process.nextTick`、`promise`、`Object.observe`等

1. 执行全局script的同步代码
2. 执行微任务，先执行`process.nextTick`中的所有任务，再执行 `promise`
3. 执行宏任务，每当一个阶段执行完后，有微任务产生则执行微任务，若无则继续执行一下个阶段的宏任务