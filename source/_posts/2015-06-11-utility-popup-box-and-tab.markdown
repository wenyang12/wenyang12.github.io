---
layout: post
title: "共用弹出框和选项卡jquery组件写法"
date: 2015-06-11 11:09:54 +0800
categories: [jquery, 弹出框, 选项卡]
tags: [jquery, 弹出框, 选项卡]
comments: true
---


给jquery加了两个实例方法，一个为弹出框方法alertMsg(),另一个为tab().

## 实现代码如下：

```javascript
    (function($){
        $.fn.extend({
                /* @method alertMsg({title,html,btnvalue,width,callback})
                *        @parameters
                *        title  弹出框的标题
                *        html 弹出框里边的html内容
                *        btnvalue  弹出框底部的按钮内容
                *        width 弹出框的宽度
                *        callback  点击确认之后的回调函数
                */
                alertMsg:function(obj){
                    var opt={//默认参数
                            title:'我是弹出框标题',
                            html:'我是弹出框内容',
                            btnvalue:'确认',
                            width:374,
                            callback:function(){
                                    $('.msgbox-modal').hide();
                                    }
                            }
                    if(arguments[0]){
                            if(typeof arguments[0] === 'object'){
                                    $.extend(opt,arguments[0])
                                    }else{
                                            throw new Error('传入的参数要以对象形式传入！否则无效！')
                                            }
                    }
                    var htmlframe=$('<div class="msgbox-modal"><div class="msgbox"><div class="msgbox-bj"><div class="msgbox-header"><div class="msgbox-title">'+opt.title+'</div><div class="msgbox-x-btn">×</div></div><div class="msgbox-body">'+opt.html+'</div><div class="msgbox-footer"><input class="msgbox-btn bbtn ok" type="button" value=""></div></div></div></div>');
                    this.click(function(){
                            htmlframe.appendTo($('body'));
                            $('.msgbox-btn').val(opt.btnvalue);
                            $('.msgbox').attr('style','margin-left:-'+opt.width/2+'px;'+'width:'+opt.width+'px;');
                            $('.msgbox-modal').show();
                            });
                    $('body').on('click','.msgbox-x-btn',function(){
                            $('.msgbox-modal').hide();
                            return false;
                            })
                    $('body').on('click','.msgbox-btn',opt.callback);
                    },
                /* @method tab()*/
                tab:function(){
                    var tabtitle=this.find('.tabbox-title ul li');
                    var tabbody=this.find('.tabbox-body .tab');
                    tabtitle.mouseover(function(){
                            var index=tabtitle.index(this);
                            $(this).addClass('on').siblings().removeClass('on');
                            tabbody.eq(index).show().siblings().hide();
                    }).eq(0).trigger('mouseover');
                }
        })
    })(jQuery)
```
## 如何使用：
### 调用弹出框示例：
>$(select).alertMsg();

这个alertMsg方法有1个参数，以对象的方式传入，当什么都不传入时，则采用默认参数

- 传入参数示例：
>$(select).alertMsg({
    title:'我是标题'，//这个事弹出框的标题
    html:'我是内容'，//该内容也可以以html的形式传入即‘<div>我是身体内容</div>’
    btnvalue:'确认'，
    width:'350'//这个事设置弹出框的宽度
    callback:function(){//点击弹出框确认后的回调函数，可以写入你希望点击确认后要执行的函数
        ..........
    }
});

### 调用选项卡示例：
>$(select).tab()//select 为包裹选项卡的元素

html代码如：

```html
<!--star选项卡的内容-->
<div class="tab-box tab-box-1">
        <div class="tabbox-title">
            <ul class="clearfix">
                <li class="on">选项一</li>
            <li>选项二</li>
            <li>选项三</li>
        </ul>
    </div>
    <div class="tabbox-body">
            <div class="tab tab-1">我是选项一的内容</div>
        <div class="tab tab-2">我是选项二的内容</div>
        <div class="tab tab-3">我是选项三的内容</div>
    </div>
</div>
<!--end选项卡的内容-->
```
样式代码如下：

```CSS
 body{
            font-family: "microsoft YaHei", Arial, "Lucida Grande", sans-serif;
        }
       body,div,p,ul,li{margin: 0; padding: 0;}
       ul,li{list-style: none;}
       .clearfix:after{
           display: block;
           height: 0;
           content: "";
           font-size: 0;
           line-height: 0;
           clear: both;
       }
        /*弹出框样式*/
        .bbtn{ border:1px solid #52a0e5; border-radius: 1px; display: inline-block; font-weight: 700; height: 28px; margin-right: 10px; text-align: center; padding:0 5px;cursor:pointer;}
        .msgbox-modal{ background:rgba(0,0,0,0.5); z-index:9999; position:fixed; left:0; top:0; width:100%; height:100%;}
        .msgbox-bj{ background:#fff; border:1px solid #aeaeae;}
        .msgbox{ left:50%; top:50%; background:#fff; position:fixed; padding:3px; background:rgba(0,0,0,0.2);}
        .msgbox-header{  padding:20px 0 20px 30px; }
        .msgbox-title{font-size:14px; color:#3c3c3c; font-weight:700;}
        .msgbox-x-btn{ position:absolute; right:20px; top:20px; cursor:pointer;}
        .msgbox-body{  padding-left:30px;}
        .msgbox-footer{ padding:20px 0 20px 30px;}
        .ok{border-color: #52a0e5; color: #fff; background:#52a0e5;}
        .cancel{border-color: #d9d9d9; color: #3c3c3c; background:#fff;}
        #alert_msg{ display:none;}
        /*选项卡样式*/
        .tab-box{ background:#fff; width:500px; margin:10px 0;}
        .tabbox-title ul{border-bottom:1px solid #dfdfdf;}
        .tabbox-title ul li{ float:left; width:80px; height:24px; text-align:center; line-height:24px; border:1px solid #dfdfdf; border-bottom:none; background:#f6f6f6; margin-right:5px; cursor:pointer;}
        .tabbox-title ul li.on{ padding-bottom:1px; margin-bottom:-1px;}
        .tabbox-body .tab{ display:none; border:1px solid #dfdfdf;border-top:none; width:100%; background:#f6f6f6; padding:20px 0; text-align:center; box-sizing:border-box;}
```
即可以这样调用：$('.tab-box-1').tab()


