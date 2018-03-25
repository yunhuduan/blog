---
title: webpack多页面配置
date: 2018-03-10 22:37:46
tags: 
	- webpack
categories:
	- tools
	- webpack
---
## 使用场景
当我们开发项目的时候不可能永远都是一成不变的单页面应用,有时候考虑到一些其他的因素影响会采用多页面应用.
举个例子:
- 1.当开发的项目关心SEO时候多页面应用更利于SEO
- 2.开发项目大部分页面需要登录认证才能访问,外页也有部分单但结构或大相径庭等;

## 多页面配置

### 新建文件入口文件
新建:app.js,app1.js,index.html,index1.html
代码路径: https://github.com/yunhuduan/webpack-demo/tree/master/mutipage

<!-- more -->

app.js代码如下:
```javascript
import com from './lib/com'
import t1 from "./lib/test";
document.getElementById("test1").innerHTML = "app module," + t1.test() + ',com:' + com.comFunc();
```

app1.js代码如下:
```javascript
import com from './lib/com'
import t from './lib/test1'
document.getElementById("test1").innerHTML = "app11 module," + t.test1() + ',com:' + com.comFunc();
```

新建:lib/com.js,lib/test.js,lib/test1.js

com.js代码如下:
```javascript
module.exports = {
	comFunc: function(){
		return 'com';
	}
}
```

test.js代码如下:
```javascript
module.exports = {
	test: function (a,b) {
		return 'im test js file';
	}
};
```


test1.js代码如下:
```javascript
module.exports = {
	test1: function (a,b) {
		return a+b;
	}
};
```

### 新建webpack配置文件
webpack.conf.js
```javascript
const path = require('path');
var webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
	entry: {
		app: "./app.js",
		app1: "./app1.js"
	},
	output: {
		path: path.resolve(__dirname, "dist"),
		filename: "[name]-[hash].js",
		chunkFilename: "chunk.[chunkhash].js",
		publicPath: "/"
	},
	plugins: [
		new webpack.optimize.CommonsChunkPlugin({
			name: 'manifest',
			minChunks: 2
		}),
		new HtmlWebpackPlugin({
			filename:  path.resolve(__dirname, 'dist/index.html'),
			template: 'index.html',
			inject: true,
			minify: {
				removeComments: true,
				collapseWhitespace: true,
				removeAttributeQuotes: true
			},
			hash: true,
			chunk:['manifest','app'],
			chunksSortMode: 'dependency'
		}),
		new HtmlWebpackPlugin({
			filename:  path.resolve(__dirname, 'dist/index1.html'),
			template: 'index1.html',
			inject: true,
			minify: {
				removeComments: true,
				collapseWhitespace: true,
				removeAttributeQuotes: true
			},
			hash: true,
			chunk: ['manifest','app1'],
			chunksSortMode: 'dependency'
		})
	]
}
```

运行命令:../node_modules/.bin/webpack --config webpack.config.js 

