## 第一部分 JavaScript 语言核心

### 第2章 词法结构

JavaScript是用 `Unicode` 字符集编写。 

### 第3章 类型、值和变量

数据类型分为2类：原始类型（number、string、boolean、null（空）、undefined（未定义））和对象类型，也可以分为拥有方法的类型和不能拥有方法的类型，同样可分为可变类型和不可变类型。

如果函数用来初始化（使用new运算符）一个新对象，称之为构造函数（constructor）。（Array、Function、Date、RegExp、Error）

#### 3.1 数字

##### 3.1.1 整形直接量

* 默认为10进制；
* 16进制：以 `0x` 或 `0X` 开头，跟随0-9、a-f（表示10-15）组成。

##### 3.1.3 运算符

* `+, -, *, /, %`
* `Math.pow(2, 53)` 2 的 53 次幂
* `Math.round(.6)` 四舍五入
* `Math.ceil(.6)` 向上取整
* `Math.floor(.6)` 向下取整
* `Math.abs(-5)` 绝对值
* `Math.max(x, y, z)` 最大值
* `Math.min(x, y, z)` 最小值
* `Math.random()` 0-1的随机数

##### 3.1.4 二进制浮点数和四舍五入错误

在任何使用二进制浮点数的编程语言中，并不能精确类似0.1这样的数，只能极其近似0.1。

#### 3.2 文本

`string`是一组由16位值组成的不可变的有序序列，字符串的长度`length`是其所含16位值的个数。

#### 3.3 布尔值

任意JavaScript的值都可以转换为布尔值。

#### 3.8 类型转换

##### A. 对象到字符串的转化经过了一下步骤：

* 1. 如果对象有`toString()`方法，则调用这个方法获取原始值并转换为字符串；
* 2. 如果没有`toString()`方法，或者无原始值返回，则调用`valueOf()`获取原始值；
* 3. 否则，如果无法从`toString()`或`valueOf()`获得原始值，则抛类型错误异常。

##### B. 字符串到对象

`valueOf()` => `toString()`，否则抛类型错误异常。

#### 3.10 变量作用域

### 第4章 表达式和运算符

### 第5章 语句

### 第6章 对象

### 第7章 数组

### 第8章 函数

### 第9章 类和模块

### 第10章 正则表达式的匹配模式

### 第11章 JavaScript的子集和扩展