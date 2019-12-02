<a id="语义化"></a>

## 语义化

* 正确使用标签
* 让页面内容结构化，便于SEO与浏览器解析
* 无样式情况下方便阅读

---

<a id="href和src"></a>

## href和src

* `src`：当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到该资源加载、编译、执行完毕。`img`标签的src也时如此
* `link`：识别为css文件，会并行下载资源且页面解析不会暂停

---

<a id="事件"></a>

## 事件

### 事件流

* 事件捕获：从最不具体到最具体
* 事件冒泡：从最具体到最不具体

* 捕获过程：`window -> document -> html -> head/body -> div`
* 冒泡过程：`div -> head/body -> html -> document -> window`

* IE使用的是事件冒泡
* W3C标准为事件捕获
* 现代浏览器是**先捕获，后冒泡**。

> `addEventListener(type, cb, false)`第1个参数为事件，第2个参数为回调，第3个参数为触发事件：true为捕获阶段、false为冒泡阶段，默认为false

### 绑定方法

1. html中绑定：`<button onclick="click()"></button>`
2. js中绑定：`element.onclick = function click () {}`
3. 事件监听：

```js
function addEventListener (element, event, func) {
    if (element.attachEvent) {
        element.attachEvent(`on${event}`, () => {
            func.call(element)
        })
    } else {
        element.addEventListener(event, func, false) // 
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

## getBoundingClientRect 与 offsetTop 获取的 top 的区别

