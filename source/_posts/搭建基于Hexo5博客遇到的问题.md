---
title: 搭建基于Hexo5博客遇到的问题
date: 2021-06-15 10:56:26
tags: ["Hexo", "hexo-theme-next", "hexo init", "hexo-theme-next 图片无法加载"]
categories: Hexo
---

# 前言

早些时候使用Hexo 4成功搭建了个人博客。现在更换了电脑，生成博客的源代码也没有备份，只好重头开始。Hexo已经更新到5.4.0，与Hexo 4有些不同，所以开篇博客记录在安装时遇到的问题(本文的操作平台是Windows 10环境)。

# hexo init

与Hexo 4不同的是，使用Hexo 5就倒在了第一步：初始化失败。报错内容大致如下：

````javascript
INFO  Cloning hexo-starter https://github.com/hexojs/hexo-starter.git
fatal: unable to access 'https://github.com/xxx': OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443
WARN  git clone failed. Copying data instead
FATAL {
  err: [Error: ENOENT: no such file or directory, scandir 'C:\Users\xxx\AppData\Roaming\npm\node_modules\hexo-cli\assets'] {
    errno: -4058,
    code: 'ENOENT',
    syscall: 'scandir',
    path: 'C:\\Users\\xxx\\AppData\\Roaming\\npm\\node_modules\\hexo-cli\\assets'
  }
} Something's wrong. Maybe you can find the solution here: %s http://hexo.io/docs/troubleshooting.html
````

在搜索解决方法时，有些答案认为是因为github被墙或者是git设置了http代理所导致，对于前者我试着挂好了梯子(github、google均能正常使用)初始化仍然失败；接着检查了http代理也不存在问题。

最后的解决办法是：替换github链接。首先在你的电脑找到init.js文件，大致路径：C:\Users\xxx\AppData\Roaming\npm\node_modules\hexo-cli\lib\console\init.js，搜索GIT_REPO_URL字段后做如下替换：

<!-- more -->

````javascript
//原链接
const GIT_REPO_URL = 'https://github.com/hexojs/hexo-starter.git';
//替换后的链接
const GIT_REPO_URL = 'https://github.com.cnpmjs.org/hexojs/hexo-starter.git';
````

重新输入命令hexo init 初始化成功。

# hexo-theme-next 主题

[hexo-theme-next](https://github.com/iissnan/hexo-theme-next) 是Hexo一个非常受欢迎的极简风格主题。在成功搭建Hexo博客后使用该主题，然而却出现了以下问题：

- 博客主页乱码

  打开个人博客主页后，出现如下乱码，无法正常显示

  ````javascript
  {% extends '_layout.swig' %} {% import '_macro/post.swig' as post_template %}
  ````

  原因是因为在 Hexo 5.0 之后删除了依赖 hexo-renderer-swig，解决方法：

  ````javascript
  npm i hexo-renderer-swig
  ````

  

- 图片无法正常显示

  查看以前写的博客发现，图片均无法加载。打开控制台，显示请求图片文件返回404。猜测 Hexo 5 引入图片方式发生变化，随后按照如下方式引入图片：

  ````javascript
  {% asset_img example.jpg avatar %}
  ````

  更改之后，请求图片文件依然返回404。仔细查看请求发现请求路径中多了：/.com//    ，解决方法如下：

  1.安装 hexo-asset-image 插件

  ````javascript
  npm i hexo-asset-image
  ````

   2.把当前目录下的  /node_modules/hexo-asset-image/index.js 文件中的代码替换成如下代码：

  ````javascript
  'use strict';
  var cheerio = require('cheerio');
  
  function getPosition(str, m, i) {
  return str.split(m, i).join(m).length;
  }
  
  var version = String(hexo.version).split('.');
  hexo.extend.filter.register('after_post_render', function(data){
  var config = hexo.config;
  if(config.post_asset_folder){    
    var link = data.permalink;
    if(version.length > 0 && Number(version[0]) == 3)
       var beginPos = getPosition(link, '/', 1) + 1;
    else
       var beginPos = getPosition(link, '/', 3) + 1;
    // In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
    var endPos = link.lastIndexOf('/') + 1;
    link = link.substring(beginPos, endPos);
    
    var toprocess = ['excerpt', 'more', 'content'];
    for(var i = 0; i < toprocess.length; i++){
      var key = toprocess[i];
    
      var $ = cheerio.load(data[key], {
        ignoreWhitespace: false,
        xmlMode: false,
        lowerCaseTags: false,
        decodeEntities: false
      });
    
      $('img').each(function(){
        if ($(this).attr('src')){
            // For windows style path, we replace '\' to '/'.
            var src = $(this).attr('src').replace('\\', '/');
            if(!/http[s]*.*|\/\/.*/.test(src) &&
               !/^\s*\//.test(src)) {
              // For "about" page, the first part of "src" can't be removed.
              // In addition, to support multi-level local directory.
              var linkArray = link.split('/').filter(function(elem){
                return elem != '';
              });
              var srcArray = src.split('/').filter(function(elem){
                return elem != '' && elem != '.';
              });
              if(srcArray.length > 1)
                srcArray.shift();
              src = srcArray.join('/');
              $(this).attr('src', config.root + link + src);
              console.info&&console.info("update link as:-->"+config.root + link + src);
            }
        }else{
            console.info&&console.info("no src attr, skipped...");
            console.info&&console.info($(this));
        }
      });
      data[key] = $.html();
        }
      }
    });
  ````

  

- 翻页按钮不正常显示

  博客页面底部翻页页码 1 2 后 显示 ：

  ````html
  <i class="fa fa-angle-right"></i>
  ````

  解决方法：把当前目录下的 \themes\next\layout\_partials\pagination.swig  文件中的代码替换成如下代码

  ````javascript
  {% if page.prev or page.next %}
    <nav class="pagination">
      {{
        paginator({
          prev_text: '<',
          next_text: '>',
          mid_size: 1
        })
      }}
    </nav>
  {% endif %}
  ````

  

