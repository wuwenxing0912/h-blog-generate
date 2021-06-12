---
title: 判断JS数据类型的方法
date: 2018-08-10 00:25:37
tags: ["typeof", "instanceof", "toString"]
categories: JavaScript
---

在 ECMAScript 规范中，一共有7种数据类型，分别为：
Number、String、Boolean、Null、Undefined、Symbol、Object。
其中Number、String、Boolean、Null、Undefined、Symbol为基本类型，Object为引用类型或称复杂类型。

# 1、typeof
typeof 是一个操作符，右侧跟一个一元表达式，并返回这个表达式的数据类型。返回的结果用该类型的字符串(全小写字母)形式表示。如下:
<!-- more -->
    ````
    typeof 123 //"number"
    typeof '123' //"string"
    typeof true //"boolean"
    typeof undefined //"undefined"
    typeof Symbol('123') //"symbol"
    typeof {} //"object"
    typeof function f(){} //"function"
    typeof new Array() //"object"
    typeof new Date() //"object"
    ````
所以，typeof的返回值有7种分别是：number、boolean、symbol、string、object、undefined、function 。
需要注意的是null返回的是"object"，函数返回的是"function",其余均符合预期结果。

# 2、instanceof
instanceof运算符返回一个布尔值(true/false)，表示对象是否为某个构造函数的实例。
    ````
    function A(){}
    var a = new A()
    a instanceof A //true
    ````

    ````
    "str" instanceof String //false 字符串不是String对象的实例（因为字符串不是对象），//所以返回false。
    ````
需要注意的是instanceof运算符只能用于对象，不适用原始类型的值。
此外instanceof检查整个原型链，因此同一个实例对象，可能会对多个构造函数都返回true。如：
    ````
    [] instanceof Array // true
    [] instanceof Object //true
    ````
上述情况成立的原因是：[].__proto__.__proto__ === Object.prototype //true。
其中[].__proto__ === Array.prototype //true,
Array.prototype.__proto__ === Object.prototype//true

# 3、toString方法
toString() 是 Object 的原型方法，这是一个内部属性，其格式为 [object Xxx] ，其中 Xxx 就是对象的类型。
对于 Object 对象，直接调用 toString()  就能返回 [object Object] 。而对于其他对象，则需要通过 call / apply 来调用才能返回正确的类型信息。
    ````
    Object.prototype.toString.call(123) //"[object Number]"
    Object.prototype.toString.call('123') //"[object String]"
    Object.prototype.toString.call(true) //"[object Boolean]"
    Object.prototype.toString.call(null) //"[object Null]"
    Object.prototype.toString.call(undefined) //"[object Undefined]"
    Object.prototype.toString.call(Symbol('123')) //"[object Symbol]"
    Object.prototype.toString.call({}) //"[object Object]"
    Object.prototype.toString.call([]) //"[object Array]"
    Object.prototype.toString.call(function f(){}) //"[object Function]"
    Object.prototype.toString.call(new Date()) //"[object Date]"
    Object.prototype.toString.call(new RegExp()) //"[object RegExp]"
    ````
由此可见使用Object.prototype.toString.call()方法可返回正确的类型。
在实际中可使用slice()方法截取所需要的值：
    ````
    Object.prototype.toString.call(function f(){}).slice(8,-1) //"Function"
    Object.prototype.toString.call([]).slice(8,-1) // "Array"
    Object.prototype.toString.call(true).slice(8,-1) //"Boolean"
    ````  
