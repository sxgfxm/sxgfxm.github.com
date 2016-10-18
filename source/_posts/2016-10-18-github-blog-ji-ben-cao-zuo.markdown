---
layout: post
title: "Github Blog - 基本操作"
date: 2016-10-18 08:58:48 +0800
comments: true
categories: octopress
---
##创建博文  
`rake new_post["article_name"]`

默认会在octopress/source/_post/目录下生成.markdown文件。  

## 删除博文

只需删除对应的markdown文件即可。

## 预览博文

`rake generate `  

默认会在octopress/public/blog/目录下生成**HTML**页面。

`rake preview`  

本地预览，地址为 http://localhost:4000 ，必须使用`ctrl+c`终止预览。  

## 发布博文

`rake deploy`  

发布博文  

## push至Github

```
git add .
git commit -m "commit message"
git push origin source
```