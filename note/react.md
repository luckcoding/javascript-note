<a id="源码解析"></a>

## 源码解析

* `ReactElement`创建虚拟DOM节点对象

```js
const ReactElement = function (type, key, ref, self, source, owner, props) {
	const element = {
		type: type,
		key: key,
		ref: ref,
		props: props,
		...
	} // element结构
	...
	if (Object.freeze) {
      Object.freeze(element.props) // 冻结对象
      Object.freeze(element)
    }
}
```

* `jsx`解析代码并使用`ReactElement`创建节点对象。服务端渲染时`self`和`source`是必传参数，客户端渲染传的是`undefined`
* `React.createElement`、`React.cloneElement`2个方法基于`ReactElement`
* `Component`创建组建

```
function Component(props, context, updater) {
	this.props = props
	this.context = context
	...
	this.updater = updater || ReactNoopUpdateQueue
}

Component.prototype.setState = function(partialState, callback) {
	this.updater.enqueueSetState(this, partialState, callback, 'setState') // setState 是放在队列里去执行，所以一个代码块内连续调用setState，渲染只会产生一次
}

Component.prototype.forceUpdate = function(callback) {
	this.updater.enqueueSetState(this, callback, 'forceUpdate')
}
```

<a id="原理"></a>

## React 原理

> [深入理解React虚拟DOM](https://www.cnblogs.com/yumingxing/p/9438457.html)

### 一、什么是虚拟DOM

模拟DOM树的JavaScript对象树。

### 二、为什么使用虚拟DOM

目的是将所有操作统一处理完之后更新一次DOM，减少了大量的DOM渲染。

### 三、虚拟DOM的原理

当Node节点更新，虚拟DOM会比较2棵DOM树。（常规的树比较算法的时间复杂度为立方阶O(n<sup>3</sup>)，react diff 为线性阶O(n)）

* **分层求异**对 tree diff 进行算法优化
* 通过不同组件类型生成不同的树形结构，同时允许设置`shouldComponentUpdate()`对 component diff 进行优化
* 通过**设置*唯一`key`*，对element diff进行算法优化

## 性能优化

* 谨慎分配 `state`，避免不必要的`render`
* 状态合并
* 使用纯函数式组件
* 使用高阶组件替代继承
* 控制更新

## 生命周期

1. 初始化阶段: `constructor` `static getDerivedStateFromProps()` `UNSAFE_componentWillMount()` `render()` `componentDidMount()`
2. 更新阶段（state,props改变）: `UNSAFE_componentWillReceiveProps()` `static getDerivedStateFromProps()` `shouldComponentUpdate()` `UNSAFE_componentWillUpdate()` `render()` `getSnapshotBreforeUpdate()` `componentDidUpdate()`
3. 卸载阶段: `componentWillUnmount()`
4. 错误处理: `componentDidCatch()`

## Fiber

异步渲染UI。组件渲染和更新时，React分为**调和阶段**和**渲染阶段**，调和阶段是采用递归遍历方式，无法中断。Fiber重新实现堆栈帧，将可中断的任务拆分成多个子任务。在`render`和`setState`的时候开始创建，在`requestIdleCallback`空闲时执行，从根节点遍历Fiber节点并构建任务树，然后生成`EffectList`进行更新DOM。

## Hook

## getDerivedStateFromPros

## 事件合成

* 事件委托：把所有事件绑定到最外层，使用一个统一的事件监听器
* 自动绑定：每个事件的上下文均指向所属组件

## setState 机制