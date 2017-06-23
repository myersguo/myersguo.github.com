---
layout: wp
title: url 检查
---

「 一个页面的所有链接是不能都能访问成功 」，这个测试需求我们经常遇到。这里介绍如何用 postman 来实现它（你不需要写一行代码）。  

前提是你已经下载了 [postman native app](https://www.getpostman.com/)，注意，不是 postman 的 chrome 插件。 

打开 postman import [这个任务文件](/public/checker_test_run.json)。 你看到的大概是这个样子：   

![postman collection](/public/postman_init_run.png)   

ok，它就是 postman 的 collection （请求的集合） 。  

这个 collection 跑起来是需要一个环境变量的：   

![postman enviroment setting](/public/postman_init_run2.png) 


点击 runner, 选择要跑的 collection 和环境变量， 执行，结果如下：   


![postman runner](/public/postman_init_run3.png)   



postman 的 craw 逻辑如下：   

```
// Tests and custom scripts can be written in JavaScript.

// get environment variables
var start_url = postman.getEnvironmentVariable('start_url');
var root_url = postman.getEnvironmentVariable('root_url');
var links = JSON.parse(postman.getEnvironmentVariable('links'));
var url = postman.getEnvironmentVariable('url');
var index = parseInt(postman.getEnvironmentVariable('index'));

// increment index counter to access links in array to check
index = index + 1;

// test if link works
if (responseCode.code > 400) {
    console.log("This link is broken: ", url);
    tests["Link works"] = false;
} else {
    tests["Link works"] = true;
}

// if the current url includes the start_url, then this is an internal link and we should crawl it for more links
if (url.includes(start_url)) {
    
    // load the response body as HTML using cheerio, get the <a> tags
    var $ = cheerio.load(responseBody);
    
    $('a').each(function (index) {
        
        var link = $(this).attr('href');
        
        // add links to the links array if not already in there
        // if you have additional links you would like to exclude, for example, ads, you can add this criteria as well
        if (!links.includes(link)) {
            links.push(link);
        }
    });
}

// if we've gone through all the links, return early
if (links.length - 1 === index) {
    console.log('no more links to check');
    return;
}

// if link is a relative one, prepend with root_url
url = links[index]
if (! /^https?:\/\//.test(url)) {
    url = root_url + url;
}

// update environment variable values
postman.setEnvironmentVariable("links", JSON.stringify(links));
postman.setEnvironmentVariable("url", url);
postman.setEnvironmentVariable("index", index);

// continue calling the same request until all links are checked
postman.setNextRequest('Check URL');

```

可以看到有时候它获取的 link 是不正确的。 这就需要你加工一下能获取争取的 link 来遍历了。   

