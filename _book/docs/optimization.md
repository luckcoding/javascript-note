## HTML， HTTP，web综合问题

### 10、`HTML5`的离线储存怎么使用，工作原理能不能解释一下？

*   在用户没有与因特网连接时，可以正常访问站点或应用，在用户与因特网连接时，更新用户机器上的缓存文件

*   原理：`HTML5`的离线存储是基于一个新建的`.appcache`文件的缓存机制(不是存储技术)，通过这个文件上的解析清单离线存储资源，这些资源就会像`cookie`一样被存储了下来。之后当网络在处于离线状态下时，浏览器会通过被离线存储的数据进行页面展示

*   如何使用：

    *   页面头部像下面一样加入一个`manifest`的属性；
    *   在`cache.manifest`文件的编写离线存储的资源
    *   在离线状态时，操作`window.applicationCache`进行需求实现

```source-js
CACHE MANIFEST
#v0.11
CACHE:
js/app.js
css/style.css
NETWORK:
resourse/logo.png
FALLBACK:
/ /offline.html
```


### 13、iframe的缺点
* 阻塞主页面的`Onload`事件
* 搜索引擎无法读取，不利于`SEO`
* `iframe`和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载
* 使用`iframe`之前需要考虑这两个缺点。如果需要使用`iframe`，最好是通过`javascript`动态给`iframe`添加`src`属性值，这样可以绕开以上两个问题

### 18、HTML全局属性(global attribute)有哪些

*   `class`:为元素设置类标识
*   `data-*`: 为元素增加自定义属性
*   `draggable`: 设置元素是否可拖拽
*   `id`: 元素`id`，文档内唯一
*   `lang`: 元素内容的的语言
*   `style`: 行内`css`样式
*   `title`: 元素相关的建议信息

### 19、Canvas和SVG有什么区别？

*   `svg`绘制出来的每一个图形的元素都是独立的`DOM`节点，能够方便的绑定事件或用来修改。`canvas`输出的是一整幅画布
*   `svg`输出的图形是矢量图形，后期可以修改参数来自由放大缩小，不会是真和锯齿。而`canvas`输出标量画布，就像一张图片一样，放大会失真或者锯齿

## CSS部分

### 1、css sprite是什么,有什么优缺点

*   概念：将多个小图片拼接到一个图片中。通过`background-position`和元素尺寸调节需要显示的背景图案。

*   优点：

    *   减少`HTTP`请求数，极大地提高页面加载速度
    *   增加图片信息重复度，提高压缩比，减少图片大小
    *   更换风格方便，只需在一张或几张图片上修改颜色或样式即可实现
*   缺点：

    *   图片合并麻烦
    *   维护麻烦，修改一个图片可能需要从新布局整个图片，样式

### 2、`display: none;`与`visibility: hidden;`的区别

*   联系：它们都能让元素不可见

*   区别：

    *   `display:none`;会让元素完全从渲染树中消失，渲染的时候不占据任何空间；`visibility: hidden`;不会让元素从渲染树消失，渲染师元素继续占据空间，只是内容不可见
    *   `display: none`;是非继承属性，子孙节点消失由于元素从渲染树消失造成，通过修改子孙节点属性无法显示`；visibility: hidden;`是继承属性，子孙节点消失由于继承了`hidden`，通过设置`visibility: visible;`可以让子孙节点显式
    *   修改常规流中元素的`display`通常会造成文档重排。修改`visibility`属性只会造成本元素的重绘。
    *   读屏器不会读取`display: none`;元素内容；会读取`visibility: hidden;`元素内容


### 17、PNG,GIF,JPG的区别及如何选

*   `GIF`

    *   `8`位像素，`256`色
    *   无损压缩
    *   支持简单动画
    *   支持`boolean`透明
    *   适合简单动画
*   `JPEG`

    *   颜色限于`256`
    *   有损压缩
    *   可控制压缩质量
    *   不支持透明
    *   适合照片
*   `PNG`
    *   有`PNG8`和`truecolor PNG`
    *   `PNG8`类似`GIF`颜色上限为`256`，文件小，支持`alpha`透明度，无动画
    *   适合图标、背景、按钮


### 21、如果需要手动写动画，你认为最小时间间隔是多久，为什么？（阿里）

*   多数显示器默认频率是`60Hz`，即`1`秒刷新`60`次，所以理论上最小间隔为`1/60＊1000ms ＝ 16.7ms`


### 25、CSS在性能优化方面的实践

*   `css`压缩与合并、`Gzip`压缩
*   `css`文件放在`head`里、不要用`@import`
*   尽量用缩写、避免用滤镜、合理使用选择器

### 26、CSS3动画（简单动画的实现，如旋转等）

*   依靠`CSS3`中提出的三个属性：`transition`、`transform`、`animation`
*   `transition`：定义了元素在变化过程中是怎么样的，包含`transition-property`、`transition-duration`、`transition-timing-function`、`transition-delay`。
*   `transform`：定义元素的变化结果，包含`rotate`、`scale`、`skew`、`translate`。
*   `animation`：动画定义了动作的每一帧（`@keyframes`）有什么效果，包括`animation-name`，`animation-duration`、`animation-timing-function`、`animation-delay`、`animation-iteration-count`、`animation-direction`

### 27、base64的原理及优缺点

*   优点可以加密，减少了`http`请求
* `base64`的体积约为原图的`4/3`
*   缺点是需要消耗`CPU`进行编解码

### 34、伪类和伪元素的区别

*   伪类表状态
*   伪元素是真的有元素
*   前者单冒号，后者双冒号

## JavaScript

### 1、闭包

*   闭包就是能够读取其他函数内部变量的函数

*   闭包是指有权访问另一个函数作用域中变量的函数，创建闭包的最常见的方式就是在一个函数内创建另一个函数，通过另一个函数访问这个函数的局部变量,利用闭包可以突破作用链域

*   闭包的特性：

    *   函数内再嵌套函数
    *   内部函数可以引用外层的参数和变量
    *   参数和变量不会被垃圾回收机制回收

**说说你对闭包的理解**

*   使用闭包主要是为了设计私有的方法和变量。闭包的优点是可以避免全局变量的污染，缺点是闭包会常驻内存，会增大内存使用量，使用不当很容易造成内存泄露。在js中，函数即闭包，只有函数才会产生作用域的概念

*   闭包 的最大用处有两个，一个是可以读取函数内部的变量，另一个就是让这些变量始终保持在内存中

*   闭包的另一个用处，是封装对象的私有属性和私有方法

*   **好处**：能够实现封装和缓存等；

*   **坏处**：就是消耗内存、不正当使用会造成内存溢出的问题

**使用闭包的注意点**

*   由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露
*   解决方法是，在退出函数之前，将不使用的局部变量全部删除

### 2、说说你对作用域链的理解

*   作用域链的作用是保证执行环境里有权访问的变量和函数是有序的，作用域链的变量只能向上访问，变量访问到`window`对象即被终止，作用域链向下访问变量是不被允许的
*   简单的说，作用域就是变量与函数的可访问范围，即作用域控制着变量与函数的可见性和生命周期

### 3、JavaScript原型，原型链 ? 有什么特点？

*   每个对象都会在其内部初始化一个属性，就是`prototype`(原型)，当我们访问一个对象的属性时

*   如果这个对象内部不存在这个属性，那么他就会去`prototype`里找这个属性，这`个prototype`又会有自己的`prototype`，于是就这样一直找下去，也就是我们平时所说的原型链的概念

*   关系：`instance.constructor.prototype = instance.__proto__`

*   特点：

    *   `JavaScript`对象是通过引用来传递的，我们创建的每个新对象实体中并没有一份属于自己的原型副本。当我们修改原型时，与之相关的对象也会继承这一改变
*   当我们需要一个属性的时，`Javascript`引擎会先看当前对象中是否有这个属性， 如果没有的

*   就会查找他的`Prototype`对象是否有这个属性，如此递推下去，一直检索到 `Object` 内建对象


### 5、Javascript如何实现继承？

*   构造继承

*   原型继承

*   实例继承

*   拷贝继承

*   原型`prototype`机制或`apply`和`call`方法去实现较简单，建议使用构造函数与原型混合方式

```source-js
 function Parent(){
        this.name = 'wang';
    }

    function Child(){
        this.age = 28;
    }
    Child.prototype = new Parent();//继承了Parent，通过原型

    var demo = new Child();
    alert(demo.age);
    alert(demo.name);//得到被继承的属性
  }
```

### 6、谈谈This对象的理解

*   `this`总是指向函数的直接调用者（而非间接调用者）
*   如果有`new`关键字，`this`指向`new`出来的那个对象
*   在事件中，`this`指向触发这个事件的对象，特殊的是，`IE`中的`attachEvent`中的`this`总是指向全局对象`Window`


### 11、模块化开发怎么做？

*   立即执行函数,不暴露私有成员

```source-js
var module1 = (function(){
　　　　var _count = 0;
　　　　var m1 = function(){
　　　　　　//...
　　　　};
　　　　var m2 = function(){
　　　　　　//...
　　　　};
　　　　return {
　　　　　　m1 : m1,
　　　　　　m2 : m2
　　　　};
　　})();
```


### 18、用过哪些设计模式？

*   工厂模式：

    *   工厂模式解决了重复实例化的问题，但还有一个问题,那就是识别问题，因为根本无法
    *   主要好处就是可以消除对象间的耦合，通过使用工程方法而不是`new`关键字
*   构造函数模式

    *   使用构造函数的方法，即解决了重复实例化的问题，又解决了对象识别的问题，该模式与工厂模式的不同之处在于
    *   直接将属性和方法赋值给 `this`对象;

### 19、为什么要有同源限制？

*   同源策略指的是：协议，域名，端口相同，同源策略是一种安全协议
*   举例说明：比如一个黑客程序，他利用`Iframe`把真正的银行登录页面嵌到他的页面上，当你使用真实的用户名，密码登录时，他的页面就可以通过`Javascript`读取到你的表单中`input`中的内容，这样用户名，密码就轻松到手了。

### 20、offsetWidth/offsetHeight,clientWidth/clientHeight与scrollWidth/scrollHeight的区别

*   `offsetWidth/offsetHeight`返回值包含**content + padding + border**，效果与e.getBoundingClientRect()相同
*   `clientWidth/clientHeight`返回值只包含**content + padding**，如果有滚动条，也**不包含滚动条**
*   `scrollWidth/scrollHeight`返回值包含**content + padding + 溢出内容的尺寸**

### 21、javascript有哪些方法定义对象

*   对象字面量： `var obj = {};`
*   构造函数： `var obj = new Object();`
*   Object.create(): `var obj = Object.create(Object.prototype);`


### 25、Node的应用场景

*   特点：

    *   1、它是一个`Javascript`运行环境
    *   2、依赖于`Chrome V8`引擎进行代码解释
    *   3、事件驱动
    *   4、非阻塞`I/O`
    *   5、单进程，单线程
*   优点：
    *   高并发（最重要的优点）
*   缺点：

    *   1、只支持单`核CPU`，不能充分利用`CPU`
    *   2、可靠性低，一旦代码某个环节崩溃，整个系统都崩溃


### 27、那些操作会造成内存泄漏？

*   内存泄漏指任何对象在您不再拥有或需要它之后仍然存在
*   `setTimeout` 的第一个参数使用字符串而非函数的话，会引发内存泄漏
*   闭包、控制台日志、循环（在两个对象彼此引用且彼此保留时，就会产生一个循环）

### 28、web开发中会话跟踪的方法有哪些

*   `cookie`
*   `session`
*   `url`重写
*   隐藏`input`
*   `ip`地址


### 30、介绍js有哪些内置对象？

*   `Object` 是 `JavaScript` 中所有对象的父对象
*   数据封装类对象：`Object`、`Array`、`Boolean`、`Number` 和 `String`
*   其他对象：`Function`、`Arguments`、`Math`、`Date`、`RegExp`、`Error`

### 31、说几条写JavaScript的基本规范？

*   不要在同一行声明多个变量
*   请使用`===/!==`来比较`true/false`或者数值
*   使用对象字面量替代`new Array`这种形式
*   不要使用全局函数
*   `Switch`语句必须带有`default`分支
*   `If`语句必须使用大括号
*   `for-in`循环中的变量 应该使用`var`关键字明确限定作用域，从而避免作用域污

### 32、JavaScript有几种类型的值？，你能画一下他们的内存图吗？

*   栈：原始数据类型（`Undefined`，`Null`，`Boolean`，`Numbe`r、`String`）
*   堆：引用数据类型（对象、数组和函数）
*   两种类型的区别是：存储位置不同；
*   原始数据类型直接存储在栈(`stack`)中的简单数据段，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储；
*   引用数据类型存储在堆(`heap`)中的对象,占据空间大、大小不固定,如果存储在栈中，将会影响程序运行的性能；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其
*   在栈中的地址，取得地址后从堆中获得实体

[![](https://camo.githubusercontent.com/d1947e624a0444d1032a85800013df487adc5550/687474703a2f2f7777772e77337363686f6f6c2e636f6d2e636e2f692f63745f6a735f76616c75652e676966)](https://camo.githubusercontent.com/d1947e624a0444d1032a85800013df487adc5550/687474703a2f2f7777772e77337363686f6f6c2e636f6d2e636e2f692f63745f6a735f76616c75652e676966)


### 34、eval是做什么的？

*   它的功能是把对应的字符串解析成`JS`代码并运行
*   应该避免使用`eval`，不安全，非常耗性能（`2`次，一次解析成`js`语句，一次执行）
*   由`JSON`字符串转换为JSON对象的时候可以用`eval，var obj =eval('('+ str +')')`


### 38、JSON 的了解？

*   `JSON(JavaScript Object Notation)` 是一种轻量级的数据交换格式

*   它是基于`JavaScript`的一个子集。数据格式简单, 易于读写, 占用带宽小

*   `JSON`字符串转换为JSON对象:

```
var obj =eval('('+ str +')');
var obj = str.parseJSON();
var obj = JSON.parse(str);

```

*   `JSON`对象转换为JSON字符串：

```
var last=obj.toJSONString();
var last=JSON.stringify(obj);

```


### 40、同步和异步的区别?

*   同步：浏览器访问服务器请求，用户看得到页面刷新，重新发请求,等请求完，页面刷新，新内容出现，用户看到新内容,进行下一步操作
*   异步：浏览器访问服务器请求，用户正常操作，浏览器后端进行请求。等请求完，页面不刷新，新内容也会出现，用户看到新内容

### 41、渐进增强和优雅降级

*   渐进增强 ：针对低版本浏览器进行构建页面，保证最基本的功能，然后再针对高级浏览器进行效果、交互等改进和追加功能达到更好的用户体验。

*   优雅降级 ：一开始就构建完整的功能，然后再针对低版本浏览器进行兼容


### 46、ECMAScript6 怎么写class么，为什么会出现class这种东西?

*   这个语法糖可以让有`OOP`基础的人更快上手`js`，至少是一个官方的实现了
*   但对熟悉`js`的人来说，这个东西没啥大影响；一个`Object.creat()`搞定继承，比`class`简洁清晰的多

### 47、什么是面向对象编程及面向过程编程，它们的异同和优缺点

*   面向过程就是分析出解决问题所需要的步骤，然后用函数把这些步骤一步一步实现，使用的时候一个一个依次调用就可以了
*   面向对象是把构成问题事务分解成各个对象，建立对象的目的不是为了完成一个步骤，而是为了描叙某个事物在整个解决问题的步骤中的行为
*   面向对象是以功能来划分问题，而不是步骤

### 48、面向对象编程思想

*   基本思想是使用对象，类，继承，封装等基本概念来进行程序设计
*   优点
    *   易维护
        *   采用面向对象思想设计的结构，可读性高，由于继承的存在，即使改变需求，那么维护也只是在局部模块，所以维护起来是非常方便和较低成本的
    *   易扩展
    *   开发工作的重用性、继承性高，降低重复工作量。
    *   缩短了开发周期

### 49、对web标准、可用性、可访问性的理解

*   可用性（Usability）：产品是否容易上手，用户能否完成任务，效率如何，以及这过程中用户的主观感受可好，是从用户的角度来看产品的质量。可用性好意味着产品质量高，是企业的核心竞争力
*   可访问性（Accessibility）：Web内容对于残障用户的可阅读和可理解性
*   可维护性（Maintainability）：一般包含两个层次，一是当系统出现问题时，快速定位并解决问题的成本，成本低则可维护性好。二是代码是否容易被人理解，是否容易修改和增强功能。


### 53、谈一谈你理解的函数式编程？

*   简单说，"函数式编程"是一种"编程范式"（programming paradigm），也就是如何编写程序的方法论
*   它具有以下特性：闭包和高阶函数、惰性计算、递归、函数是"第一等公民"、只用"表达式"


### 56、异步编程的实现方式？

*   回调函数

    *   优点：简单、容易理解
    *   缺点：不利于维护，代码耦合高
*   事件监听(采用时间驱动模式，取决于某个事件是否发生)：

    *   优点：容易理解，可以绑定多个事件，每个事件可以指定多个回调函数
    *   缺点：事件驱动型，流程不够清晰
*   发布/订阅(观察者模式)

    *   类似于事件监听，但是可以通过‘消息中心’，了解现在有多少发布者，多少订阅者
*   Promise对象

    *   优点：可以利用then方法，进行链式写法；可以书写错误时的回调函数；
    *   缺点：编写和理解，相对比较难
*   Generator函数

    *   优点：函数体内外的数据交换、错误处理机制
    *   缺点：流程管理不方便
*   async函数

    *   优点：内置执行器、更好的语义、更广的适用性、返回的是Promise、结构清晰。
    *   缺点：错误处理机制


### 58、Js动画与CSS动画区别及相应实现

*   `CSS3`的动画的优点
    *   在性能上会稍微好一些，浏览器会对`CSS3`的动画做一些优化
    *   代码相对简单
*   缺点
    *   在动画控制上不够灵活
    *   兼容性不好
*   `JavaScript`的动画正好弥补了这两个缺点，控制能力很强，可以单帧的控制、变换，同时写得好完全可以兼容`IE6`，并且功能强大。对于一些复杂控制的动画，使用`javascript`会比较靠谱。而在实现一些小的交互动效的时候，就多考虑考虑`CSS`吧


### 60、gulp是什么？

*   `gulp`是前端开发过程中一种基于流的代码构建工具，是自动化项目的构建利器；它不仅能对网站资源进行优化，而且在开发过程中很多重复的任务能够使用正确的工具自动完成
*   Gulp的核心概念：流
*   流，简单来说就是建立在面向对象基础上的一种抽象的处理数据的工具。在流中，定义了一些处理数据的基本操作，如读取数据，写入数据等，程序员是对流进行所有操作的，而不用关心流的另一头数据的真正流向
*   gulp正是通过流和代码优于配置的策略来尽量简化任务编写的工作
*   Gulp的特点：
    *   **易于使用**：通过代码优于配置的策略，gulp 让简单的任务简单，复杂的任务可管理
    *   **构建快速** 利用 `Node.js` 流的威力，你可以快速构建项目并减少频繁的 `IO` 操作
    *   **易于学习** 通过最少的 `API`，掌握 `gulp` 毫不费力，构建工作尽在掌握：如同一系列流管道

### 61、说一下Vue的双向绑定数据的原理

*   `vue.js` 则是采用数据劫持结合发布者-订阅者模式的方式，通过`Object.defineProperty()`来劫持各个属性的`setter`，`getter`，在数据变动时发布消息给订阅者，触发相应的监听回调


## 编程题

### 5、编写一个方法 求一个字符串的字节长度

*   假设：一个英文字符占用一个字节，一个中文字符占用两个字节

```source-js
function GetBytes(str){

        var len = str.length;

        var bytes = len;

        for(var i=0; i<len; i++){

            if (str.charCodeAt(i) > 255) bytes++;

        }

        return bytes;

    }

alert(GetBytes("你好,as"));

```