---
layout: post
title: "Github Blog - 基本操作"
date: 2016-10-18 08:58:48 +0800
comments: true
categories: Octopress
keywords: octopress，octopress基本操作
description: octopress创建博客

---
##创建博文  
`rake new_post["article_name"]`

默认会在octopress/source/_post/目录下生成.markdown文件。  

## 删除博文

只需删除对应的markdown文件即可。

<!--more-->

## 预览博文

`rake generate `  

默认会在octopress/public/blog/目录下生成**HTML**页面。

`rake preview`  

本地预览，地址为 http://localhost:4000 ，必须使用`ctrl+c`终止预览，`ctrl+z`只起到挂起作用，当再次预览时会提示socket端口被占用。  

## 发布博文

`rake deploy`  

先将本地博客存放至octopress/_deploy目录下，然后push至github上远程仓库的**master**分支。

可以在_deploy目录下执行`git reset --hard origin master`强制同步远程仓库master分支内容，已解决不能push的问题。  

## push至Github

手动push源代码至github上远程仓库的**source**分支。

```
git add .
git commit -m "commit message"
git push origin source
```