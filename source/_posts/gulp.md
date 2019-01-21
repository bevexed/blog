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

gulp.task('something', function() {
  // 将你的默认的任务代码放在这
});
```

## 运行
```bash
$ gulp
```

## 常用插件
> 都需要 npm 安装到依赖中 -S
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
### gulp.task(name[, deps], fn)
> 注册任务 
- name : 任务名
- deps ：
  - type ：Array
  - 一个包含任务列表的`数组`，这些任务会在你当前任务运行之前完成。
- fn : 回调函数


## 实例  
### 合并 压缩 JS 任务
```javascript
gulp.task('concatJs',function() {
  return gulp.src('路径') // 将数据读取到内存中
    .pipe(concat('临时合并文件名'))  // 临时合并文件
    .pipe(gulp.dest('输出文件目录')) // 输出文件
    .piep(uglify()) // 压缩文件
    .pipe(rename({suffix:'.min'})) // 改名
    .pipe(gulp.dest('输出文件目录')) // 输出文件
})
```
### 合并 压缩 CSS 任务
```javascript
const cssClean = require('gulp-css-clean');

// 编译less为css
gulp.task('cleanCss',function() {
  return gulp.src('路径') // 将数据读取到内存中
    .pipe(less())  // 编译less为css
})
// 合并并压缩 css文件
gulp.task('cleanCss',function() {
  return gulp.src('路径') // 将数据读取到内存中
    .piep(concat('临时合并文件名')) // 合并文件
    .pipe(rename({suffix:'.min'})) // 改名
    .pipe(cssClean({compatibility:'ie8'}))
    .pipe(gulp.dest('输出文件目录')) // 输出文件
})
```


