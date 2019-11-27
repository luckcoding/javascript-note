<a id="线程与进程"></a>

## 线程与进程

* 一个程序至少有一个进程，一个进程至少有一个线程。线程的划分尺度小于进程，使得多线程程序的并发性高。
* JS只有一个线程。
* 进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。
* 线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。
* 从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

---

<a id="requestIdleCallback"></a>

## requestIdleCallback

> 参考: [requestIdleCallback和requestAnimationFrame详解](https://www.jianshu.com/p/2771cb695c81)

当浏览器处于空闲状态时执行，属于低优先级任务。

```js
requestAnimationFrame(todo)
function todo (deadline) {
    // deadline.timeRemaining() 获取当前帧剩余时间
}
```

---

<a id="requestAnimationFrame"></a>

## requestAnimationFrame

执行时间间隔为屏幕刷新一次所需的时间。例如设备刷新率为75Hz，那么时间间隔为13.3ms（1s/75次）

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

* 微任务：`process.nextTick(node)`、`promise`、`MutationObserver`、`Object.observer`
* 宏任务：`script`、`setTimeout`、`setInterval`、`setImmediate`、I/O、UI rendering。

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

1. `timers` 执行 `setTimeout` 和 `setinterval` 预定的 `callback`
2. `I/O callbacks` fs等
3. `idle, prepare` node内部调用
4. `poll` 获取新的I/O事件，node可能会阻塞
5. `check` 执行 `setImmediate()` 的 `callbacks` 
6. `close callbacks` 执行 `xx.on('close', ...)` 这些 `callbacks`

* 宏任务：主要有 `times`、`I/O callbacks`、`check`、`close callbacks` 4个
* 微任务：`next tick queue(process.nextTick(callback))`、`other queue(如promise)`  等2个

1. 执行全局script的同步代码
2. 执行微任务，先执行`next tick queue`中的所有任务，再执行 `other queue`
3. 执行宏任务，每当一个阶段执行完后，有微任务产生则执行微任务，若无则继续执行一下个阶段的宏任务

---

<a id="设计模式"></a>

## 设计模式

* 观察者模式（发布-订阅模式）。通过定义依赖关系，当状态发送改变时，订阅者会收到通知；
* 工厂模式。返回一个对象，创建不同的引用类型；
* 构造函数模式。属性绑定到`this`，方法绑定到`prototype`，使用`new`来新增实例；
* 单例模式。产生一个类的唯一实例。

---

<a id="solid原则"></a>

## solid原则

面向对象编程和面向对象设计的五大原则：

* S 单一职责（SRP）：一个类只负责一件事情
* O 开放封闭（OCP）：封闭修改、开放扩展
* L 里式替换（LSP）：一个对象可由其子类代替
* I 接口分离（ISP）：客户不应该被强迫依赖它不实用的方法
* D 依赖倒置（DIP）：抽象不依赖具体，具体依赖于抽象