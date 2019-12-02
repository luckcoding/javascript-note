## 解析

基本上分为四大模块：

* 创建`server`服务
* 创建`request`、`response`、`context`对象
* 实现基于**洋葱模型**的`middlewares`机制
* 错误捕获与处理

## 中间件

`koa`的中间件都挂载于内部数组`this.middleware`中，**洋葱模型**将所有中间件按序执行，每执行完一个中间件，遇到`next()`就会将控制权传递到下一个中间件。

```js
// 中间件洋葱模型的核心实现 => koa-compose v2 版本改写

function compose (middleware = []) {
    return function (context, next) {
        let index = -1 // 记录已绑定的
        function dispatch (i) {
            if (i <= index) return Promise.reject(new Error('next() called multiple times'))
            index = i
            // 取出函数，如果执行到最后一个中间件，那么next为上一个中间件
            let fn = i === middleware.length
                ? next
                : middleware[i]
            if (!fn) return Promise.resolve()
            try {
                return Promise.resolve(fn(context, dispatch.bind(null, i + 1)))
            } catch (err) {
                return Promise.reject(err)
            }
        }

        return dispatch(0)
    }
}
```