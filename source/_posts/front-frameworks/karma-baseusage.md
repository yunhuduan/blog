---
title: karama基本使用
date: 2018-03-10 22:39:03
tags: 
	- testframework
	- test
	- karma
categories:
	- test
---
## 介绍
Karma是一个基于Node.js的JavaScript测试执行过程管理工具（Test Runner）。该工具可用于测试所有主流Web浏览器，也可集成到CI（Continuous integration）工具，也可和其他代码编辑器一起使用。这个测试工具的一个强大特性就是，它可以监控(Watch)文件的变化，然后自行执行，通过console.log显示测试结果。


## 安装
安装分两种:
- 1.本地项目中安装;(推荐)
- 2.全局安装;

```bash
	npm install karma --save-dev
```

## 生成配置文件

```bash
	karma init
```
生成配置文件时testingframework选mocha,不使用require.js,浏览器尽量按照自己的意愿选择.
最终会在项目的根目录下生成karma.conf.js文件内容如下:

<!-- more -->

```javascript

module.exports = function(config) {
  config.set({

    // base path that will be used to resolve all patterns (eg. files, exclude)
    basePath: '',


    // frameworks to use
    // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
    frameworks: ['mocha'],


    // list of files / patterns to load in the browser
    files: [
    	'src/**/*.js',
        'test/**/*.test.js'
    ],


    // list of files / patterns to exclude
    exclude: [
    	'karma.conf.js'
    ],


    // preprocess matching files before serving them to the browser
    // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor
    preprocessors: {
    },


    // test results reporter to use
    // possible values: 'dots', 'progress'
    // available reporters: https://npmjs.org/browse/keyword/karma-reporter
    reporters: ['progress'],


    // web server port
    port: 9876,


    // enable / disable colors in the output (reporters and logs)
    colors: true,


    // level of logging
    // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
    logLevel: config.LOG_INFO,


    // enable / disable watching file and executing tests whenever any file changes
    autoWatch: true,


    // start these browsers
    // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
    browsers: ['Chrome', 'PhantomJS'],


    // Continuous Integration mode
    // if true, Karma captures browsers, runs the tests and exits
    singleRun: false,

    // Concurrency level
    // how many browser should be started simultaneous
    concurrency: Infinity
  })
}

```
karma的具体配置项请查看[官方文档](https://karma-runner.github.io/1.0/config/configuration-file.html)

## 安装依赖
因为我们选择测试框架是mocha所以在项目中需要安装mocha,同时我们在项目中安装断言库chai,然而我们测试框架和karma需要安装额外的插件适配器才能正常使用
插件适配:
- karma-mocha
- karma-chai

安装完依赖可以直接执行命令:karma start测试是否可以成功

## 测试事例

### 编写源代码
项目src 下我们新建一个ClassicFunc.js文件
内容如下:
```javascript
function add(a,b){
	return a + b;
}

function isNum(num) {
	if (typeof num === 'number') {
		return true;
	} else {
		return false;
	}
}
```

### 编写测试代码
新建文件:unit.test.js
```javascript
describe('index.js: ', function() {
	it('isNum() should work fine.', function() {
		console.log('isNum:'+isNum(1));
		expect(isNum(1)).to.be.equal(true)
	});

	it('1+1 test', function() {
		expect(add(1,1)).to.be.equal(2)
	})
})
```

### 启动测试

```bash
	karma start
```


