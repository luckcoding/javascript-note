## 类型转换

除了`null`和`undefined`，JavaScript 的任何其他值均包含`toString()`和`valueOf()`方法

* **对象转字符串**：先`toString()`再`valueOf()`，直到获取**原始值**后转为`string`，均获取失败则抛出`TypeError`
* **字符串转对象**：先`valueOf()`再`toString()`，否则抛出`TypeError`

> 原始值转换规则：`Date`对象先`toString()`再`valueOf()`，其他对象先`valueOf()`再`toString()`


## 运算符

> 优先级由上至下

运算符 | 操作 | 优先级
----- | ----- | -----
++ | 增增 | 
-- ｜ 减减 | 
- ｜ 求反 | 

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


### 比较运算符`<,>,<=,>=`

* 如果操作数都是对象，则转换为原始值
* 如果转换后2个都是字符串，则按照“字母表顺序”（16位unicode）进行比较
* 如果转换后至少有一个不是字符串，则转换为数字进行比较。Infinity比任何数字都大（除自身），-Infinity比任何数字都小（除自身）
* 如果转换后有一个为`NaN`，则总是返回`false`

```
11 < 3 // false
"11" < 3 // => 11 < 3 => false
"abc" < 3 // => NaN < 3 => false
"11" < "3" // 按字母表顺序比较 => true
{} < 3 // NaN < 3 => false
[] < 3 // 0 < 3 => true
```

### `in` 运算符

检测是否包含属性名（转化为`string`）。

```
var a1 = { a: 1, 2: 2 };
'a' in a1 // a1['a'] => true
1 in a1 // a1['1'] => false
2 in a1 // true
```

### `instanceof` 运算符

检测左侧对象是否为右侧类的实例，如果右侧不为函数，则抛`TypeError`。

```
var d = new Date()
d instanceof Date // true
d instanceof Object // true。所有对象都是Object的实例
d instanceof Number // false
```

### 第5章 语句

5.7.1 `with`语句

用于临时扩展作用域链。

```
var o = { a: 1 }
with (o) {
	console.log(a) // => 1
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