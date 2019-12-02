## 声明

* ES5：`var`、`function`。ES5中只有**全局作用域**和**函数作用域**，函数内`var`和`function`声明的变量会被“提前”至函数顶部，称之为**变量提升**。
* ES6：`let`、`const`、`import`、`class`。ES6新增**块级作用域**（花括号内）。`let`、`const`以及`class`申明的变量只在块级作用域内有效，且申明前不可用（使用`typeof`会抛出`ReferenceError`），称之为**暂时性死区**。

## 作用域与作用域链

作用域是指代码的执行环境，当代码在一个环境中执行时，会创建变量对象的**作用域链**（一个可以按序检索的对象列表），作用域链构成为*当前执行环境的变量对象->外部->...->全局执行环境*。查找变量时（变量解析），按序查找链上的对象，如果最终都无法找到的话，则抛出`ReferenceError`。

> ES5中当声明全局变量时，实际上是定义了顶层对象的属性，可`delete`删除。

## 实参对象arguments

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

* `callee` 和 `caller` 属性（严格模式下不允许读写，否砸抛出异常）。`callee`指当前正在执行的函数，`caller`指调用当前正在执行的函数的函数

```js
var fn = function (x) {
	if (x < 1) return 1;
	return x * arguments.callee(x - 1) // 调用本身
}
```

## 闭包

每次调用函数时，都会创建一个新对象用来保存局部变量，并把该对象添加至作用域链中。当函数返回时，就用作用域链中删除该对象（没有其他引用，被当作垃圾回收）。如果函数定义了嵌套函数，并将它作为返回值返回或者存储在某处的属性里，这时就会有一个外部引用指向这个嵌套函数，那么它将不会被垃圾回收，即它所指向的变量绑定对象也不会被回收

```js
var unique = (function () {
	var id = 0; // 不会被回收掉
	return function () {
		return id++
	}
}())

unique() // => 第1次返回 0
unique() // => 第2次返回 1
```

## Function()构造函数

`var fn = new Function('x', 'y', 'return x*y')`

`Function()`所创建的函数并不是使用词法作用域，相反，其一直处于全局作用域（无论执行函数被嵌套多少层，作用域始终是顶层函数）。