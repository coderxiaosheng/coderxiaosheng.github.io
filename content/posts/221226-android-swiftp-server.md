---
title: "Android开发-Swiftp服务端移植"
date: 2022-12-26T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "android-swiftp-server"
draft: false
---

swiftp是一个开源的ftp server，源码可以从下面地址拉。
* [Google code](http://code.google.com/p/swiftp/downloads/list)
* [GitHub](https://github.com/ppareit/swiftp)

自己的需求是将ftp server移植到自己的app中，使用USB数据线管理手机上的文件。其实google code链接源码中已经包含了一个完整的android app（github链接的源码貌似有点不同）。

这里只是记录一下调试的过程，将源码import成android project之后运行，有以下几个注意的地方：

1.源码default.java中

public static final boolean release = true;//可以改成false打开log的开关，方便调试。

2.遇到“550 Invalid name or chroot violation”（可以从源码中找到打这个log的代码行）的错误，那一定是配置页面中sd卡的路径不正确。

![](https://user-images.githubusercontent.com/136188064/263538023-44842322-bc07-42c0-8733-030bfbdc9fc5.png)

对应的代码是在CmdCWD.java文件中，再去分析为什么不对。提醒一点sd卡的根路径有/mnt/sdcard和/storage/sdcard0，跟android版本有关系。
```java
/ Ensure the new path does not violate the chroot restriction
if(violatesChroot(newDir)) {
errString = "550 Invalid name or chroot violation\r\n";
sessionThread.writeString(errString);
myLog.l(Log.INFO, errString);
break mainblock;
}
```

3.在PC上使用的FileZilla这个ftp client测试的，测试连接成功但是无法进行上传删除等操作（提示类似STOR error: 451 Couldn’t open file “test.txt” aka 错误log）。

原因是源码manifest.xml中没有设sd卡的读写权限。
```xml
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
4.上面是测试在同一个wifi网络下，无线ftp可以连接成功。那通过USB的ftp能成功么？

自己调试了一下，也是可以的，不过有以下地方要注意或者要修改的：

![](https://user-images.githubusercontent.com/136188064/263538024-a2ed2188-2299-4ca1-968b-609a7e919dc5.png)

4.1USB进行ftp连接，那在client填写的地址是回环地址（就像pc和android用socket连接那样，用127.0.0.1）

4.2ftp无非也是基于HTTP，底层socket连接的，那pc端必须要执行adb forward tcp: tcp: 来进行端口转换，因为ftp协议有两个端口，上图中的2121是控制端口，那还有数据传输端口？这个是在源码中动态获取的，在NormalDataSocketFactory.java中的server = new ServerSocket(0, Defaults.tcpConnectionBacklog);这句话。

```java
public int onPasv() {
    clearState();
    try {
        // Listen on any port (port parameter 0)
        server = new ServerSocket(0, Defaults.tcpConnectionBacklog);
        myLog.l(Log.DEBUG, "Data socket pasv() listen successful");
        return server.getLocalPort();
    } catch(IOException e) {
        myLog.l(Log.ERROR, "Data socket creation error");
        clearState();
        return 0;
    }
}
```

自己是暂时改成了server = new ServerSocket(2221, Defaults.tcpConnectionBacklog);也就是2221这个固定端口。在ftp client连接之前，执行adb forward tcp:2121 tcp:2121和adb forward tcp:2221 tcp:2221这两条命令。然后再连接（无线ftp和USB都可以）都是OK的了。

上面只是个人的一个记录，可能还会有一些问题，先记录一下。

参考资料
* [Android中FTP服务器、客户端搭建以及SwiFTP、ftp4j介绍](http://blog.csdn.net/qinjuning/article/details/8545601)

