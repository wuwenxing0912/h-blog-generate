---
title: REM
date: 2018-07-12 00:47:46
tags: rem
categories: CSS
---

# 一、REM是什么
REM是（font size of the root element），意思是网页的根元素的字体大小。

# 二、使用REM适配屏幕
## (1)可以把REM作为基准，设定元素的宽高。
### 1、设定根元素的font-size的大小：
<!-- more -->
    ````
    html{
        font-size:40px;
    }
    ````
### 2、在页面上加入几个元素：
    ````
    <div class="wrapper">
        <div class="one">1</div>
        <div class="two">2</div>
        <div class="three">3</div>
        <div class="four">4</div>
    </div>
    ````
### 3、布局：
    ````
    .wrapper{
        display:flex;
        flex-wrap:wrap;
    }
    ````
### 4、元素大小：
    ````
    .wrapper>div{
        width:4rem;
        height:4rem;
        margin:0.5rem;
    }
    ````

{% asset_img rem-1.jpg %}

## （2）由于移动端屏幕大小不一，所以可以使用动态REM来适配屏幕。
### 1、通过JS获取屏幕的大小，从而决定REM的基准值：
    ````
    document.getElementsByTagName('html')[0].style.fontSize = window.innerWidth / 10 + 'px';
    ````
### 2、布局与元素大小：
html部分代码：
    ````
    <div class="wrapper">
        <div class="one">1</div>
        <div class="two">2</div>
        <div class="three">3</div>
        <div class="four">4</div>
    </div>
    ````
CSS部分代码：
    ````
    .wrapper{
        display:flex;
        flex-wrap:wrap;
    }
    ````
    ````
    .wrapper>div{
        width:4rem;
        height:4rem;
        margin:0.5rem;
    }
    ````

{% asset_img rem-2.jpg %}
上图为手机端的展示图。

# 三、对于使用sass的工程
## 1、首先安装node-sass
## 2、监听scss文件的改动：
    ````
    node-sass style.scss style.css -w
    ````
这样每当修改style.scss文件且保存后，都会同步到style.css中。
## 3、每次在规定元素的宽度和高度时，都需要计算像素转换成对应REM，如此有点不方便，所以可以在scss文件中加入一个转换的函数：
    ````
    $rem: 320px;
    @function px2rem($px) {
        @return $px / $rem*10+rem;
    }
    ````
这样在书写css代码时就不需要计算宽高对应多少REM，直接写像素就可以：
    ````
    .wrapper>div{
        width: px2rem(128px);
        height: px2rem(128px);
        margin: px2rem(16px);
    }
    ````
上述代码和之前的效果是一致的。
{% asset_img rem-3.jpg %}