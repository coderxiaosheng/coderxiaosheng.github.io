---
title: "Android网络库"
date: 2023-01-11T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "android-github-network"
draft: false
---

## android-async-http
项目地址：[android-async-http](https://github.com/loopj/android-async-http)

最主要的类：
```java
AsyncHttpResponseHandler
AsyncHttpClient
AsyncHttpRequest
```

类继承关系图：
![](https://user-images.githubusercontent.com/136188064/263567735-63d9b725-996e-4aaa-ba30-258ad0bbd6e1.png)
流程图
![](https://user-images.githubusercontent.com/136188064/263567740-d6f17367-6c08-49cd-83d3-63b66c35c422.png)

使用比较简单：
```java
syncHttpClient client = new AsyncHttpClient();
client.get("https://www.google.com", new AsyncHttpResponseHandler() {
    @Override
    public void onStart() {
    // called before request is started
    }
    @Override
    public void onSuccess(int statusCode, Header[] headers, byte[] response) {
    // called when response HTTP status is "200 OK"
    }
    @Override
    public void onFailure(int statusCode, Header[] headers, byte[] errorResponse, Throwable e) {
    // called when response HTTP status is "4XX" (eg. 401, 403, 404)
    }
    @Override
    public void onRetry(int retryNo) {
    // called when request is retried
    }
});
```

建议使用静态单例:
```java
import com.loopj.android.http.*;
public class TwitterRestClient {
  private static final String BASE_URL = "https://api.twitter.com/1/";
  private static AsyncHttpClient client = new AsyncHttpClient();
  public static void get(String url, RequestParams params, AsyncHttpResponseHandler responseHandler) {
      client.get(getAbsoluteUrl(url), params, responseHandler);
  }
  public static void post(String url, RequestParams params, AsyncHttpResponseHandler responseHandler) {
      client.post(getAbsoluteUrl(url), params, responseHandler);
  }
  private static String getAbsoluteUrl(String relativeUrl) {
      return BASE_URL + relativeUrl;
  }
}
// 使用
import org.json.*;
import com.loopj.android.http.*;
class TwitterRestClientUsage {
    public void getPublicTimeline() throws JSONException {
        TwitterRestClient.get("statuses/public_timeline.json", null, new JsonHttpResponseHandler() {
            @Override
            public void onSuccess(int statusCode, Header[] headers, JSONObject response) {
                // If the response is JSONObject instead of expected JSONArray
            }
            
            @Override
            public void onSuccess(int statusCode, Header[] headers, JSONArray timeline) {
                // Pull out the first event on the public timeline
                JSONObject firstEvent = timeline.get(0);
                String tweetText = firstEvent.getString("text");
                // Do something with the response
                System.out.println(tweetText);
            }
        });
    }
}
```

参考文档
[官网](http://loopj.com/android-async-http/)
[android-async-http框架库使用基础](http://blog.csdn.net/yanbober/article/details/45307549)

## square/okhttp
项目地址
[square/okhttp](https://github.com/square/okhttp)

参考文档
[官网](http://square.github.io/okhttp/)
[wiki](https://github.com/square/okhttp/wiki)

## square/retrofit
项目地址
[square/retrofit](https://github.com/square/retrofit)

## ThinkAndroid
项目地址：[ThinkAndroid](https://github.com/white-cat/ThinkAndroid)

参考文章：
1. [Android Universal Image Loader 源码分析-codekk](http://a.codekk.com/detail/Android/huxian99/Android%20Universal%20Image%20Loader%20%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90)
2. [Volley 源码解析-codekk](http://a.codekk.com/detail/Android/grumoon/Volley%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90)
3. [Android Volley完全解析(一)，初识Volley的基本用法-guolin](http://blog.csdn.net/guolin_blog/article/details/17482095/)
4. [Android 三大图片缓存原理、特性对比-Trnea](http://www.trinea.cn/android/android-image-cache-compare/)

