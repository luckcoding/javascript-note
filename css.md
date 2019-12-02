<a id="link-import"></a>

## `link`与`@import`

1. `link`最大限度支持并行下载，`@import`过多嵌套导致串行下载，出现`FOUC`
2. `link`可以通过`rel="alternate stylesheet"`指定候选样式

---

<a id="FOUC"></a>

## FOUC

* 页面闪烁（Flash Of Unstyled Content）：样式加载之前浏览器以默认样式显示，加载渲染新样式导致的页面闪烁
* **解决方法**：把样式放到`head`内

---

<a id="盒子模型"></a>

## 盒子模型

`margin + border + padding + content`

* w3c标准盒子模型（`box-sizing: content-box`）：`width = content`
* IE盒子模型（`box-sizing: border-box`）： `width = border + padding + content`

---

<a id="CSS选择器"></a>

## CSS选择器

选择器 | 权重
----- | -----
!important | 无穷
内敛样式 | 1000
ID | 0100
类、伪类和属性选择器 | 0010
标签、伪元素选择器 | 0001
通配符、子选择器、相邻选择器 | 0000

---

<a id="inline-block间隙"></a>

## inline-block间隙

* 原因：行内排版时**被当作字体处理**，间隙即是**white-space**，所以字体越大间隙越大
* 解决：父元素设置`display: table;word-spacing:-1em;`

---

<a id="position属性"></a>

## position属性

* `absolute`绝对定位。是相对于`static`定位**之外**的第一个父元素的`padding`来定位的
* `relative`相对定位
* `fixed`固定定位
* `sticky`类似`relative+fixed`，必须指定`top|right|bottom|left`中的一个作为阀值
* `static`默认值，无定位
* `inherit`继承父元素

---

<a id="flex布局"></a>

## flex布局

### 父容器

* 主轴方向：`flex-direction: row|row-reverse|column|column-reverse;`
* 换行方式：`flex-wrap: nowrap|wrap|wrap-reverse;`
* 主轴方向+换行方式缩写：`flex-flow: row nowrap;`
* 主轴对其方式：`justify-content: flex-start|flex-end|center|space-between|space-around`
* 交叉轴对其方式：`align-items: flex-start|flex-end|center|baseline|stretch`
* 多根轴线对其方式：`align-content: flex-start|flex-end|center|space-between|space-around|stretch`

### 子容器

* 主轴排序：`order: 0`默认为0，越小越靠前
* 放大：`flex-grow: 0`默认为0，如果存在剩余空间也不放大
* 缩小：`flex-shrink: 1`默认为1，如果空间不足，该项目将缩小
* 尺寸：`flex-basis: 100px`默认为`auto`
* 缩写flex：`flex: flex-grow flex-shrink flex-basis;`默认为`auto|0`。`flex: auto; => flex: 1 1 auto;`、`flex: none; => flex: 0 0 auto;`

---

<a id="em、rem、vw、vh"></a>

## em、rem、vw、vh

### em与rem

* em：相对于**父元素**的字体大小的倍数
* rem：相对于**根元素（html）**的字体大小的倍数

### vw与vh

* vw：窗口宽度均分为100
* vh：窗口高度均分为100
* vmin：窗口宽或高中最小的均分为100
* vmax：窗口宽或高中最大的均分为100

---

## padding和margin的百分比

当padding和margin的百分比是相对于**父元素的width**

---

<a id="垂直水平居中"></a>

## 垂直水平居中

* `flex`
* `position + transform`
* `table`

```html
<div class="outer">
    <div class="inner"></div>
</div>

<style>
/* 第一种 flex */
.outer {
    display: flex;
    align-items: center;
    justify-content: center;
}

/* 第二种 position + transform */
.outer {
    position: relative;
}
.inner {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}

/* 第三种 table */
.outer {
    display: table;
}
.inner {
    display: table-cell;
    text-align: center;
    vertical-align: center;
}
</style>
```

---

<a id="BFC"></a>

## 块级格式化上下文 BFC（Block Formatting Context）

### 创建BFC方式

* 根元素（`html`）
* 浮动元素（`float: left|right`）
* 绝对定位（`position: absolute|fixed`）
* `overflow: hidden|auto|scroll`
* 行内块元素（`display: inline-block`）
* 表格单元格（`display: table-cell`）
* 表格标题（`display: table-caption`）
* 匿名表格单元格元素（`display: table|table-row|table-row-group|table-header-group|table-footer-group|inline-table`）
* `contain: layout|content|paint`
* 弹性元素（`display: flex|inline-flex`）
* 网格元素（`display: grid|inline-grid`）

### BFC布局规则

* BFC就是页面上一个隔离的独立容器，容器内外元素不相互影响
* 内部的box会在垂直方向，一个接一个的放置
* 同一个BFC的两个相邻元素的margin会发生重叠。每个元素的左外边距与包含块的左边界相接触，即使浮动元素也是如此；
* BFC的区域不会与float的元素区域重叠
* 计算BFC的高度时，浮动子元素也参与计算

### BFC能解决的问题

* 父元素塌陷
* 外边距重叠
* 清除浮动

---

<a id="清除浮动"></a>

## 清除浮动

```css
/* 第一种 clearfix */
.clearfix {
    zoom: 1;
}
.clearfix:after {
    content: '';
    display: block;
    height: 0;
    clear: both;
    visibility: hidden;
}

/* 第二种 出发父盒子BFC */
.outer {
    overflow: hidden;
}
```

---

<a id="三栏布局（左中右、上中下）"></a>

## 三栏布局（左中右、上中下）

### 左中右（中间自适应，两边定宽）

```html
<!-- 第一种 flex -->
<div class="container"> 
    <div class="left">left</div>
    <div class="main">main</div>
    <div class="right">right</div>
</div>
<style>
    .container {
        display: flex
    }
    .left {
        flex-basis: 100px;
    }
    .main {
        flex: 1;
    }
    .right {
        flex-basis: 100px;
    }
</style>

<!-- 第二种 position + margin -->
<div class="container"> 
    <div class="left">left</div>
    <div class="right">right</div>
    <div class="main">main</div>
</div>
<style>
    .left,.right {
        position: absolute;
        top: 0;
        width: 100px;
    }
    .left {
        left: 0;
    }
    .right {
        right: 0;
    }
    .main {
        margin: 0 100px;
    }
</style>

<!-- 第三种 float + margin -->
<div class="container"> 
    <div class="left">left</div>
    <div class="right">right</div>
    <div class="main">main</div>
</div>
<style>
    .left {
        float: left;
        width: 100px;
    }
    .right {
        float: right;
        width: 100px;
    }
    .main {
        margin: 0 100px;
    }
</style>
```

### 上中下（底部吸附）

> 当页面内容高度小于可视区域高度时，footer吸附在底部；当页面内容高度大于可视区域高度时，footer 被撑开排在 content 下方。参考 [使用 flex 实现 5 种常用布局](https://segmentfault.com/a/1190000012275086)

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

---

<a id="三角形"></a>

## 三角形

![](../asset/square.jpg)

```css
/* 第一种 利用 border（ 参考上图） */
.triangle {
    height: 0;
    width: 0;
    border-width: 100px;
    border-style: solid;
    /* border-color: red blue green yellow; */
    border-color: red transparent transparent transparent;
}

/* 第二种 利用 clip-path */
.triangel {
    width: 100px;
    height: 100px;
    background: red;
    clip-path: polygon(0px 0px, 0px 30px, 30px 0); // 形成三角形
    transform: rotate(225deg);// 旋转后角向下
}
```

---

<a id="多行截断"></a>

## 多行截断

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
    content: '...';
    font-weight: bold;
    position: absolute;
    bottom: 0;
    right: 0;
    padding: 0 20px 1px 45px;
    background: #fff;
}
```

---

<a id="一像素边框"></a>

## 一像素边框

> 移动端 `window.devicePixelRatio` 不同导致

```css
.border-1px {
    position: relative;
}
.border-1px:after {
    content: '';
	display: block;
    position: absolute;
    left: 0;
    right: 0;
    width: 100%;
    border-top: 1px solid red;
}

/* 多媒体查询 */
@media (min-device-pixel-radio: 1.5) {
    border-1px:after {
        transform: scaleY(0.7);
    }
}

@media (min-device-pixel-radio: 2) {
    border-1px:after {
        transform: scaleY(0.5);
    }
}
```

---

### 动画

**animation: name duration timing-function delay iteration-count direction;**

值 | 描述
----- | -----
`animation-name` | `keyframe`名称
`animation-duration` | 耗时
`animation-timing-function` | 速度曲线（`linear/ease/ease-in/ease-out/ease-in-out/cubic-bezier(n,n,n,n)`）
`animation-delay` | 延迟
`animation-iteration-count` | 次数（`n/infinite`）
`animation-direction` | 是否反向性（`normal/alternate`）

```css
.move {
    animation: myMove 5s infinite;
}

@keyframes myMove {
    from { top: 0px; },
    to { top: 100px; }
}
```