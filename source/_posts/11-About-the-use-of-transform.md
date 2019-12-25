---
layout: b
title: 开发中关于transfrom缩放的一次应用
date: 2019-6-21 12:01:08
categories:
	- css
tags:
	- 兼容性
---

### 背景

在做项目上的登陆注册页面时，设计图给的是 1920×1080 的尺寸，当后来要加一行表单时就遇到了一个显示上问题。

<!-- more -->

如下图：
![1.jpg](https://i.loli.net/2019/12/02/2cwzlXgOd7LEUD9.jpg)
在之前一版没加‘验证码’表单项时，在笔记本上还能够勉强都显示出来，但是再加一行过后，明显已经超过浏览器的显示范围了，这对于用户体验来说很不友好。
其实如果是在正规的 1920\*1080 分辨率的屏幕下，显示效果很好，但是笔记本用户一般会对电脑显示进行放大，且一般为 125%，所以就会出现这种情况。

### 思考

最开始考虑针对这个分辨率再写一套媒体查询的样式，或者全改为自适应尺寸大小，但是这样做工作量其实比较大，也不是很好改。后来考虑能不能单独对这个页面进行缩小显示，便找到了 zoom 属性，单独设置右边表单盒子的 zoom。
渲染的时候先用 window.document.documentElement.clientHeight 读取到页面的高度，当这个高度小于 950 的时候，加上 zoom：0.8，但是这时候又遇到了一个新的问题：在 ie 和 chrome 下支持，在 firefox 下不起作用。
于是马上打开百度搜索，在看过几个答案过后，得出一个结论：zoom 确实存在兼容性问题，但是可以用 transform: scale(0.8)搭配 transform-origin：‘center center’来解决 firefox 下不起作用的问题（不过最后因为不用考虑 ie 低版本，所以直接舍弃掉 zoom 属性，统一用 transform：scale(0.8)以及 transform-origin：‘center center’来改变样式）。
最后在 125%缩放的笔记本显示效果如下：
![2.jpg](https://i.loli.net/2019/12/02/MaXqKAEQ3LVwkNF.jpg)
