---
title: webpack搭建简单环境
date: 2019-07-30 15:22:57
categories: 构建工具
tags: webpack
---
## 初始化 npm
> npm init

## 安装 webpack、webpack-cli
> npm i webpack webpack-cli -S

## 新建 webpack.dev.config.js
```js
module.exports = {
  entry: './src/index.js',
  output: {
    path: __dirname,
    filename: "./release/bundle.js"
  }
};
```

## 新建 src 目录
- src
  - index.js
- index.html
  
## 在 package.json 配置
```json
{
  "scripts": {
      "test": "",
      "dev": "webpack --config ./webpack.dev.config --mode development"
    }
}
```

## 安装插件
- webpack-dev-server 
- html-webpack-plugin
> npm i webpack-dev-server html-webpack-plugin -S

## 修改 webpack.dev.config.js
```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    path: __dirname,
    filename: "./release/bundle.js"
  },

  plugins: [
    new HtmlWebpackPlugin({
      template: "./index.html"
    })
  ],

  devServer: {
    contentBase: path.join(__dirname,'./release'), //根目录
    open: true,
    port: 9000
  }
};
```

## 修改 package.json
> "dev": "webpack-dev-server --config ./webpack.dev.config --mode development"

## 安装 babel 解析 ES6
- babel-core
- babel-loader
- babel-polyfill
- babel-preset-env
- babel-preset-latest
> npm i babel-core babel-loader babel-polyfill babel-preset-env babel-preset-latest -S

## 新建 .babelrc
```json
{
  "presets": [
    "es2015",
    "latest"
  ],
  "plugins": []
}
```

## 在 webpack.dev.config 中配置 loader
```json
module: {
    rules: [{
      test: /\.js?$/,
      exclude: /(node_modules)/,
      loader: 'babel-loader'
    }]
  }
```
