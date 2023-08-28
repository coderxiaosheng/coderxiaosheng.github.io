---
title: "Java注解相关"
date: 2023-01-12T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "android-annotation"
draft: false
---

## 编译时注解

```java
package com.shansun.seri.json;  
public class JsonSerialize implements Serialize {
    ...
    public static class SerializerUnit4Map {
    }
    ...
}
String canonicalName = SerializerUnit4Map.class.getCanonicalName();  
System.out.println(canonicalName);  
String name = SerializerUnit4Map.class.getName();  
System.out.println(name);  
String simpleName = SerializerUnit4Map.class.getSimpleName();  
System.out.println(simpleName);  
输出为
com.shansun.seri.json.JsonSerialize.SerializerUnit4Map  
com.shansun.seri.json.JsonSerialize$SerializerUnit4Map  
SerializerUnit4Map
```

## 运行时注解
[Android 进阶 教你打造 Android 中的 IOC 框架 【ViewInject】 （上）](http://blog.csdn.net/lmj623565791/article/details/39269193) ， [下](http://blog.csdn.net/lmj623565791/article/details/39275847)


参考文章：
1. [Java注解处理器](http://www.race604.com/annotation-processing/) ，[相关代码](https://github.com/sockeqwe/annotationprocessing101/tree/master/factory)
2. [AndroidSutdio-编译时自动生成源代码](http://www.septenary.cn/2015/12/19/AndroidSutdio-%E7%BC%96%E8%AF%91%E6%97%B6%E8%87%AA%E5%8A%A8%E7%94%9F%E6%88%90%E6%BA%90%E4%BB%A3%E7%A0%81/) ，[相关代码](https://github.com/Ryfthink/AnnotationProcessor)
3. [android-develper](https://developer.android.com/studio/write/annotations.html)
4. [公共技术点之 Java 注解 Annotation](http://a.codekk.com/detail/Android/Trinea/%E5%85%AC%E5%85%B1%E6%8A%80%E6%9C%AF%E7%82%B9%E4%B9%8B%20Java%20%E6%B3%A8%E8%A7%A3%20Annotation)
5. [AndroidButterKnifeSample](https://github.com/mengdd/AndroidButterKnifeSample)
6. [是否有必要在Android项目中使用IOC框架](http://www.tuicool.com/articles/3A7vQ3)






