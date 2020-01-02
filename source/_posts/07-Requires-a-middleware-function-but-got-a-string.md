---
layout: b
title: 处理报错：Router.use() requires a middleware function but got a string
date: 2018-11-16 20:58:20
categories:
	- nodejs
tags:
	- 报错处理
---

今天在配置 nodejs 时出现了一个问题如下图所示：

![问题动图](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/07-Requires-a-middleware-function-but-got-a-string/1.jpg)

<!-- more -->

大意是这个 use 方法需要一个中间件函数，但是它得到的是一个字符串。
一开始我考虑是我配置的路由 js 文件可能出了点问题，因为错误信息中有个 Router。
![需求动图.jpg](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/07-Requires-a-middleware-function-but-got-a-string/2.jpg)
我就对 router 里的 js 文件进行查错：
![需求图](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/07-Requires-a-middleware-function-but-got-a-string/3.jpg)
但是经过搜索，这么引入是没有问题的，于是我在 app.js 中把配置路由的代码给注释掉了，看能不能开启服务器（不配置路由正常情况下也是能够开启服务器的），结果还是同样的报错。
我又试着把配置服务器的注释掉，居然不报错了，看来就是 conf 文件夹里的 js 文件有问题，进入查看。
![问题图](https://myblog-1257961174.cos.ap-beijing.myqcloud.com/blog/07-Requires-a-middleware-function-but-got-a-string/4.jpg)
原来我在配置模板的时候，调用错了方法，用的 use 方法，use 里面两个值都是字符串导致报错，正确的配置应该是使用 set 方法。
**
心得：
use 方法里面不能跟字符串，出现这个报错检查 use 里的参数即可。
书写代码的时候一定要仔细，有可能一个小地方就导致了程序的崩溃。
**
