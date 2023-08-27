---
title: "Android启动定时任务"
date: 2022-12-16T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "android-PendingIntent-AlarmManager"
draft: false
---

PendingIntent和AlarmManager

pendingintent使用的三种情况

你可以通过getActivity(Context context, int requestCode, Intent intent, int flags)系列方法从系统取得一个用于启动一个Activity的PendingIntent对象

可以通过getService(Context context, int requestCode, Intent intent, int flags)方法从系统取得一个用于启动一个Service的PendingIntent对象

可以通过getBroadcast(Context context, int requestCode, Intent intent, int flags)方法从系统取得一个用于向BroadcastReceiver的Intent广播的PendingIntent对象

pendingIntent的第4个参数

FLAG_CANCEL_CURRENT:如果当前系统中已经存在一个相同的PendingIntent对象，那么就将先将已有的PendingIntent取消，然后重新生成一个PendingIntent对象。

FLAG_NO_CREATE:如果当前系统中不存在相同的PendingIntent对象，系统将不会创建该PendingIntent对象而是直接返回null。

FLAG_ONE_SHOT:该PendingIntent只作用一次。在该PendingIntent对象通过send()方法触发过后，PendingIntent将自动调用cancel()进行销毁，那么如果你再调用send()方法的话，系统将会返回一个SendIntentException。

FLAG_UPDATE_CURRENT:如果系统中有一个和你描述的PendingIntent对等的PendingInent，那么系统将使用该PendingIntent对象，但是会使用新的Intent来更新之前PendingIntent中的Intent对象数据，例如更新Intent中的Extras。

AlarmManger启动定时任务，其常用的方法

(1)set(int type,long startTime,PendingIntent pi)；

该方法用于设置一次性闹钟，第一个参数表示闹钟类型，第二个参数表示闹钟执行时间，第三个参数表示闹钟响应动作。

(2)setRepeating(int type,long startTime,long intervalTime,PendingIntent pi)；

该方法用于设置重复闹钟，第一个参数表示闹钟类型，第二个参数表示闹钟首次执行时间，第三个参数表示闹钟两次执行的间隔时间，第四个参数表示闹钟响应动作。类似JAVA的Timer里面scheduleAtFixedRate(TimerTask task, long delay, long period)：以近似固定的时间间隔（由指定的周期分隔）进行后续执行。在固定速率执行中，根据已安排的初始执行时间来安排每次执行。如果由于任何原因（如垃圾回收或其他后台活动）而延迟了某次执行，则将快速连续地出现两次或更多的执行，从而使后续执行能够“追赶上来”。从长远来看，执行的频率将正好是指定周期的倒数（假定 Object.wait(long) 所依靠的系统时钟是准确的）。

(3)setInexactRepeating（int type,long startTime,long intervalTime,PendingIntent pi）

该方法也用于设置重复闹钟，与第二个方法相似，不过其两个闹钟执行的间隔时间不是固定的而已。它相对而言更节能（power-efficient）一些，因为系统可能会将几个差不多的闹钟合并为一个来执行，减少设备的唤醒次数。 有点类似JAVA的Timer里面schedule(TimerTask task, Date firstTime, long period)：根据前一次执行的实际执行时间来安排每次执行。如果由于任何原因（如垃圾回收或其他后台活动）而延迟了某次执行，则后续执行也将被延迟。在长期运行中，执行的频率一般要稍慢于指定周期的倒数（假定 Object.wait(long) 所依靠的系统时钟是准确的）。

(4)cancel(PendingIntent operation)

取消一个设置的闹钟

(5)setTimeZone(String timeZone)

设置系统的默认时区。需要android.permission.SET_TIME_ZONE权限

注解：使用alarmManager启动service时，用service的action（service在manifest中声明多个action）来启动，防止用service.class导致pendingintent相互覆盖影响的情况！重要！