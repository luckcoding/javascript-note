## CSS3 选择器

[CSS 选择器参考手册](http://www.w3school.com.cn/cssref/css_selectors.asp)

<a id="移动端端一像素边框"></a>

## 移动端端一像素边框

> window.devicePixelRatio 不同导致

```css
.border-1px {
    position: relative;
}
.border-1px:after {
	display: block;
    position: absolute;
    left: 0;
    right: 0;
    width: 100%;
    border-top: 1px solid red;
    content: '';
}

/* 多媒体查询 */
@media (-webkit-min-device-pixel-radio: 1.5), (min-device-pixel-radio: 1.5) {
    border-1px:after {
        -webkit-transform: scaleY(0.7);
        transform: scaleY(0.7);
    }
}

@media (-webkit-min-device-pixel-radio: 2), (min-device-pixel-radio: 2) {
    border-1px:after {
        -webkit-transform: scaleY(0.5);
        transform: scaleY(0.5);
    }
}
```

<a id="BFC"></a>

## BFC

1. `float`为`left|right`
2. `overflow`为`hidden|auto|scroll`
3. `display`为`table-cell|table-caption|inline-block`
4. `posiotion`为`absolute|fixed`

<a id="清除浮动"></a>

## 清除浮动

```css
.clearfix {
    zoom: 1;
}
.clearfix:after {
    display: block;
    height: 0;
    clear: both;
    content: '';
    visibility: hidden;
}
```

<a id="圣杯布局"></a>

## 圣杯布局

> 三列布局，中间宽度自适应，两边定宽

```html
<div class="container"> 
    <div class="main">main</div> 
    <div class="left">left</div> 
    <div class="right">right</div> 
</div>
<style>
.container {
    zoom: 1;
    padding: 0 150px 0 100px;
    background-color: #ccc;
}
.container:after {
    display: block;
    height: 0;
    clear: both;
    content: '';
    visibility: hidden;
}
.left,
.main,
.right {
    position: relative;
    min-height: 50px;
    float: left;
}
.left {
    background-color: green;
    width: 100px;
    margin-left: -100%;
    left: -100px;
}
.main {
    background-color: red;
    width: 100%;
}
.right {
    background-color: blue;
    width: 150px;
    margin-left: -150px;
    right:  -150px;
}
</style>
```

<a id="双飞翼布局"></a>

## 双飞翼布局

```html
<div class="container"> 
    <div class="main">
        <div class="content">main</div>
    </div> 
    <div class="left">left</div> 
    <div class="right">right</div> 
</div>
<style>
.left,
.main,
.right {
    min-height: 50px;
    float: left;
}
.left {
    background-color: green;
    width: 100px;
    margin-left: -100%;
}
.main {
    background-color: red;
    width: 100%;
}
.right {
    background-color: blue;
    width: 150px;
    margin-left: -150px;
}
.content {
    margin: 0 150px 0 100px;
}

.container {
    zoom: 1;
    background-color: #ccc;
}
.container:after {
    display: block;
    height: 0;
    clear: both;
    content: '';
    visibility: hidden;
}
</style>
```

<a id="flex布局"></a>

## flex布局

```html
<div class="container"> 
    <div class="main">main</div> 
    <div class="left">left</div> 
    <div class="right">right</div> 
</div>
<style>
.container {
    display: flex;
    min-height: 50px;
}

.left {
    background-color: green;
    order: -1;
    flex-basis: 100px;
}
.main {
    background-color: red;
    flex-grow: 1;
}
.right {
    background-color: blue;
    flex-basis: 150px;
}
</style>
```

<a id="上中下布局"></a>

## 上中下布局

> 当页面内容高度小于可视区域高度时，footer 吸附在底部；当页面内容高度大于可视区域高度时，footer 被撑开排在 content 下方。参考 [使用 flex 实现 5 种常用布局](https://segmentfault.com/a/1190000012275086)

```html
<body>
    <header></header>
    <article></article>
    <footer></footer>
</body>
<style>
body {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
}
article {
    flex: auto;
}
</style>
```

<a id="高为宽的一半居中"></a>

## 高为宽的一半居中

```html
<body>
    <div class="wrapper">
        <div class="inner">Text</div>
    </div>
</body>
<style>
html, body {
    margin: 0;
    height: 100%;
}
.wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100%;
}
.inner {
    display: flex;
    flex: 1;
    align-items: center;
    justify-content: center;
    padding: 25% 0;
    height: 0;
    background: red;
}
</style>
```

<a id="正方形、三角形、扇形"></a>

## 正方形、三角形、扇形

![](../asset/square.jpg)

```css
.square {
    height: 0;
    width: 0;
    border-width: 100px;
    border-style: solid;
    border-color: red blue green yellow;
}
```

> 三角形把其中3个边框颜色设置为透明即可，扇形加上圆角即可

## 单行或多行截断

```css
/* 单行 */
.one {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

/* 多行（不兼容） */
.mut {
    overflow : hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
}

/* 多行（兼容） */
p {
    position: relative;
    height: 4.2em;
    line-height: 1.4em;
    overflow:hidden;
}
p:after {
    content:"...";
    font-weight:bold;
    position:absolute;
    bottom:0;
    right:0;
    padding:0 20px 1px 45px;
    background: #fff;
}
```