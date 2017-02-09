# DoubleRay's Blog





Blog介绍
====================================

模板
------------------------------------

使用git从[LessOrMore](https://github.com/luoyan35714/LessOrMore.git)主页下载的模板

``` bash
git clone https://github.com/luoyan35714/LessOrMore.git
```

配置
------------------------------------

配置文件`_config.yml`，将文件内所有自定义选项修改。

> 特别感谢模板作者提醒，文件内`baseurl`的配置。如果是`***.github.io`项目，不修改为空''的话，会导致JS,CSS等静态资源无法找到的错误


优化及自定义
------------------------------------

+ header内添加canvas背景，移动端的渲染优化
+ 优化移动端图片重写宽度的js




如何写文章
------------------------------------

在`/_posts`目录下新建一个文件，可以创建文件夹并在文件夹中添加文件，方便维护。在新建文件中粘贴如下信息，并修改以下的`titile`,`date`,`categories`,`tag`的相关信息，添加`* content {:toc}`为目录相关信息，在进行正文书写前需要在目录和正文之间输入至少2行空行。然后按照正常的Markdown语法书写正文。



致谢
====================================

+ 感谢[Github](https://github.com/)提供的代码维护和发布平台
+ 感谢[Jekyll](https://jekyllrb.com/)团队做出如此优秀的产品
+ 感谢[Solar](https://github.com/mattvh/solar-theme-jekyll)的原作者[Matt Harzewski](http://www.webmaster-source.com/)，我的博客选用了此样式模版