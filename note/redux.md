## 源码实现

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

## 异步中间件原理

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