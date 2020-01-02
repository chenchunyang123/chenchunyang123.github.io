---
layout: b
title: gulp工程化及一些常用插件的使用
date: 2018-11-26 10:21:08
categories:
	- nodejs
	- gulp
tags:
	- 常用插件
---

### 概念

所谓的工程化就是将写完的代码进行压缩合并等操作。
gulp 是一个工程化工具，代码在书写完毕之前，需要有回车换行空白符等内容。但是一旦书写完毕，交给浏览器运行时，这些内容没有用。需要移除。
此时有两种方案：

<!-- more -->

> 1、人工移除，就是由人手工删除回车、换行、空白符。
> 2、使用工具移除。

Gulp 就是这样的一个工具，本质上，gulp 就是一个 NodeJS 的第三方模块。

### 下载

> 第一次下载： npm install gulp 本地安装 提供模块文件
> 第二次下载： npm install gulp -g 全局安装 提供 gulp 命令

### 配置文件

gulp 的配置文件名是**gulpfile.js**，用于决定处理哪些模块，以及如何处理。
写完之后，就可以调用 gulp 指令执行了。

### API

gulp 是一个模块，在代码中被引入之后得到一个对象，该对象一共有 5 个 API。

1. gulp.task
2. gulp.src
3. pipe
4. gulp.dest
5. gulp.watch

#### gulp.task

用于定义任务

> 语法：gulp.task(taskName, handler);
> taskName: 任务名称。
> handler: 对应的任务函数。还可以是数组，如果是数组，数组内的成员是其它任务的名称。

![1.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/1.png)
default 是具备特殊含义的任务名。表示默认、缺省的意思。是所有任务的开始入口。
上面的定义任务表示当任务开始的时候执行的是 aaa 和 bbb 任务。

#### gulp.src

该方法用于将物理文件转换成文件流。

> gulp.src(filePath);
> filePath: 表示文件路径的值 有三个可选项

-   可以是字符串 表示单个文件
-   可以是数组 表示多个文件
-   可以是 glob 表达式

返回值：一个对象 该对象所拥有的方法可以决定如何操作目标文件
![2.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/2.png)

#### gulp.pipe

该方法不是 gulp 拥有的，而是 gulp.src 执行之后的返回值对象所拥有的。
该方法用于定义一次操作。如果有多次操作，那么需要多次调用 pipe 方法。
![3.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/3.png)
**红色部分，表示定义了一个操作。
蓝色部分，表示具体操作。**

#### gulp.dest

该方法用于将流文件转换成物理文件，与 src 正好相反。

> gulp.dest(path);
> path: 目录路径 表示发布的地址

![4.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/4.png)

#### gulp.watch

该方法用于监听文件，触发任务。
当我们一边写代码，一边工程化时，如果每一次修改代码都需要自己调用 gulp 命令，会显得很繁琐,此时我们可以使用 gulp.watch.

> gulp.watch(target, handler);
> target: 目标文件
> **三个可选项**： 文件路径字符串、数组、glob 表达式
> handler: 当目标文件发生改变时，执行的任务
> **两个可选项**： 函数、数组

![5.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/5.png)
以上代码表示：当./source/里的所有 js 文件中任一文件发生变化时，调用 update 任务。

### 插件

gulp 本身只有 5 个 API。只依靠它自己，基本什么工程化行为都无法完成，但是它具备大量的插件（这里介绍 7 个插件）。

#### 压缩 js

> 插件名称： gulp-uglify
> 作用：压缩 JS
> 下载：npm install gulp-uglify

![6.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/6.png)

#### 压缩 css

> 插件名称：gulp-clean-css
> 作用：压缩 CSS
> 下载：npm install gulp-clean-css

![7.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/7.png)

#### 压缩 html

> 插件名称：gulp-minify-html
> 作用：压缩 html
> 下载：npm install gulp-minify-html

![8.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/8.png)

#### 合并

> 插件名称：gulp-concat
> 作用：合并文件
> 下载：npm install gulp-concat

![9.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/9.png)

#### 重命名

> 模块名称: gulp-rename
> 作用: 重命名文件
> 下载: npm install gulp-rename

![10.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/10.png)

#### 压缩图片

> 插件名称：gulp-imagemin
> 作用： 压缩图片
> 下载： npm install gulp-imagemin

![11.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/11.png)

#### 书写校验

> 插件名称：gulp-jslint
> 作用： 检验 js 格式
> 下载： npm install gulp-jslint
> 格式规范：
>
> -   if 右边 一定要有一个空格

-   function 的参数列表右圆括号右侧必须要有一个空格
-   换行使用四个空格缩进
-   使用严格模式
-   尾巴不能有空格
-   等等……

![12.png](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/12.png)
检验有错误：
![13.jpeg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/13.jpeg)
检验通过：
![14.jpeg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/09-Engineering-of-gulp/14.jpeg)