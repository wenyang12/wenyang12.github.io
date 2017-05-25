---
layout: post
title: "express测试用例"
date: 2015-12-25 11:54:14 +0800
comments: true
categories: [express, Node.js框架]
tags: [express, Node.js框架]
---
express是基于 Node.js 平台，快速、开放、极简的 web 开发框架。

## 开始
创建一个文件夹
>$ mkdir express-test

进入express-test目录并创建包管理文件json
>$ cd express-test

>$ npm init

安装依赖包
>$ npm install express body-parser ejs --save

<!--more-->

创建一个app.js 入口文件

```
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
//json类型的body  如果用户通过post发送的数据，则采用json解析
app.use(bodyParser.json());
//query string类型body 通过get请求url附带数据发送过来的解析方式
app.use(bodyParser.urlencoded({
    extended: false
}));
//静态文件目录 请求一个文件时，优先到这个目录里找，找到返回
app.use(express.static(__dirname + '/public')); //把这个目录下的所有文件暴露给http接口
//路由与业务逻辑
app.set('view engine', 'ejs');//设置魔板引擎
app.set('views', __dirname + '/views');//设置魔板路径
app.get('/', function (req, res) {
    res.render('index.ejs', {name: 'wen'});
});
app.use('/user', require('./routes/users.js')); //可以通过localhost:8000/user/list/  访问
app.listen(8000);
```
启动项目
>$ node app.js

在浏览器输入http://localhost:8000 测试效果 以及可以通过 http://localhost:8000/user/list 测试效果 对应源码看看为什么这样输出

**`test1.js` 和`test2.js`是 挂载路径 app.mountpath 的例子**

test1.js
```
//app.mountpath demo
var express = require('express');
var app = express(); // the main app
var admin = express(); // the sub app
admin.get('/', function (req, res) {
    console.log(admin.mountpath); // /admin
    res.send('Admin Homepage');
});
app.use('/admin', admin); // mount the sub app
app.listen(3000);
```
on get http://localhost:3000/admin  the following is printed:

/admin 

test2.js
```
var express = require('express');
var admin = express();
var app = express();
admin.get('/', function (req, res) {
    console.log(admin.mountpath); // [ '/adm*n', '/manager' ]
    res.send('Admin Homepage');
});
var secret = express();
secret.get('/', function (req, res) {
    console.log(secret.mountpath); // /secr*t
    res.send('Admin Secret');
});
admin.use('/secr*t', secret); // load the 'secret' router on '/secr*t', on the 'admin' sub app
app.use(['/adm*n', '/manager'], admin); // load the 'admin' router on '/adm*n' and '/manager', on the parent app
app.listen(3000);
```
on get http://localhost:3000/admin or http://localhost:3000/manager  the following is printed:

\[ '/adm*n', '/manager' \]

on get http://localhost:3000/admin/secret or http://localhost:3000/manage/secret  the following is printed:

/secr*t
    
**`test3.js` 是Events 事件监听的例子**
```
var express = require('express');
var admin = express();
var app = express();
admin.on('mount', function (parent) {//监听mount事件
    console.log('Admin Mounted');
    console.log(parent); // refers to the parent app
});
admin.get('/', function (req, res) {
    res.send('Admin Homepage');
});
app.use('/admin', admin);
app.listen(3000);
```
运行服务时就立马输出：Admin Mounted 和 输出指向父app的对象

on GET http://localhost:3000/admin the following is printed:

页面输出：Admin Homepage

**`test4.js` 是使用回调函数数组处理路由例子**
```
//使用回调函数数组处理路由
var express = require('express');
var app = express();
var cb0 = function (req, res, next) {
    console.log('CB0');
    next();
};
var cb1 = function (req, res, next) {
    console.log('CB1');
    next();
};
var cb2 = function (req, res) {
    res.send('Hello from C!');
};
app.get('/', [cb0, cb1, cb2]);
app.listen(3000);
```
on GET http://localhost:3000 the following is printed:

CB0

CB1

Hello from C!

**`test5.js` 是路由例子**

```
var express = require('express');
var app = express();
var birds = require('./routes/birds');
app.use('/birds', birds);
app.listen(3000);
```
`./routes/birds.js`
```
var express = require('express');
var router = express.Router();
// 该路由使用的中间件
router.use(function timeLog(req, res, next) {
    console.log('Time: ', Date.now());
    next();
});
// 定义网站主页的路由
router.get('/', function(req, res) {
    res.send('Birds home page');
});
// 定义 about 页面的路由
router.get('/about', function(req, res) {
    res.send('About birds');
});
module.exports = router;
```
on GET http://localhost:3000/birds the following is printed:

Time:  1451011422551

页面显示：Birds home page

on GET http://localhost:3000/birds/about the following is printed:

Time:  1451011578836

页面显示：About birds

**`test6.js` 是param参数方法的使用例子**
```
var express = require('express');
var app = express();
app.param(['id', 'page'], function (req, res, next, value) {
    console.log('CALLED ONLY ONCE with', value);
    next();
});
app.get('/user/:id/:page', function (req, res, next) {
    console.log('although this matches');
    res.send(req.params);
    next();
});
app.get('/user/:id/:page', function (req, res) {
    console.log('and this matches too');
    res.end();
});
app.listen(3000);
```
On GET /user/42/3, the following is printed:

CALLED ONLY ONCE with 42

CALLED ONLY ONCE with 3

although this matches

and this matches too


**`test7.js` 是jsonp的使用例子**
```
var express = require('express');
var app = express();
app.use(express.static('public'));
app.get('/test7', function(req ,res) {
    res.jsonp({user: 'wen'});
});
app.listen(3000);
```

`./public/test7.html`

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>test7</title>
    <script src="http://code.jquery.com/jquery-latest.js"></script>
</head>
<body>
<div><button id="btn">获取jsonp数据</button></label></div>
<div>输出：<span id="output"></span></div>
<script>
    function foo(obj) {
        console.log('执行了foo函数：输出 %s',obj.user);
    }
    $('#btn').click(function() {
        $.getJSON('http://localhost:3000/test7?callback=?',function(data) {
            console.log(data);
            $('#output').html(data.user);
        })
    });
</script>
<script src="http://localhost:3000/test7?callback=foo"></script>
</body>
</html>
```
On GET http://localhost:3000/test7.html, the following is printed:

页面控制台此时输出了 “执行了foo函数：输出 wen” (说明页面加载时，执行了回调函数foo，并传入了服务器传回来的对象 {user: 'wen'} 作为参数)

点击页面上的`获取jsonp数据` 按钮 即可看到页面显示 “输出：wen”

而控制面板则打印出对象： Object { user="wen"}


以上可以对应启动项目

>$ node test1.js

> ##OR

>$ node test2.js

类似以上对应启动项目测试用例

查看全部源码可以到我的github上下载：[express-test](https://github.com/wenyang12/express-test)

##参考资料
[Express 4.x API 中文手册](http://www.expressjs.com.cn/4x/api.html)