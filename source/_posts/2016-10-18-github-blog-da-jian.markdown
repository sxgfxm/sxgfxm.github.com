---
layout: post
title: "Github Blog - 搭建"
date: 2016-10-18 08:44:43 +0800
comments: true
categories: Octopress
keywords: octopress，搭建octopress博客
description: 搭建octopress博客
---

## 安装Git

## 安装Ruby

推荐使用RVM。

<!--more-->

## 安装DevKit

```
gem install devkit
```

## 克隆Octopress库

```
git clone git://github.com/imathis/octopress.git 
```

## 安装Octopress依赖项

```
gem install bundler
bundler install
```

## 安装Octopress默认主题

```
rake install
```

## 关联Octopress至Github

```
rake setup_github_pages
```

在Repository url中输入对应的仓库地址。