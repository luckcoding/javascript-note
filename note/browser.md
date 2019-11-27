<a id="从输入URL到呈现页面过程"></a>

## 从输入URL到呈现页面过程

![what-url-happen](../../asset/what-url-happen.svg)

**参考**

* [SSL/TLS 握手过程详解](https://www.jianshu.com/p/7158568e4867)
* [当你在浏览器中输入 google.com 并且按下回车之后发生了什么？](https://github.com/skyline75489/what-happens-when-zh_CN)
* [what-happens-when-you-enter-url-in-browser/](https://vincenthou.github.io/what-happens-when-you-enter-url-in-browser/)

---

<a id="缓存"></a>

## 缓存

### CDN（内容分发网络）

客户端直接从源站点获取数据，无法保证是最短距离传输。通过CDN对数据的分发，客户端可从一个距离较近的服务器获取数据，而不是源站点。

### 客户端访问网站过程

**无CDN：**

输入域名 => DNS域名解析 => DNS返回服务器IP => 客户端向IP发送请求 => 服务器返回数据

**有CDN：**

输入域名 => CDN专用DNS服务器解析 => 返回负载均衡设备IP => 客户端向**负载均衡设备**发送请求 => **负载均衡设备**择优返回环境服务器IP => 客户端向缓存服务器IP发送请求 => 缓存服务器返回数据

### 浏览器缓存存取过程

#### 强缓存

通过`Expires`或`Cache-Control`判断资源是否在缓存有效期内。如果命中则直接取缓存，此为强缓存，其http状态为200，且不会与服务器通信。

* `Expores`：服务器绝对时间，是GMT格式的时间字符串，表示资源失效的时间；
* `Cache-Control`：表示资源有效期，单位为秒
  * `Cache-Control: max-age=3600`：缓存有效期为3600秒
  * `Cache-Control: no-cache`：不使用本地缓存
  * `Cache-Control: no-store`：不缓存数据

#### 协商缓存

利用`Etag/If-None-Match`和`Last-Modified/If-Modified-Since`判断是否命中协商缓存。如果命中协商缓存，服务器不返回资源，且http状态为304

* `Last-Modified/If-Modified-Since`：服务器绝对时间，是GMT格式的时间字符串，表示资源最后修改时间；
* `Etag/If-None-Match`：资源的唯一校验码

#### 过程

1. 浏览器第一次请求后，缓存服务端返回的资源以及`Expires`或`Cache-Control`、以及`Last-Modified`和`ETag`等信息
2. 浏览器再次请求时，先判断`Cache-Control`是否命中缓存、再判断`Expires`是否命中强缓存
3. 如果命中强缓存，就加载本地缓存，状态码为`200(from cache)`
4. 如果未命中强缓存，浏览器再发送一次请求，并携带`If-None-Match`和`If-Modified-Since`，其值为上一次缓存的`Last-Modified`和`ETag`值
5. 服务器先验证`ETag`、再验证`Last-Modified`是否命中协商缓存
6. 如果命中协商缓存，就加载本地缓存，状态码为`304(Not Modified)`，且不会返回资源，同时返回`Last-Modified`的值（即使未改变），但不返回`ETag`
6. 如果未命中协商缓存，则返回资源以及新的头信息，状态码为`200`

### 使用缓存的优点

* 节省带宽
* 减轻服务器负担
* 加快加载速度、提升性能

### 如何更新缓存

* 资源请求的URL增加tag参数值。例如：`js/main.js?version=001`

---

<a id="OSI的七层体系结构"></a>

## OSI的七层体系结构

* 应用层
* 表示层
* 会话层
* 运输层
* 网络层
* 数据链路层
* 物理层

---

<a id="TCP与UDP"></a>

## TCP与UDP

### TCP

TCP（传输控制协议）是一种面向连接的、可靠的、基于字节流的传输通讯协议。（传输层）

#### TCP三次握手

1. 【寻址】`SYN=1 seq=x` 客户端发送 syn 报文，并置发送序号为 x
2. 【确认】`SYN=1 ACK=x+1 seq=y` 服务端发送 syn+ack报文，并置发送序号为 y、确认序号为 x+1
3. 【连接】`ACK=y+1；seq=z`。客户端发送 ack 报文，并置发送序号为 z、确认序号为 y+1

#### TCP四次挥手

1. 【验证请求码】`FIN=1 ACK=Z seq=x` 主动方发送 fin+ack 报文，并置发送序号为 x
2. 【传输结束标记】`ACK=x+1 seq=z` 被动方发送 ack 报文，并置发送序号为z、确认序号为x+1
3. 【确认结束标记】`FIN=1 ACK=x seq=y` 被动方发送 fin+ack 报文，并置发送序号为 y、确认序号为 x
4. 【连接断开标记】`ACK=Y seq=x` 主动方发送 ack 报文，并置发送序号为 x、确认序号为 y

### UDP

UDP（用户数据报协议）无连接、不可靠，提供面向事务的简单传输。

### TCP、UDP区别

* TCP是面向连接的（在客户端和服务器之间传输数据之前要先建立连接），UDP是无连接的
* TCP提供可靠的数据传输（无差错、不丢失、不重复、按序到达），UDP提供面试事务的简单的不可靠的传输
* UDP有更好的实时性，适用于对高速传输和实时性比较高的通讯或广播
* 每一条TCP连接只能是点到点的，UDP支持一对一、一对多、多对多的交互通讯
* TCP对系统资源相对UDP要多
* UDP程序结构更加简单
* TCP是流模式，UDP是数据报模式

---

<a id="HTTP"></a>

## HTTP

基于TCP协议建立的连接

### 常见的 HTTP 方法

* `GET` 请求已被URI（统一资源标识符）识别的资源，可通过URL传参
* `POST` 传输信息给服务器
* `PUT` 传输信息到对应URI位置
* `HEAD` 获取报文头，不返回报文体
* `DELETE` 删除对象URI资源
* `OPTIONS` 查询相应URI支持的HTTP方法

### HTTP状态码

* `1xx` 信息，服务器收到请求，但需要请求者继续操作
* `2xx` 成功，操作被成功接收并处理
* `3xx` 重定向
* `4xx` 客服端错误
* `5xx` 服务端错误
* `200` 成功
* `204` 请求被受理但无资源返回
* `301` 永久性重定向
* `302` 临时重定向（GET:303、POST:307）
* `304` 发生附带条件请求，且附带条件不满足，与重定向无关
* `400` 请求报文语法错误
* `401` 请求需要认证
* `403` 禁止访问资源
* `404` 无对应资源
* `500` 服务器内部错误
* `503` 服务器超负荷

### 一次HTTP事务过程

1. 域名解析
2. TCP3次握手
3. 建立TCP连接后发起HTTP请求
4. 服务器响应并返回资源
5. 浏览器解析资源
6. 浏览器渲染

---

<a id="HTTP2"></a>

## HTTP/2

> [HTTP/2协议“多路复用”实现原理](https://segmentfault.com/a/1190000016975064)

* 采用二进制格式而非文本格式
* 多路复用
* 压缩报文头
* 允许服务端主动“推送”到客户端缓存中

### 多路复用实现

基于**二进制帧**设计，每个帧会带有**流**的信息，在一个TCP连接中可以存在多条流

---

<a id="HTTPS"></a>

## HTTPS

在HTTP的基础上加入SSL或TLS层

### HTTPS加密过程

* client请求server，server返回证书公钥
* client验证证书，无效就弹出警告，有效就用公钥加密一个随机值，并将加密后的密钥发送给server
* server用私钥解密密钥，然后用密钥加密要发送的内容
* client用密钥解密信息

---

<a id="websocket"></a>

## websocket

> [websocket原理](https://www.cnblogs.com/nnngu/p/9347635.html)

建立在TCP协议之上，握手阶段使用http协议，握手成功后告知浏览器切换成websocket协议，websocket是持久化的协议，且无同源策略。

---

<a id="web安全"></a>

## web安全

* XSS（跨站脚本）: 一是反射型的，如js脚本注入，是非持久化的；二是存储型的，如cookie盗取
* CSRF（跨站点请求伪造）: 请求加盐、加密
* SQL : 特殊字符进行转义

---

<a id="跨域"></a>

## 跨域

浏览器的同源策略（协议、域名、端口）防止恶意脚本、保护用户信息安全。如果不同源，cookie、localstorage、dom、ajax均无法获取或操作。

### 解决方法

####  `window.name`

```html
<iframe src=""></iframe>
<script>
  var iframe = document.querySelector('iframe')
  iframe.style.display = 'none'
  iframe.onload = function () {
    console.log(iframe.contentWindow.name) // 获取跨域
    iframe.contentWindow.close() // 关闭网页
  }
</script>
```

#### `postMessage`

```html
<iframe src=""></iframe>
<script>
// http://main.com
var iframe = document.querySelector('iframe')
iframe.postMessage('{}', 'http://other.com') // 发送

// http://other.com
window.addEventListener('message', function (e) {
  if (e.origin === 'http://main.com') {
    console.log(e.data) // {}
  }
})
</script>
```

#### jsonp

* 客户端：

```html
<script>
  function func (data) {
    console.log(data)
  }
</script>
<script type="text/javascript" src="api_path?callback=func"></script>
```

* 服务端：

```js
res.type('text/javascript')
res.send(req.query.callback + '({})')
```

