---
title: "修改Android签名密码-别名"
date: 2023-01-05T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "modify-android-keystore-alias"
draft: false
---

之前在测试Eclipse ADT的Custom debug keystore自定义调试证书的时候，发过一篇关于调试证书规格的博文 “Eclipse ADT的Custom debug keystore所需证书规格”:http://blog.k-res.net/archives/1229.html ，提到过自定义调试证书的密码和alias命名以及alias密码都是有规矩的。

其实Android应用开发接入各种SDK时会发现，有很多SDK是需要靠package name和keystore的指纹hash来识别的（百度地图SDK、Facebook SDK等等…），这样如果使用默认自动生成的debug keystore的话就会给开发调试工作带来一些麻烦。这时可以通过修改正式的release keystore，生成一份“遵守规矩”的临时自定义调试证书给开发时用，就方便多了。

具体方法如下：

1.首先当然是先复制一份正式证书出来作为要修改为的临时调试证书。

2.修改keystore密码的命令(keytool为JDK带的命令行工具)：

```shell
$ keytool -storepasswd -keystore my.keystore
```
其中，my.keystore是复制出来的证书文件，执行后会提示输入证书的当前密码，和新密码以及重复新密码确认。这一步需要将密码改为android。

3.修改keystore的alias：

```shell
$ keytool -changealias -keystore my.keystore -alias my_name -destalias androiddebugkey
```
这一步中，my_name是证书中当前的alias，-destalias指定的是要修改为的alias，这里按规矩来，改为androiddebugkey！这个命令会先后提示输入keystore的密码和当前alias的密码。

4.修改alias的密码：
```shell
$ keytool -keypasswd -keystore my.keystore -alias androiddebugkey
```
这一步执行后会提示输入keystore密码，alias密码，然后提示输入新的alias密码，同样，按规矩来，改为android！

以上几个操作执行后，my.keystore就是符合规矩的debug keystore了，接着在Eclipse的ADT设置中选中这个custom debug keystore即可.

同时还可以看到证书的MD5以及SHA1指纹。

PS：以上3步命令行并没有先后顺序。



参考文章：
* [Change alias passwords of your Android Keystore](http://blog.blundell-apps.com/tut-change-alias-passwords-of-your-android-keystore/#comment-2408)