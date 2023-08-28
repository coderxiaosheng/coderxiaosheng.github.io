---
title: "Android图片加载库-glide-picasso-fresco"
date: 2023-01-10T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "android-github-glide-picasso-fresco"
draft: false
---

## glide(未研究)
项目地址：[glide](https://github.com/bumptech/glide)

```java
// For a simple view:
@Override public void onCreate(Bundle savedInstanceState) {
  ...
  ImageView imageView = (ImageView) findViewById(R.id.my_image_view);
  Glide.with(this).load("http://goo.gl/gEgYUd").into(imageView);
}
// For a simple image list:
@Override public View getView(int position, View recycled, ViewGroup container) {
  final ImageView myImageView;
  if (recycled == null) {
    myImageView = (ImageView) inflater.inflate(R.layout.my_image_view, container, false);
  } else {
    myImageView = (ImageView) recycled;
  }
  String url = myUrls.get(position);
  Glide
    .with(myFragment)
    .load(url)
    .centerCrop()
    .placeholder(R.drawable.loading_spinner)
    .crossFade()
    .into(myImageView);
  return myImageView;
}
```
参考文档
* [Google推荐的图片加载库Glide介绍-译文](http://blog.csdn.net/theone10211024/article/details/45557859)

## square/picasso
项目地址：[square/picasso](https://github.com/square/picasso)

使用

```java
@Override public void getView(int position, View convertView, ViewGroup parent) {
  SquaredImageView view = (SquaredImageView) convertView;
  if (view == null) {
    view = new SquaredImageView(context);
  }
  String url = getItem(position);
  Picasso.with(context).load(url).into(view);
}
Picasso.with(context)
    .load(url)
    .placeholder(R.drawable.user_placeholder)
    .error(R.drawable.user_placeholder_error)
    .into(imageView);
Picasso.with(context).load(R.drawable.landing_screen).into(imageView1);
Picasso.with(context).load("file:///android_asset/DvpvklR.png").into(imageView2);
Picasso.with(context).load(new File(...)).into(imageView3);
```

参考文档
1. [官方文档](http://square.github.io/picasso/)
2. [Android图片加载库Picasso源码分析](http://www.tuicool.com/articles/6fEbeen)
3. [picasso-强大的Android图片下载缓存库](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/0731/1639.html)

## facebook/fresco(待研究)

项目地址：[facebook/fresco](https://github.com/facebook/fresco)

应用

```java
Uri uri = Uri.parse("https://raw.githubusercontent.com/facebook/fresco/gh-pages/static/logo.png");
SimpleDraweeView draweeView = (SimpleDraweeView) findViewById(R.id.my_image_view);
draweeView.setImageURI(uri);
```
参考文档
1. [中文站](http://fresco-cn.org/docs/index.html)
2. [FaceBook推出的Android图片加载库-Fresco](http://blog.csdn.net/bboyfeiyu/article/details/44943959)
3. [Android图片加载神器之Fresco-加载图片基础](http://blog.csdn.net/y1scp/article/details/49245535)
4. [Fresco图片框架内部实现原理探索](http://www.open-open.com/lib/view/open1451915129323.html)