## 含义

浏览器对象模型。

## window 对象

### 获取页面视口大小

```
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
~~history.go('baidu.com')~~ | 跳转到最近的'baidu.com'
history.back() | 后退一页
history.forward() | 前进一页

`history.length`,保存着历史记录的数量。