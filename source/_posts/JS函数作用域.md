---
title: JS函数作用域
date: 2018-05-04 12:39:09
tags: function
categories: JavaScript
---
# 1、什么是作用域
作用域是变量存在的范围。根据ECMAScript5的规定，JS有两种作用域：全局作用域与函数作用域。全局作用域是指变量存在于整个程序中，任何地方都可以读取；函数作用域变量只存在于函数内部。
    ````
    var a = 2
    function f(){
    console.log(a)
    }
    f()
    ````
上述代码变量a定义在函数外部为全局变量，且函数f内部可以读取到全局变量a，输出值为2
<!-- more -->
    ````
    function f(){
    var a = 2 
    }
    console.log(a)
    ````
上述的输出结果为"ReferenceError: a is not defined"，这说明定义在函数内部的变量a是一个变量，函数外部无法访问。
# 2、函数内部同名变量覆盖
    ````
    var a = 1
    function f(){
    var a = 2 
    console.log(a)
    }

    f()

    console.log(a)
    ````
上述代码的输出结果依次为：2、1。这表明定义在函数内部的变量会在函数作用域内覆盖同名变量。
# 3、函数本身的作用域
函数有自己的作用域，函数本身的作用域是声明时所在的作用域，和函数运行时所在的作用域无关。
    ````
    var a = 1
    function f2(){
    var a = 2
    f1()
    }

    function f1(){
    console.log(a)
    }

    f2()
    ````
上述代码的输出为1，函数f1是在函数f2的外部定义的，因此函数f1的作用域为外部而不是函数f2内部，所以变量a的值取全局变量"var a = 1"而不是局部变量"var a = 2"。
同理，在函数内部声明的函数，它的作用域绑定函数内部
    ````
    var a = 2
    function f2(){
    var a = 1
      function f1(){
      console.log(a)
      }
    return f1
    }
    var f = f2()
    f()
    ````
上述代码输出为1，函数f1在函数f2中声明，因此函数f1的作用域绑定函数f2，所以变量a的取值是函数f2内部的"var a = 1"，而不是函数f2外部的"var a = 2"。
所以，函数执行时所在的作用域，是定义时的作用域，而不是调用时所在的作用域

