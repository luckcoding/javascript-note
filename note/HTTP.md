<a id="TCP、UDP"></a>

## TCP、UDP

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

<a id="HTTP2"></a>

## HTTP/2

> [HTTP/2协议“多路复用”实现原理](https://segmentfault.com/a/1190000016975064)

* 采用二进制格式而非文本格式
* 多路复用
* 压缩报文头
* 允许服务端主动“推送”到客户端缓存中

### 多路复用实现

基于**二进制帧**设计，每个帧会带有**流**的信息，在一个TCP连接中可以存在多条流

<a id="HTTPS"></a>

## HTTPS

在HTTP的基础上加入SSL或TLS层

<a id="XMLHttpRequest"></a>

## XMLHttpRequest

### 示例

```js
function ajax () {
  var formData = new FormData();
  formData.append('name', 'zhangsan');

  // 创建 xhr
  var xhr = new XMLHttpRequest();
  // 设置超时
  xhr.timeout = 3000;
  // 返回格式
  xhr.responseType = 'text';

  // 发送异步请求
  xhr.open('POST', 'http://127.0.0.1:3000/api', true);
  
  // 设置请求头
  xhr.setRequestHeader('X-Token', 'one1');
  xhr.setRequestHeader('X-Token', 'one2'); // 不会覆盖，而是追加append
  
  // 回调
  xhr.onload = function (e) {
    if (this.status == 200 || this.status == 304) {
      console.log(this.responseText);
    }
  }
  xhr.ontimeout = function (e) {};
  xhr.onerror = function (e) {};
  xhr.upload.onpregress = function (e){};
  
  // 发送
  xhr.send(formData);
}
```

### 获取response header

`getAllResponseHeaders` `getResponseHeader`

* 无法获取 response 中的 `Set-Cookie`、`Set-Cookie2`这2个字段，无论是同域还是跨域请求
* 对于跨域请求，允许获取字段只限于 “simple response header” 和 “Access-Control-Expose-Headers”

```js
xhr.getAllResponseHeaders()
xhr.getResponseHeader('xx')
```

### 指定`xhr.response`的数据类型

`xhr.responseType`

值 | 数据类型 | 说明
------------- | ------------- | -------------
"" | `String` | 默认值
"text" | `String` |
"document" | `Document` | 返回 `XML`
"json" | `javascript`对象  | IE10/IE11不支持
"blob" | `Blob` |
"arrayBuffer" | `ArrayBuffer` |

```js
var xhr = new XMLHttpRequest();
xhr.open();
xhr.responseType = 'json';
xhr.onload = function () {
  if (this.status == 200) {
    console.log(this.response);
  }
}
xhr.send();
```

### 获取response数据

* `xhr.response`
  * 默认 `""`
  * 请求未完成时: `xhr.responseType`为`""`、`"text"`时，值为`""`；其他为`null`
* `xhr.responseText`
  * 默认 `""`
  * 当`responseType`为`"text"`、`""`时，才能调用`xhr.responseText`，否则报错
  * 成功时会拿到正确值，请求为完成、失败时为`""`
* `xhr.responseXML`
  * 默认 `null`
  * 当`responseType`为`"text"`、`""`、`document`时，才能调用`xhr.responseText`，否则报错
  * 当请求成功并放回数据被正确解析时，才拿到正确值。请求为完成、失败、返回数据无法解析时为`null`

### 请求状态

```js
xhr.onreadystatechange = function () {
  switch(xhr.readyState) {
    case 0:break; // unsent
    case 1:break; // opened
    case 2:break; // headers_received
    case 3:break; // loading
    case 4:break; // done
  }
}
```

值 | 状态 | 描述
------------- | ------------- | -------------
0 | UNSENT | `xhr`对象被成功构造, `open()`方法未被调用
1 | OPENED | `open()`已调用, `send()`未调用
2 | HEADERS_REVEIVED | `send()`已被调用，响应头和相应状态已返回
3 | LOADING | 响应体`response entity body`下载中，`xhr.response`此时可能有数据
4 | DONE | 传输结束，无论成功或失败

### 设置请求超时

`xhr.timeout`。默认为`0`，即不设置超时。

* 请求开始。调用`xhr.send()`,触发`xhr.onloadstart`时
* 请求结束。`xhr.loadend`触发时

### 同步请求

`xhr.open(method, url, async=false)`

* `xhr.timeout`必须为`0`
* `xhr.withCredentials`必须为`false`
* `xhr.responseType`必须为`""`(`"text"`也不允许)

### 上传/下载速度

* 上传`xhr.upload.onprogress`
* 下载`xhr.onprogress`

```js
xhr.onprogress = update
xhr.upload.onprogress = update
function update (e) {
  if (e.lengthComputable) {
    var completedPercent = e.loaded / e.total;
  }
}
```

### `xhr.withCredentials`与`CORS`

>发送同域请求时，浏览器会将`cookie`自动加在`request header`中。跨域不会

跨域请求中，`client`端必须设置`xhr.withCredentials=true`，且`server`端必须设置`Access-Control-Allow-Credentials:true`，这样浏览器才会将`cookie`家在`request header`中

>一旦跨越`request`能够携带认证信息，`server`端不能将`Access-Contole-Allow-Origin`设置为`*`，而要设置为请求的域名

### `xhr` 事件

* `XMLHttpRequestEventTarget`定义了7个事件
  * `onloadstart`
  * `onprogress`
  * `onabort`
  * `ontimeout`
  * `onerror`
  * `onload`
  * `onloadend`
* `onreadystatechange`是`XMLHttpRequest`独有事件


事件 | 触发条件
------------- | -------------
`onreadystatechange` | 每当`xhr.readyState`改变触发时(由非`0`变为`0`时不触发)
`onloadstart` | 调用`xhr.send()`时触发
`onprogress` | `xhr.upload.onprogress`在上传阶段(即`xhr.send()`之后，`xhr.readystate=2`之前)触发；`xhr.onprogress`在下载阶段(即`xhr.readystate=3`)触发。均为50ms触发一次
`onload` | `xhr.readystate=4`
`onloadend` | 请求结束时（包括成功或失败）触发
`onabort` | `xhr.abort()`
`ontimeout` | 
`onerror` |

### 参考

[你真的会使用XMLHttpRequest吗？](https://segmentfault.com/a/1190000004322487)


## 跨域

浏览器的同源策略（协议、域名、端口）防止恶意脚本、保护用户信息安全。如果不同源，cookie、localstorage、dom、ajax均无法获取或操作。解决方法如下：

### `window.name`

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

### `postMessage`

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

### jsonp

客户端

```html
<script>
  function func (data) {
    console.log(data)
  }
</script>
<script type="text/javascript" src="api_path?callback=func"></script>
```

服务端

```js
res.type('text/javascript')
res.send(req.query.callback + '({})')
```

## websocket

> [websocket原理](https://www.cnblogs.com/nnngu/p/9347635.html)

建立在TCP协议之上，握手阶段使用http协议，是持久化的协议，且无同源策略。