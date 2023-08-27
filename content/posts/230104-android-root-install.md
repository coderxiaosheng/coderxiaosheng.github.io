---
title: "Android-Root静默安装"
date: 2023-01-04T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "android-root-install"
draft: false
---

说明：用于简单记录。

### 请求root权限，root权限静默安装
请求root
```java
// 请求root权限，用这个请求root权限，等待授权管理返回
public static boolean upgradeRootPermission2(String pkgCodePath) {
    String cmd="chmod 777 " + pkgCodePath;
    Process process = null; 
    DataOutputStream os = null; 
    BufferedReader br = null;
    StringBuilder sb = null;
    try {
        process = Runtime.getRuntime().exec("su");
        os = new DataOutputStream(process.getOutputStream());
        os.writeBytes(cmd + "\n");
        os.writeBytes("echo return\n");
        os.writeBytes("exit\n");
        br = new BufferedReader(new InputStreamReader(
                process.getInputStream()));
        sb = new StringBuilder();
        String temp = null;
        while ((temp = br.readLine()) != null) {
            sb.append(temp + "\n");
            CLog.e("TMS", "temp=="+temp);
            if ("return".equalsIgnoreCase(temp)) {
                CLog.e("TMS", "----------" + sb.toString());
                return true;
            }
        }
        process.waitFor();
    } catch (Exception e) {
        CLog.e("TMS", "异常：" + e.getMessage());
    } finally {
        try {
            if (os != null) {
                os.flush();
                os.close();
            }
            if (br != null) {
                br.close();
            }
            process.destroy();
        } catch (Exception e) {
            return false;
        }
    }
    return false;
}
```

root安装
```java
/**
 * 请求ROOT权限后执行命令（最好开启一个线程）
 * @param cmd    (pm install -r *.apk)
 * @return
 */
public static boolean installApkInRoot(File path, Context context) {
    String cmd = "pm install -r " + path + "\n";
    Process process = null; 
    DataOutputStream os = null; 
    BufferedReader br = null;
    StringBuilder sb = null;
    try {
        process = Runtime.getRuntime().exec("su");
        os = new DataOutputStream(process.getOutputStream());
        os.writeBytes(cmd + "\n");
        os.writeBytes("exit\n");
        br = new BufferedReader(new InputStreamReader(
                process.getInputStream()));
        sb = new StringBuilder();
        String temp = null;
        while ((temp = br.readLine()) != null) {
            sb.append(temp + "\n");
            CLog.e("TMS", "temp=="+temp);
            if ("Success".equalsIgnoreCase(temp)) {
                CLog.e("TMS", "----------" + sb.toString());
                return true;
            }
        }
        process.waitFor();
    } catch (Exception e) {
        CLog.e("TMS", "异常：" + e.getMessage());
    } finally {
        try {
            if (os != null) {
                os.flush();
                os.close();
            }
            if (br != null) {
                br.close();
            }
            process.destroy();
        } catch (Exception e) {
            return false;
        }
    }
    return false;
}
```
### 永久获取root权限

实现原理是第一次请求root权限时，将自己编译的su 拷贝到system/bin目录，后续在进行root权限操作时，调用自己的su。

91手机助手类似如此，一次请求，永久取得root权限。

```java
/**
  * 调用su获取root权限再把zlsu写到system/bin目录下
  * 以便永久获取root权限（一旦获取成功，下次不再调用su）
  * 
  * @param ctx
  */
public static void preparezlsu(Context ctx) {
	try {
	 File zlsu = new File("/system/bin/" + Constants.ROOT_SU);
	 // if (!zlsu.exists())
	 // {
	 // Toast toast = Toast.makeText(ctx,
	 // "Unable to find /system/bin/zlsu.", Toast.LENGTH_LONG);
	 // toast.show();
	 // return;
	 // }
	 InputStream suStream = ctx.getResources().openRawResource(
			 R.raw.zlsu);
	 /**
	  * 如果zlsu存在，则和raw目录下的zlsu比较大小，大小相同则不替换
	  */
	 if (zlsu.exists()) {
		 if (zlsu.length() == suStream.available()) {
			 suStream.close();
			 return;
		 }
	 }
	 // File superuser = new File("/system/bin/superuser");
	 //
	 // if (superuser.exists())
	 // {
	 // // return device to original state
	 // Process process = Runtime.getRuntime().exec("superuser");
	 // DataOutputStream os = new
	 // DataOutputStream(process.getOutputStream());
	 //
	 // os.writeBytes("mount -oremount,rw /dev/block/mtdblock3 /system\n");
	 // os.writeBytes("busybox cp /system/bin/superuser /system/bin/su\n");
	 // os.writeBytes("busybox chown 0:0 /system/bin/su\n");
	 // os.writeBytes("chmod 4755 /system/bin/su\n");
	 // os.writeBytes("rm /system/bin/superuser\n");
	 // os.writeBytes("exit\n");
	 // os.flush();
	 // }
	 /**
	  * 先把zlsu 写到/data/data/com.zl.movepkgdemo中 然后再调用 su 权限 写到
	  * /system/bin目录下
	  */
	 byte[] bytes = new byte[suStream.available()];
	 DataInputStream dis = new DataInputStream(suStream);
	 dis.readFully(bytes);
	 String pkgPath = ctx.getApplicationContext()
			 .getPackageName();
	 // "/data/data/com.zl.movepkgdemo/zlsu"
	 String zlsuPath = "/data/data/" + pkgPath + File.separator + Constants.ROOT_SU;
	 File zlsuFileInData = new File(zlsuPath);
	 if(!zlsuFileInData.exists()){
		 System.out.println(zlsuPath + " not exist! ");
		 try{
		 System.out.println("creating "+zlsuPath + "......");
		 zlsuFileInData.createNewFile();
		 }catch(IOException e){
			 System.out.println("create "+zlsuPath + " failed ! ");
		 }
		 System.out.println("create "+zlsuPath + " successfully ! ");
	 }
	 FileOutputStream suOutStream = new FileOutputStream(zlsuPath);
	 suOutStream.write(bytes);
	 suOutStream.close();
	 Process process = Runtime.getRuntime().exec("su");
	 DataOutputStream os = new DataOutputStream(
			 process.getOutputStream());
	 os.writeBytes("mount -oremount,rw /dev/block/mtdblock3 /system\n");
//           "busybox cp /data/data/com.zl.movepkgdemo/zlsu /system/bin/zlsu \n"
	 os.writeBytes("busybox cp " + zlsuPath + " /system/bin/"
			 + Constants.ROOT_SU + "\n");
//           "busybox chown 0:0 /system/bin/zlsu \n"
	 os.writeBytes("busybox chown 0:0 /system/bin/" + Constants.ROOT_SU
			 + "\n");
//           "chmod 4755 /system/bin/zlsu \n"
	 os.writeBytes("chmod 4755 /system/bin/" + Constants.ROOT_SU + "\n");
	 os.writeBytes("exit\n");
	 os.flush();
	} catch (Exception e) {
//           Toast toast = Toast
//                   .makeText(ctx, e.getMessage(), Toast.LENGTH_LONG);
//           toast.show();
	 System.out.println("RootUtil preparezlsu: error");
	 e.printStackTrace();
	}
}
```

而后move apk，使用自己的su。

```java
private int movepkg(String pkgName) {
    int result = -100;
    PackageInfo info;
    String minePkgName = "";
    try {
        minePkgName = getPackageName();
        info = getPackageManager().getPackageInfo(pkgName, 0);
        int moveFlag = (info.applicationInfo.flags & ApplicationInfo.FLAG_EXTERNAL_STORAGE) == 0 ? PackageManager.MOVE_EXTERNAL_MEDIA
                : PackageManager.MOVE_INTERNAL;
        String classpath = getPackageManager().getPackageInfo(minePkgName,
                0).applicationInfo.publicSourceDir;
        System.out.println("classPath: " + classpath);
        String cmd = "movedemo -c 'export CLASSPATH="
                + classpath
                + " && export LD_LIBRARY_PATH=/vendor/lib:/system/lib && exec app_process /data/app "
                + minePkgName + "/MoveUtil " // /system/bin
                + pkgName + " " + moveFlag + "'";
        String[] progArray = new String[] {
                Constants.ROOT_SU,
                "-c",
                "export CLASSPATH="
                        + classpath
                        + " && export LD_LIBRARY_PATH=/vendor/lib:/system/lib && exec app_process /data/app "
                        + minePkgName + "/util/MoveUtil " // /system/bin
                        + pkgName + " " + moveFlag };
        // zlsu -c             export CLASSPATH=/data/app/com.zl.movepkgdemo-1.apk && export LD_LIBRARY_PATH=/vendor/lib:/system/lib
        //                        && exec app_process /data/app com.zl.movepkgdemo/util/MoveUtil com.zl.hw moveFlag
        String returnCode = "";
        returnCode = RootUtil.exeCmd(progArray,
                Constants.RETURNCODE_MOVE_PACKAGE);
        try {
            System.out.println("result: " + returnCode);
            result = Integer.parseInt(returnCode);
        } catch (NumberFormatException e) {
            return result;
        }
    } catch (NameNotFoundException e) {
        System.out.println("MainActivity: movepkg : " + pkgName
                + " not found");
        e.printStackTrace();
    }
    return result;
}
```
参考资料：
* [Android应用程序永久获取root权限方法](http://hold-on.iteye.com/blog/1901152)