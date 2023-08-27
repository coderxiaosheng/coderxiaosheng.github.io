---
title: "Android生成帮助文档"
date: 2023-01-06T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "android-doc-generate"
draft: false
---

### JavaDoc
在Android Studio中，可以使用JavaDoc生成帮助文档。使用也比较方便，菜单选择”Tool->Generate JavaDoc”。
![](https://user-images.githubusercontent.com/136188064/263539680-5436942b-a8d9-49d8-92d3-00171983ead5.png)

使用的时候，一般都要输出为utf-8的编码。在如图输入选项中添加”-encoding utf-8 -charset utf-8”。
此外，我们还可以使用更强大的DoxyGen工具，在帮助文档中增加类继承关系图、函数调用关系图等。

### DoxyGen
使用DoxyGen工具生成文档，有下面两种用法：
1.可以使用图形工具Doxywizard，图形化配置输出选项，然后运行即可。
2.也可以用命令行doxygen DoxyFile生成文档。

![](https://user-images.githubusercontent.com/136188064/263539736-ce9818c5-0db3-4eb4-b64a-ce26100768dd.png)

一般的，用DoxyGen生成文档，还要依赖于Graphviz图形生成工具（graphviz-2.38.msi）、微软的CHM编译器html help（htmlhelp.exe），可自行搜素安装。

Graphviz是一个图形可视化软件。Doxygen使用Graphviz生成各种图形，例如类的继承关系图、合作图，头文件包含关系图等。可以从Graphviz的网站下载Graphviz的最新版本。Doxygen使用了Graphviz的布局引擎dot，所以在文档中将其称作dot。

Doxygen在输出HTML文档时，可以自动准备用于制作CHM的项目文件（.hhp）、目录文件（.hhc）和索引文件（.hhk）。用HTML Help Workshop中的CHM编译器（hhc.exe）编译后生成CHM文件。

DoxyFile配置文件的部分选项如下：


```
// 输出目录
OUTPUT_DIRECTORY       = D:/Code_baidu/nuomi/merchant-android/doc
// 输入文件
INPUT                  = ../app/src/main/java
                         *.js \
                         *.cxx
// 是否能查看source
SOURCE_BROWSER         = YES
// 生成的html文件目录
HTML_OUTPUT            = html
// 强烈注意：如果要生成chm文档，这里必须是相对目录，而且是相对于HTML_OUTPUT目录。如果使用绝对目录，生成失败！
CHM_FILE               = ../merchant-android.chm
// 调用关系图
CALL_GRAPH             = YES
CALLER_GRAPH           = YES
```

其他更详细的选项配置可自行探索。

参考文章：
1. [我的参考配置文件](https://blog.tianmaosheng.cn/assert/2016-09-07-01/Doxyfile%E5%8F%82%E8%80%83)
2. [学习用 doxygen 生成源码文档](https://www.ibm.com/developerworks/cn/aix/library/au-learningdoxygen/)
3. [使用doxygen为C/C++程序生成中文文档](http://www.fmddlmyy.cn/text21.html)

