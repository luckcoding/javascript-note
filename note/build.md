## webpack

从入口文件开始，识别模块依赖，分析、编译并输出代码。**其编译结果为匿名函数，参数是一个对象，对象是包含页面名/eval(页面源码字符串)的键值对。**总的来说，webpack利用node将源码转义当作字符串放入`eval()`中，然后自己创建一个浏览器对象，并对这个对象进行处理，使其符合commonjs规范，最后根据规范调用编译好的字符串。

### 构成

* entry：入口，webpack构建的开始
* module：模块
* chunk：代码块
* loader：模块转换器
* plugin：插件

```js
{
    devtool: 'source-map', // 以source-map的形式显示错误代码位置，否则不好查看错误
    entry: {
        page1: './src/1.js',
        page2: ['./src/2.js', './src/2.1.js'],
    }, // 入口文件配置，支持数组形式
    output: {
        path: 'dist', // 打包输出目录
        filename: '[name].boundle.js',
        publicPath: 'dist', // 运行时的访问路径
    },
    resolveLoader: {
        root: path.join(__dirname, 'node_modules'), // 指定loader的路径
        alias: {
            "self-loader": path.resolve(__dirname, './self-loader.js'), //设置自定义loader别名
        }
    },
    resolve: {
        root: path.join(__dirname, '.'), // 绝对路径，配置模块的根路径
        alias: { // 配置模块别名
            '@component': './src/components'
        }
    },
    devServer: {
        historyApiFallback: true,
        onInfo: true,
        hot: true,
        inline: true,
        progress: true,
        port: 8000,
    }, // 服务器配置
    module: {
        loaders: [
            { test: /\.css$/, loader: 'style!css' },
        ]
    },
    plugins: [
        new webpack.optimize.CommonsChunkPlugin('common.js'), // 提取多个入口的公共部分
        new webpack.optimize.UglifyJsPlugin({}), // 压缩文件
        new webpack.DefinePlugin({ // 定义字符串插入代码，可判断当前开发环境
            __DEV__: JSON.stringify(JSON.parse(process.env.BUILD_DEV || 'true'))
        }),
        new webpack.ProvidePlugin({ // 定义全局变量
            JQ: 'jquery',
        })
    ],
}
```

### 构建流程

1. 初始化：从配置文件或shell读取或合并**得到最终的配置参数**，实例话插件`new Plugin()`
2. 开始编译：初始化`Compiler`对象、加载插件，通过执行`Compiler.cun`开始编译
3. 确定入口：根据`entry`找出所有入口文件
4. 编译模块：从`entry`触发，调用`loader`对模块进行转换，同时找出模块依赖的模块，依次递归，知道所有依赖模块完成转换
5. 生成资源：根据入口与模块之前的依赖关系，组装成`chunk`代码块，并生成文件输出列表
6. 写入资源：根据配置的输出路径和文件名，将文件写入，完成构建

### loader原理

* loader的执行顺序与配置顺序相反；
* 第一个执行的loader会接受源文件作为参数、，执行完毕返回的数据作为一下个loader的参数；
* 执行流程为
    * 加载loader代码
    * 获取pitch方法（`currentLoaderObject.pitch`），向loader传入`remainingRequest`、`previousRequest`等参数
    * 进入normal阶段，读取资源
    * 执行`runSyscOrAsync`进行loader转换
    * 把转换后的内容复制给模块的`_source`属性，然后调用模块源码的解析、分析资源依赖

### loader与plugin区别

loader用来对模块的源代码进行转换；plugin可以在任何阶段调用，能够跨loader进一步加工loader的输出

### ParallelUglifyPlugin

`UglifyJsPlugin`是单线程压缩代码，由于压缩耗时非常严重（先把代码解析成AST语法树，再用各种规则进行分析处理），因此`ParallelUglifyPlugin`开启多个子线程，把对多个文件的压缩任务分配给多个子线程完成，子线程依旧使用`UglifyJsPlugin`，进而提升压缩速度。

### 热加载原理

### 提取公共文件

```js
new webpack.optimize.SplitChunksPlugin({
    chunks: 'all',
    minSize: 30000,
    minChunks: 1,
    ...
    cacheGroups: {
        'YourName': {
            name: 'YourName',
            test: path.resolve(Dir, 'YourName.js'),
            ...
        }
    }
})
```

---

### 优化

* 配置 `include` `exclude` 减少文件搜索范围
* 配置编译缓存 `babel-loader?cacheDirectory=ture`
* 使用 `HappyPack` 并行执行 `loader`
* 代码可写成按序加载

---

## babel

* Parser解析代码转为AST树。**此处词法分析阶段把字符串心事的代码转换为令牌（tokens）流；语法分析阶段把令牌流转换成AST形式**
* Transformer利用配置好的plugins/presets把AST树转化为新的AST树
* Generator识别AST生成新代码。**深度遍历AST，构建代码转换后的字符串**

## git

## jenkins + git 持续集成环境

---

<a id="模块化"></a>

## 模块化

> CommonJS ---> AMD ---> CMD ---> UMD ---> ES6Module

### CommonJS && node.js

`CommonJS`规范，一个文件就是一个模块，采用同步加载模块，主要运行于服务端。`require`用于引入模块，`module.exports`输出的是**值的拷贝**。

### AMD && required.js

`AMD`规范，一个文件就是一个模块，模块和模块的依赖可以被异步加载，且申明依赖模块时，**会第一时间加载并执行模块内的代码**，主要用于浏览器。`define(id?, dependencies?, factory)`用来定义模块，`require`用于引入其他模块

```js
// mA.js
define(['jQuery', 'lodash'], function ($, _) {
    var name = 'name1'
    return {
        name,
    }
})

// main.js
require(['mA'], function (a) {
    console.log(a.name === 'name1') // => true
})
```

### CMD && sea.js

`CMD`规范，通用模块定义，可运行于浏览器和node，**执行时才加载**。

```js
// mA.js
defined(function (require, exports, module) {
    var func = function() {
        var a = require('./a')
        a.func()
        if(false) {
            var b = require('./b')
            b.func()
        }
    }
    exports.func = func
})

seajs.use('mA.js', function (m) {
    m.func()
})
```

### UMD && webpack

解决`CommonJS`和`AMD`不通用问题，同时支持老式的全局变量规范。

```js
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        define(['b'], factory)
    } else if (typeof module === 'object' && module.exports) {
        module.exports = factory(require('b'))
    } else {
        root.returnExports = factory(root.b)
    }
}...)
```

### ES6 Module && ES6

* 默认开启严格模式
* 会生成顶级的词法作用域，顶层的`this`为`undefined`

用过`import`导入，通过`export`导出，导出的是**值的引用**

```html
<script type="module" src="main.mjs" />
<script nomodule src="main.js" /> <!-- 处理兼容 -->
```