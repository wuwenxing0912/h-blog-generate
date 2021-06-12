---
title: MVC模式
date: 2018-06-21 15:36:33
tags: MVC
categories: JavaScript
---


# 一、什么是MVC
Model操作数据、View表示视图、Controller是控制器。
Model和服务器交互，Model将得到的数据交给Controller，Controller把数据填入View并监听View。
用户操作View，如点击按钮，Controller就会接受到点击事件，Controller这时会去调用Model，Model会与服务器交互，得到数据后返回给Controller，Controller得到数据就去更新View。

# 二、使用MVC制作一个留言板
<!-- more -->
html部分代码：
    ````
    <div id="formAndMessageList">
        <form id="myForm" class="basic-grey">
            <h1>留言板</h1>
            <label>
            <span>姓 名 :</span>
            <input id="name" type="text" name="name"/>
            </label>

            <label>
            <span>邮 箱 :</span>
            <input id="email" type="text" name="email"/>
            </label>

            <label>
            <span>留 言 :</span>
            <textarea id="message" name="message"></textarea>
            </label>

            <label>
            <span>&nbsp;</span>
            <input type="submit" class="button" value="提交"/>
            </label>
        </form>
        <ol id="messageList">
        </ol>
    </div>
    ````
界面效果展示：
{% asset_img 1.jpg 留言板 %}

在填写完相关信息后。点击提交按钮，留言即刻更新在留言展示区域。

JavaScript部分代码：
首先是View，包括信息填写区域和下方的留言展示区域：
    ````
    var view = document.querySelector('#formAndMessageList')
    ````

其次是Model，这里是Model与LeanCloud进行交互：
    ````
    var model = {
        init: function() {
            var APP_ID = 'wbGEKwjkKqYviJXXXXXXXXXXXXXX';
            var APP_KEY = 'QNwxXOzQmf0UIXXXXXXXXXXXXXX';
            AV.init({
                appId: APP_ID,
                appKey: APP_KEY
            })
        },
        read: function() {
            var query = new AV.Query('Test')
            return query.find()
        },
        save: function(nameContent, emailContent, messageContent) {
            var Test = AV.Object.extend('Test');
            var test = new Test();
            return test.save({
                name: nameContent,
                email: emailContent,
                message: messageContent
            })
        }
    }
    ````
在LeanCloud上新建了一个应用，同时初始化了应用的ID和应用的KEY，并且提供了与LeanCloud应用读写的方法。
最后是Controller部分代码：
    ````
    var controller = {
        view: null,
        model: null,
        messageList: null,
        myForm: null,
        init: function(view, model) {
            this.view = view
            this.model = model

            this.messageList = view.querySelector('#messageList')
            this.myForm = view.querySelector('#myForm')
            this.model.init()
            this.loadMessage()
            this.bindEvents()
        },
        loadMessage: function() {
            this.model.read().then((informations) => {
                var array = informations.map((item) => item.attributes)
                array.forEach((item) => {
                    var li = document.createElement('li')
                    li.innerText = `${item.name}(${item.email}):${item.message} `
                    messageList.appendChild(li)
                })
            })
        },
        bindEvents: function() {
            this.myForm.addEventListener('submit', (e) => {
                e.preventDefault();
                this.saveMessage()
            })
        },
        saveMessage: function() {
            var myForm = this.myForm
            var nameContent = myForm.querySelector('input[name=name]').value
            var emailContent = myForm.querySelector('input[name=email]').value
            var messageContent = myForm.querySelector('textarea').value

            this.model.save(nameContent, emailContent, messageContent)
                .then(function(object) {
                    var li = document.createElement('li')
                    li.innerText = `${object.attributes.name}:${object.attributes.message} ${object.attributes.email}`
                    messageList.appendChild(li)
                    myForm.querySelector('textarea').value = ''
                })
        }


    }
    ````

Controller部分的代码提供了一系列的逻辑处理，包括监听View中的点击事件、操作DOM（更新View）、
调用Model使用与服务器交互。
