---
title: react-router-dom的简单使用
date: 2019-02-13 10:19:28
categories:
  - React
tags:
  - Route
---
## 安装
```bash
> yarn add react-router react-router-dom -S
```

## 引入
```javascript
import {Swtich, Route, Router, HashHistory, Link} from 'react-router-dom';
```

## 简单使用
```html
<HashRouter>
  <Switch>
    <Route exact path='/' component={home}>1212</Route>
    <Route exact path='/a' component={a}>12321312</Route>
    <Route component={a}>12321312</Route> {/* 默认组件*/}
  </Switch>
</HashRouter>
```

## API
> https://reacttraining.com/react-router/web/api/BrowserRouter
### <Router\>
#### history: object
#### children: node

### <BrowserRouter\>
#### basename: string
所有位置的基准 URL。
- 如果你的应用程序部署在服务器的子目录，则需要将其设置为子目录。
- `basename` 的正确格式是前面有一个前导斜杠，但不能有尾部斜杠。

#### getUserConfirmation: func
#### forceRefresh: bool
强制刷新页面
#### keyLength: number
#### children: node

### <HashRouter\>
#### basename: string
#### getUserConfirmation: func
#### hashtype: string
- slash: #/
- noslash: #
- hashbang: #!
#### children: node

### <StaticRouter\>
#### basename: string
#### location: string
#### location: object
#### context: object
#### children: node

### <MemoruRouter\>
#### initialEntries: array
#### initialIndex: number
#### getUserConfirmation: func
#### keylength: number
#### children: node

### <Link\>
#### to: string
#### to: object
- pathname: url
- search: query
- hash: #
- state: {} 
#### replace: func
#### innerRef: func
#### others

### <NavLink\>
#### activeClassName: string
#### activeStyle: object
#### exact: bool
#### strict: bool
#### isActive: func
#### location: object

### <Prompt\>
用于在位置跳转之前给予用户一些确认信息。当你的应用程序进入一个应该阻止用户导航的状态时（比如表单只填写了一半），弹出一个提示。
#### message: string
#### message: func
#### when: bool

### <Redirect\>
#### to: string
#### to: object
#### push: bool
#### from: string
#### exact: bool
#### strict: bool

### <Switch\>
#### location: object
#### children: node

### withRouter
将非路由组件变成路由组件
```javascript
import {withRouter} from 'react-router-dom'
export default withRouter(`组件名`)
```

### 编程式导航`history`
在`props`上有`history`对象，这个对象上存在多个个方法
- push
- 
- replace





