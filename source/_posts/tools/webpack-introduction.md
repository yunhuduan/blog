---
title: webpack3.x入门(1)-概念
date: 2018-03-08 21:39:24
tags: 
	- webpack
categories:
	- tools
	- webpack
---
### 简介
本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。
它是高度可配置的，但是，在开始前你需要先理解四个核心概念：
- 入口(entry)
- 输出(output)
- loader
- 插件(plugins)

### 入口

入口起点(entry point)指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

每个依赖项随即被处理，最后输出到称之为 bundles 的文件中，我们将在下一章节详细讨论这个过程。

可以通过在 webpack 配置中配置 entry 属性，来指定一个入口起点（或多个入口起点）。

<!-- more -->

#### 单入口
```text
用法: entry: string|Array<string>
```

接下来我们看一个 entry 配置的最简单例子：
**webpack.conf.js**
```javascript
const config = {
  entry: {
    main:  './src/app.js'
  }
};

//上面一种的简写方式
module.exports = {
  entry: './src/app.js'
};

```
_当你向 entry 传入一个数组时会发生什么？向 entry 属性传入「文件路径(file path)数组」将创建“多个主入口(multi-main entry)”。
在你想要多个依赖文件一起注入，并且将它们的依赖导向(graph)到一个“chunk”时，传入数组的方式就很有用。_ 
#### 对象语法
```text
用法：entry: {[entryChunkName: string]: string|Array<string>}
```
**webpack.conf.js**
```javascript
const config = {
  entry: {
    app: './src/app.js',
    vendors: './src/vendors.js'
  }
};
```
对象语法会比较繁琐。然而，这是应用程序中定义入口的最可扩展的方式。

##### 常见场景
1.分离应用程序和第三方库
**webpack.conf.js**
```javascript
const config = {
  entry: {
    app: './src/app.js',//应用程序入口
    vendors: './src/vendors.js'//第三方库引入
  }
};
```
这是什么？从表面上看，这告诉我们 webpack 从 app.js 和 vendors.js 开始创建依赖图(dependency graph)。这些依赖图是彼此完全分离、互相独立的（每个 bundle 中都有一个 webpack 引导(bootstrap)）。这种方式比较常见于，只有一个入口起点（不包括 vendor）的单页应用程序(single page application)中。

为什么？此设置允许你使用 CommonsChunkPlugin 从「应用程序 bundle」中提取 vendor 引用(vendor reference) 到 vendor bundle，并把引用 vendor 的部分替换为 __webpack_require__() 调用。如果应用程序 bundle 中没有 vendor 代码，那么你可以在 webpack 中实现被称为长效缓存的通用模式。

2.多页面应用
**webpack.conf.js**
```javascript
const config = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
};
```
这是什么？我们告诉 webpack 需要 3 个独立分离的依赖图（如上面的示例）。

为什么？在多页应用中，页面跳转时页面重新加载新文档，并且资源被重新下载。然而，这给了我们特殊的机会去做很多事：

使用 CommonsChunkPlugin 为每个页面间的应用程序共享代码创建 bundle。由于入口起点增多，多页应用能够复用入口起点之间的大量代码/模块，从而可以极大地从这些技术中受益。

### 出口
```text
用法: 在 webpack 中配置 output 属性的最低要求是，将它的值设置为一个对象，包括以下两点：
1.filename 用于输出文件的文件名。
2.目标输出目录 path 的绝对路径。
```

output 属性告诉 webpack 在哪里输出它所创建的 bundles，以及如何命名这些文件。你可以通过在配置中指定一个 output 字段，来配置这些处理过程：
**webpack.conf.js**
```javascript
module.exports = {
	entry: {
		main:'./src/app.js'
	},
	output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js',//单个入口输出可以指定名字如:bundle.js
        //filename: '[name].js' //多入口输出使用占位符[name]
	}
};
```
我们通过 output.filename 和 output.path 属性，来告诉 webpack bundle 的名称，以及我们想要生成文件的目录。

#### 进阶设置
以下是使用 CDN 和资源 hash 的复杂示例：
```javascript
output: {
  path: "/home/proj/cdn/assets/[hash]",
  publicPath: "http://cdn.example.com/assets/[hash]/"
}
```
在编译时不知道最终输出文件的 publicPath 的情况下，publicPath 可以留空，并且在入口起点文件运行时动态设置。如果你在编译时不知道 publicPath，你可以先忽略

### loader
loader 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）。loader 可以将所有类型的文件转换为 webpack 能够处理的有效模块，然后你就可以利用 webpack 的打包能力，对它们进行处理。

本质上，webpack loader 将所有类型的文件，转换为应用程序的依赖图（和最终的 bundle）可以直接引用的模块。

> 注意，loader 能够 import 导入任何类型的模块（例如 .css,.vue文件等），这是 webpack 特有的功能，其他打包程序或任务执行器的可能并不支持。
我们认为这种语言扩展是有很必要的，因为这可以使开发人员创建出更准确的依赖关系图。

在 webpack 的配置中 loader 有两个目标：
- test 属性，用于标识出应该被对应的 loader 进行转换的某个或某些文件。
- use 属性，表示进行转换时，应该使用哪个 loader。
**webpack.config.js**
```javascript
const path = require('path');

const config = {
  entry: '.src/app.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  }
};
module.exports = config;
```
以上配置中，对一个单独的 module 对象定义了 rules 属性，里面包含两个必须属性：test 和 use。这告诉 webpack 编译器(compiler) 如下信息：
> “嘿，webpack 编译器，当你碰到「在 require()/import 语句中被解析为 '.txt' 的路径」时，
在你对它打包之前，先使用 raw-loader 转换一下。”

### 插件(plugins)
loader 被用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。插件接口功能极其强大，可以用来处理各种各样的任务。
想要使用一个插件，你只需要 require() 它，然后把它添加到 plugins 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 new 操作符来创建它的一个实例。
插件目的在于解决 loader 无法实现的其他事。
**webpack.config.js**
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
const webpack = require('webpack'); // 用于访问内置插件
const path = require('path');

const config = {
  entry: '.src/app.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new webpack.optimize.UglifyJsPlugin(),
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```
#### 剖析
webpack 插件是一个具有 apply 属性的 JavaScript 对象。apply 属性会被 webpack compiler 调用，并且 compiler 对象可在整个编译生命周期访问。
**ConsoleLogOnBuildWebpackPlugin.js**
```javascript
function ConsoleLogOnBuildWebpackPlugin() {

};

ConsoleLogOnBuildWebpackPlugin.prototype.apply = function(compiler) {
  compiler.plugin('run', function(compiler, callback) {
    console.log("webpack 构建过程开始！！！");

    callback();
  });
};
```


