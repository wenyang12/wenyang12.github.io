---
layout: post
title: "formdata异步上传文件的实现"
date: 2017-07-07 16:09:14 +0800
comments: true
categories: [html5, ajax, 文件上传]
tags: [html5, ajax, 文件上传]
---

## 使用FormData来实现文件上传

```
    var xhr = new XMLHttpRequest(),
    fd = new FormData();
    //file为上传的file对象，将文件放入FormData对象中,第三个参数是指定文件名,或者可以不指定。
    fd.append('file', file, file.name); 
    fd.append('type',1); // 继续追加数据   
    xhr.setRequestHeader('X-Requested-With', 'XMLHttpRequest');
    xhr.addEventListener('load',function(evt){ // 监听上传完成
        var response = evt.target.response;     // 返回的数据
        //do something...
    })
    xhr.upload.addEventListener('progress', function(evt){ // 监听上传进度
        //lengthComputabel: 文件长度是否可计算
        if(evt.lengthComputable){
            //evt.loaded: 已下载的字节数
            //evt.total: 文件总字节数
            var percent = Math.round(evt.loaded*100/evt.total);
            console.log(percent);
        }
    })
    xhr.abort() // 终止文件上传方法
    xhr.addEventListener('abort', function(evt){ // 监听文件被终止上传
        // do something...
    })
    xhr.addEventListener('error', function(evt){ // 监听上传错误
        // do something...
    })
    xhr.addEventListener('loaded', function(evt){ // 监听传输结束事件: 不管成功或者失败都会触发
        // do something...
    })

    xhr.open('post', url, true);
    xhr.send(fd);
```
