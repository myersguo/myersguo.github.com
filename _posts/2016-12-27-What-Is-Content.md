---
layout: wp
title: What's Content
---

Content，上下文，这个词经常看到——上下文切换(CONTEXT SWITCH),android.content.Context,golang http context,那这里的Content到底是什么东西呢？  


这篇[answer](http://stackoverflow.com/questions/6145091/the-term-context-in-programming)里做了很形象的比喻： 

>比如，你去看牙医，前台登记了你的姓名。你的姓名就是***context***，当你来看牙医时，需要提供你的姓名来标识自己；  
>再比如，你去机场候机，你的机票是你的ID。加入你的包括不见了，你需要到失物登记处登记，登记的ID就是你的***context***   


[wiki](https://en.wikipedia.org/wiki/Context_(computing))上说的，任务的context是任务的最小集合，当任务被打断后的存储内容，一边可以恢复任务时，恢复到被打断前的场景。   


php中的每一次http请求，保存的[流信息](http://php.net/manual/en/stream.contexts.php)是一系列参数的集合。[HTTP context options](http://php.net/manual/en/context.http.php)包括method,header,user_agent,content,proxy,protocol_version等http的传输信息。   

android的activity context用来标识一个activity的引用或状态。每一个[activity](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/app/Activity.java)和[application](https://android.googlesource.com/platform/frameworks/base.git/+/master/core/java/android/app/Application.java)都有是一个context。[view](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/view/View.java)必须属于一个context中。   

来看application和activity为什么是一个context:  


```
public class Application extends ContextWrapper implements ComponentCallbacks2 {
...
}
public class Activity extends ContextThemeWrapper
        implements LayoutInflater.Factory2,
        Window.Callback, KeyEvent.Callback,
        OnCreateContextMenuListener, ComponentCallbacks2,
        Window.OnWindowDismissedCallback, WindowControllerCallback {
...
}
```

view的构造： 


```
public View(Context context) 
{
}
```

这样，我们可以简单理解成context就是『现场』，或者是现场的引用。   




### 参考资料  ###   

[https://en.wikipedia.org/wiki/Context_(computing)](https://en.wikipedia.org/wiki/Context_(computing))
[https://en.wikipedia.org/wiki/Context_switch](https://en.wikipedia.org/wiki/Context_switch)   

