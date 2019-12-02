## redux源码实现

```js
function createStore (reducer) {
    var state
    var listeners = []
    var getState = () => state
    var dispatch = (action) => {
        state = reducer(state, action)
        listeners.forEach(l => l())
    }
    var subscribe = (listener) => {
        listeners.push(listener)
    }
    dispatch()
    return {
        getState,
        dispatch,
        subscribe,
    }
}
function combineReducers (reducers) {
    return function (state = {}, action = {}) {
        let newState = {}
        for (var key in reducers) {
            newState[key] = reducers[key](state[key], action)
        }
        return newState
    }
}
```

## redux异步中间件原理

```js
// redux 中间件
function compose(...funcs) {
    if (funcs.length === 0) {
        return arg => arg
    } else if (funcs.length === 1) {
        return funcs[0]
    } else {
        return funcs.reduce((a, b) => (...args) => a(b(...args)))
    }
}

// thunk
function thunk () {
    return ({ dispatch, getState }) => next => action => {
        return typeof action === 'function'
            ? action(dispatch, getState)
            : next(action)
        // 如果不是函数，就执行下一个中间件
    }
}
```

## mobx

## redux与mobx区别

* redux是函数式编程；mobx是面向对象编程
* redux每次都返回一个新的数据，其理想数据是`immutable`的，支持回溯；mobx的数据自始至终都是一份引用
* redux使用dispath进行广播，通过provider和connect对比差异，进而控制更新粒度；mobx基于observable可做到精确更新
* redux异步处理需要引入额外的代码库；mbox不需要