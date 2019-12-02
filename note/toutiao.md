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