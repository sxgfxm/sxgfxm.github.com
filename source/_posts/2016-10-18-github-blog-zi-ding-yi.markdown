---
layout: post
title: "Github Blog - 自定义"
date: 2016-10-18 15:48:51 +0800
comments: true
categories: Octopress

---

## Octopress目录结构

### Rakefile

rake的配置文件，类似于makefile。

### Gemfile

bundle要下载需要的gem依赖关系的指定文件。

### _config.yml

站点的配置文件。

### public/

`rake generate`静态编译完成后的目录，该目录为网站展示的目录。

### _deploy/

`rake deploy`deploy时生成的缓存文件夹，内容和public目录一样。

该目录中内容由Octopress自动提交至Github远程仓库的**master**分支。

### sass/

css文件的源文件目录。

### plugins/

放置自带及第三方插件目录，ruby程序。

### source/

站点的源文件目录，public目录就是根据该目录下数据生成的。

该目录中内容由用户手动提交至Github远程仓库的**source**分支。

#### 		_includes/custom/

自定义的模板目录。

#### 		_includes/asides/

边栏模板目录。

#### 		_includes/post/

文章页面相应模板目录。

#### 	_layouts/

默认网站html相关文件，最底层。

#### 	_posts/

文章源文件，由`rake new_post["article name"]`命令产生。

#### 	_stylesheets/

css文件目录。

#### 	javascripts/

js文件目录。

#### 	images/

图片目录。