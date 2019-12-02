<a id="原型、原型链、对象、构造函数"></a>

## 原型、原型链、对象、构造函数

![原型、对象、构造函数](./asset/fn-prototype-proto.png)

* 对象：无序的属性集合
* 构造函数：创建特定类型的对象
* 原型：只有函数有且每个函数都有prototype（原型）属性，指向函数的原型对象，原型对象包含函数实例共享的方法和属性
* 原型链：除了`undefined`和`null`外，JavaScript其他值都有`__proto__`隐式原型

---

<a id="var、let、const">

## var、let、const

* **let**
  * 允许你声明一个作用域被限制在块级中的变量、语句或者表达式
  * let绑定不受变量提升的约束，这意味着let声明不会被提升到当前
  * 该变量处于从块开始到初始化处理的“暂存死区”
* **var**
  * 声明变量的作用域限制在其声明位置的上下文中，而非声明变量总是全局的
  * 由于变量声明（以及其他声明）总是在任意代码执行之前处理的，所以在代码中的任意位置声明变量总是等效于在代码开头声明
* **const**
  * 声明创建一个值的只读引用 (即指针)
  * 基本数据当值发生改变时，那么其对应的指针也将发生改变，故造成 `const`申明基本数据类型时
  * 再将其值改变时，将会造成报错， 例如 `const a = 3` ; `a = 5`时 将会报错
  * 但是如果是复合类型时，如果只改变复合类型的其中某个`Value`项时， 将还是正常使用

## 箭头函数与普通函数

* **箭头函数的`this`永远指向其上下文的`this`，任何方法都改变不了其指向，如`call()`、`apply()`、`bind()`**
  * 箭头函数是匿名函数，不能作为构造函数，不能使用new
  * 不能绑定`arugments`
  * 不能绑定`this`，`this`为所在上下文的`this`，即使使用`apply`或`call`也不能改变
  * 没有原型属性
  * 不能当作`Generator`函数，不能使用`yield`
* 普通函数的`this`指向调用它的哪个对象

```js
let Fn = () => {
  console.log(arguments) // => RefrenceError: arguments is not defined
}
let f = new Fn() // => TypeError: not a constructor
console.log(Fn.prototype) // => undefined

let obj = {
  a: 1,
  fn: () => {
    console.log(this.a) // => udnefined
    console.log(this) // => Window
  },
  f: function () {
    console.log(this.a) // => 1
    console.log(this) // => {a: 1, fn: ƒ, f: ƒ}
  },
}
```