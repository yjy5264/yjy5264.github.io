# redux源码解析

#### 此篇文章可作为redux源码导读使用，只说明了其中部分核心代码，并进行了一些简化处理

## 用法回顾
1. 用createStore来创建store
2. 创建Action
3. 在Reducer中编写纯函数来处理Action
4. 用Store.dispatch()通过Action和Reducer来操作Store里的数据
5. 用Store.subscribe()来监听Store中的数据是否发生了变化
6. 用store.getState()来获取Store中的state，并更新视图

## 核心代码

### createStore(reducer, preloadedState, enhancer)
1. 初始化数据
```javascript
  let currentReducer = reducer
  let currentState = preloadedState //存放state，preloadedState是传入的默认值
  let currentListeners = [] //存放subscribe传入的listener
  let nextListeners = currentListeners
```
2. 创建dispatch, subscribe, getState等方法

### dispatch(action)
1. 执行reducer
```javascript
    currentReducer(currentState, action)
```
2. 执行listener
```javascript
    const listeners = (currentListeners = nextListeners)
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i]
      listener()
    }
```

### subscribe(listener)
```javascript
    nextListeners.push(listener)
```

### getState()
```javascript
    return currentState
```

### applyMiddleware(...middlewares)
```javascript
    const applyMiddleware = (...middlewares) => createStore => (...args) => {
        const compose = (...funcs) => {
          if (funcs.length === 0) return arg => arg
          if (funcs.length === 1) return funcs[0]
          return funcs.reduce((a, b) => (...args) => a(b(...args)))
        }
        const store = createStore(...args)
        let dispatch = () => {
          throw new Error(
            `Dispatching while constructing your middleware is not allowed. ` +
              `Other middleware would not be applied to this dispatch.`
          )
        }
        let chain = []
        const middlewareAPI = {
          getState: store.getState,
          dispatch: (...args) => dispatch(...args)
        }
        chain = middlewares.map(middleware => middleware(middlewareAPI)) //*1 middlewareAPI是store
        dispatch = compose(...chain)(store.dispatch) //*2 store.dispatch即是next
        return { ...store, dispatch } //*3 执行dispatch传入action
    }
```
#### compose是函数式编程的用法用于简化函数嵌套执行
#### 我们来看看middleware是怎么写的
```javascript
    const logger = store => next => action => {
      console.log('dispatching', action)
      let result = next(action)
      console.log('next state', store.getState())
      return result
    }
```
为什么会变成 store => next => action => ?
1. 第一个store是传入的middlewareAPI
2. 第二个next是传入的store.dispatch
3. 第三个action是执行dispatch传入的action  
(见注释)