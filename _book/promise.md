<a id="Promise"></a>

## Promise实现

```js
class MyPromise {
    constructor(executor) {
        if (typeof executor !== 'function') {
            throw new TypeError('executor is not a func')
        }

        this._status = 'pending'
        this._value = undefined
        this._onFulfilledCallbacks = []
        this._onRejectedCallbacks = []

        const _resolve = (value) => {
            if (this._status === 'pending') {
                // setTimeout(() => {
                    this._value = value
                    this._status = 'fulfilled'
                    this._onFulfilledCallbacks.forEach(fn => fn(this._value))
                // })
            }
        }

        const _reject = (reason) => {
            if (this._status === 'pending') {
                // setTimeout(() => {
                    this._value = reason
                    this._status = 'rejected'
                    this._onRejectedCallbacks.forEach(fn => fn(this._value))
                // })
            }
        }

        try {
            executor(_resolve, _reject)
        } catch (error) {
            _reject(error)
        }
    }

    then(onFulfilled, onRejected) {
        onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : function (){}
        onRejected = typeof onRejected === 'function' ? onFulfilled : function (){}

        return new MyPromise((resolve, reject) => {
            this._onFulfilledCallbacks.push(() => {
                resolve(onFulfilled(this._value))
            })
            this._onRejectedCallbacks.push(() => {
                reject(onRejected(this._value))
            })
        })
    }

    catch (onRejected) {
        return this.then(null, onRejected)
    }
}
```

---

<a id="PromiseAll"></a>

## Promise.all实现

```js
Promise.all = function (promises) {
    if (!Array.isArray(promises)) {
        throw new TypeError('type error')
    }

    return new Promise(function (resolve, reject) {
        var result = [], len = promises.length, count = 0;
        for (let i = 0; i < len; i++) {
            promises[i]().then(function (data) {
                count++
                result[i] = data
                if (count === len) {
                    resolve(result)
                }
            }).catch(reject)
        }
    })
}
```

---

<a id="PromiseRetry"></a>

## Promise.retry实现

如果不成功，多尝试 times 次

```js
Promise.retry = function (fn, times, delay) {
    return new Promise((resolve, reject) => {
        let error
        let attempt = () => {
            if (times === 0) {
                reject(error)
            } else {
                fn().then(resolve).catch(e => {
                    times--
                    error = e
                    setTimeout(attempt, delay)
                })
            }
        }
        attempt()
    })
}
```

---

<a id="Promise串行"></a>

## Promise串行

```js
function serialPromises(promises) {
    function dispatch (i, ...args) {
        const fn = promises[i]
        const next = (...nextArgs) => {
            dispatch(i + 1, nextArgs)
        }
        if (fn) {
            fn(...args).then(next).catch(next)
        }
    }
    dispatch(0)
}
```

---

<a id="promisify"></a>

## promisify

callback包装成promise

```js
// fs.read(param, function (err, data) {})
function promisify (fn, context) {
    return (...args) => {
        return new Promise((resolve ,reject) => {
            fn.apply(context, [...args, (err, res) => {
                return err ? reject(err) : resolve(res)
            }])
        })
    }
}
```