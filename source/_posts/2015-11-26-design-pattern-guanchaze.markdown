---
layout: post
title: "设计模式--观察者模式"
date: 2015-11-26 09:41:27 +0800
comments: true
categories: [设计模式, 观察者模式]
tags: [设计模式, 观察者模式]
---
## 概念解读

文字解读
>观察者模式由叫发布订阅模式（Publish/Subscribe），它定义了一种一对多的关系，让多个观察者对象同时监听某一个主题对象，这个主题对象的状态发生变化时就会通知所有的观察者对象，使得他们自动更新自己。

## 作用和主意事项
作用

- 支持简单广播通信，自动通知所有已订阅过的对象。
- 载入页面后目标对象很容易与观察者存在一种动态关联，增加了灵活性。
- 目标对象与观察者之间的抽象耦合关系能够单独扩展及重用。

主意事项
>监听要在触发之前

## 代码实战

```javascript
    //用jquery来实现观察者模式
    (function($) {
        var o = $({});//创建一个jquery空对象
        $.extend({//扩展两个jquery静态方法
            dingyue:function() {//订阅方法
                o.on.apply(o,arguments);
            },
            fabu:function() {//发布方法
                o.trigger.apply(o,arguments);
            },
            tuiding:function() {//退订方法
                o.off.apply(o, arguments);
            }
        });
        //订阅者A
        $.dingyue('tianqi',function(e,data){//订阅了天气
            console.log('我是订阅者A【收到】'+data);
        });
        //订阅者B
        $.dingyue('zazhi',function(e,data){//订阅了杂志
            console.log('我是订阅者B【收到】'+data);
        });
        //发布者
        $.fabu('tianqi', "今天夜间有雷阵雨");//发布者天气发布消息
        $.fabu('zazhi', "杂志最近有新版本更新");//发布者杂志发布消息
        $.tuiding('zazhi');
        //第二次发布消息
        $.fabu('tianqi', "天气第二次发布");//发布者天气发布消息
        $.fabu('zazhi', "杂志第二次发布");//发布者杂志发布消息，此时发布的消息，订阅者B无法收到了，它退订了
    })(jQuery);
    //js原声代码实现
    //创建发布者构造函数
    function Publisher() {
        this.subscribers = [];//这个属性用来保存订阅者的引用
    }
    //为发布者添加一个发送消息给订阅者的方法
    Publisher.prototype.deliver = function(data) {
        this.subscribers.forEach(function(fn) {
            fn(data);
        });
        return this;
    };
    //创建订阅者，订阅方法
    Function.prototype.subscribe = function(publisher) {
        var that = this;
        var alreadyExists = publisher.subscribers.some(function(el) {//判断订阅者在发布者属性subscribers是否存在
            return el === that;
        });
        if(!alreadyExists){//如果不存在
            publisher.subscribers.push(this);//把订阅者保存到发布者的属性subscribers中，即把订阅者注册到发布者中
        }
        return this;
    };
    // 创建订阅者，退订方法
    Function.prototype.unsubscribe = function(publisher) {
        var that = this;
        publisher.subscribers = publisher.subscribers.filter(function(el) {
            return el !== that;
        });
        return this;
    };
    //运用
    var fbA = new Publisher();
    var fbB = new Publisher();
    //创建一个订阅者A
    var dyA = function(data){
        console.log("订阅者A接收到的消息为："+data);
    };
    var dyB = function(data) {
        console.log("订阅者B接收到的消息为：" + data);
    };
    var dyC = function(data) {
        console.log("订阅者C接收到的消息为：" + data);
    };
    dyA.subscribe(fbA);//订阅者A订阅了发布者fbA；
    dyB.subscribe(fbB);//订阅者B订阅了发布者fbB；
    dyC.subscribe(fbA).subscribe(fbB);//订阅者C订阅了发布者fbA和发布者fbB；
    //有时候，有些订阅者在监听到某一次性的事件后会在回调阶段立即退订该事件。其做法大致如下：
    //创建一个订阅者D
    var dyD = function(data) {
        console.log("订阅者D接收到的消息为：" + data);
        //订阅后，立即退订
        dyD.unsubscribe(fbB);
    };
    dyD.subscribe(fbB);//订阅了发布者B，然后监听完发布者B后立即退订
    //发布者发送消息
    fbA.deliver("欢迎您订阅了我，我是发布者A");//发布者发送资讯时，这个时候，注册到它这里的所有订阅者，都可以接收到这个信息
    fbB.deliver("我这里有最新的javscript消息，我是发布者B").deliver('这是我第二次发布的消息');//第二次发布消息时，订阅者D是收不到消息的。
```
>以上信息来自[极客学院](http://www.jikexueyuan.com/)的视频观看，属于个人记录，以及添加了一些个人理解。


