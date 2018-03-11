---
title: webpack3.x基本使用
date: 2018-03-10 22:34:19
tags: 
	- webpack
categories:
	- tools
	- webpack
---
本文算是webpack的基本使用入门,如果刚接触建议先看下webpack的相关概念和介绍.{% post_link tools/webpack-introduction 点击这里 %}
### 初始化项目
使用npm初始化项目,按照npm提示一步步输入自己的相关信息.
```bash
	mkdir webpack-demo1;
	cd webpack-demo1;
	npm init;
```
并且安装webpack到项目目录下,请注意当前我们使用的webpack版本为3.x版本.如果直接安装webpack可能安装最新的4.x所以安装时请附带上版本号,我的是使用webpack3.6
```bash
	npm install --save-dev webpack@3.6
```
<!-- more -->
### 建立项目基本结构
项目基本结构如下:
```text
webpack-demo1
--src
----lib
----app.js
--index.html
--webpack.conf.js
```
- lib目录:存放其他js文件
- app.js:入口文件
- webpack.conf.js:webpack配置文件
###基本配置
我们编写下app.js中的文件内容如下:
```javascript
document.getElementById('test').innerHTML = 'hello';
```
下面来配置webpack,我们先从简单的输入输出开始,配置如下:
```javascript
const path = require('path');
var webpack = require('webpack');

module.exports = {
	entry: {
		app: "./src/app.js"
	},
	output: {
		path: path.resolve(__dirname, "dist"),
		filename: "[name].js",
		chunkFilename: "[chunkhash].js",
		publicPath: "/"
	},
	plugins: [

	],
}
```
打开package.json script中添加**"build": "node_modules/.bin/webpack --config  webpack.config.js"**
运行命令:
```bash
npm run build
```
这时项目的根目录中生成dist文件夹app.js文件内容为webpack编译后的内容,编译后的内容中存在webpack运行时代码和我们自己编写的代码
### 

