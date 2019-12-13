<a id="fetch取消"></a>

## fetch取消

`AbortController`代表一个控制器对象，允许在需要时终止一个或多个DOM请求。

```js
function ajax (url = '', options = {}) {
    const { timeout, ...opts } = options

    const ctrl = new AbortController()
    const signal = ctrl.signal
    const TIMEOUT = new Response('timeout', { status: 504, statusText: 'timeout' })
    
    const send = fetch(url, { ...rest, signal })

    let promises = [send]

    if (timeout) {
        promises.push(() => new Promise(resolve => {
            setTimeout(() => {
                resolve(TIMEOUT)
                ctrl.abort()
            }, timeout)
        }))
    }

    this.abort = function () {
        promises.push(() => new Promise(resolve => {
            setTimeout(() => {
                resolve(TIMEOUT)
                ctrl.abort()
            })
        }))
    }

    return new Promise((resolve) => {
        setTimeout(() => {
            resolve(Promise.race(promises))
        })
    })
}
```

---

<a id="请求并发"></a>

## 请求并发

function request(urls, maxNumber, callback) 要求编写函数实现，根据urls数组内的url地址进行并发网络请求，最大并发数maxNumber,当所有请求完毕后调用callback函数(已知请求网络的方法可以使用fetch api)

```js
function request(urls = [], maxNumber = 1, callback) {
    const len = urls.length
    let count = 0, index = 0

    async function _request() {
        while (index < len && maxNumber > 0) {
            maxNumber-- // 并发数 -1
            try {
                await fetch(urls[index++])
            } finally {
                count++
                maxNumber++
                if (count === len) {
                    return callback()
                } else {
                    _request()
                }
            }
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

class LazyManBase {
    constructor(name) {
        this.name = name
        this.queue = []
        this.say()
        setTimeout(() => {
            this.next()
        })
    }

    next () {
        const fn = this.queue.shift()
        fn && fn()
    }

    say () {
        this.queue.push(() => {
            console.log('Hi This is' + this.name + '!')
            this.next()
        })
    }

    eat(food = '') {
        this.queue.push(() => {
            console.log('Eat ' + food + '~')
            this.next()
        })
        return this
    }

    sleep(time = 0) {
        this.queue.push(() => {
            setTimeout(() => {
                console.log('Wake up after ' + time)
                this.next()
            }, time * 1000)
        })
        return this
    }

    sleepFirst(time = 0) {
        this.queue.unshift(() => {
            setTimeout(() => {
                console.log('Wake up after ' + time)
                this.next()
            }, time * 1000)
        })
        return this
    }
}

function LazyMan (name) {
    return new LazyManBase(name)
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

    once(name, cb) {
        const onlyOne = () => {
            cb.apply(this, arguments)
            this.off(name, onlyOne)
        }
        this.on(name, onlyOne)
    }
}
```