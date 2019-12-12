## 浏览器内核

* 分成**渲染引擎**和**JS引擎**

---

<a id="从输入URL到呈现页面过程"></a>

## 从输入URL到呈现页面过程

![what-url-happen](../../asset/what-url-happen.svg)

**参考**

* [SSL/TLS 握手过程详解](https://www.jianshu.com/p/7158568e4867)
* [当你在浏览器中输入 google.com 并且按下回车之后发生了什么？](https://github.com/skyline75489/what-happens-when-zh_CN)
* [what-happens-when-you-enter-url-in-browser/](https://vincenthou.github.io/what-happens-when-you-enter-url-in-browser/)

---

## DNS解析

1. 检查浏览器缓存是否有该域名对应的IP地址
2. 检查本机系统是否缓存过IP
3. 向本地域名服务器发起域名解析请求
4. 向根域名解析服务器发起域名解析请求，无的化返回gTLD域名解析服务器地址
5. 向gTLD发起解析请求，返回Name Server服务器地址（域名服务商）
6. Name Server服务器返回IP
7. 本地域名服务器解析，判断TTL时间进行缓存
8. 返回客户端

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
* `Cache-Control`：表示资源有效期，单位为秒（HTTP1.1引入）
  * `max-age=3600`：缓存有效期为3600秒
  * `no-cache`：不使用本地缓存
  * `no-store`：不缓存数据
  * `public`：代表http返回经过的任何路径中（代理服务器、浏览器等）都可以进行缓存
  * `private`：只有发起请求的浏览器才可以进行缓存
  * `s-maxage`：同`max-age`，只有在代理服务器中才会生效。如果代理服务器设置了`max-age`和`s-maxage`，只采用`s-maxage`
  * 多值写法：`Cache-Control: max-age=200,public`

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
* 传输层
* 网络层
* 数据链路层
* 物理层

---

<a id="TCP与UDP"></a>

## TCP与UDP

### TCP

TCP（传输控制协议）是一种面向连接的、可靠的、基于字节流的传输通讯协议。（传输层）

#### TCP三次握手

1. `SYN=1 seq=X` 客户端发送SYN包，并置发送序号为X，进入`SYN-SEND`
2. `SYN=1 ACK=X+1 seq=Y` 服务端发送SYN+ACK报文，并置发送序号为Y、确认序号为X+1，进入`SYN-RECV`
3. `ACK=Y+1；seq=Z` 客户端发送ACK包，并置发送序号为Z、确认序号为Y+1，进入`ESTABLISHED`

![TCP三次握手](../asset/TCP3.png)

#### TCP四次挥手

1. `FIN=1 seq=X` 主动方发送FIN包，并置发送序号为X，进入`FIN-WAIT`
2. `ACK=X+1` 被动方发送ACK包，并置确认序号为X+1，进入`CLOSE-WAIT`
3. `FIN=1 seq=y` 被动方发送FIN包，并置发送序号为Y，进入`LAST-ACK`
4. `ACK=Y seq=x` 主动方发送ACK报文，并置确认序号为Y+1，进入`CLOSED`

![TCP四次挥手](../asset/TCP4.png)

#### TCP如何保证传输可靠

* 将数据**截断**成合理的长度
* 当TCP发出一个报文段，启动一个定时器，如果不能及时收到一个确认，将**重发这个报文段**
* 当TCP收到另一端TCP发来的数据，**校验后**发送一个确认消息
* 校验出有误时，丢弃报文段，不做响应，TCP发送端超时后会**重发数据**
* TCP报文段作为IP数据报，达到可能会失序，TCP接受端会对**数据重新排序后交给应用层**
* TCP报文段作为IP数据报，可能会发生重复，对**重复数据进行丢弃**
* TCP可以进行**流量控制**，防止较快主机致较慢主机的缓存区溢出

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

### 报文解构

* 请求：
  * 起始行：`<method> <request-URL> <version>`
  * 头部：`<headers>`
  * 主体：`<entity-body>`
* 返回：
  * 起始行：`<version> <status> <reason-phrase>`
  * 头部：`<headers>`
  * 主体：`<entity-body>`

### 常见的HTTP请求头

协议头 | 说明 | 示例
-----|-----|-----
Accept | 可接受的响应内容类型 | Accept:text/plain
Authorization | 认证信息 | Authorization: Baber xxx
Cache-Control | 缓存机制 | Cache-Control: no-cache
Connection | 连接类型 | Connection: keep-alive
Cookie | | 
Content-Type | 请求体类型 | Context-Type: application/x-www-form-unlencoded
Host | 服务器域名及端口 | Host: www.baidu.com
If-Match | 缓存资源标记 | 
If-Modified-Since | 缓存资源过期时间 | 
Origin | 允许来源 | Origin: http://www.baidu.com
Referer | 浏览器访问的前一个页面 | Referer: http://baidu.com/prev
User-Agent | 浏览器标识 | 

### 常见的HTTP方法

* `GET` 请求已被URI（统一资源标识符）识别的资源，可通过URL传参
* `POST` 传输信息给服务器
* `PUT` 传输信息到对应URI位置
* `HEAD` 获取报文头，不返回报文体
* `DELETE` 删除对象URI资源
* `OPTIONS` 查询相应URI支持的HTTP方法

#### POST与GET的区别

* 语意不同
* GET请求可被缓存，POST不可
* GET没有请求体（除非强制设置），在TCP中只需传输一次（而非一个包）；POST将数据放在请求体中
* GET有最大1024字节长度大小限制

### HTTP状态码

* `1XX`：信息状态码
  * `100 Continue` 继续，一般在发送`post`请求时，已发送了`http header`之后服务端将返回此信息，表示确认，之后发送具体参数信息
* `2XX`：成功状态码
  * `200 OK` 正常返回信息
  * `201 Created` 请求成功并且服务器创建了新的资源
  * `202 Accepted` 服务器已接受请求，但尚未处理
* `3XX`：重定向
  * `301 Moved Permanently` 请求的网页已永久移动到新位置。
  * `302 Found` 临时性重定向（GET:303、POST:307）。
  * `303 See Other` 临时性重定向，且总是使用 `GET` 请求新的 `URI`。
  * `304 Not Modified` 自从上次请求后，请求的网页未修改过。
*   `4XX`：客户端错误
  * `400 Bad Request` 服务器无法理解请求的格式，客户端不应当尝试再次使用相同的内容发起请求。
  * `401 Unauthorized` 请求未授权。
  * `403 Forbidden` 禁止访问。
  * `404 Not Found` 找不到如何与 `URI` 相匹配的资源。
* `5XX:` 服务器错误
  * `500 Internal Server Error` 最常见的服务器端错误。
  * `503 Service Unavailable` 服务器端暂时无法处理请求（可能是过载或维护）。

### 一次HTTP事务过程

1. 域名解析
2. TCP3次握手
3. 建立TCP连接后发起HTTP请求
4. 服务器响应并返回资源
5. 浏览器解析资源
6. 浏览器渲染

### TCP连接的复用（长连接/并发请求）

http1.0 需要设置`Connection: keep-alive`才能复用TCP连接，http1.1默认开启复用，但是是串行请求

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

### XSS（跨站脚本攻击）

例如表单提交含有可执行的js内容，而服务端为转义，当通过内容形式发布后，浏览器就会执行脚本，从而导致被攻击、获取用户cookie等

#### 类型

* 反射型XSS。诱导用户打开恶意链接，服务端将链接中的参数
* 持久型XSS。通过提交恶意脚本并存储至服务器，当内容再次渲染时达到攻击目的

#### 防御

* 敏感cookie使用HttpOnly
* 用户输入进行转义
* 设置内容安全策略`Content-Security-Policy`头
  * `'Content-Security-Policy': 'default-src http: https:'`：表示只能通过外联的方式引用css或js
  * `'Content-Security-Policy': 'default-src \'self\''`：表示只能加载同域下的资源

> 请求默认都会带上cookie，token需要自己设置，这也是导致cookie会被XSS，而token不会被XSS的原因，但token会被用于CSRF

### CSRF（跨站点请求伪造）

在用户登陆目标网站后，诱导用户访问攻击页面，利用目标网站对用户的信任，以用户身份在攻击页面对目标网站发起伪造用户操作的请求，达到攻击目的

#### 防御

* 敏感请求使用验证码
* 验证HTTP Referer字段。（浏览器Referer也存在被篡改的可能）
* 请求头加入token

### SQL注入

攻击者把sql语句当表单提交，如果服务端把这些字段当作普通变量进行拼接sql查询语句，会导致数据库的异常操作

#### 防御

* 表单过滤，字符转义
* 数据库权限最小化
* 使用预编译语句，而不是直接拼接sql

### HTTP头攻击

HTTP协议在response header与content之间，有一个空格（两组CRLF字符），利用空格将执行脚本注入到请求header中，达到攻击目的

#### 防御

过滤所有的response headers字段中的非法字符，尤其是CRLF字符

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

script、img、link等标签是可以跨域的，只支持`GET`

```js
// 客户端
var head = document.querySelector('head')
var script = document.createElement('script')
script.src = 'http://xxx.com?callback=get'
head.appendChild(script)
function get (data) {
  console.log(data)
}

// 服务端
res.type('text/javascript')
res.send(req.query.callback + '({})')
```

#### cors（跨域资源共享）

##### 简单请求与复杂请求的区分

* 请求方式为`HEAD`、`GET`、`POST`三者之一
* HTTP不超出以下范围：
  * `Accept` / `Accept-Language`
  * `Context-Language`
  * `Last-Event-ID`
  * `Context-Type`限于三个值：`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`
* 同时满足上2个条件为简单请求，否则为复杂请求

##### 服务端配置

* `Access-Control-Allow-Origin: '*'`：必设。表示接受的源，可为`*`
* `Access-Control-Allow-Headers: 'token,custom'`：必设。表示接受的头信息字段，以`,`隔开的字符串
* `Access-Control-Allow-Credentials: true`：可选。表示是否允许发送Cookie（Ajax需配置`(xhr.withCredentials = true`，且`Orgin`必须设置为完整的域名，否则无法跨域保存）
* `Access-Control-Request-Method: 'POST,PUT'`：复杂请求必须设置。表示接受的请求类型

> 注意: 复杂请求会先发送一个预请求**OPTIONS**

#### nginx跨域配置

```
server {
  listen 80;
  server_name baidu.com;
  location / {
    
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Headers X-Requested-With;
    add_header Access-Control-Allow-Methods GET,POST,OPTIONS;

    proxy_pass http://.qq.com
  }
}
```

> 表单是允许跨域的

---

<a id="垃圾回收"></a>

## 垃圾回收

### 标记清除

对于脱离作用域的变量进行回收。进入作用域时进行标记，离开作用域时标记并回收。大部分浏览器都采用这种方式，区别在于垃圾回收的周期间隔不一样。

### 引用计数

跟踪记录每个值被引用的次数。被引用则次数加1。引用计数循环引用会导致内存泄露。

```js
function p () {
  var a = {}, b = {};
  a.name = b;
  b.name = a;
}
// 互相引用，被引用次数都为2，执行完后次数不能变为0，导致内存泄露
```

### 内存泄露的原因

* 全局变量引起的内存泄露
* 闭包
* dom清空或删除时，事件未清除导致的内存泄露
* 循环引用

### 减少垃圾回收开销

* 对象赋值`null`，解除循环引用
* 慎用闭包

---

## 流量劫持

### 常见劫持

* 域名劫持：劫持域名的DNS解析结果，将HTTP劫持到特定IP，使客户端和攻击者的服务器建立TCP连接
* 流量修改：数据通路上对页面进行固定的内容插入

### 原因

HTTP协议没有办法对通讯对方的身份进行校验，以及无法对据数完整性进行校验

### 解决

使用HTTPS

---

<a id="service-worker"></a>

## service worker

service worker是独立于当前页面运行在浏览器后台进程的脚本。

* 不能直接访问DOM。可通过`postMessage`通讯
* 只能在HTTPS或localhost环境下运行
* 不能使用xhr、localStorage，可使用fetch

### 生命周期

`注册sw` -> `安装` -> `激活(首次不生效，下次加载页面生效)` -> `处理fetch、message或被终止`

### 更新

1. 首次访问sw控制的页面时，sw会被立刻下载，之后浏览器至少每24小时会下载一次，这是浏览器自己的行为。
2. 如果下载的ws与现有的sw不同，就会进行安装。此时旧的sw还在运行，新的sw完成安装后进入waiting状态
3. 等所有已打开的页面都关闭后，就的sw停止，新的sw在下次打开时生效

**如何保证立即更新**

```js
navigator.serviceWorker.addEventListener('controllerchange', () => {
  window.location.reload(); // 刷新站点，避免新老sw交替问题
})
```

---

<a id="路由"></a>

## 路由

### hash

* 路径`#`后面的部分，用作锚点在页面进行导航，不会引起页面刷新
* 监听`hashchange`事件处理路由。触发`hashchange`的事件如下：
  * 浏览器前进后退
  * `<a>`标签改变
  * `window.location`改变

```js
// 路由实现
window.addEventListener('hashchange', handle)
function handle () {
  if (location.hash === '#/home') {}
}
```

### history

* 利用`pushState(data,title,url)`和`replaceState`改变URL的path部分，不会引起页面刷新
* 监听`popstate`事件处理路由。但`pushState/replaceState`不会触发此事件，通过拦截`pushState/replaceState`、`<a>`标签的点击事件来处理路由

```js
// 路由实现
window.addEventListener('popstate', handle)

var linkList = document.querySelectorAll('a[href]')
linkList.forEach(function (element) {
  element.addEventListener('click', function (event) {
    e.preventDefault()
    history.pushState(null, '', element.getAttribute('href'))
    handle()
  })
})

function handle () {
  if (location.pathname === '/home') {}
}
```

#### history API

例子 | 说明
------ | ------
`history.go(-1)` | 后退一页
`history.go(2)` | 前进2页
`history.back()` | 后退一页
`history.forward()` | 前进一页
`history.length` | 历史记录数量