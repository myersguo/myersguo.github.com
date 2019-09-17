---
layout: wp
title: switch chrome header extension
---

### basic knowledge ###

插件组成： 后台程序(backgroud scripts)、 前台脚本(content script)、设置页面(option page)、ui 页面(ui elements)和其他业务逻辑处理文件。   

![https://developer.chrome.com/static/images/overview/popuparc.png](https://developer.chrome.com/static/images/overview/popuparc.png)   

创建  manifest.json 文件.  
创建 background.js.    

[browser_action](https://developer.chrome.com/extensions/browserAction) 描述默认的 icon, 标题和 popup menu 文件。   
[permissions](https://developer.chrome.com/extensions/permissions), 设置插件运行时获取的权限。    


runtime events:
    runtime.onInstalled: 扩展安装时执行。   
    runtime.onStartup:   
    onMessage:   

chrome.webRequest: 
    使用 Use the chrome.webRequest API 来处理流量相关。

事件：
    onpageChanged: 页面切换,chrome.declarativeContent.onPageChanged . 
    

### step by step ###

ok, 这里直接使用 [vue-web-extension](https://github.com/Kocal/vue-web-extension) 这个库来直接创建一个扩展 demo:   

```
vue init kocal/vue-web-extension my-extension
```

结果生成目录:    

```
├── background.js
├── icons
│   ├── icon.xcf
│   ├── icon_128.png
│   └── icon_48.png
├── manifest.json
├── options
│   ├── App.vue
│   ├── options.html
│   └── options.js
├── popup
│   ├── App.vue
│   ├── popup.html
│   ├── popup.js
│   └── router
│       ├── index.js
│       ├── pages
│       │   └── Index.vue
│       └── routes.js
└── store
    ├── actions.js
    ├── getters.js
    ├── index.js
    ├── mutation-types.js
    └── mutations.js
```

接着我们更新 manifest:  

```
vi manifest.json
```
修改为：

```
{
  "name": "env-switch-tool",
  "description": "Env Switch Tool",
  "version": null,
  "manifest_version": 2,
  "icons": {
    "48": "icons/online.png",
    "128": "icons/online.png"
  },
  "permissions": [
		"tabs",
		"webRequest",
		"webRequestBlocking",
		"contextMenus",
		"storage",
		"downloads",
		"*://*/*",
		"unlimitedStorage"
	],
  "browser_action": {
    "default_title": "env-switch-tool",
    "default_popup": "popup/popup.html"
  },
  "background": {
    "scripts": [
      "background.js"
    ]
  },
  "options_ui": {
		"page": "options/options.html",
		"open_in_tab": true
  },
  "options_page": "options/options.html"
}
```

修改 backgroud.js:   

```
var rules = 
chrome.webRequest.onBeforeSendHeaders.addListener(
    function(details) {
        let headers = details.requestHeaders;
        //这里省略了一些代码
        return {requestHeaders: headers};
    },
    {urls: ["<all_urls>"]}, extraInfoSpec);

```

下面就是修改 options（选项） 和 popup （下拉 menu）。这里省略具体的例子。   


这个上面全是例子：[https://developer.chrome.com/extensions/samples#search:](https://developer.chrome.com/extensions/samples#search:)
