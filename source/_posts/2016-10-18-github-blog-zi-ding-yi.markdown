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

<!--more-->

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

## 设置博客中的链接在新窗口中打开

将如下代码添加到**occtopress/source/_includes/custom/head.html**文件末尾。

```javascript
<script type="text/javascript">
function addBlankTargetForLinks () {
  $('a[href^="http"]').each(function(){
      $(this).attr('target', '_blank');
  });
}
$(document).bind('DOMNodeInserted', function(event) {
  addBlankTargetForLinks();
});
</script>
```

## 自定义侧边栏

首先，在**octopress/source/_includes/asides/**目录下创建**custom.html**。

然后，在**_config.yml**的**default_asides:**设置中加入创建的自定义侧栏文件**/asides/custom.html**。

侧边栏顺序即为**default_asides:**中参数的顺序，可以自己设置。

## 增加Google Analytics统计工具

首先，在[Google Analytics](https://analytics.google.com/analytics/web/)注册并按要求填写网站信息；

然后，获取**Tracing Id**，添加到**_config.yml**的**google_analytics_tracking_id**后面即可。

![](http://ofj92itlz.bkt.clouddn.com/GoogleAnalytics:trackingID1.jpeg)

![](http://ofj92itlz.bkt.clouddn.com/GoogleAnalytics:config1.jpeg)

## 列表排版的缩进问题

列表默认会冲出文章主主体区块。

在**octopress/sass/custom/_layout.sccs**文件中找到**#$indented-lists: true**行，去掉**#**注释即可。

## 公益404页面

创建404.markdown，按如下编辑。

```
---

layout: page

title: "404 Error"

date: 2013-4-21 02:35

comments: false

sharing: false

footer: false

---

<script type="text/javascript" src="http://www.qq.com/404/search_children.js" charset="utf-8"></script>
```

## 博客末尾增加原文链接、版权等

参考博客[为octopress文章添加原文链接](http://www.cnblogs.com/maoxiong/p/4298207.html)

## 参考资料

[自定义你的Octopress博客](http://foggry.com/blog/2014/04/28/custom-your-octopress-blog/)

[定制Octopress](http://blog.csdn.net/biaobiaoqi/article/details/9289563)



