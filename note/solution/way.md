<a id="依赖注入"></a>

## 依赖注入

```js
function DI(deps) {
    this.deps = deps
}
DI.prototype.inject = function (func) {
    // 取参数名
    var self = this
    var args = /^[^(]+\(([^)]+)/.exec(func.toString())

    args = args ? args[1].split(',').map(function (arg) {
        return self.deps[arg.trim()]
    }) : []

    return function () {
        return func.apply(self, args)
    }
}

// demo
var deps = {
    dep1: function () { return 'dep1' },
    dep2: function () { return 'dep2' },
    dep3: function () { return 'dep3' },
}
var di = new DI(deps)

var myFunc = di.inject(function (dep2, dep1, dep3) {
    return [dep1(), dep2(), dep3()]
})
```

---

<a id="柯里化"></a>

## 柯里化

```js
function curry (fn, ...args) {
    if (fn.length <= args.length) {
        return fn(...args)
    }
    return function (...innerArgs) {
        return curry(fn, ...args, ...innerArgs)
    }
}
```

---

<a id="防抖和节流"></a>

## 防抖和节流

### 防抖

> 使用场景: 联想搜索时避免立即触发查询.

```js
function debounce (func, dely = 0) {
    let timer = null
    return (...args) => {
        timer && clearTimeout(timer)
        timer = setTimeout(() => {
            func.apply(this, args)
        }, dely)
    }
}
```

### 节流

> 使用场景: 滚动时触发接口请求.

```js
function throttle (func, dely = 0) {
    let canRun = true
    return (...args) => {
        if (canRun) {
            canRun = false
            setTimeout(() => {
                func.apply(this, args)
                canRun = true
            }, dely)
        }
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
            const current = obj[key]
            if (typeof current === 'object' && current !== null) {
                newObj[key] = deepClone(current)
            } else {
                newObj[key] = current
            }
        }
    }

    return newObj
}
```

---

<a id="继承"></a>

## 继承

```js
// es5
function Parent(name, age) {
    this.name = name
    this.age = age
}
Parent.prototype.say = function () {
    console.log('i am' + this.name)
}

function Child(name, age, sex) {
    Parent.call(this, name, age) // 绑定this到父类
    this.sex = sex
}
Child.prototype = Object.create(Parent.prototype)
Child.prototype.constructor = Child // 修复构造函数指向

// es6
class Parent {
    constructor(name, age) {
        this.name = name
        this.age = age
    }
    say() {
        console.log('i am' + this.name)
    }
}
class Child extends Parent {
    constructor(name, age, sex) {
        super(name, age) // 子类实例的构建，基于父类实例，所以必须调用super
        this.sex = sex
    }
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
