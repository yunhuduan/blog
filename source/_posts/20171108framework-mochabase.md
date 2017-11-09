---
title: 测试框架-mocha入门(1)
date: 2017-11-08 23:34:40
tags: [testframework,test,mocha]
---

### 1. 官方简介

> **[Mocha][1]** is a feature-rich JavaScript test framework running on Node.js and in the browser, 
making asynchronous testing simple and fun. 
Mocha tests run serially, allowing for flexible and accurate reporting,
 while mapping uncaught exceptions to the correct test cases. Hosted on [GitHub][2].

摩卡是一个功能丰富的JavaScript测试框架运行在Node.js和浏览器中，使得异步测试简单而有趣。

    以上是官方对于mocha的简介个人喜欢mocha是因为名字谐音:**摩卡**,
    另外据说是比较流行的测试框架(原谅我是个随波逐流的人^ _^).
    
所谓"测试框架"，就是运行测试的工具。通过它，可以为JavaScript应用添加测试，从而保证代码的质量。本文全面介绍如何使用Mocha。如果你以前对测试一无所知，本文也可以当作JavaScript单元测试入门。值得说明的是，除了Mocha以外，类似的测试框架还有Jasmine,Tape,QUnit等，也可以去学习。

### 2.安装 

``` shell
npm install --global mocha
```
或者作为项目的开发依赖安装
```shell
npm install --save-dev mocha
```
### 3.新建项目

```shell
npm init
```
按步骤输入项目的基本信息完成项目初始化,新建src,test目录准备开始撸官网的代码了.

新建文件:_test/Assert.test.js_
```javascript
var assert = require('assert');
describe('Array test',function(){
	describe('#indexOf()',function(){
		it('shoud return -1 when value is not present',function(){
			assert.equal(-1,[1,2,3].indexOf(4));
		});
		it('shoud return 1 when value is present',function(){
			assert.equal(1,[1,2,3].indexOf(2));
		});
	})
})
```
打开终端cd到当前项目目录下执行命令
```shell
mocha
```

	  Array test
        #indexOf()
          ✓ shoud return -1 when value is not present
          ✓ shoud return 1 when value is present
      2 passing (8ms)

代码解释:

- **describe**: 称为"测试套件"（test suite）,其中可以包含多个**it**,describe是可以嵌套的
- **it**: 称为"测试用例"（test case）
- 以上两个方法中第一个都是描述,第二个参数是实际运行测试的function

### 4.断言库

所谓"断言"，就是判断源码的实际执行结果与预期结果是否一致，如果不一致就抛出一个错误。
所有的测试用例（it块）都应该含有一句或多句的断言。它是编写测试用例的关键。
断言功能由断言库来实现，Mocha允许你使用任何你喜欢的断言库，以上的事例中我们使用的是nodejs中内置的[assert][4]。
相关的断言库有如下:

| 库           | 风格     |
| --------    |  :----:  |
| should.js   | BDD style shown throughout these docs        |
| expect.js   | expect() style assertions                    |
| chai        |expect(), assert() and should-style assertions|
|better-assert| C-style self-documenting assert()             |
|unexpected   |“the extensible BDD assertion toolkit”         |

chai(本文主要和这个有暧昧),
chai中的断言又区分两种style分别是:BDD(行为驱动开发),TDD(测试驱动开发),具体文档查看[这里][3],
chai中断言包含:expect(BDD),should(BDD),assert(TDD)

### 5.chai断言库assert
#### 1.新建文件*chaiAssert.test.js*来简单使用下assert
```javascript
var assert = require('chai').assert
  , foo = 'bar'
  , beverages = { tea: [ 'chai', 'matcha', 'oolong' ] };

describe('chai assert test',function(){
	it('it string',function(){
		assert.typeOf(foo, 'string'); // without optional message
	})
	it('it string optional message',function(){
		assert.typeOf(foo, 'string', 'foo is a string'); // with optional message
	})
	it('foo equal bar',function(){
		assert.equal(foo, 'bar', 'foo equal `bar`');
	})
	it('foo length is 3',function(){
		assert.lengthOf(foo, 3, 'foo`s value has a length of 3');
	})
	it('it beverages lengh is 3',function(){
		assert.lengthOf(beverages.tea, 3, 'beverages has 3 types of tea');
	})
});
```
执行命令: mocha 查看输出结果

	



  [1]: http://mochajs.org
  [2]: https://github.com/mochajs/mocha
  [3]: http://chaijs.com
  [4]: https://nodejs.org/api/assert.html