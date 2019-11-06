<a id="layout-1"></a>

# 圣杯布局、双飞翼布局、Flex布局和绝对定位布局的几种经典布局的具体实现示例

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>圣杯布局、双飞翼布局、Flex布局和绝对定位布局的几种经典布局的具体实现示例</title>
  <style>
    /**
     * 圣杯布局
     */
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
    .left,.main,.right {
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

    /**
     * 双飞翼布局
     */
    .left2,.main2,.right2 {
      min-height: 50px;
      float: left;
    }
    .left2 {
      background-color: green;
      width: 100px;
      margin-left: -100%;
    }
    .main2 {
      background-color: red;
      width: 100%;
    }
    .right2 {
      background-color: blue;
      width: 150px;
      margin-left: -150px;
    }
    .content {
      margin: 0 150px 0 100px;
    }

    .container2 {
      zoom: 1;
      background-color: #ccc;
    }
    .container2:after {
      display: block;
      height: 0;
      clear: both;
      content: '';
      visibility: hidden;
    }


    /**
     * flex 布局
     */
    .container3 {
      display: flex;
      min-height: 50px;
    }

    .left3 {
      background-color: green;
      order: -1;
      flex-basis: 100px;
    }
    .main3 {
      background-color: red;
      flex-grow: 1;
    }
    .right3 {
      background-color: blue;
      flex-basis: 150px;
    }

    /**
     * 绝对定位
     */
    
    .container4 {
      position: relative;
    }

    .left4,.main4,.right4 {
      min-height: 50px;
      top: 0;
    }

    .left4 {
      background-color: green;
      position: absolute;
      width: 100px;
      left: 0;
    }
    .main4 {
      background-color: red;
      margin: 0 150px 0 100px;
    }
    .right4 {
      background-color: blue;
      position: absolute;
      width: 150px;
      right: 0;
    }

  </style>
</head>
<body>
  <h1>具体参考文档：<a href="https://blog.csdn.net/wangchengiii/article/details/77926868" target="_black">圣杯布局、双飞翼布局、Flex布局和绝对定位布局的几种经典布局的具体实现示例</a></h1>
  <h3>要求</h3>
  <code style="background-color: #f2f2f2;">
    &lt;div class=&quot;container&quot;&gt;<br /> 
    &nbsp;&nbsp;&lt;div class=&quot;main&quot;&gt;main&lt;/div&gt; <br />
    &nbsp;&nbsp;&lt;div class=&quot;left&quot;&gt;left&lt;/div&gt; <br />
    &nbsp;&nbsp;&lt;div class=&quot;right&quot;&gt;right&lt;/div&gt; <br />
    &lt;/div&gt;
  </code>
  <ul>
    <li>三列布局，中间宽度自适应，两边定宽；</li>
    <li>中间栏要在浏览器中优先展示渲染；</li>
    <li>允许任意列的高度最高；</li>
    <li>允许增加额外的DOM节点，但不能修改现有节点顺序。</li>
  </ul>
  <h1>圣杯布局</h1>
  <p>圣杯布局是一种相对布局</p>
  <div class="container"> 
    <div class="main">main</div> 
    <div class="left">left</div> 
    <div class="right">right</div> 
  </div>

  <hr>

  <h1>双飞翼布局</h1>
  <div class="container2"> 
    <div class="main2">
      <div class="content">main</div>
    </div> 
    <div class="left2">left</div> 
    <div class="right2">right</div> 
  </div>

  <hr />

  <h1>Flex布局</h1>
  <div class="container3"> 
    <div class="main3">main</div> 
    <div class="left3">left</div> 
    <div class="right3">right</div> 
  </div>

  <hr>

  <h1>绝对定位布局</h1>
  <div class="container4"> 
    <div class="main4">main</div> 
    <div class="left4">left</div> 
    <div class="right4">right</div> 
  </div>
</body>
</html>
```