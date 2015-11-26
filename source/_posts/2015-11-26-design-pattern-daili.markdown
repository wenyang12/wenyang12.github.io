---
layout: post
title: "设计模式--代理模式"
date: 2015-11-26 10:15:07 +0800
comments: true
categories: [设计模式, 代理模式]
tags: [设计模式, 代理模式]
---
## 概念解读
文字解读

代理，顾名思义就是帮助别人做事，GoF（《Design Patterns: Elements of Reusable Object-Oriented Software》（即后述《设计模式》一书），由 Erich Gamma、Richard Helm、Ralph Johnson 和 John Vlissides 合著（Addison-Wesley，1995）。这几位作者常被称为"四人组（Gang of Four）"）对代理模式的定义如下：
>代理模式（Proxy），为其他对象提供一种代理以控制对这个对象的访问。
>代理模式使得代理对象控制具体对象的引用。代理几乎可以是任何对象：文件，资源，内存中的对象，或者是一些难以复制的东西。

## 作用和主意事项
作用

- 远程代理（一个对象将不同空间的对象进行局部代理）。
- 虚拟代理（根据需要创建开销很大的对象如渲染网页暂时用占位代替真图）。
- 安全代理（控制真实对象的访问权限）。
- 智能指引（调用对象代理处理另外一些事情如垃圾回收机制）。

注意事项

不能滥用代理，有时候仅仅是给代码增加复杂度。

## 代码实战

```javascript
//创建 买家构造函数
    var Maijia = function(name) {
        this.name = name;
    };
    //创建 卖家对象 即房东
    var fangdong = {//单一一个对象
        //创建一个卖房的方法
        sell : function(money,from) {
            console.log("收到买家" + "【" + from.name + "】付的" + money + "人民币");
        }
    };
    // 创建一个代理对象，即代理者,这个代理者是代理房东做事的
    var daili = {//代表房东，一对一的关系
        all:{},//用于储存买家的对象
        zhuce:function(obj) {//买家到中介里登记个人
            this.all[obj.name] = obj;
        },
        sell : function(money,from) {//创建一个卖房的方法
            fangdong.sell(money,this.all[from.name]);
        }
    };
    //运用
    var maijiaA = new Maijia("maijiaA");//创建一个买家A
    daili.zhuce(maijiaA);//买家A到中介注册
    daili.sell("100万", maijiaA);//然后中介给买家A卖了一套100万的房，房东那边就可以收到钱了，中介给房东办了事
```
## 题外话：代理模式跟中介者模式的区别

- 代理模式有以下特点：
    - 一对一，这个代理只能代表一个对象。
    - 只能代表一方，PB是B的代理，A可以通过PB访问B，但是B不能通过PB访问A。
- 中介者模式有以下特点：
    - 多对多，这些被管理的对象都可以通信，它们的业务关系应该是交织在一起的。
    - A能够通过中介访问B，B也能够通过中介访问A

>以上信息来自[极客学院](http://www.jikexueyuan.com/)的视频观看，属于个人记录，以及添加了一些个人理解。