---
title: gulp 学习
date: 2019-01-21 17:32:53
tags: gulp
categories: 构建工具
---
# Gulp
> * 任务化
  Gulp.js 是一个自动化构建工具，开发者可以使用它在项目开发过程中自动执行常见任务。
> * 基于流
  Gulp.js 是基于 Node.js 构建的，利用 Node.js 流的威力，你可以快速构建项目并减少频繁的 IO 操作。
  Gulp.js 源文件和你用来定义任务的 Gulp 文件都是通过 JavaScript（或者 CoffeeScript ）源码来实现的。

## 安装  

### 全局安装
```bash
$ npm install -g gulp
```

### 局部安装
```bash
$ npm install -S gulp
```

## 使用
> 在项目根目录下创建一个名为 gulpfile.js 的文件：

```javascript
const gulp = require('gulp');

gulp.task('任务名', function() {
  // 将你的默认的任务代码放在这
});
```

## 运行
```bash
$ gulp
```

## 常用插件
### gulp-concat
> 合并 `js/css` 文件
### gulp-uglify
> 压缩 `js` 文件
### gulp-rename
> 文件重命名 
### gulp-less
>  编译 `less`
### gulp-clean-css 
> 压缩 css
### gulp-livereload
> 实时自动编译刷新

## 常用 API

