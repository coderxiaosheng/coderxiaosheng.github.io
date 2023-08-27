---
title: "Android图片加载库-Android-Universal-Image-Loader"
date: 2023-01-08T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "android-github-imageloader"
draft: false
---

本文简单汇总Android中图片加载库的使用，并非深度源码的解析。

## Android-Universal-Image-Loader

项目地址：[Android-Universal-Image-Loader](https://github.com/nostra13/Android-Universal-Image-Loader)

```java
// 初始化相关
ImageLoader imageLoader = ImageLoader.getInstance(); // Get singleton instance
imageLoader.init(ImageLoaderConfiguration.createDefault(BDApplication.instance()));
// 使用方式1：根据uri显示到ImageView上，是否异步要根据option.isSyncLoading
// Load image, decode it to Bitmap and display Bitmap in ImageView (or any other view 
//  which implements ImageAware interface)
imageLoader.displayImage(imageUri, imageView);
// 使用方式2：在回调里处理
// Load image, decode it to Bitmap and return Bitmap to callback
imageLoader.loadImage(imageUri, new SimpleImageLoadingListener() {
    @Override
    public void onLoadingComplete(String imageUri, View view, Bitmap loadedImage) {
        // Do whatever you want with Bitmap
    }
});
// 使用方式3：同步返回bitmap
// Load image, decode it to Bitmap and return Bitmap synchronously
Bitmap bmp = imageLoader.loadImageSync(imageUri);
```

另外，可以配置显示图片的样式，设置option的BitmapDisplayer。UIL提供了FadeInBitmapDisplayer渐显样式、SimpleBitmapDisplayer、RoundedBitmapDisplayer、CircleBitmapDisplayer、RoundedVignetteBitmapDisplayer圆角显示颜色会有个渐变的效果 这5中Displayer。

```java
private ImageLoaderUtil() {
	// 正常图片
	optionsNormal = new DisplayImageOptions.Builder()
		.showImageOnLoading(R.drawable.ic_stub)
		.showImageForEmptyUri(R.drawable.ic_stub)
		.showImageOnFail(R.drawable.ic_stub).cacheInMemory(true)
		.cacheOnDisk(true).considerExifParams(true)
		.displayer(new SimpleBitmapDisplayer()).build();
	
	// 圆角矩形
	optionsRounded = new DisplayImageOptions.Builder()
		.showImageOnLoading(R.drawable.ic_stub)
		.showImageForEmptyUri(R.drawable.ic_stub)
		.showImageOnFail(R.drawable.ic_stub).cacheInMemory(true)
		.cacheOnDisk(true).considerExifParams(true)
		.displayer(new RoundedBitmapDisplayer(10)).build();
	
	// 圆圈图片
	optionsCircle = new DisplayImageOptions.Builder()
		.showImageOnLoading(R.drawable.ic_stub)
		.showImageForEmptyUri(R.drawable.ic_stub)
		.showImageOnFail(R.drawable.ic_stub).cacheInMemory(true)
		.cacheOnDisk(true).considerExifParams(true)
		.displayer(new CircleBitmapDisplayer(4)).build();
	
	imageLoader = ImageLoader.getInstance();
	imageLoader.init(ImageLoaderConfiguration.createDefault(BDApplication.instance()));
	
	animateFirstListener = new AnimateFirstDisplayListener();
}
```

总体架构图：
![](https://user-images.githubusercontent.com/136188064/263540898-3f8dd5ec-1206-4bea-b681-61d2d273147f.png)
流程图：
![](https://user-images.githubusercontent.com/136188064/263540901-c93b8f36-1865-4d8f-be4f-4798df395d70.png)

参考文档：
[Android Universal Image Loader 源码分析](http://a.codekk.com/detail/Android/huxian99/Android%20Universal%20Image%20Loader%20%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90)