<a id="call"></a>

## call实现

```js
Function.prototype.call = function(context) {
    if (typeof this !== 'function') {
        throw new TypeError('not a func')
    }
    context = context || {}
    context.fn = this
    var args = []
    for (var i = 1; i < arguments.length; i++) {
        args.push(arguments[i])
    }
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
    // 原型处理
    function F() {}
    F.prototype = this.prototype
    function bound() {
        var bindArgs = [].slice.call(arguments)
        context = this instanceof F ? this : context
        return self.apply(context, args.concat(bindArgs))
    }
    bound.prototype = new F() // 继承原型
    return bound
}
```

---

<a id="filter实现"></a>

## filter实现

```js
Array.prototype.filter = function (fn, context) {
    if (typeof fn !== 'function') {
        throw new TypeError(fn + ' is not function')
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

<a id="instanceof"></a>

## instanceof实现

```js
function _instanceof (left, right) {
    var proto = left.__proto__
    var prototype = right.prototype
    while(true) {
        if(proto === null) {
            return false
        }
        if(proto == prototype) {
            return true
        }
        proto = proto.__proto__
    }
}
```

---

<a id="new"></a>

## new实现

```js
function _new (Fn) {
    // 1.创建一个空对象
    var obj = {}
    // 2.链接到该函数的原型
    obj.__proto__ = Fn.prototype
    // 3.绑定this
    var result = Fn.call(obj)
    // 4.返回新对象
    return typeof result === 'object' ? result : obj
}
```