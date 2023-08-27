---
title: "Svn代码库迁移到Git"
date: 2022-12-18T08:35:08+08:00
categories: ["开发"]
tags: ["git"]
slug: "change-svn-to-git"
draft: false
---

说明：根据SVN库地址，拉取数据生成一个git本地代码库。用于svn迁移数据到git。

GIT客户端与SVN服务器

下面是必须要安装的几个软件：

Git-1.7.4, 这个是GIT客户端，可以在GIT的官方网站上下载。

SVN客户端，只有安装了SVN客户端才能利用Tortoisegit访问SVN服务端，如果不装那就只能访问GIT的服务器了。

Tortoisegit , 这个工具为Windows用户提供了良好的GIT操作界面，而且如果习惯了使用SVN的人几乎没有难度。

这几个软件的关系是：Tortoisegit提供了良好的界面操作GIT，底层实际使用Git-1.7.4客户端完成所有GIT功能，并且Tortoisegit依赖SVN客户端来提供将本地GIT管理的数据提交到SVN服务器上的功能。

最好按照上面列出的顺序依次安装，否则需要手动配置一些路径。

这个时候可以在Windows的右键菜单中发现Git Clone这个选项。通过这个选项就可以把SVN服务器上的数据下载到本地，并由GIT客户端进行管理。

![](https://user-images.githubusercontent.com/136188064/263536761-878fe31d-84ca-4fdf-bfc9-f27b3ee699eb.png)

如上图，从SVN克隆数据必须勾选红框中的CheckBox，然后在填写分支名称，登陆用户名等信息。这个对话框上面一半是用于从GIT服务器上克隆已有项目的。

这样就可以按照一般GIT客户端的方式在本地管理代码，上传到远端服务器时需要在右键菜单中选择SVN Dcommit命令，就可以把代码提交到SVN服务器上了。

注解：填写Url，Directory，勾选上图的红框，不要勾选trunk，tags，branch，填写username，之后填写密码等，就可以了。

之后再拉取代码用菜单中的SVN fetch，svn rebase，提交代码用svn dcommit（没试过）。

参考资料：
1. [从SVN迁移到GIT](http://www.cnblogs.com/cppguru/archive/2011/06/22/2086996.html)
2. [SVN数据迁移到Git笔记](http://sfzhang88.blog.51cto.com/4995876/1198867)
3. [如何一起使用 Git 和 SVN](http://www.oschina.net/translate/howto_use_git_with_svn)