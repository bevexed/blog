---
title: Vue-cli搭建基于typeScript的Vue项目
date: 2019-02-01 10:09:14
categories: vue
tags: TypeScript 
---
## 安装 Vue-cli
```bash
> npm install -g @vue/cli
```

## 生成项目
选一个文件夹
```bash
> vue create <项目名称>
```

## 配置项目
### 选择 `Manually select features`
```bash
? Please pick a preset: 
  default (babel, eslint) 
❯ Manually select features  // 选这个
```
### 配置具体选项
这里根据具体情况选择,因为要使用 `TypeScript`,所以务必选上
```bash
? Please pick a preset: Manually select features
? Check the features needed for your project: 
 ◉ Babel
 ◉ TypeScript
 ◯ Progressive Web App (PWA) Support
 ◯ Router
 ◯ Vuex
❯◯ CSS Pre-processors
 ◉ Linter / Formatter
 ◯ Unit Testing
 ◯ E2E Testing
```

## 参考文章
[教你搭建基于typescript的vue项目](https://juejin.im/post/5ba75b355188255c5e66e4d3)


