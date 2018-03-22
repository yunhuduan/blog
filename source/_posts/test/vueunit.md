---
title: vue单元测试(1)
date: 2017-11-22 23:08:22
tags:
	- vue
	- test
categories:
	- test
	- vue
---
## 缘起

> 目前团队中使用vue开发有一段时间了,但是对于通用组件也好业务组件也罢,总感觉在质量上心里有点不放心或者说从近期几个项目来看在测试阶段还是有很多没必要的bug提出来.
于是乎有点闲暇时间来研究下vue的单元测试了,vue的脚手架生成工具其实已经内置了单元测试和端到端的测试.我们先从单元测试一步步来最后再到端到端的测试,
毕竟技术比较关心的还是单元测试.对于vue的单元测试目前比较推荐的还是使用官方的测试工具:[vue-test-utils][1],当然还有其他的一些测试工具或者库您可以到[这里][2]去搜索.
下面我们来新建一个项目来一步步编写单元测试

## vue脚手架生成项目
运行命令:`vue init webpack vuetest`
```html
 A newer version of vue-cli is available.

  latest:    2.9.1
  installed: 2.8.2

? Project name vuetest
? Project description A Vue.js project
? Author yohance <xxxxxx@sina.cn>
? Vue build standalone
? Install vue-router? Yes
? Use ESLint to lint your code? Yes
? Pick an ESLint preset Standard
? Setup unit tests Yes
? Pick a test runner karma
? Setup e2e tests with Nightwatch? No

   vue-cli · Generated "vuetest".

   To get started:
   
     cd vuetest1
     npm install
     npm run dev
   
   Documentation can be found at https://vuejs-templates.github.io/webpack
```
以上就是我们运行vue脚手架后的选项设置下面我们接着开始写个vue官方的TodoList

<!-- more -->

## 安装依赖

```shell
npm install 
npm install --save-dev vue-test-utils
```
## eslint规则修改
添加如下规则到项目根目录下的.eslintrc.js,根据个人习惯这步骤可以自行处理决定自己的规则
```text
// 通用空格一致
'comma-spacing': 0,
//方法前空格允许
'space-before-function-paren': 0,
// 限制使用单引号,
'quotes': 0,
//不允许行尾有空格
'no-trailing-spaces': 0,

"no-unused-vars": 0,
// 关闭语句强制分号结尾
"semi": [0],
//空行最多不能超过100行
"no-multiple-empty-lines": [0, {"max": 100}],
//关闭禁止混用tab和空格
"no-mixed-spaces-and-tabs": [0],
"no-tabs": 0,
"indent": ["error", "tab"],
//注释空格
"spaced-comment": 0,
//判断无等号
"padded-blocks": 0,
'no-useless-escape':0,
//禁止使用new Function
"no-new-func": 0
```
## TodoList组件编写
### 基本组件列表如下:
```text
|- src
    |- components
        |- Todo.vue
        |- TodoList.vue
        |- TodoItem.vue
```
### 组件及测试代码如下:
#### TodoItem.vue组件
```text
<template>
	<div class="todo-item">
		<p>
			<span class="index-class">{{idx}}</span>
			<span class="item-ctx">{{data}}</span>
			<a class="item-op" href="javascript:void(0);" @click="del(idx)">done</a>
		</p>
	</div>
</template>

<script>
	export default {
		props: {
			data: {
				type: String,
				required: true
			},
			idx: {
				type: Number,
				required: true
			}
		},
		data() {
			return {}
		},
		methods: {
			del: function (index) {
				console.log('item del')
				this.$emit('delItem', index)
			}
		}
	}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
	.todo-item {
		margin-bottom: 5px;
		border: 1px solid #ff3445;
	}

	.item-op {
		margin-left: 5px;
	}
</style>
```
#### TodoItem.vue测试用例
在test/unit/specs 目录下新建文件TodoItem.spec.js内容如下:
```javascript
import TodoItem from '@/components/TodoItem'
import {shallow} from 'vue-test-utils'
//eslint 默认开启无用表达式检测避免eslint出错提示
/*eslint no-unused-expressions: [0]*/
const clickHandler = sinon.stub();
describe('TodoItem.vue', () => {
	it('should render correct contents', () => {
		var text = 'bar';
		var index = 0;
		const wrapper = shallow(TodoItem, {
			propsData: {
				data: text,
				idx: index
			}
		})
		expect(wrapper.find('.index-class').text()).to.equal(index + '')
		expect(wrapper.find('.item-ctx').text()).to.equal(text)
	})

	it('should trigger event invoke correct', () => {
		var text = 'bar';
		var index = 0;
		const wrapper = shallow(TodoItem, {
			propsData: {
				data: text,
				idx: index
			}
		})
		//需要找到操作按钮a标签触发点击事件
		wrapper.find('.item-op').trigger('click');
		//wrapper.emitted().delItem : [[0]]
		expect(wrapper.emitted().delItem.length).to.equal(1);
		expect(wrapper.emitted().delItem).to.deep.equal([[0]]);
		expect(wrapper.emitted().delItem[0]).to.deep.equal([index]);

	})

})
```
运行命令: `npm run unit` 查看当前测试用例运行结果,如果是在webstorm中编写可直接在测试用例的describe位置点击`Run 'TodoItem.vue'`
ide会自动执行当前karma命令


#### TodoList.vue组件

```text
<template>
	<div class="todo-list">
		<TodoItem v-for="(item, index) in items" :key="index" :data="item" :idx="index" @delItem="del"></TodoItem>
		<p class="no-data" v-if="items.length <= 0">请输入项目</p>
	</div>
</template>

<script>
	import TodoItem from './TodoItem.vue'

	export default {
		name: 'todoList',
		props: {
			items: {
				type: Array,
				default: []
			}
		},
		data() {
			return {}
		},
		methods: {
			del: function (index) {
				this.$emit('delItem',index)
			}
		},
		components: {TodoItem}
	}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
	.todo-list {
		border: 1px solid #ffdf1a;
		padding: 5px;
	}
</style>
```
#### TodoList测试用例
新建文件test/unit/specs/TodoList.spec.js,内容如下:
```javascript
import TodoList from '@/components/TodoList'
import TodoItem from '@/components/TodoItem'
import {mount} from 'vue-test-utils'

//eslint 默认开启无用表达式检测避免eslint出错提示
/*eslint no-unused-expressions: [0]*/

describe('TodoList.vue', () => {
	it('should render correct contents', () => {
		var items = ['Apple', 'Banana', 'Orange']
		const wrapper = mount(TodoList, {
			propsData: {
				items: items
			}
		});
		expect(wrapper.findAll('.todo-item').length).to.equal(items.length)
		expect(wrapper.contains(TodoItem)).to.be.true;
	})
	it('should render correct with empty items contents', () => {
		var items = []
		const wrapper = mount(TodoList, {
			propsData: {
				items: items
			}
		});
		expect(wrapper.findAll('.todo-item').length).to.equal(items.length);
		expect(wrapper.contains(TodoItem)).to.be.false;
		expect(wrapper.findAll('.no-data').length).to.equal(1)
	})
})
```
运行命令: `npm run unit`查看运行测试用例结果

#### Todo.vue组件
```text
<template>
	<div class="todo">
		<div class="add-item-container">
			<input v-model="inputItem" @keyup.enter="addItem" id="itemInput"/>
		</div>
		<div class="item-list-container">
			<TodoList @delItem="delItem" :items="itemList"></TodoList>
		</div>
	</div>
</template>

<script>
	import TodoList from './TodoList.vue'

	export default {
		data() {
			return {
				inputItem: '',
				itemList: []
			}
		},
		methods: {
			addItem: function () {
				this.itemList.push(this.inputItem)
			},
			delItem: function (index) {
				console.log('todo del item:' + index);
				this.itemList.splice(index, 1);
			}
		},
		components: {TodoList}
	}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
```

#### Todo测试用例
新建文件test/unit/specs/Todo.spec.js
```javascript
import TodoList from '@/components/TodoList'
import Todo from '@/components/Todo'
import {mount} from 'vue-test-utils'

//eslint 默认开启无用表达式检测避免eslint出错提示
/*eslint no-unused-expressions: [0]*/

describe('Todo.vue', () => {
	it('should render correct contents', () => {
		var items = ['Apple', 'Banana', 'Orange']
		const wrapper = mount(Todo);
		wrapper.setData({itemList: items})

		expect(wrapper.findAll('.todo-item').length).to.equal(items.length)

		expect(wrapper.contains(TodoList)).to.be.true;
	})

	it('should input text correct', () => {
		const wrapper = mount(Todo);
		var item = 'Apple';
		var input = wrapper.find('#itemInput');
		wrapper.vm.inputItem = item;
		input.trigger('keyup.enter');
		console.log('itemList:' + wrapper.vm.itemList + ',length:' + wrapper.vm.itemList.length);
		expect(wrapper.vm.itemList).to.deep.equal([item]);

	})
	it('should del item correct', () => {
		const wrapper = mount(Todo);
		var items = ['Apple', 'Banana'];
		wrapper.setData({
			itemList: items
		})

		expect(wrapper.vm.itemList.length).to.be.equal(items.length)

		//find 组件时被find的组件(TodoList)必须是要声明组件  name属性的
		wrapper.find(TodoList).vm.$emit('delItem', 0);

		expect(wrapper.vm.itemList.length).to.be.equal(1)
		expect(wrapper.vm.itemList).to.deep.equal(['Banana'])

	})
})
```
运行命令: `npm run unit`查看运行测试用例结果

4.在router中添加路由
在router/index.js中添加/todo的路由如下代码:

```text
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import Todo from '@/components/Todo'

Vue.use(Router)

export default new Router({
	routes: [
		{
			path: '/',
			name: 'HelloWorld',
			component: HelloWorld
		},
		{
			path: '/todo',
			name: 'todo',
			component: Todo
		}
	]
})
```

### 运行demo
1. 运行命令: `npm run dev` 
2. 打开浏览器输入: http://localhost:8080/todo ,查看当前demo运行结果

本文的代码在github[这里][3]
更多关于vue的测试以后再深入,后期逐步引入 vue-router,vuex,sinon等进行测试简单的测试到目前为止





[1]: https://vue-test-utils.vuejs.org/zh-cn/
[2]: https://curated.vuejs.org/
[3]: https://github.com/yunhuduan/vuetest