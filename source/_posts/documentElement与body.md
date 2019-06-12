---
title: documentElement 与 body
date: 2019-01-23 16:08:53
categories: 
  - DOM
tags: 
  - Scroll
---
# documentElement 与 body
## document.documentElement
> 返回 html 
> dom 中的 root 节点

## document.body
> 返回 html
> dom 中的 body 节点

## 差异
### scrollTop
- iso 上两者都可以获取 scrollTop，但是只有`body`可以设置该值
#### 兼容解决方案
> 对两则同时赋值
```js
let top = 100
document.documentElement.scrollTop = top
document.body.scrollTop = top
```

