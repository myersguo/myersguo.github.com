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
    ((MonkeySourceRandom) mEventSource).setFactors(i, mFactors[i]);
    mEventSource.validate();//验证事件，并调整比例
    mNetworkMonitor.start();//监听网络变化
    crashedAtCycle = runMonkeyCycles();//monkey核心逻辑

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


monkey有11种事件，在`MonkeyEventSource`中有事件的比例设置。   

下面，我们来看monekey的核心执行逻辑；  

```java
while (!systemCrashed && cycleCounter < mCount) {
    //检查是否发生了ANR
    if (mRequestAnrBugreport){
        getBugreport("anr_" + mReportProcessName + "_");
        mRequestAnrBugreport = false;
    }
    //检查系统watchdog是否报告bug
     if (mRequestWatchdogBugreport) {
         System.out.println("Print the watchdog report");
         getBugreport("anr_watchdog_");
         mRequestWatchdogBugreport = false;
     }
    //检查是否发生了CRASH
    if (mRequestAppCrashBugreport){
        getBugreport("app_crash" + mReportProcessName + "_");
        mRequestAppCrashBugreport = false;
    }
    //检查bugreport报告生成
     if (mRequestPeriodicBugreport){
         getBugreport("Bugreport_");
         mRequestPeriodicBugreport = false;
     }
    //报告系统信息，ANR时出发
     if (mRequestDumpsysMemInfo) {
         mRequestDumpsysMemInfo = false;
         shouldReportDumpsysMemInfo = true;
     }
    //获取下一个随机时间
    MonkeyEvent ev = mEventSource.getNextEvent();    
    //注入事件
     int injectCode = ev.injectEvent(mWm, mAm, mVerbose);
}

```

回到之前的代码逻辑，这个`mEventSource`有三种来源：   

```
//脚本模式
            mEventSource = new MonkeySourceScript(mRandom, mScriptFileNames.get(0), mThrottle,
                    mRandomizeThrottle, mProfileWaitTime, mDeviceSleepTime);
     mEventSource = new MonkeySourceRandomScript(mSetupFileName,
                        mScriptFileNames, mThrottle, mRandomizeThrottle, mRandom,
                        mProfileWaitTime, mDeviceSleepTime, mRandomizeScript);
//网络模式，monkeyrunner的使用方式
 mEventSource = new MonkeySourceNetwork(mServerPort);
//默认模式，一般都使用随机事件
mEventSource = new MonkeySourceRandom(mRandom, mMainApps,
                    mThrottle, mRandomizeThrottle, mPermissionTargetSystem);
```

好，我们这里展开说一下脚本模式怎么使用monkey.先写一个简单的monkey事件脚本文件:   

```
/**
 * monkey event queue. It takes a script to produce events sample script format:
 *
 * <pre>
 * type= raw events
 * count= 10
 * speed= 1.0
 * start data &gt;&gt;
 * captureDispatchPointer(5109520,5109520,0,230.75429,458.1814,0.20784314,0.06666667,0,0.0,0.0,65539,0)
 * captureDispatchKey(5113146,5113146,0,20,0,0,0,0)
 * captureDispatchFlip(true)
 * ...
 * </pre>
 */
    
#我们以小米商城为例，进入商城，滑动到最下面
type= user
count= 49
speed= 1.0
start data >>
LaunchActivity(com.xiaomi.shop, com.xiaomi.shop.activity.MainTabActivity)
#wait for launch
UserWait(10000)
#drag to down
Drag(542,1326,542,560,15)
#wait for 500 milliseconds
UserWait(500)
#tap second tab
Tap(346,1868)

```

那这个脚本是怎么解析的呢？（这里不详细展开）:   

```
readHeader();//打开文件，读文件头，设置参数,文件头的结尾必须是：STARTING_DATA_LINE    
当然，脚本中也可以不写文件头的。   
readLines();
readNextBatch();
processLine();//处理每一行命令，加入事件队列中。命令包括：  


```java
    // event key word in the capture log
    private static final String EVENT_KEYWORD_POINTER = "DispatchPointer";
    private static final String EVENT_KEYWORD_TRACKBALL = "DispatchTrackball";
    private static final String EVENT_KEYWORD_ROTATION = "RotateScreen";
    private static final String EVENT_KEYWORD_KEY = "DispatchKey";
    private static final String EVENT_KEYWORD_FLIP = "DispatchFlip";
    private static final String EVENT_KEYWORD_KEYPRESS = "DispatchPress";
    private static final String EVENT_KEYWORD_ACTIVITY = "LaunchActivity";
    private static final String EVENT_KEYWORD_INSTRUMENTATION = "LaunchInstrumentation";
    private static final String EVENT_KEYWORD_WAIT = "UserWait";
    private static final String EVENT_KEYWORD_LONGPRESS = "LongPress";
    private static final String EVENT_KEYWORD_POWERLOG = "PowerLog";
    private static final String EVENT_KEYWORD_WRITEPOWERLOG = "WriteLog";
    private static final String EVENT_KEYWORD_RUNCMD = "RunCmd";
    private static final String EVENT_KEYWORD_TAP = "Tap";//点击,轻触

    private static final String EVENT_KEYWORD_PROFILE_WAIT = "ProfileWait";
    private static final String EVENT_KEYWORD_DEVICE_WAKEUP = "DeviceWakeUp";
    private static final String EVENT_KEYWORD_INPUT_STRING = "DispatchString";
    private static final String EVENT_KEYWORD_PRESSANDHOLD = "PressAndHold"; //
    private static final String EVENT_KEYWORD_DRAG = "Drag"; //拖动
    private static final String EVENT_KEYWORD_PINCH_ZOOM = "PinchZoom";
    private static final String EVENT_KEYWORD_START_FRAMERATE_CAPTURE = "StartCaptureFramerate";
    private static final String EVENT_KEYWORD_END_FRAMERATE_CAPTURE = "EndCaptureFramerate";
    private static final String EVENT_KEYWORD_START_APP_FRAMERATE_CAPTURE =
            "StartCaptureAppFramerate";
    private static final String EVENT_KEYWORD_END_APP_FRAMERATE_CAPTURE = "EndCaptureAppFramerate";
```

就是酱紫。执行一下我们的脚本(命令列表):   

```
adb -s 8b52f091 push d:\script.txt /sdcard/data
monkey -f /sdcard/data/script.txt 1
```
你可以看到，我们滑动到了底部，然后打开了第二个TAB。当然，我们可以直接通过adb shell来执行上面的操作：  

```
adb shell input swipe 542 1326 560 15
adb shell input swipe 542 1326 560 15
adb shell input tap 346 1868

```

这里使用的是input命令来执行。和monkey一样，input是一个脚本,执行的是`/system/framework/input.jar`:   

```
$ cat /system/bin/input
# Script to start "input" on the device, which has a very rudimentary
# shell.
#
base=/system
export CLASSPATH=$base/framework/input.jar
exec app_process $base/bin com.android.commands.input.Input "$@"
```

回到monkey上去,上面说到   

`int injectCode = ev.injectEvent(mWm, mAm, mVerbose);`    

这个事件来源三类，我们现在看默认的随机事件(MonkeySourceRandom),它的getnextevent返回多种随机事件，这里以`MonkeyMotionEvent`为例进行说明

```java
@Override
    public int injectEvent(IWindowManager iwm, IActivityManager iam, int verbose) {
        MotionEvent me = getEvent();
        if ((verbose > 0 && !mIntermediateNote) || verbose > 1) {
            StringBuilder msg = new StringBuilder(":Sending ");
            msg.append(getTypeLabel()).append(" (");
            switch (me.getActionMasked()) {
                case MotionEvent.ACTION_DOWN:
                    msg.append("ACTION_DOWN");
                    break;
                case MotionEvent.ACTION_MOVE:
                    msg.append("ACTION_MOVE");
                    break;
                case MotionEvent.ACTION_UP:
                    msg.append("ACTION_UP");
                    break;
                case MotionEvent.ACTION_CANCEL:
                    msg.append("ACTION_CANCEL");
                    break;
                case MotionEvent.ACTION_POINTER_DOWN:
                    msg.append("ACTION_POINTER_DOWN ").append(me.getPointerId(me.getActionIndex()));
                    break;
                case MotionEvent.ACTION_POINTER_UP:
                    msg.append("ACTION_POINTER_UP ").append(me.getPointerId(me.getActionIndex()));
                    break;
                default:
                    msg.append(me.getAction());
                    break;
            }
            msg.append("):");

            int pointerCount = me.getPointerCount();
            for (int i = 0; i < pointerCount; i++) {
                msg.append(" ").append(me.getPointerId(i));
                msg.append(":(").append(me.getX(i)).append(",").append(me.getY(i)).append(")");
            }
            System.out.println(msg.toString());
        }
        try {
            //InputManager.getInstance返回input manager的实例
            //Injects an input event into the event system on behalf of an application
            //注入事件
            if (!InputManager.getInstance().injectInputEvent(me,
                    InputManager.INJECT_INPUT_EVENT_MODE_WAIT_FOR_RESULT)) {
                return MonkeyEvent.INJECT_FAIL;
            }
        } finally {
            me.recycle();
        }
        return MonkeyEvent.INJECT_SUCCESS;
    }

```

其他如MonkeyRotationEvent，使用`iwm.freezeRotation(mRotationDegree);`来实现旋转屏幕。   

 
### 总结 ###   

* monkey事件来源三种：默认随机事件、脚本定义事件、network网络事件；  
* monkey事件根据类型比例生成事件队列，循环查找事件;   
* monkey事件的实现使用系统内部API(activemanager,inputmanager,windowmanager)来实现；  

### 备注 ###   

* Android系统通过Binder机制给应用提供一系列系统服务，如am,wm..,提供了跨进成通信的方式，Binder是客户端和服务端通信的媒介。   
* Android系统异常，包括ANR(系统超过5秒没有响应，在/data/anr下生成traces.txt),CRASH(JAVA异常),NDK CRASH(Android Native Crash,/data/tombstones/tombstone_xx)    

* 手势事件  
![gestures](/public/gesture.jpg)   

  

 

### 参考资料 ###  


[https://github.com/simple-android-framework/android_design_patterns_analysis/tree/master/proxy/singwhatiwanna](https://github.com/simple-android-framework/android_design_patterns_analysis/tree/master/proxy/singwhatiwanna)   
[https://android.googlesource.com/platform/development/+/master/cmds/monkey/src/com/android/commands/monkey/Monkey.java](https://android.googlesource.com/platform/development/+/master/cmds/monkey/src/com/android/commands/monkey/Monkey.java)   
[http://www.cnblogs.com/samchen2009/p/3294713.html](http://www.cnblogs.com/samchen2009/p/3294713.html)  
[http://gityuan.com/android/](http://gityuan.com/android/)   
[https://developer.sony.com/2013/11/28/how-to-include-the-sony-gesture-api-in-your-google-tv-app-2/](https://developer.sony.com/2013/11/28/how-to-include-the-sony-gesture-api-in-your-google-tv-app-2/)  

