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

<a id="函数柯里化实现"></a>

## 函数柯里化实现

```js
function curry (fn) {
     var len = fn.length // 形参个数
     return function currying () {
          var args = Array.prototype.slice.call(arguments)
          if (args.length >= len) {
               return fn.apply(null, args)
          } else {
               return function () {
                    var args2 = Array.prototype.slice.call(arguments)
                    return currying.apply(null, args.concat(args2))
               }
          }
     }
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

<a id="防抖和节流"></a>

## 防抖和节流

### 防抖

> 使用场景: 联想搜索时避免立即触发查询.

```js
function debounce (func, time) {
    let timer = null
    return function () {
        if (timeout) {
            clearTimeout(timeout)
        }
        timeout = setTimeout(() => {
            func.apply(this, arguments)
        }, time)
    }
}
```

### 节流

> 使用场景: 滚动时触发接口请求.

```js
function throttle (func, time) {
    let canRun = true
    return function () {
        if (!canRun) {
            return
        }
        canRun = false
        setTimeout(() => {
            func.apply(this, arguments)
            canRun = true
        }, time)
    }
}
```

---

<a id="深拷贝"></a>

## 深拷贝

```js
// 方法一
function deepClone(arr){
    return JSON.parse(JSON.stringify(arr))
}

// 方法二
function deepClone(obj) {
    var newObj = Array.isArray(obj) ? [] : {}

    for (var key in obj) {
        if (obj.hasOwnProperty(key)) {
            if (typeof obj[key] === 'object' && obj[key] !== null) {
                newObj[key] = deepClone(obj[key])
            } else {
                newObj[key] = obj[key]
            }
        }
    }

    return newObj
}
```