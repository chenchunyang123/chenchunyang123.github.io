---
title: hook实现todolist
date: 2019-07-09 15:04:48
categories:
	- react
tags:
	- hook语法
---

### 背景

最近不是特别忙，所以想学一点新的东西充实一下自己，毕竟程序员这个职业就是要不断学习。又打算学学 hook 是因为 vue3.0 计划废除 class 相关 api 而使用 hook，并且 react 中 hook 正式推出也有一段时间了，就让我对这个 hook 语法很好奇，它到底是何方神圣。
这篇文章本来是打算记录一些 hook 的使用规则，用法，概念性的东西，但是在 react 的[**官方文档**](https://react.docschina.org/docs/hooks-intro.html)上，对 hook 语法的描述已经很清楚了！在这里就不再赘述，主要想记录一下自己用 hook 实现 todolist 的思路。

<!-- more -->

### 实现思路

#### 项目结构

```
|-- react-hook							// 项目文件夹
    |-- .eslintrc						// 检验hook语法
    |-- .gitignore
    |-- package.json
    |-- README.md
    |-- yarn.lock
    |-- build							// 打包后的文件
    |-- public							// 打包会直接输出的文件
    |   |-- favicon.ico
    |   |-- index.html
    |   |-- manifest.json
    |-- src
        |-- App.css
        |-- App.js						// todolist容器
        |-- index.css
        |-- index.js						// 入口文件
        |-- components
            |-- FilterOptions					// 选项组件（底部）
            |   |-- index.css
            |   |-- index.js
            |-- InputBox					// 输入组件（顶部）
            |   |-- index.css
            |   |-- index.js
            |-- List						// 列表组件（中部）
                |-- index.css
                |-- index.js
```

#### 代码详情

##### App.js

```
import React, { useState } from 'react';
import './App.css';

// 引入相关组件
import InputBox from './components/InputBox';
import List from './components/List';
import FilterOptions from './components/FilterOptions';

function App() {
	// 定义所有的state
	const [inputValue, setValue] = useState('');	// 输入内容相关
	const [todoThings, setTodo] = useState([{ id: 1, text: '学习hook', done: true }]);	// 待办事项列表相关
	const [renderValue, setRenderValue] =  useState(1);		// 1为全部， 2为已完成， 3为未完成

	// 往下穿的对象
	const InputBoxProps = {
		inputValue,
		setValue,
		todoThings,
		setTodo,
	}
	const ListProps = {
		renderValue,
		todoThings,
		setTodo,
	}
	const optionsProps = {
		renderValue,
		setRenderValue,
	}

	return (
		<div className='box'>
			<h2>TODO计划表</h2>
			<InputBox {...InputBoxProps} />
			<List {...ListProps} />
			<FilterOptions {...optionsProps} />
		</div>
	);
}

export default App;
```

该文件是整个 app 的容器文件，从上到下依次引入了 3 个组件（InputBox、List、FilterOptions），然后利用 hook 的 api 定义了 3 种 state 数据，再根据不同组件与 state 数据之间的联系，把 state 传到相应的组件中去。

##### InputBox.js

```
import React from 'react';

// 添加一项的方法(参数： 输入框当前值和list事项的值)
const addOne = (value, all) => {
	return [...all, { id: new Date().getTime(), text: value, done: false }];
}

function InputBox(props) {
	let { inputValue, setValue, todoThings, setTodo } = props;
	return (
		<div>
			<input
				placeholder='请输入待办事项'
				type='text'
				value={inputValue}
				onChange={e => setValue(e.target.value)}
			/>
			<button
				onClick={() => {
					inputValue && setTodo(addOne(inputValue, todoThings))
					// 增加完过后清空输入框
					setValue('')
				}}
			>ADD</button>
		</div>
	);
}

export default InputBox;
```

输入框中内容的改变需要一个输入文本数据，点击添加按钮是把文本数据添加到 list 展示数据的 state 里面去，所以需要 list 的 state 数据。再加上他们 2 个对应的改变值的方法，所以这个组件需要接收 4 个数据。

##### FilterOptions.js

```
import React from 'react';
import './index.css';

const renderArr = ['全部', '已完成', '未完成'];     // 定义出来简化书写

function FilterOptions(props) {
    let { renderValue, setRenderValue } = props;
    return (
        <div className='options'>
            {
                renderArr.map((item, idx) => {
                    return (
                        <button
                            key={idx}
                            style={{backgroundColor: renderValue === idx + 1 ? 'orange' : null}}
                            onClick={() => setRenderValue(idx + 1)}
                        >{item}</button>
                    )
                })
            }
        </div>
    )
}

export default FilterOptions;
```

该组件的实现思路是改变 state 中的一个‘信号量’，当该信号量为 1 时，list 组件里面渲染全部事件，当为 2 时渲染已完成，当为 3 时渲染未完成。

##### List.js

```
import React from 'react';
import './index.css';

// 改变当前选中的change方法(参数：当前点击的text值以及渲染的所有数组值)
const changeDone = (id, all) => {
	all.forEach(item => {
		if(item.id === id) {
			item.done = !item.done;
		}
	})
	// 返回新的渲染数组(直接返回all的话不会进行更新，会造成修改的数据丢失)
	return [...all];
}

// 删除某一个事件的方法
const deleteOne = (id, all) => {
	let newArr = all.filter(item => item.id !== id);
	return [...newArr];
}

// 根据条件过滤todo的方法
const filterTodo = (value, all) => {
	switch (value) {
		case 1:
			return all;
		case 2:
			return all.filter(item => item.done === true);
		case 3:
			return all.filter(item => item.done === false);
		default:
			throw new Error('过滤条件错误');
	}
}

function List(props) {
	let { renderValue, todoThings, setTodo } = props;
	// 根据renderValue过滤一次todoThings数组
	let renderArr = filterTodo(renderValue, todoThings);
	return (
		<ul className='listWrap'>
			{
				renderArr.length ?
				renderArr.map((item, idx) => {
					return (
						<li key={idx} style={{width: '200px'}}>
							<div
								style={{
									textDecoration: item.done ? 'line-through' : 'none',
									float: 'left',
								}}
								onClick={() => setTodo(changeDone(item.id, todoThings))}
							>{item.text}</div>
							<div
								className='close'
								onClick={() => setTodo(deleteOne(item.id, todoThings))}
							>X</div>
						</li>
					)
				}) :
				null
			}
		</ul>
	);
}

export default List;
```

list 里面涉及到对事件的删除，事件已完成与未完成之间的状态切换，以及根据信号量渲染事件，所以接收了 3 个变量（信号量 state， 事件 state 和改变事件的方法）
有一点需要注意：在 state 为对象的时候，直接去改变对象里面的某个数据再返回当前对象并不会触发更新，会造成数据丢失，一定要返回一个新的对象，才能被 react 检测到。

##### .eslintrc

```
{
  "plugins": [
    "react-hooks"
  ],
  "rules": {
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

这个是检测 react hook 语法的插件，官方推荐在代码中加上此插件。

#### 最后

该 demo 只是用来体会 hook 语法之间数据定义，数据传递的过程，在样式及某些功能上存在着一些缺陷，这里就不再大幅度修改。
该项目的 github 线上地址：http://chenchunyang.site/hook-todolist/build/
