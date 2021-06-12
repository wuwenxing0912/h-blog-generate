---
title: Cookie、Session和LocalStorage
date: 2018-06-30 23:29:11
tags: ["Cookie", "Session", "LocalStorage"]
categories: JavaScript
---
# 一、Cookie
Cookie是服务器发送给客户端（浏览器）一些信息，浏览器在之后的请求都会带上这些信息。

Cookie主要用于以下三个方面：
1、会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）；
2、个性化设置（如用户自定义设置、主题等）；
3、浏览器行为跟踪（如跟踪分析用户行为等）。

服务器使用Set-Cookie响应头部向浏览器发送Cookie信息。语法如下：
<!-- more -->
    ````
    Set-Cookie: <cookie名>=<cookie值>
    ````
可以使用Set-Cookie设置某些在浏览器发出请求时所要带上的信息：
    ````
    setHeader('Set-Cookie', `username=${username}`)
    ````
上述代码表示在浏览发出请求时，HTTP请求头就会带上用户的用户名。
此外，还可以设置Cookie的有效期：
    ````
    setHeader('Set-Cookie', 'Max_Age=60')
    ````
上述代码表示Cookie在60秒之后将会过期失效。

document.cookie能够读写当前页面的Cookie。
    ````
    document.cookie//username=xxx
    document.cookie = 'username=yyy'
    ````
HttpOnly属性指定该Cookie无法通过JavaScript代码获取到
    ````
    Set-Cookie: <cookie-name>=<cookie-value>; HttpOnly
    ````

# 二、Session
Session一般是基于Cookie来实现的。首先服务器通过Cookie把SessionId发送给浏览器，
这个SessionId是唯一的，也是区分用户的标识。之后浏览器的每次请求都要带上SessionId，这样服务器就能辨别用户。
Session是在服务器保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中。
    ````
    var sessionID = Math.random() * 10000
    response.setHeader('Set-Cookie', `sessionID=${sessionID}`)
    ````
上述代码通过随机数给用户分配一个SessionId。

# 三、LocalStorage
LocalStorage保存的数据长期存在（除非用户清理缓存），下一次访问该网站的时候，网页可以直接读取以前保存的数据。
LocalStorage与HTTP协议无关，在浏览器发送请求时，请求头不会带上LocalStorage的数据。

Storage接口提供了setItem()方法用来存入数据，该方法接受两个参数，第一个是键名，第二个是保存的数据。
    ````
    window.localStorage.setItem('name', 'BOb');
    ````

Storage接口还提供了getItem()方法用来存入数据，该方法只接受一个参数，参数就是键名。
    ````
    window.localStorage.getItem('name');//'BOb'
    ````
上述代码通过键名读取到了之前存入的数据，如果键名不存在则返回null。

removeItem()方法能够删除键名对应的键值，该方法只接受一个参数，参数就是键名。
    ````
    window.localStorage.removeItem('name');
    window.localStorage.getItem('name');//null
    ````
上述代码通过removeItem()方法删除对应的键值后，再使用getItem()方法得到的是null。

在某个网页存入的数据，只有相同域名的页面才能读取到。