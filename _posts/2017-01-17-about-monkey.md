---
layout: wp
title: About Monkey
---

### 前言 ###   

这里简单说一下monkey的实现原理。  

### 起步 ###   

当你执行adb shell monkey的时候，它到底干了什么。   

`monkey`位于`/system/bin`目录下。内容为：  

```
# Script to start "monkey" on the device, which has a very rudimentary
# shell.
#
base=/system
export CLASSPATH=$base/framework/monkey.jar
trap "" HUP
exec app_process $base/bin com.android.commands.monkey.Monkey $*
```

首先，这个[`app_process`](https://android.googlesource.com/platform/frameworks/base/+/master/cmds/app_process/app_main.cpp)是什么呢？    


app_process是Android的系统启动进程,用于启动`zygote`和其他`java`进程:   

```
 if (zygote) {
        runtime.start("com.android.internal.os.ZygoteInit", args, zygote);
    } else if (className) {
        runtime.start("com.android.internal.os.RuntimeInit", args, zygote);
    }
```

更详细的内容，需要阅读android源码，这里不做详细扩展。   

adb这里是runtime执行`com.android.internal.os.RuntimeInit`来启动,位置在：   

`/system/framework/`下面。有很多系统的包，其中有一个`/system/framework/monkey.jar`为monkey的所在包。   

### [com.android.commands.monkey.Monkey](https://android.googlesource.com/platform/development/+/master/cmds/monkey/src/com/android/commands/monkey/Monkey.java)  ###    

Application that injects random key events and other actions into the system.   

下面，我们一步一步讲解一下：   

```
public static void main(String[] args) {
        // Set the process name showing in "ps" or "top"
        Process.setArgV0("com.android.commands.monkey");

        int resultCode = (new Monkey()).run(args);
        System.exit(resultCode);
    }

```

看一下`run`具体方法：   

![active manager](/public/android_active.gif)   

monkey中注入系统事件是通过***使用内部API***来实现的(activemanger, windowmanger, packagemanger),其他方式(instrumentation)只能是二等公民。     


```
private int run(String[] args) {
    processOptions();//处理参数
    loadPackageLists();//加载黑白名单，可测的有效包名   
    getSystemInterfaces();//获取系统接口，都是系统的隐藏接口。
    //mAm = ActivityManagerNative.getDefault();
    //这里返回了一个ActivityManagerProxy对象,用来执行mangerservice接口。 
    //mWm = IWindowManager.Stub.asInterface(ServiceManager.getService("window"));    
    //上面，获取了系统窗口服务
    //mPm = IPackageManager.Stub.asInterface(ServiceManager.getService("package"));   
    getMainApps();//获取要执行的activity
    mEventSource = new MonkeySourceRandom(mRandom, mMainApps,
                    mThrottle, mRandomizeThrottle, mPermissionTargetSystem);//产生一个随机事件
}
```


我们看一下monkey的事件列表类：   

```
public abstract class MonkeyEvent {
    protected int eventType;
    public static final int EVENT_TYPE_KEY = 0;
    public static final int EVENT_TYPE_TOUCH = 1;
    public static final int EVENT_TYPE_TRACKBALL = 2;
    public static final int EVENT_TYPE_ROTATION = 3;  // Screen rotation
    public static final int EVENT_TYPE_ACTIVITY = 4;
    public static final int EVENT_TYPE_FLIP = 5; // Keyboard flip
    public static final int EVENT_TYPE_THROTTLE = 6;
    public static final int EVENT_TYPE_PERMISSION = 7;
    public static final int EVENT_TYPE_NOOP = 8;

    public static final int INJECT_SUCCESS = 1;
    public static final int INJECT_FAIL = 0;

    // error code for remote exception during injection
    public static final int INJECT_ERROR_REMOTE_EXCEPTION = -1;
    // error code for security exception during injection
    public static final int INJECT_ERROR_SECURITY_EXCEPTION = -2;

    public MonkeyEvent(int type) {
        eventType = type;
    }
    ...
```




### 备注 ###   

* Android系统通过Binder机制给应用提供一系列系统服务，如am,wm..,提供了跨进成通信的方式，Binder是客户端和服务端通信的媒介。    


### 参考资料 ###  


[https://github.com/simple-android-framework/android_design_patterns_analysis/tree/master/proxy/singwhatiwanna](https://github.com/simple-android-framework/android_design_patterns_analysis/tree/master/proxy/singwhatiwanna)   
[https://android.googlesource.com/platform/development/+/master/cmds/monkey/src/com/android/commands/monkey/Monkey.java](https://android.googlesource.com/platform/development/+/master/cmds/monkey/src/com/android/commands/monkey/Monkey.java)   
[http://www.cnblogs.com/samchen2009/p/3294713.html](http://www.cnblogs.com/samchen2009/p/3294713.html)  
[http://gityuan.com/android/](http://gityuan.com/android/)   

