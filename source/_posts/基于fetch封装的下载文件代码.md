---
title: 基于fetch封装的下载文件代码
date: 2019-5-20 18:16:48
categories:
	- fetch
tags:
	- 封装
---
##### 背景
公司的一个项目当中，之前的所有请求并不需要在请求头上都加上token，不需要验证，但是最近后台逻辑改了，要都加上token。大多数请求都还好，直接加上就可以了，但是下载文件之前是使用a标签来做的，a标签又没有办法直接加headers，那么只能考虑用另一种方法模拟，再加上项目中请求基本都是用的fetch，于是自然是想着基于fetch封装一个下载文件的方法。
<!-- more -->
```javascript
import fetch from 'dva/fetch';
import { message } from 'antd';

// 检查状态码的函数
function checkStatus(res) {
    if (res && res.status === 401) {
        message.error('下载出错，查看控制台', 2);
    }

    if (res.status >= 200 && res.status < 300) {
        return res;
    }

    const error = new Error(res.statusText);
    error.res = res;
    throw error;
}

/**
 * 下载文件的主函数
 * 
 * @param {string} url       请求地址
 * @param {string} fileName  文件名
 * @param {string} ext       扩展名
 * 
 */
function downloadFile(url, fileName, ext) {
    fetch(url, {
        headers: {
            'Authorization': localStorage.getItem('token')
        }
    }).then(checkStatus).then(res => res.blob().then(blob => {
        let a = document.createElement('a');
        let url = window.URL.createObjectURL(blob);
        let filename = `${fileName}.${ext}`;
        a.href = url;
        a.download = filename;
        a.click();
        window.URL.revokeObjectURL(url);
    }))
}

export default {
    downloadFile,
}
```