---
title: "Java线程池相关"
date: 2023-01-07T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "android-thread"
draft: false
---

本文简单介绍Android中线程池使用，相关源码在android.java中的 java.util.concurrent 包中。

```java
//创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
//创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
//创建一个定长线程池，支持定时及周期性任务执行。
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
    return new ScheduledThreadPoolExecutor(corePoolSize);
}
//创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```

参考文章：
* [Java线程池](http://www.trinea.cn/android/java-android-thread-pool/)