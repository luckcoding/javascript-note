<a id="语义化"></a>

## 语义化

* 正确使用标签
* 让页面内容结构化，便于SEO与浏览器解析
* 无样式情况下方便阅读

---

<a id="viewport"></a>

## viewport

```js
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no" />
// width    设置viewport宽度，为一个正整数，或字符串‘device-width’
// device-width  设备宽度
// height   设置viewport高度，一般设置了宽度，会自动解析出高度，可以不用设置
// initial-scale    默认缩放比例（初始缩放比例），为一个数字，可以带小数
// minimum-scale    允许用户最小缩放比例，为一个数字，可以带小数
// maximum-scale    允许用户最大缩放比例，为一个数字，可以带小数
// user-scalable    是否允许手动缩放
```

---

<a id="href和src"></a>

## href和src

* `src`：用于替换当前内容。当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到该资源加载、编译、执行完毕。`img`标签的src也是如此
* `href`：指向网络资源所在位置，建立和当前元素或文档直接的联系。如果识别为css文件，会并行下载资源且页面解析不会暂停

---

<a id="事件"></a>

## 事件

### 事件流


* **类型**
    * 事件捕获：从最不具体到最具体
    * 事件冒泡：从最具体到最不具体
* **过程**
    * 捕获过程：`window -> document -> html -> head/body -> div`
    * 冒泡过程：`div -> head/body -> html -> document -> window`
* **使用**
    * IE使用的是事件冒泡
    * W3C标准为事件捕获
    * 现代浏览器是**先捕获，后冒泡**。

`addEventListener(type, cb, false)`第1个参数为事件，第2个参数为回调，第3个参数为触发的阶段：`true`为捕获阶段、`false`为冒泡阶段，默认为`false`

### 绑定方法

1. html中绑定：`<button onclick="click()"></button>`
2. js中绑定：`element.onclick = function click () {}`
3. 事件监听：

```js
// 事件监听
function addEventListener (element, type, handler) {
    if (element.attachEvent) {
        element.attachEvent(`on${type}`, () => {
            handler.call(element)
        })
    } else {
        element.addEventListener(type, handler, false) // 冒泡
    }
}

// 事件移除
function removeEvent(element, type, handler) {
    if (element.datachEvent) {
        element.detachEvent(`on${type}`, handler)
    } else {
        element.removeEventListener(type, handler, false) // 移除冒泡
    }
}

// 获取事件目标
function getTarget(event) {
    return event.target || event.srcElement
}

// 阻止冒泡
function stopPropagation(event) {
    if (event.stopPropagation) {
        event.stopPropagation()
    } else {
        event.cancelBubble = true
    }
}

// 取消默认事件
function preventDefault(event) {
    if (event.preventDefault) {
        event.preventDefault()
    } else {
        event.returnValue = false
    }
}
```

### target、this、currentTarget

* `target`：**触发**事件的某个具体对象
* `currentTarget`：**绑定**事件的对象，**恒等于this**，可能出现在事件流的任意一个阶段中
* 通常情况下`target`与`currentTarget`一致。但在父子嵌套关系中，父元素绑定事件，单击子元素后触发了父元素事件，这时的`currentTarget`指向的是父元素，因为它是**绑定事件的对象**，但`target`指向的是子元素，因为它是**触发事件的具体对象**

```html
<div id="father">
    <div id="son"></div>
</div>
<script>
father.addEventListener('click', function (e) {
    console.log(e.target) // son
    console.log(e.currentTarget) // father
    console.log(this) // 恒等于 currentTarget，即为 father
}, false)
</script>
```

---

## window

```js
// 获取屏幕宽高
var pageWidth = window.innerWidth,
    pageHeight = window.innerHeight;
if (typeof pageWidth !== 'number') {
  if (document.compatMode === 'CSS1Compat') { // 判断是否是标准模式
  	 pageWidth = document.documentElement.clientWidth;
  	 pageHeight = document.documentElement.clientHeight;
  } else {
    pageWidth = document.body.clientWidth;
    pageHeight = document.body.clientHeight;
  }
}
```

---

<a id="location"></a>

## location

既是window对象的属性，也是document对象的属性。`document.location === window.location`

属性 | 例子 | 说明
------------- | ------------- | -------------
`herf` | "http://www.baidu.com/news#content" | 返回当前全路径
`hash` | "#content" | 返回URL中的hash，如果不包含散列，则返回空
`host` | "www.baidu.com:80" | 返回服务器+端口(如果端口存在)
`hostname` | "www.baidu.com" | 返回服务器
`pathname` | "/news" | 返回目录或文件名
`port` | 80 | 返回端口
`protocol` | "http" | 返回协议
`search` | "?query=js | 返回查询字符串，以"?"开头

### 位置操作

#### 打开窗口的3种方法
* `location.href = "http://www.baidu.com"`
* `location.assign("http://www.baidu.com")`
* `window.location = "http://www.baidu.com"`

#### 替换窗口
* `location.replace("http://www.baidu.com/")`。**不生成历史记录**

#### 重新加载

* `location.reload()`。重新加载（有可能从缓存中加载
* `location.reload(true)`。重新加载（从服务器重新加载）

---

<a id="history"></a>

## history

例子 | 说明
------ | ------
`history.go(-1)` | 后退一页
`history.go(2)` | 前进2页
`history.back()` | 后退一页
`history.forward()` | 前进一页
`history.length` | 历史记录数量

---

## getBoundingClientRect 与 offsetTop 获取的 top 的区别

---

<a id="存储"></a>

## 存储

### cookie

浏览器根据`domain`来发送响应的`cookie`，可通过设置`expires`、`max-age`来设定过期时间。有个数限制和大小限制（一般4k）。浏览器每次请求都会携带`cookie`，设置`HttpOnly`可让前端无法读取此条`cookie`

```js
// 每次只能设置一条，后设置的path与name会覆盖之前与此一样的cookie
document.cookie = 'namv=value;expires=时间/max-age=秒'
```

### sessionStorage

仅在当前会话下有效，关闭页面或浏览器后会被清除。大小限制一般为5M，仅保存在客户端中，不参与服务端通讯。且与`localStorage`有相同的方法。

### localStorage

除非主动清除，否则存储是永久的。大小限制一般为5M，仅保存在客户端中，不参与服务端通讯。