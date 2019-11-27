<a id="call"></a>

## call实现

```js
Function.prototype.call = function(context = {}) {
    if (typeof this !== 'function') {
        throw new TypeError('not a func')
    }
    context.fn = this
    var args = [].slice.call(arguments, 1)
    var result = eval('context.fn(' + args +')')
    delete context.fn
    return result
}
```

---

<a id="apply"></a>

## apply实现

```js
Function.prototype.apply = function(context) {
    if (typeof this !== 'function') {
        throw new TypeError('not a func')
    }
    context = context || {}
    context.fn = this
    var args = arguments[1]
    var result
    if (args) {
        result = eval('context.fn(' + args + ')')
    } else {
        result = context.fn()
    }
    delete context.fn
    return result
}
```

<a id="bind"></a>

## bind实现

```js
Function.prototype.bind = function (context) {
    if (typeof this !== 'function') {
        throw new TypeError('not a func')
    }
    var self = this
    // 拿到除了上下文参数的其他所有参数
    var args = Array.prototype.slice.call(arguments, 1)
    var F = function() {}
    F.prototype = this.prototype
    var bound = function() {
        var bindArgs = [].slice.call(arguments)
        return self.apply(this instanceof F ? this : context, args.concat(bindArgs))
    }
    bound.prototype = new F()
    return bound
}
```

---

<a id="filter实现"></a>

## filter实现

```js
Array.prototype.filter = function (fn, context) {
    if (!Array.isArray(this)) {
        throw new TypeError('not array')
    }
    if (typeof fn !== 'function') {
        throw new TypeError(fn + ' not array')
    }
    let result = []
    for (var i = 0; i < this.length; i++) {
        let temp = fn.call(context, this[i], i, this)
        if (temp) {
            result.push(temp)
        }
    }
    return result
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
          var result = [], len = promises.length;
          for (var i = 0; i < len; i++) {
               promises[i].then(function (data) {
                    result.push(data)
                    if(result.length === len){
                         resolve(result)
                    }
               }).catch(reject)
          }
     })
}
```

---

<a id="PromiseAll"></a>