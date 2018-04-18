---
title: babel基本使用
date: 2018-04-18 16:09:15
tags:
	- babel
categories:
	- tools
	- babel
---
## 简介
Babel是一个JavaScript的语法编译器,可以将ES6/7/8等将来使用的特性的代码转换成ES5,从而在现有的比较低版本的浏览器中使用.

## Babel基本使用

### 安装
由于 JavaScript 社区没有统一的构建工具、框架、平台等等，因此 Babel 正式集成了对所有主流工具的支持。 从 Gulp 到 Browserify，从 Ember 到 Meteor，不管你的环境设置如何，Babel 都有正式的集成支持。
本手册的目的主要是介绍 Babel 内建方式的安装，不过你可以访问交互式的[安装页面](http://babeljs.io/docs/setup/)来查看其它的整合方式。

Babel 的 CLI 是一种在命令行下使用 Babel 编译文件的简单方法。建议安装到项目的本地开发中
```bash
	npm install --save-dev babel-cli
```
<!-- more -->
#### 编写demo代码
新建src,lib目录
src目录下编写第一个文件:
```javascript 1.6

class Animal {
	constructor(name){
		this.name = name;
	}
}
class Snake extends Animal {
	constructor(){
		super('Snake');
		this.color = ['red','green','black'];
	}
	getName(){
		return this.name;
	}
	getColors(){
		return Array.from(arguments).reduce(function(a, b) {
			return a + b;
		});
	}
}
```
运行编译命令:
```bash
	./node_modules/.bin/babel src --out-dir lib
```
编译的结果嘛自己可以运行下看下,lib目录下的文件和src目录下的文件内容是一样一样一样的,那为什么没有编译成es5的代码呢 ?
原因在于babel只是个编译器真正进行编译的是babel中的插件,那我们下一步来加入插件.

#### 配置.babelrc
在根目录下新建文件.babelrc(babel配置文件),文件中基本包含两个东西presets,plugins(后面解释).

安装babel-preset-env
```bash
	npm install --save-dev babel-preset-env
```
.babelrc文件中添加内容:
```json
{
  "presets": [
    ["env", {
      "targets": {
        "browsers": ["last 2 versions", "safari >= 7"]
      }
    }]
  ]
}
```
运行命令再次编译就可以看到代码已经编译成es5了但是细心的你发现没**Array.from**是啥?es5没有这个api啊,完了这东西这么垃圾就不能一次帮我编译好吗?
哎!等等怎么文件头部还有那什么_classCallCheck,_createClass这个又是什么鬼?要是文件多了估计也是每个都有吧
对的!起初我也是这么觉得但是我们还是要把代码跑起来不是,有两种方法:
- 添加babel-polyfill(需要安装),然后在入口文件顶部 import "babel-polyfill"就好了;
- 使用babel-runtime,babel-plugin-transform-runtime 来搞定(推荐)

#### 转换新的api
安装babel-runtime,babel-plugin-transform-runtime
```bash
	npm install --save-dev babel-plugin-transform-runtime
    npm install --save babel-runtime
```
更新.babelrc文件

```json
{
	"presets":[
		["env", {
	      "targets": {
	        "browsers": ["last 2 versions", "safari >= 7"]
	      }
	    }]
    ],
	"plugins":["transform-runtime"]
}
```
再次运行编译命令,现在看下所有的都转换了,连_classCallCheck,_createClass 这种都是导入了并没有在每个文件中定义一个了

## 总结

- **plugins,presets到底是什么鬼?**

	按照Babel官网的介绍，其实Preset和Stage-X都是归属到Plugin里面的，只不过所覆盖的范围不同而已。
举个例子，如果需要转换ES2015(ES6)的语法，那么你可以在.babelrc的plugins中按需引入check-es2015-constants、es2015-arrow-functions、es2015-block-scoped-functions等等几十个不同作用的plugin：
```json
// .babelrc
{
  "plugins": [
    "check-es2015-constants",
    "es2015-arrow-functions",
    "es2015-block-scoped-functions",
    // ...
  ]
}
```

但是Babel团队为了方便，将同属ES2015的几十个Transform Plugins集合到babel-preset-es2015一个Preset中，这样你只需要在.babelrc的presets加入es2015一个配置就可以完成全部ES2015语法的支持了：

```json
// .babelrc
{
  "presets": [
    "es2015"
  ]
}
```
现在官方推荐使用 babel-preset-env
在没有任何配置选项的情况下，babel-preset-env 与 babel-preset-latest（或者babel-preset-es2015，babel-preset-es2016和babel-preset-es2017一起）的行为完全相同。

- **那plugins和presets都是插件的话他们执行编译的顺序是怎么排的啊?**

1.plugins优先于presets进行编译；
2.plugins按照数组的index增序(从数组第一个到最后一个)进行编译；
3.presets按照数组的index倒序(从数组最后一个到第一个)进行编译，因为作者认为大部分会把presets写成["es2015", "stage-0"]，具体细节可以看这个。




