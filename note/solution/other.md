<a id="fetch取消"></a>

## fetch取消

`AbortController`代表一个控制器对象，允许在需要时终止一个或多个DOM请求。

```js
function ajax (options = {}) {
     const { url, timeout, ...opts } = options
     
     let promises = [fetch(url, opts)]

     const controller = new AbortController()
     const { signal } = controller
     const TIMEOUT = new Response('timeout', { status: 504, statusText: 'timeout' })

     if (timeout) {
          promises.push(() => new Promise(resolve => {
               setTimeout(() => {
                    resolve(TIMEOUT)
                    controller.abort()
               }, timeout)
          }))
     }

     return Promise.race(promises)
}
```

---

<a id="请求并发"></a>

## 请求并发

function request(urls, maxNumber, callback) 要求编写函数实现，根据urls数组内的url地址进行并发网络请求，最大并发数maxNumber,当所有请求完毕后调用callback函数(已知请求网络的方法可以使用fetch api)

```js
function request(urls = [], maxNumber = 1, callback) {
     const len = urls.length
     let index = 0
     let counter = 0

     function _request () {
          while (index < len && maxNumber > 0) {
               maxNumber-- // 任务 -1
               fetch(urls[index++]).finally(() => {
                    maxNumber++ // 执行完后任务池 +1
                    counter++
                    if (counter === len) {
                         return callback()
                    } else {
                         _request()
                    }
               })
          }
     }
     _request()
}
```

---

<a id="LazyMan"></a>

## LazyMan

```
实现一个LazyMan，可以按照以下方式调用:
LazyMan(“Hank”)输出:
Hi! This is Hank!

LazyMan('Hank').sleep(10).eat('dinner')输出
Hi! This is Hank!
//等待10秒..
Wake up after 10
Eat dinner~

LazyMan('Hank').eat('dinner').eat('supper')输出
Hi This is Hank!
Eat dinner~
Eat supper~

LazyMan('Hank').sleepFirst(5).eat('supper')输出
//等待5秒
Wake up after 5
Hi This is Hank!
Eat supper

以此类推。
```

> 链式调用、任务队列、流程控制

```js
function L(name) {
    this.name = name
    this.queue = []
}

L.prototype.next = function () {
    var fn = this.queue.shift()
    fn && fn()
}

L.prototype.eat = function (food) {
    this.queue.push(() => {
        console.log('Eat ' + food + '~')
        this.next()
    })
    return this
}

L.prototype.sleep = function (time) {
    this.queue.push(() => {
        setTimeout(() => {
            console.log('Wake up after ' + time)
            this.next()
        }, time)
    })
    return this
}

L.prototype.sleepFirst = function (time) {
    this.queue.unshift(() => {
        setTimeout(() => {
            console.log('Wake up after ' + time)
            this.next()
        }, time)
    })
    return this
}

function LazyMan (name) {
    return new L(name)
}
```

---

<a id="观察者模式"></a>

## 观察者模式

实现`on`、`emit`、`off`、`once`

```js
class EventEmitter {
    constructor() {
        this.events = {}
    }

    on(name, cb) {
        if (!this.events[name]) {
            this.events[name] = [cb]
        } else {
            this.events[name].push(cb)
        }
    }

    emit(name, ...args) {
        if (this.events[name]) {
            this.events[name].forEach(fn => {
                fn.call(this, ...args)
            })
        }
    }

    off(name, cb) {
        if (this.events[name]) {
            this.events[name] = this.events[name].filter(fn => {
                return fn !== cb
            })
        }
    }

    once(name, fn) {
        const onlyOne = () => {
            fn.apply(this, arguments)
            this.off(name, onlyOne)
        }
        this.on(name, onlyOne)
    }
}
```