---
layout: post
title: "HTML5 Web Workers"
date: 2015-08-12 18:01:50 +0800
categories: [html5, webWorker]
tags: [html5, webWorker]
comments: true
---

##什么是 Web Worker？
当在 HTML 页面中执行脚本时，页面的状态是不可响应的，直到脚本已完成。
web worker 是运行在后台的 JavaScript，独立于其他脚本，不会影响页面的性能。您可以继续做任何愿意做的事情：点击、选取内容等等，而此时 web worker 在后台运行。
##浏览器支持情况
Internet Explorer 10, Firefox, Chrome, Safari 和 Opera 都支持Web workers.
##局限性
- 不能跨域加载js；
- worker内代码不能访问DOM；
- 各个浏览器对Worker的实现不大一致，例如FF里允许worker中创建新的worker,而Chrome中就不行；
- 不是每个浏览器都支持这个新特性。

## 测试用例 代码如下：
```
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>web worker</title>
</head>
<body>
<div>计数：<output id="result"></output> </div>
<button id="btn1">开始计数</button>
<button id="btn2">停止计数</button>
<script>
    /*web worker 是运行在后台的 JavaScript，不会影响页面的性能。*/
    (function(){
        var w;
        function workerHand() {
            if(typeof(Worker) !== 'undefined'){
                if(typeof(w) === 'undefined'){
                    w = new Worker('http://1.ywenblog.sinaapp.com/wp-content/themes/twentyeleven/js/demo_workers.js');
                }
                w.onmessage = function(e) {//监听脚本发送过来的信息
                    document.getElementById('result').innerHTML = e.data;
                };
            }else{
                document.getElementById('result').innerHTML = "抱歉！，您的浏览器不支持web workers...";
            }
        }
        document.getElementById('btn1').onclick = function() {
            workerHand();
        };
        document.getElementById('btn2').onclick = function() {
            w.terminate();
            w = undefined;
        };
    })()
</script>
</body>
</html>
```
`http://3.wenphp.sinaapp.com/test/demo_workers.js` 代码如下：

``` demo_workers.js
/**
 * Created by Administrator on 2015/8/12.
 */
(function(){
    var i = 0;
    function calc() {
        postMessage(i);//给worker对象放松信息i
        i++;
        setTimeout(calc, 1000);
    }
    calc();
})();
```

>节选自菜鸟教程
