---
layout: wp
title: Understand Appium UiAutomator
comments: true
---

在 [Over view of Appium](https://myersguo.github.io/2017/02/03/all-appium.html?x)中我们已经提到过 appium驱动android 的实现： ` appium-uiautomator2-server`。 它基于 `netty` 实现了一个 http server,监听本地6790端口，基于restful api的 json protocol协议来封装 uiautomator的实现。   

因为手机一般不具备固定的IP， 因此，一般通过 adb forward 讲 PC端请求转发给手机:   

```
adb forward tcp:6790 tcp:6790
```

如果不使用 adb forward 也可以，查看手机的IP (adb shell ifconfig)，假如手机的WLAN的ip是 `10.236.226.39`。   

我们安装 `appium-uiautomator2-server`：   

```
git clone  https://github.com/appium/appium-uiautomator2-server.git
gradle clean assembleServerDebug assembleServerDebugAndroidTest
#如果是windows，生成的目标文件在  c:\tmp 下
adb shell install -r appium-uiautomator2-server-v0.1.4.apk
```
启动 server:   

```
adb shell am force-stop io.appium.uiautomator2.server
adb shell am instrument -w io.appium.uiautomator2.server.test/android.support.test.runner.AndroidJUnitRunner

```

浏览器请求：  

```
http://10.236.226.39:6790/wd/hub/status
```

看到：  

```
{
sessionId: "SESSIONID",
status: 0,
value: "Status Invoked"
}
```

我们通过 logcat 查看日志：   

```
adb shell logcat -s appium:*
```

看到：  

```
04-19 11:04:32.366 25719 25732 I appium  : [AppiumUiAutomator2Server] Starting Server
04-19 11:04:33.387 25719 25732 I appium  : AndroidServer created on port 6790
04-19 11:04:33.388 25719 25732 I appium  : io.appium.uiautomator2.server started:
04-19 11:04:33.430 25719 25751 I appium  : Started UiAutomator2 io.appium.uiautomator2.http io.appiu
m.uiautomator2.server on port 6790
04-19 11:04:35.466 25719 25755 I appium  : channel read invoked!
04-19 11:04:35.466 25719 25755 I appium  : channel read: GET /wd/hub/status
04-19 11:04:35.472 25719 25755 I appium  : AppiumResponse: {"sessionId":"SESSIONID","status":0,"valu
e":"Status Invoked"}

```

so, 我们执行测试用例怎么办？有两个思路：   

方案一：  

* 扩展 appium-uiautomator2-server，比如注册一个 /wd/hub/session/:sessionId/runtest 的方法来执行测试（方式1）；  
* 新创建一个 service 来调用  appium-uiautomator2-server ,test service 负责解析、执行测试用例（方式2）;   

(ps，这个方案的尝试进行中）   

方案二：  

现在比较通用的，一般使用 appium 提供的 client端并基于 junit 实现测试用例。   

封装常用操作，封装 page，调用 appium server 并生成测试包。   

