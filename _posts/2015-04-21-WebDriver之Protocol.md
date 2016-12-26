---
layout: wp
title: continue
comments: true
---

### WebDriver之JsonWireProtocol ###  

**JsonWireProtocol说明**  
JsonWireProtocol(以下简称JWP)是通过使用webdriver与remote server进行通信的[web service 协议](https://code.google.com/p/selenium/wiki/JsonWireProtocol)。通过http请求，完成和remote server的交互。  

**交互过程** 
下面，我将列出一系列http请求，利用JSP和remote server进行通信，来进行浏览器操作。（注：书写格式为：GET  URL 参数）  


***1. 打开浏览器***
即，创建一个新的browser session。  
POST http://127.0.0.1:4444/wd/hub/session 

```
{desiredCapabilities:{browserName: 'chrome' }}
```


结果返回：  

```
{
    "status": 0,
    "sessionId": "277e823a-8be2-48eb-9001-971cc1bff9c4",
    "state": null,
    "value": {
        "platform": "XP",
        "acceptSslCerts": true,
        "javascriptEnabled": true,
        "browserName": "chrome",
        "chrome": {
            "userDataDir": "C:\\Users\\GUOLIA~1\\AppData\\Local\\Temp\\scoped_dir4600_564"
        },
        "rotatable": false,
        "locationContextEnabled": true,
        "webdriver.remote.sessionid": "277e823a-8be2-48eb-9001-971cc1bff9c4",
        "version": "41.0.2272.118",
        "takesHeapSnapshot": true,
        "cssSelectorsEnabled": true,
        "databaseEnabled": false,
        "handlesAlerts": true,
        "browserConnectionEnabled": false,
        "nativeEvents": true,
        "webStorageEnabled": true,
        "applicationCacheEnabled": false,
        "takesScreenshot": true
    },
    "class": "org.openqa.selenium.remote.Response",
    "hCode": 28232762
}
```

记住上面返回的

``` 
"sessionId": "277e823a-8be2-48eb-9001-971cc1bff9c4"
```

***2. 打开一个url*** 
比如，打开百度首页：  

POST
http://127.0.0.1:4444/wd/hub/session/277e823a-8be2-48eb-9001-971cc1bff9c4/url
{url :'http://www.baidu.com' }

***3. 查找输入框*** 

POST
http://127.0.0.1:4444/wd/hub/session/277e823a-8be2-48eb-9001-971cc1bff9c4/elements
{using:'css selector',value:'#kw'}

返回：

```
{
    "status": 0,
    "sessionId": "b6d34570-8300-4507-b60c-ab7cd1fe6536",
    "state": "success",
    "value": [
        {
            "ELEMENT": "0"
        }
    ],
    "class": "org.openqa.selenium.remote.Response",
    "hCode": 30641031
}
```


记住返回值， 下面要用。
"ELEMENT": "0"
***4. 输入元素***

 POST
http://127.0.0.1:4444/wd/hub/session/277e823a-8be2-48eb-9001-971cc1bff9c4/element/0/value
{value:[hello,world]}

*** 5. 查找OK按钮***
http://127.0.0.1:4444/wd/hub/session/277e823a-8be2-48eb-9001-971cc1bff9c4/elements
{using:'css selector',value:'#su'}
***4. 点击OK***
http://127.0.0.1:4444/wd/hub/session/277e823a-8be2-48eb-9001-971cc1bff9c4/element/1/click

返回点击成功了：

```
{
    "status": 0,
    "sessionId": "277e823a-8be2-48eb-9001-971cc1bff9c4",
    "state": "success",
    "value": null,
    "class": "org.openqa.selenium.remote.Response",
    "hCode": 11068806
}
```







