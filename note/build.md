## webpack

从入口文件开始，识别模块依赖，分析、编译并输出代码。

* 配置 `include` `exclude` 减少文件搜索范围
* 配置编译缓存 `babel-loader?cacheDirectory=ture`
* 使用 `HappyPack` 并行执行 `loader`
* 代码可写成按序加载

<a id="AMD-CMD-CommonJS-ES6"></a>

## AMD CMD CommonJS ES6

* AMD：依赖前置的异步模块加载器
* CMD：同步模块定义框架`require()`
* Commonjs：规范，在运行时加载模块
* ES6：编译时加载模块