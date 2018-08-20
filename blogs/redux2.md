# 企业级后台react-redux异步操作实践

## 一、redux基础

### redux
1. 通过 dispatch(action) -> 中间件 -> reducer处理数据 -> 改变store -> 使用subscribe()监听store改变更新视图 的方式管理状态
2. 将所有状态存储在一个store对象里面
3. reducer为纯函数，而异步操作由于结果的不确定性所以含有副作用，所以需要特殊处理

### react-redux
1. 容器组件，负责管理数据和业务逻辑，不负责UI呈现
2. UI组件，提供UI呈现，无状态即不使用this.state，状态全部由this.props提供
3. 由connect生成容器组件，每次store改变会调用connect，connect接收两个参数: mapStateToProps, mapDispatchToProps
4. mapStateToProps，将状态映射到UI组件的props
5. mapDispatchToProps，将dispatch方法映射到UI组件的props
6. Provider组件，使用content API将store从顶层开始传到每一层component供connect使用

## 二、redux处理异步的中间件

### redux-thunk
1. redux-thunk中间件允许action是一个方法
2. 中间件收到action后会执行action方法并将结果提供给reducer
3. action混乱导致不易维护

### redux-saga
1. saga会监听action并基于这个action执行Effects操作
2. Effects提供灵活的API，包括阻塞、非阻塞调用，取消、等待、race等操作
3. 方便隔离并执行异步操作，并易于测试

## 三、redux-request-async-middleware

### 先从redux文档中的异步action说起，每个接口调用需要dispatch三个同步action，分别是:
1. 一种通知 reducer 请求开始的 action。对于这种 action，reducer 可能会切换一下 state 中的 isFetching 标记。以此来告诉 UI 来显示加载界面。
2. 一种通知 reducer 请求成功的 action。对于这种 action，reducer 可能会把接收到的新数据合并到 state 中，并重置 isFetching。UI 则会隐藏加载界面，并显示接收到的数据。
3. 一种通知 reducer 请求失败的 action。对于这种 action，reducer 可能会重置 isFetching。另外，有些 reducer 会保存这些失败信息，并在 UI 里显示出来。

也就是一个接口发起是这样的

```javascript
dispatch(fetchPostsRequest(subject));
fetch(url).then(res => {
    dispatch(fetchPostsSuccess(subject, res));
}).catch(e => {
    dispatch(fetchPostsFailure(subject, e));
})
```

### 而我做的事情只是将这个操作封装进中间件里，特殊的地方在于:
1. 所有的异步请求共用这三个action
2. 用subject来区分是哪一个请求
3. 将所有的结果都放到store.requests里

### 中间件源码

```javascript
export const reduxRequest = store => next => action => {
    let result = next(action);
    let { type, subject, model } = action;
    let _next = action.next;
    if(type === FETCH_POSTS_REQUEST) {
        model().then(response => {
            _next && _next(response);
            store.dispatch(fetchPostsSuccess(subject, response));
        }).catch(error => {
            console.error(error);
            store.dispatch(fetchPostsFailure(subject, error));
        });
    }
    return result
};
```

1. 和redux-thunk一样，将方法放进action里
2. 中间件拦截FETCH_POSTS_REQUEST action，并进行异步处理

### reducer源码

```javascript
export const requests = (state = {}, action) => {
    switch (action.type) {
        case FETCH_POSTS_REQUEST:
            return assign({},
                state,
                {
                    [action.subject]: {
                        isFetching: true,
                        state: 'loading',
                        subject: action.subject,
                        response: null,
                        error: null,
                    }
                }
            );
        case FETCH_POSTS_FAILURE:
            return assign({},
                state,
                {
                    [action.subject]: {
                        isFetching: false,
                        state: 'error',
                        subject: action.subject,
                        response: state[action.subject].response,
                        error: action.error,
                    }
                }
            );
        case FETCH_POSTS_SUCCESS:
            return assign({},
                state,
                {
                    [action.subject]: {
                        isFetching: false,
                        state: 'success',
                        subject: action.subject,
                        response: action.response,
                    }
                }
            );
        case FETCH_POSTS_CLEAR:
            return assign({},
                state,
                {
                    [action.subject]: {
                        isFetching: false,
                        state: 'cleared',
                        subject: null,
                        response: null,
                        error: null,
                    }
                }
            );
        default:
            return state;
    }
}
```

1. 将结果放入该subject对应下的response，如果错误的话将错误信息放入error当中
2. isFetching表示当前的请求状态
3. 另外还加入了当前的状态state和subject信息

### 将请求进行封装

```javascript
const request = (subject, model, next) => {
    _dispatch(fetchPostsRequest(subject, model, next));
    return true;
};
```

1. 写一个方法来发起FETCH_POSTS_REQUEST action
2. 也就是说写请求的时候不用再管action这东西了，直接调用request方法

### 将结果进行封装

```javascript
const getResponse = state =>
    state
    && state.response !== null
    && state.response;

const getLoading = (states = []) =>
    states.reduce((pre, cur) =>
        pre || (cur && cur.isFetching)
        , false)
    || false;
```

1. 可以获取结果和多个请求下loading的状态
2. 有更多的操作或者格式还可以继续封装，比如列表

### 使用方法[redux-request-async-middleware](https://github.com/yjy5264/redux-request-async-middleware)

## 四、总结

1. 使用了redux来进行状态管理，而并不需要编写redux那一套复杂逻辑，最大程度的减少异步操作的复杂度
2. 适用于前端通过接口来处理和存储数据的项目
3. 接口由redux处理，而视图组件由内部state来处理，而外部只暴露简单的接口来进行操作，分离业务层和视图层
4. 对比react 16.3 new content API，redux的优势在于热插播的中间件和纯函数reducer写法

<a href="https://github.com/yjy5264/yjy5264.github.io"><img style="position: absolute; top: 0; left: 0; border: 0;" src="https://github.com/yjy5264/yjy5264.github.io/raw/master/images/forkMe.png" alt="Fork me on GitHub"></a>
