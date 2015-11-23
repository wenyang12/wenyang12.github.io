---
layout: post
title: "倒计时jquery插件"
date: 2015-11-19 11:55:44 +0800
comments: true
categories: [倒计时, jQuery插件]
tags: [倒计时, jQuery插件]
---
## 前言
当想在前端显示一个后端传过来的剩余毫秒数时，可不可以自动让传入的值直接显示为倒计时呢？

## 代码实现思路
> html结构为`<div data-time="[后端传过来的剩余毫秒数]" data-id="time"><span class="t_d"></span><span class="t_h"></span><span class="t_m"></span><span class="t_s"></span></div>`

##实现代码
```javascript
/*倒计时 start*/
    /**
     * 测试用例 在元素中加入data-time="ms" data-id ="time" ,并且在其子元素分别添加4个类class="t_d"、class="t_h"、class="t_m"、class="t_s" 注：（其中要是
     * 没有倒计时天数的话时间类要改为class="t_allh" 其他的不变）；
     * 例如：<div data=time ="60" data-id="time"><span class="t_d"></span><span class="t_h"></span><span class="t_m"></span><span class="t_s"></span></div>
     * */
    $.fn.countDown =function(ms) {//ms为传入的剩余秒数
            var Timer;
            var self=this;
            var timerHandle = function() {
                if(!Number(ms)){//当剩余时间为0时
                    clearInterval(Timer);
                    self.html('-');
                    return false;
                }
                var day=0,
                    hour= 0,
                    allhour=0,
                    minute=0,
                    second=0;//时间默认值
                if(ms > 0){
                    day = Math.floor(ms / (60 * 60 * 24));
                    hour = Math.floor(ms / (60 * 60)) - (day * 24);
                    allhour =Math.floor(ms / (60 * 60));
                    minute = Math.floor(ms / 60) - (day * 24 * 60) - (hour * 60);
                    second = Math.floor(ms) - (day * 24 * 60 * 60) - (hour * 60 * 60) - (minute * 60);
                }
                if (minute <= 9) minute = '0' + minute;
                if (second <= 9) second = '0' + second;
                if (hour <= 9) hour = '0' + hour;
                if (allhour <= 9) allhour = '00' + allhour;
                if (allhour <= 99 && allhour >=10) allhour = '0' + allhour;
                self.children('.t_d').html(day+'\u5929');
                self.children('.t_h').html(hour+'\u65f6');
                self.children('.t_allh').html(allhour+'\u65f6');
                self.children('.t_m').html(minute+'\u5206');
                self.children('.t_s').html(second+'\u79d2');
                ms--;
            };
            timerHandle();
            Timer=window.setInterval(timerHandle, 1000);
    };
    if($("[data-id='time']").length){
        var timeEle=$("[data-id='time']");
        for(var i = 0,len =timeEle.length; i < len; i++){
            var timeEleItem = timeEle.eq(i);
            var time = timeEleItem.attr('data-time');
            if(!timeEleItem.attr('flag')){
                timeEleItem.countDown(time);
            }
            timeEleItem.attr('flag',true);//添加一个属性，用于判断是否添加了倒计时，方便异步加载的时候遍历，可以不重复加载倒计时。
        }
    }
    /*倒计时 end*/
```
