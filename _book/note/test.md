
### 闭包的理解

#### 资料

* [学习Javascript闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)

#### 按值传递问题

* 输出结果？

```
var z = 10;

function foo(){
    console.log(z);
}

(function(funArg){
    var z = 20;
    funArg();
})(foo)
```

> 引用类型的传参问题
> 
> 答案: 10

### 作用域链问题

* 输出结果？

```
var data = []
for (var k = 0; k < 3; k++) {
  data[k] = function () {
    console.log(k)
  }
}
data[0]()
data[1]()
data[2]()
```

> 独立作用域只能通过“函数(function)”代码类型的执行上下文创建
> 
> 结果: 3 3 3