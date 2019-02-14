---
title: js-cookie
date: 2019-02-14 11:48:21
categories:
  - 插件
tags:
  - cookie
  - 插件
---

## 安装
```bash
> yarn add js-cookie -S
```
### cdn
```html
<script src="https://cdn.jsdelivr.net/npm/js-cookie@2/src/js.cookie.min.js"></script>
```
## 使用
```javascript
import Cookies from 'js-cookie'
```

## api
### Cookies.set
```javascript
Cookies.set('name', 'value');
Cookies.set('name', 'value', { expires: 7 });
Cookies.set('name', 'value', { expires: 7, path: '' });
```
### Cookies.get
```javascript
Cookies.get('name'); // => 'value'
Cookies.get('nothing'); // => undefined
Cookies.get(); // => { name: 'value' }
```
### Cookies.remove
```javascript
Cookies.remove('name');
```
## 参考网站
https://www.npmjs.com/package/js-cookie
