## 含义

浏览器对象模型。

## window 对象

### 获取页面视口大小

```js
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

## location 对象

既是window对象的属性，也是document对象的属性。`document.location === window.location`

属性 | 例子 | 说明
------------- | ------------- | -------------
herf | "http://www.baidu.com/news#content" | 返回当前全路径
hash | "#content" | 返回URL中的hash，如果不包含散列，则返回空
host | "www.baidu.com:80" | 返回服务器+端口(如果端口存在)
hostname | "www.baidu.com" | 返回服务器
pathname | "/news" | 返回目录或文件名
port | 80 | 返回端口
protocol | "http" | 返回协议
search | "?query=js | 返回查询字符串，以"?"开头

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

## navigator 对象

## screen 对象

## history 对象

例子 | 说明
------ | ------
history.go(-1) | 后退一页
history.go(2) | 前进2页
history.back() | 后退一页
history.forward() | 前进一页

`history.length`,保存着历史记录的数量。

---

<a id="cookie、session和localStorage"></a>

## cookie、session和localStorage

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