

<a id="序列化"></a>

### 序列化

把对象转换为字节序列的过程称为对象的序列化。

> 由于存在于内存中的对象都是暂时的，无法长期驻存，为了把对象的状态保持下来，这时需要把对象写入到磁盘或者其他介质中，这个过程就叫做序列化。

### 反序列化

把字节序列恢复为对象的过程称为对象的反序列化。

---

<a id="clone"></a>

### 数组的浅拷贝

```
var arr = [1, 2, { n: 'xx' }]

// 第一种 concat
var new1 = arr.concat()

// 第二种 slice
var new2 = arr.slice()
```

### 浅拷贝

```
function shallowCopy(obj) {
  // 判断是否是数组或者对象
  if (typeof obj !== 'object') {
    return
  }

  var newObj = obj instanceof Array ? [] : {}

  for (var key in obj) {
    if (obj.hasOwnProperty(key)) {
      newObj[key] = obj[key]
    }
  }

  return newObj

}
```


### 深拷贝

#### JSON.parse 方法 （序列/反序列）

`const newObj = JSON.parse(JSON.stringify(oldObj))`

> JSON对象parse方法可以将JSON字符串反序列化成JS对象，stringify方法可以将JS对象序列化成JSON字符串

##### 注意

* 无法实现对函数、RegExp等特殊对象的克隆
* 会抛弃对象的constructor,所有的构造函数会指向Object
* 对象有循环引用,会报错

#### 够用的方法

```
function isType(input, type) {
  return Object.prototype.toString.call(input).slice(8, -1) === type
}

function getRegExp(input) {
  var flags = ''
  if (input.global) {
    flags += 'g'
  }

  if (input.ignoreCase) {
    flags += 'i'
  }

  if (input.multiline) {
    flags += 'm'
  }

  return flags
}

function deepClone(parent) {
  var parents = []
  var children = []

  function _clone(parent) {
    if (parent === null) {
      return null
    }

    if (typeof parent !== 'object') {
      return parent
    }

    var child, proto;

    if (isType(parent, 'Array')) {
      child = []
    } else if (isType(parent, 'RegExp')) {
      // 对正则对象做特殊处理
      child = new RegExp(parent.source, getRegExp(parent))
      if (parent.lastIndex) {
        child.lastIndex = parent.lastIndex
      }
    } else if (isType(parent, 'Date')) {
      // 对Date对象做特殊处理
      child = new Date(parent.getTime())
    } else {
      // 处理对象原型
      proto = Object.getPrototypeOf(parent)
      // 利用Object.create切断原型链
      child = Object.create(proto)
    }

    const index = parents.indexOf(parent)

    if (index !== -1) {
      return children[index]
    }

    parents.push(parent)

    children.push(child)

    for (var i in parent) {
      child[i] = _clone(parent[i])
    }

    return child
  }

  return _clone(parent)
}
```

* 测试代码

```
function person(pname) {
  this.name = pname;
}

const Messi = new person('Messi');

function say() {
  console.log('hi');
}

const oldObj = {
  a: say,
  c: new RegExp('ab+c', 'i'),
  d: Messi,
};

oldObj.b = oldObj;


const newObj = clone(oldObj);
console.log(newObj.a, oldObj.a); // [Function: say] [Function: say]
console.log(newObj.b, oldObj.b); // { a: [Function: say], c: /ab+c/i, d: person { name: 'Messi' }, b: [Circular] } { a: [Function: say], c: /ab+c/i, d: person { name: 'Messi' }, b: [Circular] }
console.log(newObj.c, oldObj.c); // /ab+c/i /ab+c/i
console.log(newObj.d.constructor, oldObj.d.constructor); // [Function: person] [Function: person]
```

### 重点阅读(代码来源)

* [面试官:请你实现一个深克隆](https://juejin.im/post/5abb55ee6fb9a028e33b7e0a)

---

<a id="checkArray"></a>

### 判断一个对象是不是数组类型

```
// 第1种
Array.prototype.isPrototypeOf([])

// 第2种
[].constructor == Array

// 第3种
[] instanceof Array

// 第4种
Object.prototype.toString.call([]) === '[object Array]'

// 第5种
Array.isArray([])
```

---

<a id="findArrDup"></a>

### 找出数组中重复出现过的元素

输入`duplicates([1, 2, 4, 4, 3, 3, 1, 5, 3]).sort()`,要求输出`[1, 3, 4]`

```
function duplicates(arr) {
  var result = []
  arr.forEach(function (item) {
    if (arr.indexOf(item) !== arr.lastIndexOf(item) && result.indexOf(item) === -1) {
      result.push(item)
    }
  })
  return result
}
```

---

<a id="max"></a>

### 找出数组中的最大值

* `reduce`

```
function max(arr) {
  return arr.reduce(function (prev, next) {
    return Math.max(prev, next)
  })
}
```

* apply

```
function max(arr) {
  return Math.max.apply(null, arr)
}
```

* ES6

```
function max(arr) {
  return Math.max(...arr)
}
```
---


---

<a id="LazyMan"></a>

### LazyMan

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

> 思路：顺序执行的，是一个队列

```
(function (window, undefined) {
  var taskList = []

  // 类
  function LazyMan() {}

  LazyMan.prototype.eat = function(str){
    subscribe('eat', str);
    return this;
  };

  LazyMan.prototype.sleep = function(num){
    subscribe('sleep', num);
    return this;
  };

  LazyMan.prototype.sleepFirst = function(num){
    subscribe('sleepFirst', num);
    return this;
  };

  // 订阅
  function subscribe() {
    var args = Array.prototype.slice.call(arguments)
    if (args.length < 1) {
      throw new Error('subscribe error')
    }

    var param = {
      msg: args[0], // 事件名
      args: args.slice(1) // 参数列表
    }

    param.msg === 'sleepFirst'
      ? taskList.unshift(param)
      : taskList.push(param)
  }

  // 发布
  function publish() {
    if (taskList.length > 0) {
      run(taskList.shift())
    }
  }

  // 鸭子叫
  function run(options) {
    var msg = options.msg,
      args = options.args

    switch (msg) {
      case 'lazyMan':
        lazyMan.apply(null, args);break;
      case 'eat':
        eat.apply(null, args);break;
      case 'sleep':
        sleep.apply(null, args);break;
      case 'sleepFirst':
        sleepFirst.apply(null, args);break;
      default:
    }
  }

  // 具体方法
  function lazyMan(str) {
    lazyManLog('Hi!This is ' + str + '!');
    publish()
  }

  function eat(str) {
    lazyManLog('Eat ' + str + '~')
    publish()
  }

  function sleep(num) {
    setTimeout(function () {
      lazyManLog('Wake up after ' + num)
      publish()
    }, num * 1000)
  }

  function sleepFirst(num) {
    setTimeout(function () {
      lazyManLog('Wake up after ' + num)
      publish()
    }, num * 1000)
  }

  // 输出文字
  function lazyManLog(str){
    console.log(str);
  }

  window.LazyMan = function (str) {
    subscribe('lazyMan', str)

    setTimeout(function () {
      publish()
    }, 0)

    return new LazyMan()
  }

})(window)
```

* 另外的解法

> 利用 next()

```
function LazyMan(name){
  return new _LazyMan(name);
}

function _LazyMan(name) {
  this.taskList = []
  this.init(name)
  var self = this
  // setTimeout 在宿主所有任务执行完，才会执行
  setTimeout(function () {
    self.next()
  }, 0)
}

_LazyMan.prototype.next = function () {
  var fn = this.taskList.shift()
  if (typeof fn === 'function') {
    fn()
  }
}

_LazyMan.prototype.init = function (str) {
  var self = this
  var fn = (function (str) {
    return function () {
      console.log('Hi! This is ' + str + '!')
      self.next()
    }
  })(str)

  this.taskList.push(fn)
  return this
}

_LazyMan.prototype.eat = function (str) {
  var self = this
  var fn = (function (str) {
    return function () {
      console.log('Ear ' + str + '~')
      self.next()
    }
  })(str)

  this.taskList.push(fn)
  return this
}

_LazyMan.prototype.sleep = function(num) {
  var self = this
  var fn = (function (num) {
    return function () {
      setTimeout(function () {
        console.log('Wake up after ' + num)
        self.next()
      }, num * 1000)
    }
  })(num)

  this.taskList.push(fn)
  return this
};

_LazyMan.prototype.sleepFirst = function(num) {
  var self = this
  var fn = (function (num) {
    return function () {
      setTimeout(function () {
        console.log('Wake up after ' + num)
        self.next()
      }, num * 1000)
    }
  })(num)

  this.taskList.unshift(fn)
  return this
};
```

---

<a id="jsComputeBgColor"></a>

### 用JS代码求出页面上一个元素的最终的background-color，不考虑IE浏览器，不考虑元素float情况

```

```