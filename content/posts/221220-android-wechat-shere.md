---
title: "Android开发-微信分享功能"
date: 2022-12-20T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "android-wechat-shere"
draft: false
---

在你的app应用里增加微信分享的功能，可以分享给好友、朋友圈。
首先，看官方文档这是必须的：
[微信Android终端SDK新手使用指南](http://open.weixin.qq.com/document/gettingstart/android/?lang=zh_CN)
官方文档已经介绍的很详细了，不过有一些地方，我们在开发的时候需要注意。说明以下两块：

微信SDK Sample Demo源码 build
Demo代码目录下有一个debug.keystore文件，在build的时候按照这个来签名，demo编出来的app才能成功分享到微信。
修改android默认签名文件见下图：windown->preferences
![](https://user-images.githubusercontent.com/136188064/263537256-e02b6b46-4b01-4133-85b8-9fd327196e7a.png)

我们自己开发

1） 申请AppId，注意申请提交的app要用你最终打包的签名。申请成功之后，app包名就不要变了，因为与appID是对应的。

2） 按照官方文档中，新建WXEntryActivity，注意其所在的包必须是”app包名”+.wxapi。其实这个实现了IWXAPIEventHandler 这个接口，是被微信那边来回调的。

请求微信分享的调用api.sendReq在其他Activity中都行，跟WXEntryActivity这个没有关系。

3） 打包测试的时候，用最终要打包的签名文件测试，这样才能分享成功。（packagename、打包签名的key、申请的appid这三者要对应，测试才能成功）

4） 在app中，我们往往都要知道是否分享成功了，微信已经提供了回调。

回调后是进入到WXEntryActivity这个activity了，所以在onResp()函数中添加this.finish来退出这个activity，返回到我们自己app的activity。
在WXEntryActivity的回调函数中，可以类似这么写。

```java
public void onResp(BaseResp resp) {
int result = 0;  
switch (resp.errCode) {  
case BaseResp.ErrCode.ERR_OK:  
result = R.string.errcode_success;  
break;  
case BaseResp.ErrCode.ERR_USER_CANCEL:  
result = R.string.errcode_cancel;  
break;  
case BaseResp.ErrCode.ERR_AUTH_DENIED:  
result = R.string.errcode_deny;  
break;  
default:  
result = R.string.errcode_unknown;  
break;  
}  
Toast.makeText(this, result, Toast.LENGTH_LONG).show();  
// TODO 微信分享 成功之后调用接口  
this.finish();  
}
```

另外，声明Activity的时候，把activity透明.

```xml
<activity
android:name=".wxapi.WXEntryActivity"
android:screenOrientation="portrait"
android:theme="@style/No_Background"
android:exported="true"
/>
```
参考文档：
1. [Android微信分享操作后在当前界面提示方案解决](http://androidmaster.iteye.com/blog/1887876)
2. [微信分享实例](http://www.eoeandroid.com/thread-297874-1-1.html)