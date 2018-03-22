---
title: webpack3.x入门(3)-loader使用
date: 2018-03-10 22:34:19
tags: 
	- webpack
categories:
	- tools
	- webpack
---
## 简介

loader 用于对模块的源代码进行转换。loader 可以使你在 import 或"加载"模块时预处理文件。因此，loader 类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法。loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL。loader 甚至允许你直接在 JavaScript 模块中 import CSS文件！

## 使用loader方式

loader有三种使用方式:
- 配置(推荐):在webpack.config.js文件中配置指定的loader
- 内联: 在没个import或者require语句中显式指定loader
- cli: 在shell命令中指定

<!-- more -->

## loader执行顺序

1.在rules中的一条规则中如果use使用多个loader,顺序**自右向左**执行;
2.在整个rules数组中执行顺序**自下而上**


## loader配置事例

我们还是使用webpack-demo中simple目录下代码演示,先安装style-loader,css-loader
在app.js中修改如下:
```javascript
import './css/index.css'//添加引入css

var p = document.createElement('p');
p.innerHTML = "hello webpack";
p.setAttribute("id", "test");
document.body.appendChild(p);

```
修改webpack.config.js如下:
```javascript
const path = require('path');
var webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin')

// 在根目录下执行命令 ../node_modules/.bin/webpack
module.exports = {
	entry: {
		app: "./app.js"
	},
	output: {
		path: path.resolve(__dirname, "dist"),
		filename: "[name].js"
	},
	module: {
		rules: [
			{test: /\.css$/, use: ['style-loader','css-loader']}
		]
	},
	plugins: [
		new HtmlWebpackPlugin()
	]
}
```
然后我们运行命令:
```bash
cd simple
../node_modules/.bin/webpack
```





