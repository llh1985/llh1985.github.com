---
layout: post
title: 22端口被封被封导致无法ssh到github问题
categories: Other
description: 测试jekyll博文
keywords: hello, word, 第一, jekyll
---

###问题描述

在公司内外环境，发现连github去clone东西搞不下来
错误提示上ssl相关问题，发现时公司封了端口

###解决办法

在.ssh目录下新建一个名为config(没有后缀名)的文件，内容如下：
Host github.com  
User XXX@XXX.com  
Port 443  
Hostname ssh.github.com


