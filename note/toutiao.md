<a id="数字千分位处理"></a>

### 数字千分位处理

> `Number.prototype.toLocaleString()`也可做到

#### 1.使用正则

* `?=`表示正向引用，匹配到的内容不获取，并作为下一次查询的开始
* `?:`匹配到的内容不获取，也不作为下次查询的开始
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