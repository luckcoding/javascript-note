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

```js
function Component(props, context, updater) {
	this.props = props
	this.context = context
	...
	this.updater = updater || ReactNoopUpdateQueue
}

Component.prototype.setState = function(partialState, callback) {
	this.updater.enqueueSetState(this, partialState, callback, 'setState')
	// 创建此组件实例的renderer（可能是react-dom/react-native等等）来执行任务队列
}

Component.prototype.forceUpdate = function(callback) {
	this.updater.enqueueSetState(this, callback, 'forceUpdate')
}
```

## 虚拟DOM

虚拟DOM是描述DOM树的对象，目的是将所有操作统一处理完之后再进行DOM更新。（常规的树比较算法的时间复杂度为立方阶O(n<sup>3</sup>)，react diff 为线性阶O(n)）

* `tree diff`对比2棵树的同一层次的节点
* `component diff`通过不同组件类型生成不同的树形结构，同时允许设置`shouldComponentUpdate()`对进行优化
* `element diff`通过唯一`key`进行比较

<a id="生命周期"></a>

## 生命周期

1. 初始化阶段
	1. `constructor`
	2. `static getDerivedStateFromProps()`
	3. `UNSAFE_componentWillMount()`
	4. `render()`
	5. `componentDidMount()`
2. 更新阶段
	1. `UNSAFE_componentWillReceiveProps()`（onyl props改变）
	2. `static getDerivedStateFromProps()`（onyl props改变）
	3. `shouldComponentUpdate()`
	4. `UNSAFE_componentWillUpdate()`
	5. `render()`
	6. `getSnapshotBreforeUpdate()`
	7. `componentDidUpdate()`
3. 卸载阶段: `componentWillUnmount()`
4. 错误处理: 
	1. `static getDerivedStateFromError()`
	2. `componentDidCatch()`

### 更新机制

* `setState`后会进入到`shouldComponentUpdate`钩子函数，由该函数的返回值**决定是否调用`render`**
* 如果包含子组件，那么父组件`render`完成后进入子组件的生命周期，直到子组件的生命周期完成后，回到父组件`render`的后续生命周期
* `render`返回虚拟DOM，在其后进行`diff`计算，从而决定是否需要重新渲染
* `PureComponent`是浅比较

```js
// 父子组件初始化阶段的生命周期
Parent.constructor
Parent.getDerivedStateFromProps
Parent.render
Child.constructor
Child.getDerivedStateFromProps
Child.render
Child.componentDidMount
Parent.componentDidMount
```

### getDerivedStateFromPros(nextProps, prevState)

返回`null`表示不做任何改变

### shouldComponentUpdate(nextProps, nextState)

返回false控制组件不更新

### getSnapshotBreforeUpdate(prevProps, prevState)

其返回值会传入`componentDidUpdate(prevProps, prevState, fromSnapData)`

## setState



* 第一个参数可以是对象，也可以是`updater`函数。
* 第二个参数是回调函数，在`componentDidUpdate`后执行
* `setState`更新过程是异步的，会合并所有的更新（如果是`addEventListener`或`setTimeout`里，则不会合并更新）

## forceUpdate

跳过`shouldComponentUpdate()`直接调用`render()`钩子函数

## 性能优化

* 谨慎分配`state`，避免不必要的`render`
* 状态合并
* 使用纯函数式组件
* 使用高阶组件替代继承
* 控制更新
* 副作用代码放在`commit`阶段

## Fiber

> 参考: [图解Fiber架构](https://zhuanlan.zhihu.com/p/92832843)

除了React元素树（虚拟DOM）之外，有一个用于保存状态的内部实例树（Host Instance）。该内部实例树的新实现以及操作树的算法被成为Fiber。Fiber也是一个任务调和器：

* 可分片（拆分任务）
* 可中断（执行一个任务后可回头继续执行未完成的任务）
* 具备优先级（任务优先级高的先执行）

```js
function FiberNode(tag, pendingProps, key, mode) {
	this.tag = tag
	this.key = key
	...

	// Fiber
	this.return = null
	this.child = null
	this.sibling = null
	this.index = 0
	...
	// effects
	this.effectTag = NoEffect
	this.nextEffect = null
	...
}
```

### Fiber Tree的构建

通过child找子节点、sibling找兄弟节点、return找父节点的循环方法构建Fiber tree。

```js
function List () {
	return [1,2,3].map((i) => <span key={i}>{i}</span>)
}
function App () {
	return [
		<button>btn</button>,
		<List />,
		<div></div>
	]
}
ReactDom.render(<App />, document.getElementById('root'))
```

## Hook

hooks的数据作为Fiber组件上的节点。

### 调用原理

基于`FunctionalComponent`

1. 首先调用`prepareToUseHooks`初始化一些模块内的全局变量
2. 调用`finishHooks`



## 事件合成

* 事件委托：把所有事件绑定到最外层，使用一个统一的事件监听器
* 自动绑定：每个事件的上下文均指向所属组件

## setState

setState和forceUpdate