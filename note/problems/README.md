<a id="oneBorder"></a>

### 一像素边框

```
.border-1px {
    position: relative;
}
.border-1px:after {
	 display: block;
    position: absolute;
    left: 0;
    right: 0;
    width: 100%;
    border-top: 1px solid red;
    content: '';
    // transform: scaleY(0.7);
    // -webkit-transform: scaleY(0.7);
}

// 多媒体查询
@media (-webkit-min-device-pixel-radio: 1.5), (min-device-pixel-radio: 1.5) {
    border-1px:after {
        -webkit-transform: scaleY(0.7);
        transform: scaleY(0.7);
    }
}

@media (-webkit-min-device-pixel-radio: 2), (min-device-pixel-radio: 2) {
    border-1px:after {
        -webkit-transform: scaleY(0.5);
        transform: scaleY(0.5);
    }
}
```

---

<a id="BFC"></a>

### 形成BFC(Block Formatting Context)(块级格式化上下文)的几种方式

1. `float`为`left|right`
2. `overflow`为`hidden|auto|scroll`
3. `display`为`table-cell|table-caption|inline-block`
4. `posiotion`为`absolute|fixed`

---

<a id="clearfix"></a>

### 不同的清除浮动的技巧

* 1. 添加新元素

```
<div class="outer">
  <div class="div1"></div>
  <div class="div2"></div>
  <div class="div3"></div>
  <div class="clearfix"></div>
</div>

.clearfix {
 clear: both
}

```

* 2. 为父元素增加样式

```
<div class="clearfix">
  <div class="div1"></div>
  <div class="div2"></div>
  <div class="div3"></div>
</div>

.clearfix {
  overflow: auto;
  zoom: 1; // 处理兼容性
}
```

* 3. :after 伪元素烦那个发（作用于父元素）

```
<div class="outer">
  <div class="div1"></div>
  <div class="div2"></div>
  <div class="div3"></div>
</div>

.outer {
  zoom: 1;
  &:after {
    display: block;
    height: 0;
    clear: both;
    content: '';
    visibility: hidden;
  }
}
```

---

<a id="线程与进程的区别"></a>

### 线程与进程的区别，什么是线程同步

* 一个程序至少有一个进程，一个进程至少有一个线程。线程的划分尺度小于进程，使得多线程程序的并发性高。
* JS只有一个线程。
* 进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。
* 线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。
* 从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。


---

<a id="asyncDefer"></a>

### async与defer区别

`<script async></script>`
`<script defer></script>`

异步(async) 脚本将在其加载完成后立即执行，而 延迟(defer) 脚本将等待 HTML 解析完成后，并按加载顺序执行。


---

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

<a id="tree"></a>

> 数据源

```
[{
	"name": "1",
	"children": [{
		"name": "1-1",
		"children": [{
			"name": "1-1-1"
		}, {
			"name": "1-1-2"
		}]
	}, {
		"name": "1-2",
		"children": [{
			"name": "1-2-1"
		}]
	}]
}, {
	"name": "2"
}, {
	"name": "3",
	"children": [{
		"name": "3-1"
	}, {
		"name": "3-2",
		"children": [{
			"name": "3-2-1"
		}, {
			"name": "3-2-2"
		}]
	}]
}]
```

<a id="广度优先遍历"></a>

### 广度优先遍历

![bsf](../../asset/bfs.png)

[数据源](#tree)

```
function doBFS(source) {
  const dataSource = JSON.parse(JSON.stringify(source))
  let stack = [...dataSource]
  let item
  while (stack.length) {
    item = stack.shift()
    
    console.log(item.name)

    if (item.children && item.children.length) {
      stack = stack.concat(item.children)
    }
  }
}

// 打印

1
2
3
1-1
1-2
3-1
3-2
1-1-1
1-1-2
1-2-1
3-2-1
3-2-2
```

<a id="深度优先遍历"></a>

### 深度优先遍历

![dsf](../../asset/dfs.png)

[数据源](#tree)

```
function doDFS(source) {
  const dataSource = JSON.parse(JSON.stringify(source))
  let stack = [...dataSource]
  let item
  while (stack.length) {
    item = stack.shift()

    console.log(item.name)

    if (item.children && item.children.length) {
      stack = item.children.concat(stack)
    }
  }
}

// 打印
1
1-1
1-1-1
1-1-2
1-2
1-2-1
2
3
3-1
3-2
3-2-1
3-2-2
```

<a id="递归遍历"></a>

### 递归遍历

[数据源](#tree)

```
function loop(source) {
  const dataSource = JSON.parse(JSON.stringify(source))
  let stack = [...dataSource]
  for (let i = 0, len = dataSource.length; i < len; i++) {

    console.log(dataSource[i].name)

    const children = dataSource[i].children
    if (children && children.length > 0) {
      loop(children)
    }
  }
}

// 打印，同上深度优先遍历
1
1-1
1-1-1
1-1-2
1-2
1-2-1
2
3
3-1
3-2
3-2-1
3-2-2
```

---

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

#### 简单方法（不考虑特殊情况）

```
function deepCopy(obj) {
  // 判断是否是数组或者对象
  if (typeof obj !== 'object') {
    return
  }

  var newObj = obj instanceof Array ? [] : {}

  for (var key in obj) {
    if (obj.hasOwnProperty(key)) {
      newObj[key] = typeof obj[key] === 'object' ? deepCopy(obj[key]) : obj[key]
    }
  }

  return newObj

}
```

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

<a id="unique"></a>

### 数组去重

* `filter` + `indexOf`

```
function unique(arr) {
  var res = arr.filter(function (item, index, array) {
    // 如果当前下标与数组内找到当前值的下标不等，则排除
    return array.indexOf(item) === index
  })
  return res
}
```

* `filter` + `sort`

```
function unique(arr) {
  return arr.concat().sort().filter(function (item, index, array) {
    // 按顺序排序，如果当前值与前一个值相等，则排除
    return !index || item !== array[index - 1]
  })
}
```

* ES6

```
function unique(arr) {
  return [...new Set(arr)]
}
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

<a id="arrayrandom"></a>

### 打乱数组

```
[].sort(function() {
  return 0.5 - Math.random()
})
```

---

<a id="flatten"></a>

### 数组扁平化

```
function flatten(arr) {
  while (arr.some(function (item) {
    return Array.isArray(item)
  })) {
    arr = [].concat(...arr)
  }

  return arr
}
```

---

<a id="BubbleSort"></a>

### 冒泡排序

```
function bubbleSort(arr) {
  if (arr.length <= 1) {
    return arr
  }

  var len = arr.length

  for (var i = 0; i <= len - 1; i++) {
    for (var j = 0; j <= len -1; j++) {
      if (arr[j + 1] < arr[j]) {
        // var temp
        // temp = arr[j]
        // arr[j] = arr[j + 1]
        // arr[j + 1] = temp
        // 不通过临时变量实现变量交换
        arr[j + 1] = [arr[j], arr[j] = arr[j + 1]][0]
      }
    }
  }

  return arr
}
```

<a id="BubbleSort"></a>

### 快速排序

```
function quickSort(arr) {
  if (arr.length <= 1) {
    return arr
  }
  
  // 计算参照位
  var cIndex = Math.floor(arr.length / 2)

  var c = arr.splice(cIndex, 1)
  // 注意 Number([1]) === 1
  var l = []
  var r = []

  for (var i = 0; i < arr.length; i++) {
    if (arr[i] < c) {
      l.push(arr[i])
    } else {
      r.push(arr[i])
    }
  }

  return quickSort(l).concat(c, quickSort(r))
}
```

---

<a id="InsertionSort"></a>

### 插入排序

```
function insertionSort (arr) {

}
```

---

<a id="binarySearch"></a>

### 二分搜索法

也称折半搜索，是一种在有序数组中查找特定元素的搜索算法。

实现步骤：

　　1. 首先从数组中间开始查找对比，若相等则找到，直接返回中间元素的索引。

　　2. 若查找值小于中间值，则在小于中间值的那一部分执行步骤1的操作。

　　3. 若查找值大于中间值，则在大于中间值的那一部分执行步骤1的操作。

　　4. 否则，返回结果为查不到，返回-1。

```
// 递归实现
function binarySeach(target, arr, start, end) {
  var start = start || 0
  var end = end || arr.length - 1
  var mid = parseInt(start + (end - start) / 2)

  // 排除查找对象在数组之外
  if (target > arr[end] || target < arr[start]) {
    return -1
  } else if (target === arr[mid]) {
    return true
  } else if (target > arr[mid]) {
    return binarySeach(target, arr, mid + 1, end)
  } else {
    return binarySeach(target, arr, start, mid - 1)
  }
}

// 不适用递归
function binarySeach(target, arr, start, end) {
  var start = start || 0
  var end = end || arr.length - 1
  
  // 排除查找对象在数组之外
  if (target > arr[end] || target < arr[start]) {
    return -1
  }

  while (start <= end) {
    var mid = parseInt(start + (end - start) / 2)

    if (target === arr[mid]) {
      return mid
    } else if (target > arr[mid]) {
      start = mid + 1
    } else {
      end = mid - 1
    }
  }
}
```

#### 无序的二分搜索

```
function binarySeach(target, arr) {
  while (arr.length > 0) {
    // 快速排序
    var left = []
    var right = []
    var pivot = arr[0]

    for (var i = 1; i < arr.length; i++) {
      var item = arr[i]
      item > pivot ? right.push(item) : left.push(item)
    }

    if (target === pivot) {
      return true
    } else if (target > pivot) {
      arr = right
    } else {
      arr = left
    }
  }
}
```

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

<a id="nanNullUndefined"></a>

### 几道关于null、NaN、undefined的试题

```javascript

```

---

# 参考

* [2017前端面试题及答案总结|掘金技术征文](https://juejin.im/post/59be99a0f265da0644289dde)
* [常见前端面试题及答案](http://www.cnblogs.com/syfwhu/p/4434132.html#)