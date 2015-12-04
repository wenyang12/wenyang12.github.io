---
layout: post
title: "React学习笔记"
date: 2015-12-04 15:18:02 +0800
comments: true
categories: [react]
tags: [react]
---
##我的理解
React是基于状态驱使的。在整个React开发中，你会体会到，组件的状态贯穿着一切，一切都是基于状态驱使开发的。

##为什么使用React
[React](https://facebook.github.io/react/) 是一个 Facebook 和 Instagram 用来创建用户界面的 JavaScript 库。很人多认为 React 是 MVC 中的 V（视图）。

我们创造 React 是为了解决一个问题：构建随着时间数据不断变化的大规模应用程序。为了达到这个目标，React 采用下面两个主要的思想。

**简单**

仅仅只要表达出你的应用程序在任一个时间点应该长的样子，然后当底层的数据变了，React 会自动处理所有用户界面的更新。

**表达能力**

当数据变化了，React 概念上是类似点击了更新的按钮，但仅会更新变化的部分。

## 语法
```javascript
//穿件一个组件
var MyComponent = React.createClass({
    render: function(){
        return <h1>hello world!</h1>;
    }
})
//把组件渲染到页面body中
React.render(<MyComponent></MyComponent>,document.body)
```
<!--more-->
##可配置的函数

**初始化中的函数**

+ getDefaultProps //初始化属性，只调用一次，实例之间共享引用
+ getInitialState //初始化每个实例特有的状态
+ componentWillMount //render之前最后一次修改状态的机会
+ render //只能访问this.props和this.state，只有一个顶层组件，不允许修改状态和DOM输出
+ componentDidMount //成功render并渲染完成真实DOM之后触发，可以修改DOM

**改变状态时，触发的运行中的函数**

+ componentWillReceiveProps //父组件修改属性时触发，可以修改属性、状态
+ shouldComponentUpdate //是否让组件更新渲染到页面当中，返回fasle会阻止render的执行，返回true，则会执行render
+ componentWillUpdate //组件更新之前最后一次要执行的，不能修改属性和状态
+ render //执行渲染
+ componentDidUpdate //组件更新后的操作，可以修改DOM
+ componentWillUnmount //在删除组件之前进行清理操作，比如计数器和事件监听器等

>以上函数，会从上到下依次执行。运行中的函数需要执行的条件是：状态改变才会依次执行，状态不断更新时，运行中的函数会不断循环执行。运行中的函数是由状态驱使的。

## 代码实战
执行React所需要的代码结构如下：

首先引入两个js

```javascript
<script src="react.js"></script>
<script src="JSXTransformer.js"></script>
```
然后在下边即可写入React的组件代码了

```javascript
<script src="react.js"></script>
<script src="JSXTransformer.js"></script>
<script type="text/jsx">// 注意这里的type='text/jsx'
    //这里可以写入你的代码
</script>
```
**实战**

```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
<div id="box"></div>
<script src="build/react.js"></script>
<script src="build/JSXTransformer.js"></script>
<script type="text/jsx">
    var style={
        color:"blue",
        border:"1px solid #ddd"
    };
    var rawHTML = {
        __html:"<span>I'm inner HTML</span>"
    };
    //初始化，创建一个组件
    var HelloWorld = React.createClass(
            {
                //初始化中的函数
                getDefaultProps:function() {//只调用一次，实例之间共享引用
                    console.log('getDefaultProps,1');
                },
                getInitialState:function() {// 初始化每个实例特有的状态
                    console.log('getInitialState,2');
                    return null;
                },
                componentWillMount:function() {//render之前最后一次修改状态的机会
                    console.log('componentWillMount,3');
                },
                render: function() {//只能访问this.props和this.state,只有一个顶层组件，不允许修改状态和DOM输出
                    console.log('4');
                    return <p>Hello ,{this.props.name1 + " " + this.props.name2}</p>;
                },
                componentDidMount:function() {//成功render并渲染完成真实DOM之后触发，可以修改DOM
                    console.log('componentDidMount,5');
                },
                //运行中的函数
                componentWillReceiveProps:function(obj) {//父组件修改属性触发，可以修改新属性、修改状态
                    console.log("运行中：1");
                    console.log(obj);//obj为接收到修改了属性的对象，对象包含了修改后的属性在里边
                },
                shouldComponentUpdate: function() {//返回false会阻止render调用
                    console.log("运行中：2");
                    return true;
                },
                componentWillUpdate: function() {//不能修改属性和状态
                    console.log("运行中：3");
                },
                componentDidUpdate:function() {//可以修改DOM
                    console.log("运行中：5");
                    var newNode = document.createElement('span');
                    newNode.innerHTML = 'sups';
                    React.findDOMNode(this).appendChild(newNode);
                },
                //销毁阶段，即把我的这个组件移出后执行的回调函数
                componentWillUnmount: function() {//在删除组件之前进行清理操作，比如计时器和事件监听器
                    alert('销毁，我是子组件');
                }
            }
    );
    var HelloUniverse = React.createClass({
        getInitialState: function() {
            return {
                name1:"wen",
                name2:"yang"
            }
        },
        handleChange: function(event) {
            if(event.target.value === "123"){
                React.unmountComponentAtNode(document.body);//移除body里的所有组件
                return;
            }
            this.setState({
                name:event.target.value
            });
        },
        render: function () {
            return <div><HelloWorld  {...this.state}></HelloWorld>
             <br/>
             <input type="text" onChange={this.handleChange} />
            </div>
        },
        //销毁阶段，即把我的这个组件移出后执行的回调函数
        componentWillUnmount: function() {//在删除组件之前进行清理操作，比如计时器和事件监听器
            alert('销毁，我是父组件');
        }
    });
    //把组件渲染到页面中
    React.render(<div style={style}><HelloUniverse></HelloUniverse></div>, document.body);//把标签渲染到body标签中
</script>
</body>
</html>
```
更多例子可以到我的github上下载[react-test](https://github.com/wenyang12/react-test)


## 参考质料

[React中文站](http://www.reactjs-china.com/)

[极客学院ReactJS视频](http://www.jikexueyuan.com/course/reactjs/)