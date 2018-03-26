<a id="线程与进程的区别"></a>

### 线程与进程的区别

一个程序至少有一个进程，一个进程至少有一个线程。JS只有一个线程。

<a id="函数防抖"></a>

### 函数防抖

> 使用场景: 联想搜索时避免立即触发查询.

```
/*
 * func 要调用的函数
 * wait 防抖时间
 * immediate 布尔值,是否立即执行
 */
var debounce = function (func, wait, immediate) {
  // 定义一个判定时间
  var timeout
  return function () {
    var context = this
    var args = arguments

    if (timeout) {
      clearTimeout(timeout)
    }

    if (immediate) {
      var callNow = !timeout
      timeout = setTimeout(function () {
        timeout = null
      }, wait)

      if (callNow) {
        func.apply(context, args)
      }
    } else {
      timeout = setTimeout(function () {
        func.apply(context, args)
      }, wait)
    }
  }
}


/**
 * ================
 * demo
 */
var action = debounce(function () {
  console.log(arguments)
}, 1000);
action() // 连续间隔1秒内执行，只会打印最后一次

var action = debounce(function () {
  console.log(arguments)
}, 1000, true);
action() // 连续间隔1秒内执行，只会打印第一次
```

<a id="函数节流"></a>

### 函数节流

> 使用场景: 滚动时触发接口请求.

```
/*
 * func 要调用的函数
 * wait 节流间隔时间
 */
var throttle = function (func, wait) {
  var timeout
  var prevTime
  return function () {
    var nowTime = Date.now()
    var context = this
    var args = arguments
    var remaining = wait - (nowTime - prevTime) // 剩余时间

    if (remaining <= 0 || remaining > wait) {
      if (timeout) {
        clearTimeout(timeout)
        timeout = null
      }
      prevTime = nowTime
      func.apply(context, args)
      if (!timeout) {
        context = args = null
      }
    } else if (!timeout) {
      timeout = setTimeout(function () {
        prevTime = Date.now()
        timeout = null
        func.apply(context, args)
      }, remaining)
    }

  }
}
```

---