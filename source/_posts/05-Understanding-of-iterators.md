---
title: 迭代器（jq、underscore、es5、自建）
date: 2018-11-03 21:12:31
categories:
	- 归纳
tags:
	- 迭代器方法
---

##### Jq 中的迭代器

###### dom 结构

![jq1.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq1.jpg)

<!-- more -->

###### js 方法

![jq2.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq2.jpg)

###### 输出

![jq3.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq3.jpg)（省略后面重复部分）

###### 定义一个数组及遍历

![jq4.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq4.jpg)
输出：（this 的实质就是每一次遍历到的 value 的数字包装类型）
![jq5.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq5.jpg)

---

##### underscore 中的迭代器

###### 定义一个数组及遍历

![jq6.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq6.jpg)
输出：
![jq7.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq7.jpg)

---

##### es5 中的迭代器

###### 定义一个数组及遍历

![jq8.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq8.jpg)
输出：
![jq9.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq9.jpg)

---

##### 自己的迭代器

###### 目的

定义一个函数，该函数可以实现对数组和对象的遍历。

```javascript
/**
 * each函数 实现迭代器
 * @target 要遍历的目标
 * @fn     要执行的函数
 **/
function each(target, fn) {
    // 判断传递的target是数组还是对象
    if (target instanceof Array) {
        // 说明传递的是数组
        for (var i = 0; i < target.length; i++) {
            // 执行fn
            fn.call(target[i], target[i], i, target);
        }
    } else if (Object.prototype.toString.call(target) === '[object Object]') {
        for (var i in target) {
            // 传递二个参数
            fn.call(target[i], target[i], i);
        }
    }
}
```

###### 数组遍历结果

![jq10.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq10.jpg)
![jq11.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq11.jpg)

###### 对象遍历结果

![jq12.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq12.jpg)
![jq13.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/05-Understanding-of-iterators/jq13.jpg)

