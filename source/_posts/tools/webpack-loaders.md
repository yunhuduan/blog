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

### css-loader&style-loader

我们还是使用webpack-demo中simple目录下代码演示,先安装style-loader,css-loader

css/index.css代码如下:
```css
body {
    background-color: gray;
}

#test {
    font-size: 20px;
    color: #333333;
    font-style: italic;
}
```


在app.js中修改如下:
```javascript
import './css/index.css'//添加引入css

let testFunc = (str1, str2) => {
	return str1 + ',' + str2
}

let p = document.createElement('p');
p.innerHTML = "hello webpack," + testFunc('p1','p2');
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
打开simple/dist目录下的index.html文件可以看到背景颜色变了id为test的p标签样式也正确,然后再控制台的element中查看元素head
中存在style的内容正是index.css中内容;
css-loader:将css文件中的import或者url等抓换成base64或者合并导入文件
style-loader:将css内容写入到html的头部

### babel-loader
由上面的事例我们看到我们打包完成的js中还是es6的语法,我们需要使用另外一个loader来将es6转换成es5,安装babel-loader,babel-core,babel-preset-env

```bash
npm install -D babel-loader babel-core babel-preset-env 
```
在webpack.config.js中的rules中添加如下loader配置:
```javascript
{
	test: /\.js$/,
	exclude: /(node_modules|bower_components)/,
	use: {
		loader: 'babel-loader',
		options: {
			presets: ['env']
		}
	}
}

```
然后再执行命令查看dist目录下的app.js中的代码是否已经是es5


