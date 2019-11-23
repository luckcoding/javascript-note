## 词法结构

JavaScript 采用`Unicode`字符集编写。

## 值的类型

* 原始类型：`number`、`string`、`boolean`、`null`、`undefined`、`symbol`
* 引用类型： `object`

原始类型（原始值）存储在**栈**（stack）中，引用类型存储在**堆**（heap）中。

### number

* **整形直接量**默认为10进制。16进制以 `0x` 或 `0X` 开头，跟随0-9、a-f（表示10-15）组成。

* **浮点型直接量**（小数）不能精确类似0.1这样的数，只能极其近似0.1。这在任何使用二进制浮点数的编程语言中都如此。

### string

`string`是一组由**16位值**组成的**不可变的有序序列**，其长度`length`是只所含**16位值**的个数。

### boolean

只有2个值：`true`和`false`。任意 JavaScript 的值都可以转换为布尔值。

### null 和 undefined

* `null`：描述“空值”，特殊的对象。`typeof null => 'object'`
* `undefined`：描述“未定义”。`typeof undefined => 'undefined'`

## 值的类型转换

除了`null`和`undefined`，JavaScript 的任何其他值均包含`toString()`和`valueOf()`方法

* **对象转字符串**：先`toString()`再`valueOf()`，直到获取**原始值**后转为`string`，均获取失败则抛出`TypeError`
* **字符串转对象**：先`valueOf()`再`toString()`，否则抛出`TypeError`

> 原始值转换规则：`Date`对象先`toString()`再`valueOf()`，其他对象先`valueOf()`再`toString()`

## 声明

* ES5：`var`、`function`。ES5中只有**全局作用域**和**函数作用域**，函数内`var`和`function`声明的变量会被“提前”至函数顶部，称之为**变量提升**。
* ES6：`let`、`const`、`import`、`class`。ES6新增**块级作用域**（花括号内）。`let`、`const`以及`class`申明的变量只在块级作用域内有效，且申明前不可用（使用`typeof`会抛出`ReferenceError`），称之为**暂时性死区**。

## 作用域与作用域链

作用域是指代码的执行环境，当代码在一个环境中执行时，会创建变量对象的**作用域链**，作用域链构成为*当前执行环境的变量对象->外部->...->全局执行环境*。查找变量时（变量解析），按序查找链上的对象，如果最终都无法找到的话，则抛出`ReferenceError`。

> ES5中当声明全局变量时，实际上是定义了顶层对象的属性，可`delete`删除。

## 算术表达式

### `+` 运算符

优先考虑`string`连接，再考虑算数加法。

* 如果有对象，遵循**原始值转换规则**；
* 如果转换后有`string`，则进行字符串连接；
* 否则，2个操作数都转换为数字（包括`NaN`），然后进行加法操作。

```
1 + "2" // "12"
1 + {} // 1 + "[object Object]" = "1[object Object]"
true + true // 0 + 0 = 0
2 + null // 2 + 0 = 2
2 + undefined // 2 + NaN = NaN
```

### 一元算数运算符`+,-,++,--`

先把操作数转换为数字，再做运算。

```
+"1" // 1
-{} // NaN
var a = "i"; ++a // 2
```

## 关系表达式

### `===`

* 2个值类型不同，则不相等
* 都是`null`或者都是`undefined`，则相等
* 都是`true`或者`false`，则相等
* 有个一为`NaN`，则不相等
* `-0 === 0`（类型相同，值相同）
* 如果都为字符串，则判断长度和内容
* 如果都是对象，则判断是否引用的是同一个对象

> `NaN`与任何值（包括`NaN`）都不相等，所以`x!==x`可判断`x`是否为`NaN`

### `==`

* 如果2个值类型相同，参照`===`
* 如果不同
	* `null == undefined`
	* 字符串与数字比较，把字符串转化为数字
	* 如果有布尔值，`true`转化为1，`false`转化为0
	* 对象与数字或字符串比较，则对象转化为原始值进行比较

```

```

##### 比较运算符 “<,>,<=,>=”

* 如果操作数都是对象，则转换为原始值
* 如果转换后2个都是字符串，则按照“字母表顺序”（16位unicode）进行比较
* 如果转换后至少有一个不是字符串，则转换为数字进行比较。Infinity比任何数字都大（除自身），-Infinity比任何数字都小（除自身）
* 如果转换后有一个为`NaN`，则总是返回`false`

```
11 < 3 // false
"11" < 3 // => 11 < 3 => false
"11" < "3" // 按字母表顺序比较 => true
"abc" < 3 // => NaN < 3 => false
```

##### `in` 运算符

如果右侧对象拥有一个名为左侧操作数值（字符串或者可转换为字符串）的属性名，则返回true。

```
var a1 = { a: 1 };
'a' in a1 // => true
1 in a1 // false

var a2 = [1, 2]
"0" in a2 // => true
2 in a2 // => a2['2'] => false
```

##### `instanceof` 运算符

如果左侧的对象是右侧类的实例，则返回true。（如果右侧不为函数，则跑抛异常）

```
var d = new Date()
d instanceof Date // true
d instanceof Object // true。所有对象都是Object的实例
d instanceof Number // false
```

### 第5章 语句

5.7.1 with语句

用于临时扩展作用域链（一个可以按序检索的对象列表）。

```
with (object) {
	// 提升object至作用域链头部
}
```

```

function demo() {
	with (document.fprms[0]) {
		input1.value = ''
	}
}

=> 

function demo() {
	var f = document.fprms[0]
	f.input1.value = ''
}

```

<a id="对象"></a>

### 第6章 对象

对象是属性的无序集合，每个属性都是一个名/值对，也可称之为“散列”。除了string、number、boolean、null和undefined之外，JavaScript中的值都是对象。

属性名可以是包含空字符串在内的任意字符串，值可以是任意JavaScript值，或者是一个getter或setter函数（或2者都有）。

除了名/值之外，每个属性还有与之相关的值，称为**“属性特性”**：

* 可写（writeable）
* 可枚举（enumerable），表明是否可for/in循环
* 可配置（configurable），表明是否可删除或修改该属性

除了属性之外，每个对象还拥有3个相关的**“对象特性”**：

* 对象的原型（prototype）指向另一个对象
* 对象的类（class）是一个标志对象类型的字符串
* 对象的扩展标记（extensible flag）指明是否可以对该对象添加新属性

3类对象和2类属性：

* 内置对象。如数组、函数、日期和正则
* 宿主对象。如表示网页结构的HTMLElement
* 自定义对象
* 自有属性（own property）
* 继承属性（inhertied property）

#### 6.1 创建对象

##### 6.1.1 对象直接量。

`var obj = { "a": 1 }`

##### 6.1.2 通过new创建对象

`var d = new Date()`

##### 6.1.3 原型

每一个JavaScript对象（null除外）都和另一个对象关联，这个对象即为原型，每一个对象都从原型继承属性。

##### 6.1.4 Object.create()

##### 6.2.2 继承

JavaScript对象具有“自有属性”，也有一些从原型对象继承而来的属性。查找属性时，先查找“自有属性”再查找原型上的属性。

#### 6.4 检测属性

* `in`
* `hasOwnPreperty()` 检测自有属性

```
var o = {x: 1}
o.hasOwnProperty('x') // => true
o.hasOwnProperty('y') // => false
o.hasOwnproperty('toString') // => false, toString是继承属性
```

* `propertyIsEnumerable()` 是`hasOwnProperty`的增强版，检测自有属性且可枚举。

#### 6.5 枚举属性

* `for/in` 遍历对象中所有可枚举的属性（包括自有属性和继承属性）。

```
var o = {x:1}
o.propertyIsEnumerable('toString') // => false
for (p in o) {
	console.log(p) // => 输出'x'，不会输出'toString'
}
```

* `Object.keys()`返回对象中可枚举的自有属性的数组集合。

#### 6.6 属性getter和setter

对象属性是由`名/值`构成，属性值可以用1个或2个方法（getter/setter）替代，此被称为“存取器属性”，其他为“数据属性”。

* 如果属性有getter方法，那么它可读，若无读取属性时返回undefined
* 如果属性有setter方法，那么它可写，若无则赋值不会成功

#### 6.7 属性的特性

* `Object.getOwnPropertyDescriptor()` 获取对象的自有属性的描述。

```
Object.getOwnPropertyDescriptor({x:1}, 'x')
// => {value:1, writable: true, enumerable: true, configurable: true }

Object.getOwnPropertyDescriptor({}, 'toString')
// => undefined
```

* `Object.defineProperty()`设置属性的特性，默认特性值为false（writeable，enumerable， configurable）或undefined(value)

```
var o = {}
Object.definProperty(o, 'x', {
	value: 1,
	writable: true, // 可写
	enumerable: false, // 不可枚举
	configurable: true, // 可修改属性
})

console.log(o.x) // => 1
console.log(Object.keys(o)) // => [] 不可枚举

Object.defineProperty(0 ,'x', {
	writable: false, // 改为不可写
})
o.x = 2;
coonsole.log(o.x) // => 1，无改变

Object.defineProperty(o, 'x', {
	get: function () {
		return 0; // 修改存取器属性 getter
	}
})
console.log(o.x) // => 0
```

*  `Object.defineProperties()`设置多个属性特性。

```
Object.defineProperties({}, {
	x: {
		value: 1,
	},
	y: {},
	...
})
```

##### 6.8.1 原型属性

`Object.getPrototypeOf()`查询对象原型，`p.isProtortpeOf(o)`检测p是否是o的原型

```
var p = {x: 1}
var o = Object.create(p)
p.isPrototypeOf(o) // => true
Object.prototype.isPrototypeOf(o) // => true，p继承自Object.prototype
```

##### 6.8.3 可扩展性

* `Object.isExtensible({})` 用来判断对象是否可扩展
* `Object.preventExtensions()` 设置对象本身为不可扩展，但对其原型无效
* `Object.seal()` 设置对象为不可扩展，同时所有自有属性设置为不可配置。可用`Object.isSealed()`判断对象是否封闭
* `Object.freeze()` 冻结对象，在`Object.seal()`的基础上，将所有数据属性设置为只读。可用`Object.isFrozen()`判断对象是否冻结

### 第7章 数组

数组是值的有序集合。每个值叫元素，元素在数组中的位置以数字表示，称为索引。

#### 7.3 稀疏数组

> 此章节有出入。验证在新版本chrome中：`var arr = [,,,]` => `[empty x 3]`

稀疏数组就是包含从0开始的不连续索引的数组。

```
var arr = []
arr[10] = 0 // length为11，只有一个元素为0

var arr = [1, 2]
delete arr[1] // => [1, empty] 使用delete删除数组元素，会产生稀疏数组
```

#### 7.8 数组方法

* `join(:?string)`
* `reverse()`颠倒元素顺序（改变调用数组并返回）
* `sort(:?fn)`排序。不带参数时，按照元素的**字母表顺序**排序（改变调用数组并返回）

```
var arr1 = [21, 1, 223, 221, 4]
arr1.sort() => [1, 21, 221, 223, 4] // 字母表顺序
arr1.sort(function (a, b) {
	return a - b // >0 第2个数在前，<0 第1个数在前，=0不改变顺序
})
=> [1, 4, 21, 221, 223]
```

* `concat(:any)`连接数组元素，返回新数组，不改变调用数组。

```
var arr = [1, 2]
arr.concat(3, 4, 5) // [1,2,3,4,5]
arr.concat([3, 4, 5]) // [1,2,3,4,5]
arr.concat([3, 4], 5) // [1,2,3,4,5]
arr.concat([3, 4], [5]) // [1,2,3,4,5]
arr.concat([3, 4], [5,[6]]) // [1,2,3,4,5,[6]]
```

* `slice(a1:number, a2:?number)`返回下标从a1到a2(不包含a2)的子数组，不改变调用数组。如果不指定a2，则截取到最后一个元素。并且参数为负数时，表示倒数第几个元素的位置。

```
var arr = [1,2,3,4,5]
arr.slice(2) // [3,4,5]
arr.slice(1,3) // [2,3]
arr.slice(-2) // [4,5]
arr.slice(2,-1) // [2, -1]
```

* `splice()`，插入或删除元素，会改变调用数组。第1个参数指定起始位置，第2个参数指定删除的元素个数（可为空），随后为新插入的元素。

```
var arr1 = [1,2,3,4,5]
arr1.splice(3) // 返回 [4,5]，arr1变为[1,2,3]

var arr2 = [1,2,3,4,5]
arr2.splice(2, 2) // 返回[3,4]，arr2变为[1,2,5]

var arr3 = [1,2,3,4,5]
arr3.spilce(2, 2, 1, [1,2]) // 返回[3,4], arr3变为[1,2,1,[1,2], 5]
```

* `push()`与`pop()`将数组当作栈操作。`push(arg1:any, ...)`方法在末尾添加N个元素（一次插入），返回新数组长度；`pop()`方法删除最后一个元素，返回删除的值

* `unshift()`与`shift()`将数组当作栈操作。`unshift(arg1:any, ...)`在数组头部添加N个元素（一次插入），返回新数组长度；`shift()`删除第一个元素，返回删除的值。

* `toString()`将数组元素转为用逗号隔开的字符串。

```
[1,2,3].toString() // "1,2,3"
[1, [2,"a"]].toString() // "1,2,a"
```

* `forEach(fn(item:元素，index:索引, self:引用本身))`，为每个元素调用指定函数。无法在专递参数给调用函数之前终止遍历，即无相应的break语句，如需提前终止，必须在try块中抛出一个异常。

```
try {
	[1,2,3,4].forEach(...)
} catch () {}
```

* `map()`返回新数组，不改变操作数组。
* `filter(fn)`返回数组子集，如果调用函数返回true或能转化为true，那么该元素就是这个子集成员。
* `every(fn)`和`some(fn)`数组的逻辑判定，返回true或者false。前者所有元素调用返回true才为true，后者只需要一个元素调用返回true即为true。
* `reduce(fn(init:初始值/上一次函数的返回值, item:元素, index:索引, self:引用本身), init?:初始值)`和`reduceRight(fn, init?:初始值)`，使用指定函数将元素进行组合，返回值是最后一个函数返回的值。
* `indexOf(who:元素，position?:指定索引，可为负)`和`lastIndexOf()`返回指定元素的索引，如果未找到则返回-1

#### 7.11 类数组对象

有length、有索引（索引为只读），实际上并不是数组，不能使用数组特有的方法。

* 函数内`arguments`
* dom集合`HTMLCollection`与`NodeList`等

使其数组化的方法

```
// 第一种
Array.prototype.slice.apply(arguments)

// 第二种
Array.prototype.concat.apply([], arguments)

// 第三种
Array.apply([], arguments)
```

7.12 作为数组的字符串

字符串的行为类似于只读数组，如`push`、`sort`、`reverse`、`splice`等会修改操作数组的方法，在字符串上是不可用的。

```
var str = 'abc123'
str.charAt(0) // => 'a'
str[0] // => 'a'

Array.prototype.join.call(str, ',') // => "a,b,c,1,2,3"
```

<a id="函数"></a>

### 第8章 函数

* 如果函数挂载在对象上，作为对象的一个属性，称之为对象的方法。该对象就是函数的上下文（this）

> 是否可理解为所有函数都是对象的方法，全局函数挂载全局对象上

* 函数声明语句会被提前

#### 8.2 函数调用

* 作为函数 `fn()`
* 作为方法 `o.fn()`
* 作为构造函数 `var o = new fn()`。没有形参的函数可以省略`()`，构造函数调用创建一个新的对象，该对象继承构造函数的`prototype`属性，并且其构造函数的上下文为该对象，例如`new o.fn()`中的上下文并不是`o`
* 通过`call()`和`apply()`方法间接调用

#### 8.3 函数的实参和形参

##### 8.3.1 可选形参

当调用函数时传入的实参比函数声明时的形参个数少，剩下的形参都将设置为undefined

##### 8.3.2 可变长的实参列表：实参对象

arguments是实参对象，包含以数字为索引的一组元素，以及拥有length属性，但它并不是数组

* `callee` 和 `caller` 属性（严格模式下不允许读写）。`callee`指当前正在执行的函数，`caller`指调用当前正在执行的函数的函数

```
var fn = function (x) {
	if (x < 1) return 1;
	return x * arguments.callee(x - 1)
}
```

#### 8.6 闭包

和大多数编程语言一样，JavaScript也采用词法作用域，即函数的执行依赖于变量作用域，这个**作用域是在函数定义时就决定的**，而不是调用时决定。

> 每次调用函数时，都会创建一个新对象用来保存局部变量，并把该对象添加至作用域链中。当函数返回时，就用作用域链中删除该对象（没有其他引用，被当作垃圾回收）。如果函数定义了嵌套函数，并将它作为返回值返回或者存储在某处的属性里，这时就会有一个外部引用指向这个嵌套函数，那么它将不会被垃圾回收，即它所指向的变量绑定对象也不会被回收

```
var unique = (function () {
	var id = 0; // 不会被回收掉
	return function () {
		return id++
	}
}())

unique() // => 第1次返回 0
unique() // => 第2次返回 1
```

##### 8.7.3 `call()`方法和`apply()`方法

通过调用方法的形式来间接调用函数

```
fn.call(o)
fn.apply(o)

// 以上等同于
o.m = fn
o.m() // 此时fn执行上下文为o
delete o.m
```

* `call(any, arg1, arg2, ...)`第1个参数为上下文，后续为要传入函数的实参
* `apply(any, [arg1, arg2, ...])`第1个参数为上下文，第2个参数为要传入函数的实参的数组集合。

##### 8.7.4 `bind()`方法

将函数绑定至某个对象。除了第一个实参之外，传入`bind()`的实参均绑定至`this`（柯里化currying）。

```
// bind实现

if (!Function.prototype.bind) {
	Function.prototype.bind = function (o) {
		var self = this
		var boundArgs = arguments
		return function () {
			var args = []
			var i
			for (i = 1; i < boundArgs.length; i++) {
				args.push(boundArgs[i])
			}
			for (i = 0; i < arguments.length; i++) {
				args.push(arguments[i])
			}
			return self.apply(o, args)
		}
	}
}

```

##### 8.7.6 Function()构造函数

`var fn = new Function('x', 'y', 'return x*y')`

`Function()`所创建的函数并不是使用词法作用域，相反，其一直处于全局作用域（无论执行函数被嵌套多少层，作用域始终是顶层函数）。

#### 8.8 函数式编程

##### 8.8.2 高阶函数

操作函数的函数，接受一个或者多个函数作为参数，并返回一个新函数

### 第9章 类和模块

### 第10章 正则表达式的匹配模式

#### 10.1.1 直接量字符

| 字符 | 匹配 |
|------|------|
| 字母/数字 | 自身 |
| \n | 换行符(\u000A) |
| \r | 回车符(\u000D) |

#### 10.1.2 字符类

| 字符 | 匹配 |
|------|------|
| `[...]` | 方括号内的任意字符 |
| `[^...]` | 不在方括号内的任意字符 |
| `.` | 除换行符其他任意字符 |
| `\w` | `[a-zA-Z0-9]` |
| `\W` | `[^a-zA-Z0-9]` |
| `\s` | 空白字符 |
| `\S` | 非空白字符` |
| `\d` | `[0-9]` |
| `\D` | `[^0-9]` |

#### 10.1.3 重复

| 字符 | 含义 |
|------|------|
| `{n,m}` | n <= 匹配次数 <= m |
| `{n,}` | 匹配次数 >= n |
| `{n}` | 匹配n次 |
| `?` | 0或1次, 等价 `{0,1}` |
| `+` | 1次或多次, 等价 `{1,}` |
| `*` | 0次或多次, 等价 `{0,}` |

**非贪婪的重复**。在匹配字符后跟`?`: `??`、`+?`、`*?`、`{1,2}?`

#### 10.1.4 选择、分组和引用

> 正则后续补充。。。

### 第13章 web浏览器中的JavaScript

#### 13.3.1 同步、异步和延迟脚本

当HTML解析器遇到`<script>`元素时，需等待脚本加载并执行后才继续解析，即JavaScript代码会阻塞UI渲染。`defer`使脚本在文档载入和解析完成后执行，多个`defer`之间会按序执行；`async`使脚本在脚本加载完后立即执行。

如果script同时存在`defer`与`async`属性，则忽略defer

> 参考 [https://segmentfault.com/q/1010000000640869](https://segmentfault.com/q/1010000000640869)


#### 13.3.3 客户端JavaScript线程模式

客户端无任何线程机制。

#### 13.3.4 客户端JavaScript时间线

1. web浏览器创建document对象，开始解析HTML元素，此时 `document.readyState === 'loading'`
2. 遇到无`async`和`defer`属性的<script>元素时，停止解析器并下载脚本并执行
3. 当遇到`async`属性的<script>时，下载脚本并继续解析文档，脚本在其下载完成之后立即执行
4. 文档解析完成时，`document.readyState === 'interactive'`
5. `defer`属性脚本会按照其在文档中出现的顺序进行执行。延迟脚本能够访问完整的文档树，静止使用`document.write()`
6. 触发`DOMContentLoaded`事件，这标志程序冲同步脚本执行阶段转换为异步事件驱动阶段。注意，此时可能还有异步脚本没有执行完成
7. 此时可能还在等待其他内容载入，如图片等。当所有内容完成载入、且脚本完成载入和执行，`document.readyState === 'complete'`，并触发window对象的load事件

#### 13.6.2 同源策略

包括协议、主机、端口等。

* 跨域设置 `access-control-allow-origin: *`
* 跨文档消息 `window.postMessage()`

#### 13.6.4 跨站脚本(XSS)

### 15 脚本化文档

#### 15.2 选取文档元素

* `document.getElementById()`
* `document.getElementsByName()`
* `document.getElementsByTagName()`
* `document.getElementsByClassName()`
* `document.querySelectorAll()` 与 `document.querySelector()`

#### 15.3 文档结构与遍历

Node:

* `parentNode`父节点
* `childNodes`子节点(`NodeList`对象)
* `firstChild`、`lastChild`第一个和最后一个节点，无的话则为`null`
* `nextSibling`、`previoursSibling`兄弟节点的前一个和后一个
* `nodeType`
* `nodeValue`
* `nodeName` 

#### 17.3.7 事件取消

```
function cancelEvent (event) {
	var e = event || window.event // IE
	if (e.preventDefault) e.preventDefault() //  标准技术
	if (e.returnValue) e.returnValue = false // IE
	return false
}
```