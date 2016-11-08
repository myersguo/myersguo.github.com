---
layout: wp
title: About ACRA
---

当我search "android crash analytics open source"时，ACRA赫然在列。让我们解开它的面纱。   

### 技术架构 ###   

ACRA分为三个部分：   

* Application Crash Reports for Android:app的SDK   
* acra-storage: couchdb web application，存储ACRA SDK上报的数据
* acralyzer: ACRA的统计工具  

### couchdb web application ###   

传统的WEB框架有三层：   

```
Browser (UI and links between pages)
------------------------ HTTP -------------------------
Application server (business logic, templates, etc)
------------------- custom binary ---------------------
Persistence: MySQL, PostgreSQL, Oracle
```

couchdb的web框架，只有两层：   

```
Browser (UI and links between pages)
------------------------ HTTP -------------------------
CouchDB (persistence, business logic, and templating)
```

我们可以看到，couchdb不仅是一个数据库服务器，还是一个应用服务器。      


>由于 CouchDB 的 REST API 使用 JSON 作为展现形式，因此使用 CouchDB 的 Web 应用只需要编写浏览器端的代码就可以使用 JavaScript 与 CouchDB 进行交互；而 CouchDB 所支持的附件功能，又使得浏览器端的 HTML、JavaScript 和 CSS 代码可以直接存放在 CouchDB 中。这样 CouchDB 中不但保存了 Web 应用的数据，也保存了 Web 应用的逻辑。也就是说，只需要 CouchDB 就可以构建一个完整的 Web 应用运行环境。
>在 CouchDB 中，一个 Web 应用对应的是一个设计文档。这个 Web 应用可以操作 CouchDB 中保存的文档型数据。当需要创建新的 Web 应用的时候，只需要创建新的设计文档即可。 CouchDB 使得 Web 应用的部署和管理变得非常简单，只需要通过 REST API 管理设计文档即可。从更大的角度来说，CouchDB 有可能创造一种新的 Web 应用开发模式。在这种模式中，CouchDB 中保存的文档型数据可以为每个应用开发者所使用，开发者在数据之上创建满足各种需求的 Web 应用。
>CouchApp 是一个开发使用 CouchDB 的 Web 应用的小型框架。它的主要功能是可以把一个文件系统的目录转换成 CouchDB 中的一个设计文档。   


couchapp怎么来创建一个couchdb的web server参见：[https://www.ibm.com/developerworks/cn/opensource/os-cn-couchdb/](https://www.ibm.com/developerworks/cn/opensource/os-cn-couchdb/)和[https://couchapp.readthedocs.io/en/latest/couchapp/gettingstarted.html](https://couchapp.readthedocs.io/en/latest/couchapp/gettingstarted.html),

![iamge](https://www.ibm.com/developerworks/cn/opensource/os-cn-couchdb/couchapp-diectory-layout.JPG)  
>views— 包含数据库上的视图。它们是 JavaScript 函数，这些函数构建了您想要返回的键和数据的列表，等同于一般的数据库查询。
>lists— 包含用来构建视图输出格式化版本的列表。 列表是 JavaScript 函数，它读取视图信息并格式化信息（通常是 HTML）用于显示。
>shows— 显示单个文档，而不是视图中提供的一组文档。和列表一样，显示也是定义为 JavaScript 函数。
>attachments— 包含应用程序的附件，包括 index.html 和 JavaScript 文件。
>vendor目录中包含的是 CouchApp 提供的一些 JavaScript 类库；
>templates目录中包含的是 show 和 list 方法所需的 HTML 模板。在下面的章节中将会具体介绍这些目录中存放的文件。  

### crash 上报  ###  

我们先看acra怎么上报CRASH的。   

使用acra的SDK，它提供了上报的注解[https://github.com/ACRA/acra/blob/master/src/main/java/org/acra/annotation/ReportsCrashes.java](https://github.com/ACRA/acra/blob/master/src/main/java/org/acra/annotation/ReportsCrashes.java)：  

用的时候直接：   
```
@ReportsCrashes(formUri = "http://yourserver.com/yourscript",
                mode = ReportingInteractionMode.TOAST,
                resToastText = R.string.crash_toast_text)
public class MyApplication extends Application {
mode有多种，这里的实例为toast形式,可不写，默认静默方式
```

程序启动的时候需要初始化一下就行：  

```
ACRA.init(this);
```

我们看它的[原理](https://github.com/ACRA/acra/blob/master/src/main/java/org/acra/ACRA.java#L137)：  

```
public static void init(@NonNull Application app) {
        final ReportsCrashes reportsCrashes = app.getClass().getAnnotation(ReportsCrashes.class);
        if (reportsCrashes == null) {
            log.e(LOG_TAG, "ACRA#init(Application) called but no ReportsCrashes annotation on Application " + app.getPackageName());
            return;
        }
        init(app, new ConfigurationBuilder(app));
}
...
public static void init(@NonNull Application app, @NonNull ACRAConfiguration config, boolean checkReportsOnApplicationStart){

        final boolean senderServiceProcess = isACRASenderServiceProcess();
        if (senderServiceProcess) {
            if (ACRA.DEV_LOGGING) log.d(LOG_TAG, "Not initialising ACRA to listen for uncaught Exceptions as this is the SendWorker process and we only send reports, we don't capture them to avoid infinite loops");
        }
...
        errorReporterSingleton = new ErrorReporter(mApplication, configProxy, prefs, enableAcra, supportedAndroidVersion, !senderServiceProcess);


}
```
大致流程是：  
反射获取注解参数，
初始化ErrorReporter，截获crash   

```
public class ErrorReporter implements Thread.UncaughtExceptionHandler {

```
这个ErrorReport把自己设置成默认的异常处理：  

```
if (listenForUncaughtExceptions) {
      defaultExceptionHandler = Thread.getDefaultUncaughtExceptionHandler();
      Thread.setDefaultUncaughtExceptionHandler(this);
  } else {
      defaultExceptionHandler = null;
}
```

异常捕获后的上报：  

```
public static boolean isACRASenderServiceProcess() {
    final String processName = getCurrentProcessName();
    if (ACRA.DEV_LOGGING) log.d(LOG_TAG, "ACRA processName='" + processName + '\'');
    //processName sometimes (or always?) starts with the package name, so we use endsWith instead of equals
    return processName != null && processName.endsWith(ACRA_PRIVATE_PROCESS_NAME);
}
```

此处没看懂。   



### acra-storage ###

crash上包的URL。  
存储的REST API为：   
`http://127.0.0.0.1:5984/myapp/_design/acra-storage/_update/report` 

源码[https://github.com/ACRA/acra-storage/blob/master/updates/report.js](https://github.com/ACRA/acra-storage/blob/master/updates/report.js)：  


```
...
data.user_ip = req.peer;
data["_id"] = data.REPORT_ID;
if(data.STACK_TRACE) {
    data.STACK_TRACE = data.STACK_TRACE.split('\n');
}
...
```

这里要保存的_id是新增的documents,即 data.REPORT_ID   

问题： 这个REPORT_ID是怎么来的？   





参考资料：   
[couchdb web app](https://couchapp.readthedocs.io/en/latest/intro/what-is-couchapp.html)   
[http://docs.couchdb.org/en/latest/couchapp/ddocs.html](http://docs.couchdb.org/en/latest/couchapp/ddocs.html)  
[http://www.jianshu.com/p/fd4d6a7c6175](http://www.jianshu.com/p/fd4d6a7c6175)  
[https://github.com/ACRA/acra/wiki/AdvancedUsage#User_Interaction](https://github.com/ACRA/acra/wiki/AdvancedUsage#User_Interaction)  
[http://guide.couchdb.org/draft/validation.html](http://guide.couchdb.org/draft/validation.html)   
[https://wiki.apache.org/couchdb/Document_Update_Handlers#Creating_an_Update_Handler](https://wiki.apache.org/couchdb/Document_Update_Handlers#Creating_an_Update_Handler)   


