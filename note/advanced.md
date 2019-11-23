## requestIdleCallback

> 参考: [requestIdleCallback和requestAnimationFrame详解](https://www.jianshu.com/p/2771cb695c81)

当浏览器处于空闲状态时执行，属于低优先级任务。

```js
requestAnimationFrame(todo)
function todo (deadline) {
    // deadline.timeRemaining() 获取当前帧剩余时间
}
```

## requestAnimationFrame

执行时间间隔为屏幕刷新一次所需的时间。例如设备刷新率为75Hz，那么时间间隔为13.3ms（1s/75次）

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

