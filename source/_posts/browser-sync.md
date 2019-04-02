---
title: browser-sync 安装与使用
date: 2019-04-02 14:35:48
categories: 构建工具
tags: 
---
#  browser-sync
> 浏览器同步工具

## 安装
```bash
> npm i browser-sync -g
```

## 使用
```bash
> browser-sync init
```

### 普通使用
```bash
> browser-sync start --server --files "**"
```

### 跨域
```bash
> browser-sync start --proxy "localhost:8080" --files "**"
```

