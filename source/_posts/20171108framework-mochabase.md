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

### 5.chai断言库
#### 5.1 chai断言库之assert
##### 5.1.1 新建文件*test/chaiAssert.test.js*来简单使用下assert
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
##### 5.1.2 执行命令: mocha 查看输出结果
```shell
mocha test/chaiAssert.test.js

  chai assert test
    ✓ it string
    ✓ it string optional message
    ✓ foo equal bar
    ✓ foo length is 3
    ✓ it beverages lengh is 3

  5 passing (13ms)
```
#### 5.2 chai断言库之expect
##### 5.2.1 新建文件*test/chaiExpect.test.js*
```javascript

var expect = require('chai').expect;

describe('expect equal',function(){
	it('basic data type',function () {
		expect(1).to.equal(1);
		expect('foo').to.equal('foo');
	});
	it('object data deep equal',function () {
		expect({a:1}).to.deep.equal({a:1});
		expect({a:1}).to.not.equal({a:1});

	});
});

describe('expect eql(deep equal)',function () {
	it('expect({a: 1}).to.eql({a: 1}).but.not.equal({a: 1});',function () {
		expect({a: 1}).to.eql({a: 1}).but.not.equal({a: 1});
	});
});

describe('expect not suit', function () {
	it('[0,1,2,3].indexOf(3).to.not.equal(2)', function () {
		expect([0, 1, 2, 3].indexOf(3)).to.not.equal(2)
	});
	it('expect(function () {}).to.not.throw()', function () {
		expect(function () {
		}).to.not.throw();
	});
	it('expect({a: 1}).to.not.have.property(\'b\')', function () {
		expect({a: 1}).to.have.property('a');
	});
	it('expect([1, 2]).to.be.an(\'array\').that.does.not.include(3)', function () {
		expect([1, 2]).to.be.an('array').that.does.not.include(3);
	});
});

describe('expect deep suit',function(){
	it('expect({a:1}).to.deep.equal({a:1})',function () {
		expect({a:1}).to.deep.equal({a:1});
		expect({a: 1}).to.not.equal({a: 1});
	});

	it('expect([{a: 1}]).to.deep.include({a: 1})',function () {
		expect([{a: 1}]).to.deep.include({a: 1});
		expect([{a: 1}]).to.not.include({a: 1});
		expect({x: {a: 1}}).to.deep.include({x: {a: 1}});
		expect({x: {a: 1}}).to.not.include({x: {a: 1}});
	});

});
```
##### 5.2.2 执行命令:mocha test/chaiExpect.test.js (自行查看下输出)
以上就是常用的chai断言,更多断言使用请自行chai官网查看,不过比较慢可能需要翻墙

### 6.异步代码
异步代码执行中需要在it的function中接收传入的函数(这里是**done**),在异步操作完成的时候调用done函数.
如果当异步执行代码时间过长需要设置下当前测试超时时间
```javascript
var expect = require('chai').expect;
describe('ASYNCHRONOUS CODE test', function () {
	this.timeout(5000);//异步时超时时间设置
	it('settime out with asynchronous test', function (done) {
		setTimeout(function () {
			expect(1).to.be.equal(1);
			done();//异步时需要手动调用回调
		}, 2000)
	});
});
```
### 7.与promise一起使用
当与promise一起使用时it中的函数返回promise来代替**done()** 的回调
```javascript
beforeEach(function() {
  return db.clear()
    .then(function() {
      return db.save([tobi, loki, jane]);
    });
});

describe('#find()', function() {
  it('respond with matching records', function() {
    return db.find({ type: 'User' }).should.eventually.have.length(3);
  });
});
```
在Mocha v3.00或者更新版本,返回一个*promise*并且调用 _done()_的回调函数会抛出异常,异常如下:

`Error: Resolution method is overspecified. Specify a callback *or* return a Promise; not both.`
错误代码如下(只要去掉then部分及it的函数中入参done即可正常运行):
```javascript
var expect = require('chai').expect;
it('should complete this test', function (done) {
	return new Promise(function (resolve) {
		expect(true).to.be.true
		resolve();
	}).then(function() {
		done()
	});
});
```
### 8.mocha中的回调
mocha提供了默认的"BDD"风格的回调: before(), after(), beforeEach(), afterEach().这些通常使用在测试前置的条件设置和测试完成后清理等
```javascript
describe('hooks test', function () {
	var curTime = new Date().getTime();
	var totalStart = curTime;
	var totalEnd = curTime;

	var eachStart = curTime;
	var eachEnd = curTime;

	before(function () {
		// runs before all tests in this block
		totalStart = new Date().getTime();
		console.log('所有的测试开始前调用:' + totalStart);
	});

	after(function () {
		// runs after all tests in this block
		totalEnd = new Date().getTime();
		console.log('所有的测试结束后调用:' + totalEnd + ',total cost time:' + (totalEnd - totalStart));
	});

	beforeEach(function () {
		// runs before each test in this block
		eachStart = new Date().getTime();
		console.log('每个测试it的测试前调用:' + eachStart);
	});

	afterEach(function () {
		// runs after each test in this block
		eachEnd = new Date().getTime();
		console.log('每个测试it的测试后调用:' + eachEnd + ',cost time:' + (eachEnd - eachStart));
		console.log('===========================我是分割线============================================')
	});

	it('basic data type', function () {
		expect(1).to.equal(1);
		expect('foo').to.equal('foo');
	});
	it('object data deep equal', function () {
		expect({a: 1}).to.deep.equal({a: 1});
		expect({a: 1}).to.not.equal({a: 1});

	});
});
```
### 9.mocha回调描述
所有回调都有个可选项 _description_,描述信息可以在测试发生错误时准确的提示出来.如果没有描述回调函数是非匿名函数(就是有个名字)同样会当成description
```javascript
beforeEach(function() {
  // beforeEach hook
});
beforeEach(function namedFun() {
  // beforeEach:namedFun
});
beforeEach('some description', function() {
  // beforeEach:some description
});
```
### 10.mocha异步回调
所有的回调(before(), after(), beforeEach(), afterEach())可能是同步或者异步,例如你希望在所有测试开始前使用模拟数据录入到数据库中:
```javascript
describe('Connection', function() {
  var db = new Connection,
    tobi = new User('tobi'),
    loki = new User('loki'),
    jane = new User('jane');

  beforeEach(function(done) {
    db.clear(function(err) {
      if (err) return done(err);
      db.save([tobi, loki, jane], done);
    });
  });

  describe('#find()', function() {
    it('respond with matching records', function(done) {
      db.find({type: 'User'}, function(err, res) {
        if (err) return done(err);
        res.should.have.length(3);
        done();
      });
    });
  });
});
```
### 11.根级回调
你可以选择任何文件添加根级回调.例如:添加 _beforeEach()_ 在所有的*describe()*块以外,那么所有的test case 开始执行之前都会调用 _beforeEach()_ 回调函数.
这是因为在mocha测试框架中存在一个隐含的根级 _describe()_ 块,被称为"root suite"

### 12.动态生成测试用例

```javascript
describe('DYNAMICALLY GENERATING TESTS', function () {
	function add() {
		return Array.prototype.slice.call(arguments).reduce(function (prev, curr) {
			return prev + curr;
		}, 0);
	}
	var tests = [
		{args: [1, 2], expected: 3},
		{args: [1, 2, 3], expected: 6},
		{args: [1, 2, 3, 4], expected: 10}
	];
	tests.forEach(function (test) {
		it('correctly adds ' + test.args + ' args', function () {
			var res = add.apply(null, test.args);
			expect(res).to.be.equal(test.expected);
		});
	});

});
```

### 13.mocha测试报告
mocha的测试报告可以使用命令: mocha --reporters 查看所有内置的报告
```html
    dot - dot matrix
    doc - html documentation
    spec - hierarchical spec list
    json - single json object
    progress - progress bar
    list - spec-style listing
    tap - test-anything-protocol
    landing - unicode landing strip
    xunit - xunit reporter
    min - minimal reporter (great with --watch)
    json-stream - newline delimited json events
    markdown - markdown documentation (github flavour)
    nyan - nyan cat!
```
mocha还可以安装其他的第三方的报告插件,如:mochawesome
```shell
npm install --save-dev mochawesome

mocha --reporter mochawesome
```


  [1]: http://mochajs.org
  [2]: https://github.com/mochajs/mocha
  [3]: http://chaijs.com
  [4]: https://nodejs.org/api/assert.html