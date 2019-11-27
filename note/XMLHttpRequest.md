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