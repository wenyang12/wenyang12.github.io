---
layout: post
title: "原生AJAX"
date: 2016-01-04 14:41:44 +0800
comments: true
categories: [ajax]
tags: [ajax]
---
为了方便，我们一直用jquery写AJAX异步获取数据，要是别人问，您能否用原生的javascript自己写一个异步获取数据的脚本呢？我想应该很多人都忘记了。

## AJAX是什么？
AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。

AJAX 不是新的编程语言，而是一种使用现有标准的新方法。

AJAX 是与服务器交换数据并更新部分网页的艺术，在不重新加载整个页面的情况下。

## XMLHttpRequest对象
其实，这个技术无非就是一个XMLHttpRequest对象而已，只要你熟悉这个对象的API的使用即可实现异步加载你想要的数据。

那怎么来创建这个对象呢？

<!--more-->

**创建对象XMLHttpRequest**
```
var xmlHttp = new XMLHttpRequest();
```
>要是不用兼容旧浏览器IE5和IE6 就是以上一行代码即可创建这个对象。当然现在IE5和IE6，可以说淘汰了，所以就不兼容它了，没必要。想要兼容它就加个if判断即可，它的创建方式如下：

```
var xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
//判断如：
var xmlHttp;
if(window.XMLHttpRequest){//现代浏览器
    xmlHttp = new XMLHttpRequest();
}else{//旧版浏览器IE5、IE6
    xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
}
```
>以下给出的实例都不考虑旧版浏览器了，淘汰了没必要。

**这个对象有如下3个方法和5个属性，如下**

**方法：**

1、 `xmlHttp.open(method,url,async);`

参数method：规定请求的类型；GET或POST

参数url: 请求的地址；

参数async: true（异步）或false（同步）。

GET 还是 POST？
 
与 POST 相比，GET 更简单也更快，并且在大部分情况下都能用。
 
然而，在以下情况中，请使用 POST 请求：

 - 无法使用缓存文件（更新服务器上的文件或数据库）
 - 向服务器发送大量数据（POST 没有数据量限制）
 - 发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠

2、 `xmlHttp.send(string)` 

将请求发送到服务器

参数String：仅用于POST请求

3、 `xmlHttp.setRequestHeader(header, value)` 

向请求添加http头

header：规定头的名称

value：规定头的值

如：`xmlhttp.setRequestHeader("Content-type", "application/x-www-form-urlencoded");`  这个是告诉服务器端以这个类型类解析数据

**属性：**

1、 `onreadystatechange` : 	存储函数（或函数名），每当 readyState 属性改变时，就会调用该函数。

2、 `readyState`: 

存有 XMLHttpRequest 的状态。从 0 到 4 发生变化。

- 0 请求未初始化
- 1 服务器连接已建立
- 2 请求已接收
- 3 请求处理中
- 4 请求已完成，且响应已就绪

3、 `status`: 

200:"ok"  

404:"未找到页面"

一般接收服务器返回的数据写法如下：

```
xmlhttp.onreadystatechange= function()
  {
  if (xmlhttp.readyState === 4 && xmlhttp.status === 200)
    {
    document.getElementById("myDiv").innerHTML=xmlhttp.responseText;//获得字符串形式的响应数据。
    }
  } 
```

4、 `responseText`: 获得字符串形式的响应数据。

5、 `responseXML` : 获得 XML 形式的响应数据。

##代码实战

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>ajax</title>
</head>
<body>
<button id="getAjax1">获取responseText</button>
<button id="getAjax2">获取responseXML</button>
<br/>
<div>
    <p>responseText返回的文本：<span id="output1"></span></p>
    <p>responseXML返回的文本：<span id="output2"></span></p>
</div>
<script>
    //采用模块模式来封装代码
    var myAjax = (function(){
        function Temp(){
            this.xmlHttp = new XMLHttpRequest();
        }
        Temp.prototype.init =function(method, url, callback) {
            var self = this;
            this.xmlHttp.onreadystatechange=function(){
                if(self.xmlHttp.readyState === 4 && self.xmlHttp.status === 200){
                    callback(self.xmlHttp);
                }
            };
            this.xmlHttp.open(method,url,true);
            this.xmlHttp.send();
        };
       return{
           ajax: function(method, url, callback) {
               var ajaxChild = new Temp();
               ajaxChild.init(method, url, callback);
           }
       }
    })();
    //测试用例
    document.getElementById('getAjax1').onclick = function() {
        myAjax.ajax('GET', 'demo.php?name=wen', function (xmlHttp) {//获取responseText文本数据
            document.getElementById('output1').innerHTML = xmlHttp.responseText;
            console.log(xmlHttp.responseText);
        })
    };
    document.getElementById('getAjax2').onclick = function() {
        myAjax.ajax('GET', 'demo.xml',function(xmlHttp){//获取responseXML文档对象
            var xmlEleValue = xmlHttp.responseXML.getElementsByTagName('name')[0].firstChild.nodeValue;
            document.getElementById('output2').innerHTML=xmlEleValue;
            console.dir(xmlHttp.responseXML);
        });
    }
</script>
</body>
</html>
```
`demo.php`

```
<?php
echo "hello world";
```

`demo.xml`

```
<?xml version="1.0" encoding="UTF-8"?>
<name>wen</name>
```

> 以上用到的源码可以到我的github下载[ajax-text](https://github.com/wenyang12/ajax-test)

##参考链接
[菜鸟教程-AJAX教程](http://www.runoob.com/ajax/ajax-tutorial.html)


