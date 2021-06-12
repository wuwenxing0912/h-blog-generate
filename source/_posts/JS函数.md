---
title: JS函数
date: 2018-05-03 14:19:16
tags: function
categories: JavaScript
---
# 1、函数概念
    函数是一组执行任务或计算值得语句。

# 2、函数声明
JS中有两种常用的声明函数的方法
## (1)function命令
    ````
    funtion square(a){
        return a*a;
    }
    ````
## (2)函数表达式
<!-- more -->
    ````
    var square = funtion(a){
        return a*a;
    };
    ````
此函数声明的方式不需要带有函数名，如果加上函数名，函数名只在函数体内部有效，在函数体外部无效。且此种声明方式需要在花括号后加上分号。
# 3、函数名提升
浏览器把函数名看作成为变量名，因此在采用function命令声明函数时，函数会和变量声明一样提升到代码头部。
    ````
    f();
    function f(){}
    ````
上述代码不会报错，上述等同于下方
    ````
    function f(){}
    f()
    ````
但是使用函数表达式声明函数时却会报错
    ````
    f();
    var f = function(){}
    ````
上面代码等同于
    ````
    var f;
    f();
    f = function(){}
    ````
上述代码调用f时，f只是被声明却没有赋值（undefined）。
# 4、函数的属性与方法
## (1)、name属性
name属性用以返回函数的名字，如：
    ````
    function add(){}
    add.name//得到函数名"add"
    ````
对于使用函数表达式声明函数返回的是变量名,如:
    ````
    var add = function(){};
    add.name//得到变量名"add"
    ````
## (2)、length属性
length属性用于返回函数的参数个数，如：
    ````
    function add(x,y){}
    add.length//值为2
    ````
## (3)、toString()方法
toString方法返回的内容是整个函数的源代码,如：
    ````
    function add(x,y){
        return x+y
    }
    add.toString()
    ````
    得到
    ````
    function f(a,b){
     return a+b
    }
    ````
