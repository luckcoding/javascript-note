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
		.split('') // 拆成数组
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

<a id="事件委托"></a>

## 事件委托

```js
var UL = document.getElementById('ul')
UL.onclick = function (e) {
     e = e || window.event
     var target = e.target || e.srcElement
     if (target.nodeName.toLowserCase() === 'li') {
          target.style.background = 'red'
     }
}
```

## 同源tab之间的数据交互与同步

```js
// A tab（注意如果在当前tab修改值不会触发该事件）
window.addEventListener('storage', function (e) {
     console.log(e.key, e.newValue)
})
// B tab
localStorage.setItem('xxx', 'xxx')
```

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




## JavaScript 中的并行与串行

`while`、`for`等都算并行


<a id="数组内求目标和"></a>

## 数组内求目标和

给定一个不含重复数字的数组arr,指定个数n,目标和sum,判断是否含有由n个不同数字相加得到sum的情况

```js
function hasSum(arr = [], n = 1, sum = 0) {
     if (arr.length < n) {
          return false
     }
     for (let i = 0, len = arr.length; i < len; i++) {
          if (n === 1) {
               if (arr[i] === sum) {
                    return true
               }
          } else {
               return hasSum(arr.slice(i + 1), n - 1, sum - arr[i])
          }
     }
     return false
}
```

<a id="数组求2值和"></a>

## 数组求2值和

给定一个数组，一个期望值，找到数组中两个相加等于期望值

```js
function twoSum (nums = [], target = 0) {
     for (let i = 0, len = nums.length; i < len - 1; i++) {
          const index = nums.lastIndexOf(target - nums[i])
          if (index !== -1 && index !== i) {
               return [i, index]
          }
     }
}
```

<a id="依赖注入实现"></a>

## 依赖注入实现

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

## 时间标签

输入一个日期 返回几秒前 几天前或者几月前

```js
function timeTag(time) {
     if (!time) {
          throw Error('error')
     }
     var ts = (new Date(time)).getTime()
     var duration = Date.now() - ts
     var sTime = 1000 * 60
     var dTime = sTime * 60 * 24
     var mTime = dTime * 30
     var mLast = Math.floor(duration/mTime)
     if (mLast) {
          return mLast
     }
     var dLast = Math.floor(duration/dTime)
     if (dLast) {
          return dLast
     }
     return Math.floor(duration/sTime)
}
```

4.实现一个方法，参数是一个generator函数，执行结果是执行完所有generator中的yield

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
3.项目深度:比如现场实现vue的数据代理等
4.技术广度:什么是微前端等
2. 接口攻击的方式和防御措施
5. redux和mobx的区别
6. js多线程如何共享大的数据
