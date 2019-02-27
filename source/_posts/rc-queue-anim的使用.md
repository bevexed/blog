---
title: rc-queue-anim的使用
date: 2019-02-18 09:56:50
categories:
  - React
tags:
  - 插件
  - 动画
---
## 安装
```bash
> npm i -S rc-queue-anim
```

## 使用
```jsx harmony
import QueueAnim from 'rc-queue-anim';

ReactDOM.render(
  <QueueAnim delay={300} className="queue-simple">
    <div key="a">依次进场</div>
    <div key="b">依次进场</div>
    <div key="c">依次进场</div>
    <div key="d">依次进场</div>
  </QueueAnim>
, mountNode);
```

## 官网
https://motion.ant.design/
