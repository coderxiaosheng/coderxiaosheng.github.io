---
title: "Android2.3解析Json出错"
date: 2022-12-19T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "parser-json-error"
draft: false
---

在android2.3上json出现此问题，提示错误“JSONException: Value of type java.lang.String cannot be converted to JSONObject”，而4.0上没问题。
部分错误log如下：
```
1 12-18 20:23:27.389: W/System.err(12927): org.json.JSONException: Value  of type
java.lang.String cannot be converted to JSONObject
2 12-18 20:23:27.389: W/System.err(12927):     at org.json.JSON.typeMismatch(JSON.java:107)
3 12-18 20:23:27.389: W/System.err(12927):     at org.json.JSONObject.<init>(JSONObject.java:158)
4 12-18 20:23:27.389: W/System.err(12927):     at org.json.JSONObject.<init>(JSONObject.java:171)
```

原因是服务器返回的数据UTF-8带有BOM头，含有BOM头的数据会多出来三个字节 efbbbf。
这样在解析json的时候就会有问题，android4.0对此已经做了处理，所以没有报错。

```java
//android 2.3
public JSONTokener(String in) {
this.in = in;
}
//android 4.0
public JSONTokener(String in) {
// consume an optional byte order mark (BOM) if it exists
if (in != null && in.startsWith("\ufeff")) {
in = in.substring(1);
}
this.in = in;
}
```

参考资料
1. [JSONException: Value of type java.lang.String cannot be converted to JSONObject](http://hi.baidu.com/wuuubo/item/761f10134f771ef087ad4e93)
2. [UTF-8文件的BOM头的来由及去除方法](http://www.cnblogs.com/lfire/archive/2012/11/20/2778939.html)
3. [Android 由于BOM头导致的Json解析出错](http://blog.sina.com.cn/s/blog_783ede0301016zdb.html)

