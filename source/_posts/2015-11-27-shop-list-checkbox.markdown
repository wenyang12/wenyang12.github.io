---
layout: post
title: "购物车订单列表的复选框组件"
date: 2015-11-27 13:40:24 +0800
comments: true
categories: [复选框组件]
tags: [复选框组件]
---
## 起源
我们要是写电商网页的时候，会经常碰到购物车列表的复选框点选操作，点击全选，然后上边的复选框都能选中，然后结算，或者单个选中上边的复选框时，假如都选中了，下边的全选复选框也会打上勾，
所以为了将来开发到电商网站时，可以复用自己写过的脚本，特地抽象写成了一个组件。

## 实现思路
**需求**

- 点击全选复选框按钮时，列表中的所有复选框都被选中；
- 选中列表中的复选框时，假如全选中了，全选复选框状态也变为选中，否则不选中。

<!--more-->
**代码架构**

声明一个类CheckList

属性：

- checkItem （用于储存所有复选框对象，不包含全选复选框）；
- checkAll (用于储存全选复选框对象)；
- allCheckItem (用于储存所有复选框，包含全选复选框在内)；
- state （储存状态，假如为true时，当页面加载完后所有复选框都为选中状态；反之false，为不选中状态）。

方法：

- init 用于初始化加载
- \_check 用于选中或不选中复选框（包含全选复选框在内）的操作
- \_allCheck 全选
- \_notAllcheck 全不选

>每次复用这个组件的时候，实例化这个类时，同时传入全选复选框对象，和所有复选框（包含全选复选框），和自己想要的初始化页面时的复选框状态即可复用。

## 代码实现（其中可以自定义复选框样式可以参考以下代码贴出的css样式代码）

```html
    <!DOCTYPE html>
    <html>
    <head lang="en">
        <meta charset="UTF-8">
        <title>购物车订单列表复选框组件</title>
        <style>
            .form-group{
                margin-bottom: 30px;
                height: 30px;
            }
            /*改变复选框样式*/
            .form-group > label > span{
                display: inline-block;
                margin-right: 5px;
                border: 1px solid #d9d9d9;
                width: 1.375em;
                height: 1.375em;
                -webkit-border-radius: 50%;
                -moz-border-radius: 50%;
                border-radius: 50%;
                -webkit-box-sizing:border-box;
                -moz-box-sizing:border-box;
                -o-box-sizing:border-box;
                -ms-box-sizing:border-box;
                box-sizing:border-box;
            }
            .form-group > label input{
                visibility: hidden;
            }
            /*选中复选框样式*/
            .form-group > label > span.active{
                background: #ff5252;
                position: relative;
                border-color: #ff5252;
            }
            .form-group > label > span.active:after{
                position: absolute;
                content: '';
                width: 0.75em;
                height: 0.41em;
                border: 2px solid #ffffff;
                border-top: none;
                border-right: none;
                left: 0.3em;
                top: 0.35em;
                -webkit-box-sizing:border-box;
                -moz-box-sizing:border-box;
                -o-box-sizing:border-box;
                -ms-box-sizing:border-box;
                box-sizing:border-box;
                -webkit-transform: rotate(-45deg);
                -moz-transform: rotate(-45deg);
                -ms-transform: rotate(-45deg);
                -o-transform: rotate(-45deg);
                transform: rotate(-45deg);
            }
        </style>
    </head>
    <body>
    <script src="http://libs.baidu.com/jquery/2.0.0/jquery.min.js"></script>
    <div class="box">
        <div class="form-group">
            <label><span><input type="checkbox"/></span>2000金币</label>
        </div>
        <div class="form-group">
            <label><span><input type="checkbox"/></span>2000金币</label>
        </div>
        <div class="form-group">
            <label><span><input type="checkbox"/></span>2000金币</label>
        </div>
        <div class="form-group">
            <label><span><input id="checkAll" type="checkbox"/></span>全选</label>
        </div>
    </div>
    <script>
        //复选框选择
        function CheckList(obj) {
            this.checkItem = obj.checkItem.not(obj.checkAll) || $("input[type='checkbox']").not(obj.checkAll);//所有复选框（不包含全选复选框）
            this.allCheckItem = obj.checkItem || $("input[type='checkbox']");//所有复选框
            this.checkAll = obj.checkAll;//全选复选框
            this.state = obj.state || false;//状态为true时，页面刚加载复选框默认都为选中
        }
        CheckList.prototype ={
            init:function() {
                this._check();
                if(this.state){
                    this._allCheck();
                }else{
                    this._notAllcheck();
                }
            },
            _check:function() {
                //单击全选
                var that = this;
                this.checkAll.on('click',function() {
                    if($(this).prop('checked')){
                        that.checkItem.prop('checked', true).parent().addClass('active');
                        $(this).prop('checked', true).parent().addClass('active');
                    }else{
                        that.checkItem.prop('checked', false).parent().removeClass('active');
                        $(this).prop('checked', false).parent().removeClass('active');
                    }
                });
                //单击每个复选框（不包含全选复选框）
                this.checkItem.on('click',function() {
                    var checkItem =Array.prototype.slice.call(that.checkItem);
                    var flag =checkItem.every(function(elem,index,array) {
                        return $(elem).prop('checked');
                    });
                    if($(this).prop('checked')){
                        $(this).prop('checked',true).parent().addClass('active');
                    }else{
                        $(this).prop('checked',false).parent().removeClass('active');
                    }
                    if(flag){//如果都为选中
                        that.checkAll.prop('checked', true).parent().addClass('active');
                    }else{
                        that.checkAll.prop('checked', false).parent().removeClass('active');
                    }
                })
            },
            _allCheck:function() {// 全选
                this.allCheckItem.prop('checked', true).parent().addClass('active');
            },
            _notAllcheck:function() {//全不选
                this.allCheckItem.prop('checked', false).parent().removeClass('active');
            }
        };
        //兼容旧环境，es5新增的数组方法every
        if (!Array.prototype.every)
        {
            Array.prototype.every = function(fun /*, thisArg */)
            {
                'use strict';
                if (this === void 0 || this === null)
                    throw new TypeError();
                var t = Object(this);
                var len = t.length >>> 0;
                if (typeof fun !== 'function')
                    throw new TypeError();
                var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
                for (var i = 0; i < len; i++)
                {
                    if (i in t && !fun.call(thisArg, t[i], i, t))
                        return false;
                }
                return true;
            };
        }
        //实例化运用
        var checklistA = new CheckList({
            checkItem: $("input[type='checkbox']"),//所有复选框对象
            checkAll:$("#checkAll"),//全选复选框对象
            state:true//状态，为true全选中，为false为不选中（默认为false）
        });
        checklistA.init();
    </script>
    </body>
    </html>
```
