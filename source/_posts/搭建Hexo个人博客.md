---
title: 搭建Hexo个人博客
date: 2018-05-03 14:12:41
tags:
---
一、搭建前准备工作
1、git安装；
2、node.js安装；
3、GitHub账户注册与配置；
4、创建一个新仓库（wuwenxing0912.github.io）--不需要输入“...or create a new repository on the command line”下的命令；
5、生成SSH key远程连接GitHub仓库。
二、使用Hexo搭建博客
1、使用命令 'npm install -g hexo-cli'，安装 Hexo；
2、初始化目录myBlog 'hexo init myBlog'，安装模块 'npm i';
3、新建md文件编写个人博客 'hexo new 开博大吉'；
4、修改博客网站配置  'start _config.yml'，将Hexo与新建GitHub仓库（wuwenxing0912.github,io） 关联起来；
5、安装git部署插件，'npm install hexo-deployer-git --save'；
6、加载配置 'hexo deploy'；
7、进入wuwenxing0912.github.io仓库的settings，打开 GitHub Pages 功能，点击链接进入个人博客；
三、搭建环节出现的问题
1、在创建新仓库之后输入了“...or create a new repository on the command line”下的命令初始化仓库导致搭建失败；
2、配置文件_config.yml中的最后两行type：以及repo:后未加空格导致搭建失败。
