---
title: redux的使用
date: 2019-02-13 14:11:39
categories:
  - React
tags:
  - React
  - 插件
---
# 文档
> https://www.redux.org.cn/
## redux 模块
- `redux`模块整体是一个对象模块
- 内部包含的函数：
  - createStore(reducers) // reducers:fucntion(state,action)(return newState)
  - combineReducers(reducers) // reducers:{reducer1,reducer2} 返回：function(state,action){rteturn newState}
  - applyMiddleware()

 
## 安装
### 基本包
```bash
> npm install --save  redux react-redux redux-thunk redux-devtools-extension
```
### 开发工具
```bash
npm install --save-dev redux-devtools
```

## 使用
### 目录结构
#### redux
##### store.js
redux 最核心的管理模块
```javascript
import {createStore,applyMiddleware} from "redux";
import thunk from 'redux-thunk'
import {composeWithDevTools} from "redux-devtools-extension";
import reducers from './reducers'

// 向外暴露 store 对象
export default createStore(reducers,composeWithDevTools(applyMiddleware(thunk)))
```

##### reducers.js
包含 n 个 reducer 函数 根据老的 `state` 和 指定的 `action` 返回一个 新的 `state`
```javascript
// 合并状态
import {combineReducers} from "redux"; 
import {AUTH_SUCCESS, ERROR_MSG} from "./action-types";

export const register = (user) => {
    return async dispatch => {
        // 发送注册的异步 ajax 请求
        const result = await reqRegister(user)
        console.log(result);
        if (result.code === 0) {
            // 分发成功的同步action
            dispatch(authSuccess(result.data))
        } else {
            dispatch(errprMsg(result.msg))
        }
    }
}

```

##### actioncs.js
包含 n 个 action creator
* 异步 action
* 同步 action

```javascript
import {
    AUTH_SUCCESS,
    ERROR_MSG
} from "./action-types";
```

##### action-types.js
包含 n 个 action-type 常量
```javascript
export const AUTH_SUCCESS='auth_success'
export const ERROR_MSG='error_msg'
```

##### app.js
引入`Provider`、`store`
```typescript jsx
import {Provider} from 'react-redux'
import store from './redux/store'
class App extends Component {
  render() {
    return (
      <div className="App">
        <Provider store={store}>
          <HashRouter>
            <Switch>
              <Route exact path='/register' component={Register}/>
              <Route exact path='/login' component={Login}/>
              <Route component={Main}/> {/* 默认组件*/}
            </Switch>
          </HashRouter>
        </Provider>
      </div>
    );
  }
}
```

## 参考文章
[Redux 入门教程](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html)
- https://cn.vuejs.org/v2/guide/state-management.html
- https://vuex.vuejs.org/
- https://cn.redux.js.org/docs/react-redux/
- http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_two_async_operations.html
- http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_three_react-redux.html
- https://redux-saga-in-chinese.js.org
- https://juejin.im/post/59e6cd68f265da43163c2821
- https://react-redux.js.org/introduction/why-use-react-redux
- https://segmentfault.com/a/1190000007248878
- http://es6.ruanyifeng.com/#docs/generator
- https://juejin.im/post/5ac1cb9d6fb9a028cf32a046
- https://zhuanlan.zhihu.com/p/35437092
- https://github.com/dvajs/dva/issues/1
- https://cn.mobx.js.org
- https://zhuanlan.zhihu.com/p/25585910
- http://imweb.io/topic/59f4833db72024f03c7f49b4
