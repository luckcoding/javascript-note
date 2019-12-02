<a id="数据类型"></a>

## 数据类型

* 原始类型：`number`、`string`、`boolean`、`null`、`undefined`、`symbol`
* 引用类型： `object`

原始类型（原始值）存储在**栈**（stack）中；引用类型存储在**堆**（heap）中，其指针存粗在**栈**中。

---

<a id="Number"></a>

### Number

#### 类型

* **整形**：默认为10进制。16进制以 `0x` 或 `0X` 开头，跟随0-9、a-f（表示10-15）组成。
* **浮点型**：不能精确类似0.1这样的数，只能极其近似0.1。这在任何使用二进制浮点数的编程语言中都如此。

#### 范围

* `Number.MAX_VALUE=1.7976931348623157e+308`
* `Number.MIN_VALUE=5e-324`
* `Number.POSITIVE_INFINITY=Infinity`
* `Number.NEGATIVE_INFINITY=-Infinity`

#### 方法

* `toString(?进制)`：把数字转换成指定进制形式的字符串，参数为进制，默认十进制
* `toFixed(?保修位数)`：把数字转换成十进制形式的字符串（四舍五入），参数为保留几位小数，默认为0

```js
var num1 = 10
console.log(num1.toString()) // 十进制：10
console.log(num1.toString(2)) // 十进制：1010

var num2 = 3.515
console.log(num2.toFixed()) // 4
console.log(num2.toFixed(2)) // 3.52
```

#### 转换

##### `Number()`强转

```js
console.log(Number(true)) // 1
console.log(Number(false)) // 0
console.log(Number(null)) // 0
console.log(Number(undefined)) // NaN
console.log(Number(NaN)) // NaN

// 如果是空字符串（包括包含空的字符串），转为0；其他任何不是类似数字i形式的字符串，都转为NaN
console.log(Number('')) // 0
console.log(Number('    ')) // 0
console.log(Number('1a')) // NaN
console.log(Number('1.1')) // 1.1
console.log(Number('1.1.1')) // NaN

// 如果是对象，先转为原始值，再进行转换
console.log(Number([])) // 0
console.log(Number([1])) // -> "1" -> 1
console.log(Number([1,2])) // -> "1,2" -> NaN
console.log(Number({})) // -> "[object Object]" -> NaN

// 先用 valueOf 再用 toString 去获取原始值，所以返回 0
console.log(Number({
    valueOf: function () {
        return 0
    },
    toString: function () {
        return 1
    }
}))
```

##### `parseInt()`和`parseFloat()`函数转换

* 如果接收的**不是数字或者字符串**，其他**均转换为NaN**
* 如果接收的字符串还有非数字的字符，那么从首部开始查找到非数字字符为止，然后转换前面的数字字符
* 第二个参数为进制数，默认为十进制

```js
console.log(parseInt('')) // NaN
console.log(parseInt('a1')) // NaN
console.log(parseInt('1a')) // 1

console.log(parseInt('070')) // 70
console.log(parseInt('070', 8)) // 56
console.log(parseInt('70')) // 70
```

---

<a id="String"></a>

### String

`string`是一组由**16位Unicode字符**组成的**不可变的有序序列**，其长度`length`是指所含**字符**的个数。

#### 方法

* `concat(:?string,...)`：连接字符串，返回新字符串
* `charAt(:?string)`：返回指定下标的字符。如果超出有效范围，返回为空字符串
* `charCodeAt(:?string)`：返回指定下标的字符的Unicode编码。如果指定位置没有字符，返回NaN
* `slice()`：返回字符串片段。参数1为开始的下标，参数2为获取长度
* `indexOf()`、`lastIndexOf()`：返回子串在字符串中的下标，如果未找到，返回-1
* `substr()`：返回字符串片段。参数1为开始的下标，参数2为获取长度
* `substring()`：返回字符串片段。参数1和参数2或为起始下标和获取长度，取最小值为开始的下标，**如果参数有undefined、NaN或者负数，那么将其替换为0**
* `toUpperCase()`、`toLowerCase()`：首字符转为大/小写
* `trim()`：删除前后空格，返回新字符串
* `split()`：按条件分割字符串为数组。参数1为分割条件，参数2为返回数组的最大长度
* `replace()`：按指定替换字符串
* `search()`：返回子串在字符串中的下标，如果未找到，返回-1
* `match()`：查找第一个匹配的子串，作为数组返回，未找到返回null

#### `String()`转换

可将任何值转为字符串

```js
console.log(String({})) // '[object Object]'
console.log(String([])) // ''
console.log(String(function(){})) // 'function(){}'
console.log(String(null)) // 'null'
console.log(String(undefined)) // 'undefined'
console.log(String(false)) // 'false'
```

---

<a id="Boolean"></a>

### Boolean

只有2个值：`true`和`false`。任意 JavaScript 的值都可以转换为布尔值。

---

<a id="null和undefined"></a>

### null和undefined

* `null`：描述“空值”，特殊的对象。`typeof null => 'object'`
* `undefined`：描述“未定义”。`typeof undefined => 'undefined'`

---

<a id="Array"></a>

### Array

数组是值的有序集合。每个值叫元素，元素在数组中的位置以数字表示，称为索引。

### `length`

数组的`length`不是只读。

```js
// 末尾删除
var m = ['a', 'b', 'c']
m.length = 2
console.log(m) => ['a', 'b']

// 末尾添加
var m = ['a', 'b', 'c']
m.length = 4
console.log(m) => ['a', 'b', 'c', empty]
console.log(m[3]) => undefined

// 最后插入
var m = ['a', 'b', 'c']
m[m.length] = 'd'
console.log(m) => ['a', 'b', 'c', 'd']
```

#### 转换

* `valueOf()`：返回数组本身
* `toString()`：调用每一项的`toString()`方法，并用`,`拼接。
* `join(argument)`：调用每一项的`toString()`方法，并用传入的参数拼接，如果参数为空或者`undefined`，则默认用`,`拼接。

```js
var m = [1,2,3]
console.log(m.valueOf()) // => [1,2,3]

var m = ['a', { a: 'a' }, 2]
console.log(m.toString()) // => "a,[object Object],2"
```

#### 稀疏数组

> 新版本chrome中：`var arr = [,,,]` -> `[empty x 3]`

稀疏数组就是包含从0开始的不连续索引的数组。

```js
var arr = []
arr[10] = 0 // length为11，只有一个元素为0

var arr = [1, 2]
delete arr[1] // => [1, empty] 使用delete删除数组元素，会产生稀疏数组
```

#### 方法

* `join(:?string)`
* `reverse()`颠倒元素顺序（改变调用数组并返回）
* `sort(:?fn)`排序。不带参数时，按照元素的**字母表顺序**排序（改变调用数组并返回）

```js
var arr1 = [21, 1, 223, 221, 4]
arr1.sort() => [1, 21, 221, 223, 4] // 字母表顺序
arr1.sort(function (a, b) {
	return a - b // >0 第2个数在前，<0 第1个数在前，=0不改变顺序
}) // => [1, 4, 21, 221, 223]
```

* `concat(:any)`连接数组元素，返回新数组，不改变调用数组。

```js
var arr = [1, 2]
arr.concat(3, 4, 5) // [1,2,3,4,5]
arr.concat([3, 4, 5]) // [1,2,3,4,5]
arr.concat([3, 4], 5) // [1,2,3,4,5]
arr.concat([3, 4], [5]) // [1,2,3,4,5]
arr.concat([3, 4], [5,[6]]) // [1,2,3,4,5,[6]]
```

* `slice(a1:?number, a2:?number)`返回下标从a1到a2(不包含a2)的子数组，不改变调用数组。如果不指定a2，则截取到最后一个元素。并且参数为负数时，表示倒数第几个元素的位置。

```js
var arr = [1,2,3,4,5]
arr.slice() // [1,2,3,4,5]
arr.slice(2) // [3,4,5]
arr.slice(1,3) // [2,3]
arr.slice(-2) // [4,5]
arr.slice(2,-1) // [2, -1]
```

* `splice(a1?:number,a2:?number,...)`，插入或删除元素，会改变调用数组。第1个参数指定起始位置，第2个参数指定删除的元素个数（可为空），随后为新插入的元素。

```js
var arr1 = [1,2,3,4,5]
arr1.splice(3) // 返回 [4,5]，arr1变为[1,2,3]

var arr2 = [1,2,3,4,5]
arr2.splice(2, 2) // 返回[3,4]，arr2变为[1,2,5]

var arr3 = [1,2,3,4,5]
arr3.spilce(2, 2, 1, [1,2]) // 返回[3,4], arr3变为[1,2,1,[1,2], 5]
```

* `push()`与`pop()`将数组当作**栈**操作。`push(arg1:any, ...)`方法在末尾添加N个元素（一次插入），返回新数组长度；`pop()`方法删除最后一个元素，返回删除的值
* `unshift()`与`shift()`将数组当作**栈**操作。`unshift(arg1:any, ...)`在数组头部添加N个元素（一次插入），返回新数组长度；`shift()`删除第一个元素，返回删除的值
* `toString()`将数组元素转为用逗号隔开的字符串

```js
[1,2,3].toString() // "1,2,3"
[1, [2,"a"]].toString() // "1,2,a"
[1, []].toString() // "1,"
```

* `forEach(fn(item:元素，index:索引, self:引用本身))`：为每个元素调用指定函数，无返回。无法在专递参数给调用函数之前终止遍历，即无相应的break语句，如需提前终止，必须在try块中抛出一个异常。

```js
// 中断 forEach
try {
	[1,2,3,4].forEach(...)
} catch () {}
```

* `map(fn)`返回新数组，不改变操作数组，如果**内部函数无返回，那么默认返回undefined**
* `filter(fn)`返回数组子集，如果调用函数返回true或能转化为true，那么该元素就是这个子集成员。
* `every(fn)`和`some(fn)`数组的逻辑判定，返回true或者false。前者所有元素调用返回true才为true，后者只需要一个元素调用返回true即为true。
* `reduce(fn(init:初始值/上一次函数的返回值, item:元素, index:索引, self:引用本身), init?:初始值)`和`reduceRight(fn, init?:初始值)`，使用指定函数将元素进行组合，返回值是最后一个函数返回的值。
* `indexOf(who:元素，position?:指定索引，可为负)`和`lastIndexOf()`返回指定元素的索引，如果未找到则返回-1

#### 类数组对象

有length、有索引（索引为只读），实际上并不是数组，不能使用数组特有的方法。

* 函数内`arguments`
* dom集合`HTMLCollection`与`NodeList`等

##### 类数组的数组化

```js
// 第一种
Array.prototype.slice.apply(arguments)
// 第二种
Array.prototype.concat.apply([], arguments)
// 第三种
Array.apply([], arguments)
```

---

<a id="Symbol"></a>

### Symbol

* `Symbol` 实例都是唯一的
* `Symbol.for()`可以注册或获取一个全局`Symbol`实例
* 以`Symbol`为key的对象不可被`Object.keys`、`for in`枚举到