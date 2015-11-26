---
layout: post
title: "设计模式--中介者模式"
date: 2015-09-16 10:54:30 +0800
categories: [设计模式, 中介者模式]
tags: [设计模式, 中介者模式]
comments: true
---
## 概念解读
文字解读
>用一个中介对象来封装一序列的对象交互。中介者使各对象不需要显式的引用，从而使其耦合松散，而且可以独立的改变它们之间的交互。


## 作用和注意事项
作用
>软件开发中，中介者是一个行为设计模式，通过提供一个统一的接口让系统的不同部分进行通信。一般如果系统有很多子模块需要直接沟通，都需要创建一个中央控制点让其各模块通过该中央控制点进行交互。中介者模式可以让这些子模块不需要直接沟通，而达到进行解耦的目的。

主意事项
>当系统出现多对多交互复杂的对象群时，先不要急着使用中介者模式，而是要思考一下是不是系统设计有问题。

##代码实战
```javascript
    //创建一个飞机的构造函数
    var Plane = function(name) {
        this.name = name;
    };
    //创建飞机发送消息的方法
    Plane.prototype.send = function(msg,to) {
        var from = this;
        tatai.send(msg,from,to);//用塔台给我发信息,别自己发
    };
    //创建飞机接收信息的方法
    Plane.prototype.recevie = function(msg,from) {
        console.log(this.name + "【接收到】" + msg+"(消息来自"+from.name+")");
    };
    //创建一个塔台对象，即在这里充当了中介者对象
    var tatai = {
        all :{},//空对象，用于装载飞机的实例
        zhuce : function(plane){//把飞机的实例传入即把飞机注册到塔台里
            this.all[plane.name] = plane;
        },
        send : function(msg,from,to) {
            if(to === undefined){//没传递to时，发送给所有飞机
                for(var key in this.all){
                    if(this.all[key] !== from) {
                        this.all[key].recevie(msg, from);
                    }
                }
            }else{
                this.all[to.name].recevie(msg, from);
            }
        }
    };
    //运用
    var planeA = new Plane('planeA');//创建飞机A
    var planeB = new Plane('planeB');//创建飞机B
    var planeC = new Plane('planeC');//创建飞机C
    tatai.zhuce(planeA);//把飞机A注册到塔台里
    tatai.zhuce(planeB);//把飞机B注册到塔台里
    planeA.send("我距离地面还有500米", planeB);//飞机A给飞机B发送消息
    planeB.send("我准备降落白云机场", planeA);//飞机B给飞机A发送消息
    planeC.send("我飞机装到小鸟了，出现故障了！");//飞机C给所有飞机发送消息。注：飞机C因为没有注册到塔台，所以他不能接收到塔台传来的信息，但是它可以发送信息
</script>
```
>以上信息来自[极客学院](http://www.jikexueyuan.com/)的视频观看，属于个人记录，以及添加了一些个人理解。