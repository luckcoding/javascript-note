### 含义

数组是值的有序集合。

### length

数组的length不是只读。

```
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

### 检测

* xx instanceof Array
* Array.isArray(xx)

### 转换

* **valueOf**。返回数组本身

```
var m = [1,2,3]
console.log(m.valueOf()) => [1,2,3]
```

* **toString**。调用每一项的toString方法，并用`,`拼接。

```
var m = ['a',{a:'a'},2]
console.log(m.toString()) => "a,[object Object],2"
```

* **toLocaleString**。调用每一项的toLocaleString方法，并用`,`拼接。

```
var date = new Date()
var m = ['a', date]
console.log(m. toLocaleString()) => "a,2018/2/23 下午2:59:41"
```

* **join(argument)**。调用每一项的toString方法，并用`传入的参数`拼接，如果参数为空或者undefined，则默认用`,`。

---

### 栈方法

* push。*接受任意个数的参数*。推入数组末尾，并返回**数组新length**。
* pop。移除数组末尾项，并返回该项。

### 队列方法

* shift。移除数组第一项，并返回该项。
* unshift。*接受任意个数的参数*。推入数组前端，并返回**数组新length**。

### 重排序

* reverse。颠倒数组中元素的顺序。
* sort。默认根据每项的**`toString`**结果进行**从小到大**排序。接受一个比较函数，比较函数接受2个参数。如果第一个参数在第二个参数之前，返回负数；参数相等，返回0；第一个参数在第二个参数后面，返回正数。

```
// 从小到大
var m = [1,5,2,'2',3]

m.sort(function(x,y){
  return Number(x) - Number(y)
})

console.log(m) => [1, 2, "2", 3, 5]
```

### 操作方法

* concat。创建当前数组的一个副本，将接受到的参数添加到副本的末尾，如果参数不是数组，则放入数组副本末尾。

> 不改变原数组

```
var m = ['a','b']
var n = m.concat('c',['d','e'])
console.log(m) => ['a','b']
console.log(n) => ['a','b','c','d','e']
```

* slice。复制当前数组。接受2个参数，第1个为复制的起始位，第2个参数为复制的结束位（复制的新数组不包含这一位，并且参数可为空）。

> 不改变原数组

```
var m = [1,2,3]
var n = m.slice(0)
var x = m.slice(0,1)
var y = m.slice(0,-2)

// 如果参数有负数，复制位 = 参数 + 数组长度

console.log(n) => [1, 2, 3]
console.log(x) => [1]
console.log(y) => [1]
```

* splice。接受任意个参数。第1个参数为要删除的位置，第2个参数为要删除的个数。

> 会改变原数组，返回的是删除的项

```
// 删除
var m = [1,2,3,4]
var n = m.splice(0,2)
console.log(m) => [3,4]
console.log(n) => [1,2]

// 插入
var m = [1,2,3,4]
var n = m.splice(1,0,'a','b')
console.log(m) => [1,'a','b',2,3,4]
console.log(n) => []

// 替换
var m = [1,2,3,4]
var n = m.splice(1,1,'a')
console.log(m) => [1,'a',3,4]
console.log(n) => [2]
```

### 位置方法

* indexOf
* lastIndexOf

### 迭代方法

* every。对数组的每一项运行指定函数，如果都返回true，则返回true。
* some。对数组的每一项运行指定函数，如果有一项返回true，则返回true。
* forEach。对数组的每一项运行指定函数，无返回。
* filter。对数组的每一项运行指定函数，返回**每一项返回true的项组成的数组**。
* map。对数组的每一项运行指定函数，返回**每一项返回的结果组成的数组**。

### 并归方法

* reduce。迭代数组所有项，构建最终返回值。接受2个参数，第1个为执行函数，第2个为作为归并的初始值（可选）。执行函数接受4个参数，前一个值、当前值、当前项的索引、数组对象。

```
//  计算数组所有值的和
var m = [1,2,3,4,5]
var sum = m.reduce(function(prev,cur,index,array){
  return prev + cur
})
/**
 * 第一次执行：prev 为 1，cur 为 2, index 为 1
 * 第二次执行：prev 为 上一次计算的结果 3，cur 为 3, index 为 2
 * ...
 */
```

* reduceRight。