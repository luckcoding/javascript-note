### fetch取消

`AbortController`代表一个控制器对象，允许在需要时终止一个或多个DOM请求。

```js
const ctrl = new AbortController()
const signal = ctrl.signal
const timeoutResp = new Response("timeout", { status: 504, statusText: "timeout" })

const abort = (duration = 0) => new Promise(resolve => {
	setTimeout(() => {
		resolve(timeoutResp)
		ctrl.abort()
	}, duration)
})

const nextFetch = (url = '', options = {}) => {
	const { timeout = 10000, ...rest } = options
	
	const send = fetch(url, rest)
	
	return timeout ? Promise.race([abort(timeout), send]) : send
}

```

<a id="Event-Loop"></a>

### Event Loop

#### 进程与线程

> https://www.cnblogs.com/jokezl/p/10469503.html

**进程**是指一个内存中运行的应用程序，保存了上下文；**线程**是进程中的一个执行流程。JS是单线程，可节省内存、节约上下文切换时间、且没有锁的问题。

#### 执行栈

执行栈是一个存储函数调用的栈结构，遵循先进后出的原则。当执行JS代码时，首先会执行一个main函数，然后执行代码，根据先进后出的原则，后执行的函数会先弹出栈。

#### 浏览器中的 Event Loop

当执行异步代码时，会被**挂起**并在需要执行的时候**加入到Task（有多种Task）队列**中，一旦执行栈为空，Event Loop就会从Task队列中拿到所需要执行的代码放入执行栈中执行，所以本质上JS中的异步还是同步行为。

不同的任务源会被分配到不同的Task队列中，任务源分为**微任务（jobs）**和**宏任务（task）**。

* 微任务：`process.nextTick(node)`、`promise`、`MutationObserver`、`Object.observer`
* 宏任务：`script`、`setTimeout`、`setInterval`、`setImmediate`、I/O、UI rendering。

1. 首先执行全局script同步代码，这些同步代码有一些是同步语句，有一些是异步语句（如setTimeout）
2. 全局script代码执行完毕后，执行栈会被清空
3. 从微任务队列中取出队首的回调任务，放入执行栈中执行，执行完后微任务长度减1，直到长度为0。**如果执行微任务过程中又产生了新的微任务，那么加入到队列末尾继续等待被执行完毕**。
4. 所有微任务执行完毕后，取出队首的宏任务，放入执行栈中执行，直到宏任务执行完毕。
5. 重复3-4
6. ...

#### 

> [带你彻底弄懂Event Loop](https://segmentfault.com/a/1190000016278115?utm_source=tag-newest)

#### Nodejs 中的 Event Loop

nodejs 的 Event Loop中，宏任务队列的回调任务有**6个阶段**

1. `timers` 执行 `setTimeout` 和 `setinterval` 预定的 `callback`
2. `I/O callbacks` fs等
3. `idle, prepare` node内部调用
4. `poll` 获取新的I/O事件，node可能会阻塞
5. `check` 执行 `setImmediate()` 的 `callbacks` 
6. `close callbacks` 执行 `xx.on('close', ...)` 这些 `callbacks`

* 宏任务：主要有 `times`、`I/O callbacks`、`check`、`close callbacks` 4个
* 微任务：`next tick queue(process.nextTick(callback))`、`other queue(如promise)`  等2个

1. 执行全局script的同步代码
2. 执行微任务，先执行`next tick queue`中的所有任务，再执行 `other queue`
3. 执行宏任务，每当一个阶段执行完后，有微任务产生则执行微任务，若无则继续执行一下个阶段的宏任务

<a id="数字千分位处理"></a>

### 数字千分位处理

> `Number.prototype.toLocaleString()`也可做到

#### 1.使用正则

* `?=`表示正向引用，匹配到的内容不获取，并作为下一次查询的开始
*  `?:`匹配到的内容不获取，也不作为下次查询的开始
* `$&`表示与正则表达式相匹配的内容

```js
function format(num) {
	var reg = /\d{1,3}(?=(\d{3})+(?:$|\.))/g
	return (num + '').replace(reg, '$&,')
}
```

#### 2.使用reduce

```js
function format (num) {
	var str = num + ''
	return str
		.split("") // 拆成数组
		.reverse() // 反转
		.reduce(function (prev, next, index) {
			return ((index % 3) ? next : (next + ',')) + prev
		})
}
```

<a id="Symbol"></a>

### Symbol

* `Symbol` 实例都是唯一的
* `Symbol.for()`可以注册或获取一个全局`Symbol`实例
* 以`Symbol`为key的对象不可被`Object.keys`、`for in`枚举到

<a id="隐式转化"></a>

### `==` 的隐式转化

* null、undefined不会进行类型转换，但相等
* 如果有 boolean 值，true 转为 1，false 转为 0
* NaN 不与任何值相等
* 字符串与数字比较，把字符串转为数字
* 如果对象与字符串或数字比较，对象先`valueOf()`再`toString()`取到原始值后进行比较

<a id="数据类型转换"></a>

### 数据类型转换

* 函数转换：`parseInt()`、`parseFloat()`、`toString()`
* 强类型转换：`Boolean()`、`Number()`、`String()`
* 若类型转换：`==`、`-`、`+`、`if()`

<a id="继承"></a>

## 实现一个子类实例可以继承父类的所有方法

```js
// 父类
function Father (name) {
     // 属性
     this.name = name || '->father'
      // 实例方法
     this.sayName = function () {
          console.log(this.name)
     }
}
// 原型方法
Father.prototype.age = 18
Father.prototype.sayAge = function () {
     console.log(this.age)
}

// 1、原型链继承（将父类的实例作为子类的原型）
function Son(name) {
     this.name = name || '->son'
}
Son.prototype = new Father()
// console =>
var son = new Son('son')
console.log(son.name) // son
son.sayAge() // 18
son.sayName() // son
console.log(son.age) // 18
console.log(son instanceof Father) // true
console.log(son instanceof Son) // true

// 2、构造继承（复制父类的实例属性给子类）
function Son(name) {
     Father.call(this)
     this.name = name || '->son'
}
// console =>
var son = new Son('son')
console.log(son.name) // son
son.sayAge() // throw Error
son.sayName() // son
console.log(son.age) // undefined
console.log(son instanceof Father) // false
console.log(son instanceof Son) // true

// 3、实例继承（为父类实例添加新特征，并作为子类实例返回）
function Son(name) {
     var father = new Father()
     father.name = name || '->son'
     return father
}
// console =>
var son = new Son('son')
console.log(son.name) // son
son.sayAge() // 18
son.sayName() // son
console.log(son.age) // 18
console.log(son instanceof Father) // true
console.log(son instanceof Son) // false

// 4、拷贝继承（对父类实例中的方法与属性，拷贝给子类的原型）
function Son(name) {
     var father = new Father()
     for (var k in father) {
          Son.prototype[k] = k
     }
     Son.prototype.name = name || '->son'
}
// console =>
var son = new Son('son')
console.log(son.name) // son
son.sayAge() // 18
son.sayName() // son
console.log(son.age) // 18
console.log(son instanceof Father) // false
console.log(son instanceof Son) // true

// 5、组合继承（调用父类构造函数，继承父类的属性并保留传参，然后将父类实例作为子类原型）【缺点：既是子类实例又是父类实例】
function Son(name) {
     Father.call(this)
     this.name = name || '->son'
}
Son.prototype = new Father()
// 修复构造函数指向
Son.prototype.constructor = Son
// console =>
var son = new Son('son')
console.log(son.name) // son
son.sayAge() // 18
son.sayName() // son
console.log(son.age) // 18
console.log(son instanceof Father) // true
console.log(son instanceof Son) // true

// 6、寄生组合继承（完美）
function Son(name) {
     Father.call(this)
     this.name = name || '->son'
}
var None = function () {}
None.prototype = Father.prototype
Son.prototype = new None() // 将实例作为子类的原型
Son.prototype.constructor = Son // 修复构造函数指向
// console =>
var son = new Son('son')
console.log(son.name) // son
son.sayAge() // 18
son.sayName() // son
console.log(son.age) // 18
console.log(son instanceof Father) // true
console.log(son instanceof Son) // true
```


* 22.css实现正方形div水平垂直居中
* 23.koa1的原理
45、点击table的td显示td内容
51. 杭州一面:
       Koa中间件机制及代码实现
       给定一个数组，一个期望值，找到数组中两个相加等于期望值

53.深圳前端一面：
          2. React Hook原理
          3. 列表diff中key的作用
54.成都前端：
        1、换行字符串格式化
        6、事件委托
55.成都前端：
     1，React Hook, Fiber Reconciler
     getDerivedStateFromPros 为什么是Static
      2，redux 异步
      3，redux 异步中间件原理
      4，express koa 中间件原理
56.北京前端一面：
  3. express ctx 中间键代码实现
  5. 请实现如下的函数，可以批量请求数据，所有的 URL 地址在 urls 参数中，同时可以通过 max 参数      控制请求的并发度，当所有请求结束之后，需要执行 callback 回调函数。发请求的函数可以直接        使用 fetch 即可 

57. 南京前端1面：
 1.事件循环
     2.react diff算法，key的作用，setData的机制，事件合成

4.实现一个方法，参数是一个generator函数，执行结果是执行完所有generator中的yield
5.获取页面所有img并且下载

6.两个同源tab之间的交互，数据同步

58.上海前端一面：

<a id="Promise.all"></a>

### 实现 `Promise.all()`

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

5. wepack-dev-server 热更新功能实现原理

<a id="arguments"></a>

### `arguments`是数组吗，如果不是怎么变为数组

> 是类数组，还有NodeList等

* `arguments`是个由实参组成的类数组，可以按数组一样访问实参，如`arguments[0]`
* 有自己独特的属性，如`callee`，`callee`是指当前正在执行的函数引用
* `length`是实参的个数（`arguments.callee.length`是当前正在执行的函数的**形参**个数，类似`function.length`）

```js
// 第一种
Array.prototype.slice.apply(arguments)

// 第二种
Array.prototype.concat.apply([], arguments)

// 第三种
Array.apply([], arguments)

// 第四种
function args2Array() {
	var argsArr = []
	for (var i = 0, len = arguments.length; i < len; i++) {
		argsArr[i] = arguments[i]
	}
	return argsArr
}
```

### 如何实现for循环内定时器依次输出123

> 此题考查作用域

```
// 第一种
for (let i = 1; i <= 3; i++) {
	setTimeout(function () {
		console.log(i)
	}, 0)
}

// 第二种
for (var i = 1; i <= 3; i++) {
	(function (j) {
		setTimeout(function () {
			console.log(j)
		}, 0)
	})(i)
}
```

<a id="bind"></a>

### `bind` 实现

```js
Function.prototype.bind = function (newThis) {
     var self = this
     // 拿到除了上下文参数的其他所有参数
     var args1 = Array.prototype.slice.call(arguments, 1)
     return function () {
          var args2 = Array.prototype.slice.call(arguments)
          return self.apply(newThis, args.concat(args2))
     }
}
```

71. 动态规划算法

59. 北京前端一面：
     1. function request(urls, maxNumber, callback) 要求编写函数实现，根据urls数组内的url地址进行并发网络请求，最大并发数maxNum   ber,当所有请求完毕后调用callback函数(已知请求网络的方法可以使用fetch api)

    4.二叉树路径总和（leetcode 112）

    5. 给定一个不含重复数字的数组arr,指定个数n,目标和sum,判断是否含有由n个不同数字相加得到sum的情况（leetcode 40 变种， 数   字不得重复使用）
60. 上海前端一面：

9. Nodejs express koa

62.成都前端笔试：1、输入一个日期 返回几秒前 几天前或者几月前；

63. 北京前端一面
①['a','b'],['A','B'],['1','0']，输出['aA1','aA0','aB1','aB0','bA1','bA0','bB1','bB0']，算法的排列组合问题

④请实现如下的函数，可以批量请求数据，所有的 URL 地址在 urls 参数中，同时可以通过 max 参数，控制请求的并发度，实现max个请求执行完之后再执行下max个请求，当所有请求结束之后，需要执行 callback 回调函数。发请求的函数可以直接 使用 fetch 即可

<a id="函数柯里化实现"></a>

### 函数柯里化实现

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

Web安全、react性能优化、react算法原理

7. js设计模式；
8. solid原则；

11.深圳二面：
       1，一千个棋子，甲先取乙后取，每次最多取七个最少取一个，问是否有一个方案让甲一定赢
        2，3×7的格子，从左上角到右下角，只能往右或者往下，有多少种走法，
        3，一个不均匀硬币，如何抛出均匀概率
        4，然后有一个生成0到13随机数的算法，如何用它均匀生成0到9随机数
        5，两千万高考生成绩如何排序
        6，用链表表示的大数求和

13.深圳抖音二面：
最近在做项目（痛点，难点，怎么解决），ssr（ssr csr混合怎么处理），小程序架构（带来的优缺点），状态管理，异步编程（各个优缺点）

三轮：
1.自己做得最有成就的项目
2.自己主动承担并是核心的项目
3.项目深度:比如现场实现vue的数据代理等
4.技术广度:什么是微前端等
5.职业发展
6. 1. js实现依赖注入
    2. 接口攻击的方式和防御措施
    4. 设计模式
    5. redux和 mobx的区别
    6. js多线程如何共享大的数据
