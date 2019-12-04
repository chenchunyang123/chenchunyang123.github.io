---
title: 观察者模式结合seajs模块化的小实例
date: 2018-10-30 17:20:33
categories:
	- 设计模式
tags:
	- 观察者模式
---

#### 实例演示

首先我们打算实现如下图所示的效果：  
![GIF.gif](https://i.loli.net/2019/12/02/aLjBlcPWpEGqohA.gif)
<!-- more --> 
不难，一个人写很简单，这里不是想讨论在一个js当中该怎么写出来，而是主要体会模块化开发多人协作的过程。

________________________________________________________
#### 分析
我们把这个需求分给三个人做：
1.小a做”消息数量“
2.小b做”中部消息内容“
3.小c做”文本框及按钮“
如果一个人做，那么可以很容易控制先做什么后做什么，但是三个人来做，每个人的进度都不同，那么模块之间该如何互相通信这是一个很重要的问题。
##### 初步设想
由于在seajs中，每个模块之间都是独立互不干扰的，换句话说，模块与模块之间是相互访问不到的，想让它们通信的话，初步设想在每个模块中向全局暴露内容变量。
虽然这么做确实能够实现，但是也有问题，因为我们在设计程序的时候，原则就是尽量减少全局变量，每多一个全局变量，就会多占用一些资源，并且存在内存泄漏的隐患，影响性能。
##### 再次假设
既然变量太多了，那我们看看能不能减少变量，因为在js中，对象里面是可以写方法的。想到这里，再次尝试，定义一个ob对象，里面写方法，然后在每个模块里面分别调用这个对象的方法，确实可行。但是这样就完美了吗？
答案是no，当在全局中直接暴露了这个存有方法的对象，人为地进行修改很方便，很不安全。
##### 继续假设
想到这里，问题就来了，怎么做才能避免直接就能够修改ob对象？
对，没错，利用IIFE自执行函数把它包一下。
```javascript
	// 定义对象
	var Observer = (function() {
		// 定义真正的观察者
		var ob = {

		};
		// 定义返回接口
		return {
			/**
			 * on方法要用于向观察者对象中添加事件
			 * @type 字符串  表示事件名称
			 * @fn   函数    表示事件函数
			 **/
			on: function(type, fn) {
				ob[type] = fn;
			},
			/**
			 * trigge方法用于触发观察者对象中的事件
			 * @type 要触发的事件名称
			 **/
			trigger: function(type, val) {
				ob[type](val);
			}
		}
	})();
```
但是现在在全局中还是有一个Observer变量，现在我们也把它放入模块文件中。
```javascript
// 定义模块
define(function(require, exports, module) {
	// 定义对象
	 var Observer = (function() {
	 	// 定义真正的观察者
	 	var ob = {
        
	 	};
	// 定义返回接口
	 	return {
	 		/**
	 		 * on方法要用于向观察者对象中添加事件
	 		 * @type 字符串  表示事件名称
	 		 * @fn   函数    表示事件函数
	 		 **/
	 		on: function(type, fn) {
	 			ob[type] = fn;
	 		},
	 		/**
	 		 * trigge方法用于触发观察者对象中的事件
	 		 * @type 要触发的事件名称
	 		 **/
	 		trigger: function(type, val) {
	 			ob[type](val);
	 		}
	 	}
        // 向外暴露功能
		module.exports = Observer;
	 })();
```
#### 最终形态
我们发现在第三个模块的点击事件中，里面的触发事件写了两个，一个是模块一中定义的消息数量++，一个是模块二中的li元素增加，既然它们是点击按钮过后一起执行的，并且具有相同的语义，那我们能否把这两个合并成一个呢？确实也可以，也就是说书写一个trigger执行函数要同时触发两个定义函数，那么两个定义函数的名字必须要相同，才能用一个trigger函数来触发。但是如果使用之前的”观察者“，在用同一个名字定义了第一个模块和第二个模块中的事件后，触发第三个模块中的事件，会发现其中一个模块函数并不能被触发，因为当给一个对象设置同名属性的时候，会有一个覆盖的现象。那么这时候我们可以考虑利用数组，如果对象存在这个属性方法，那么把后一个属性方法push进数组，如果不存在，直接定义这个属性方法，并且存在数组中。后面的事件处理函数也要改成遍历数组处理定义函数，这样便可以达到简化的目的。
升级过后的观察者对象如下：
```javascript
var Observer = (function() {
		// 定义真正的观察者
		var ob = {

		};
		// 定义返回接口
		return {
			on: function(type, fn) {
				// 判断当前type是不是一个数组， 如果是数组说明被使用过，就可以直接push进去
				if (ob[type]) {
					ob[type].push(fn);
				} else {
					// 说明不是数组， 将其变为数组
					ob[type] = [fn];
				}
			},
			trigger: function(type, val) {
				// 循环执行
				for (var i = 0; i < ob[type].length; i++) {
					ob[type][i](val);
				}
			}
	})();
```
最后再贴上最终完成的其他三个模块的文件代码：
- 模块一

```javascript
// 定义模块
define(function(require, exports, module) {
	// 引入工具模块
	var Observer = require("modules/tools");
	// 获取元素
	var num = document.getElementById("num");
	// 监听添加消息事件
	Observer.on("add", function() {
		num.innerHTML = +num.innerHTML + 1;
	})
	// 监听减少消息事件
	Observer.on("remove", function() {
		num.innerHTML = +num.innerHTML - 1;
	})
})
```
- 模块二

```javascript
// 定义模块
define(function(require, exports, module) {
	// 引入工具模块
	var Observer = require("modules/tools");
	// 获取元素
	var ul = document.getElementById("list");
	// 监听消息添加事件
	Observer.on("add", function(val) {
		// 创建元素
		var li = document.createElement("li");
		var textNode = document.createTextNode(val);
		var span = document.createElement("span");
		// 添加类名
		span.className = "close";
		// 添加内部文本
		span.innerHTML = "&times;";
		// 添加点击事件
		span.onclick = function() {
			ul.removeChild(this.parentNode);
			// 通知第一个模块，消息数量要减一
			Observer.trigger("remove");
		}
		// 上树
		li.appendChild(textNode);
		li.appendChild(span);
		ul.appendChild(li);
	})
	
})
```
- 模块三

```javascript
// 定义模块
define(function(require, exports, module) {
	// 引入工具模块
	var Observer = require("modules/tools");
	// 获取元素
	var words = document.getElementById("words");
	var btn = document.getElementById("btn");
	// 添加点击事件
	btn.onclick = function() {
		// 获取用户输入的内容
		var val = words.value;
		// 触发事件
		Observer.trigger("add", val);
	}
})
```

