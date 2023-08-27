---
title: "Github简单使用记录"
date: 2022-12-13T08:35:08+08:00
categories: ["开发"]
tags: ["git"]
slug: "how-github-use"
draft: false
---

git配置命令

```shell
git config --global user.name "name"
git config --global user.email "your.email@gmail.com"
git config --global push.default simple
git config --list
```

git远程库操作命令
```shell
//为本地库创建一个远程库，以--bare为参数，本地库 远程库
git clone --bare . ../remote-repository.git
//clone远程库代码，在当前目录拉去全程库的文件，远程库 本地目录
git clone ../remote-repository.git .
//本地库中创建对应远程库的别名
git remote add origin ../remote-repository.git
git remote//列出远程库
git remote -v//显示所有的远程仓库
git remote rename github gh//重命名远程仓库
git remote rm github//删除远程仓库
//从远程库拉去代码
git pull --rebase
//提交代码到远程库
git push ../remote-repository.git
git push origin
//打tag
git tag version1.6 -m 'version 1.6'
//解决冲突
git mergetool
```

git常用操作
```shell
//回退所有的修改（未提交到暂存区）&&删除所有修改（包括在ignore中的文件）
git checkout . && git clean -fdx
//如果有修改提交到暂存区，回退该修改
git reset --hard
git clean -fdx
// 移除缓存区的所有文件（撤销上次git add）:
git reset HEAD
```

github help
```shell
Global setup:
Set up git
git config --global user.name "Your Name"
git config --global user.email defnngj@gmail.com

Next steps:
mkdir hibernaet-demo2
cd hibernaet-demo2
git init
touch README
git add README
git commit -m 'first commit'
git remote add origin git@github.com:defnngj/hibernaet-demo2.git
git push -u origin master
Existing Git Repo?
cd existing_git_repo
git remote add origin git@github.com:defnngj/hibernaet-demo2.git
git push -u origin master
```

参考资料
1. [git/github初级运用自如](http://www.cnblogs.com/fnng/archive/2012/01/07/2315685.html)
2. [gitHub help](https://help.github.com/articles/set-up-git)
3. [Git 的origin和master分析](http://lishicongli.blog.163.com/blog/static/1468259020132125247302/)
4. [github简单使用教程](http://wuyuans.com/2012/05/github-simple-tutorial/)
5. [GitHub详细教程](http://blog.csdn.net/lishuo_os_ds/article/details/8078475)
6. [git remote](http://blog.csdn.net/pgmsoul/article/details/7883706)
7. [如何高效利用GitHub](http://www.yangzhiping.com/tech/github.html)
