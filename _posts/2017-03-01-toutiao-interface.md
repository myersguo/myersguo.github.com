---
layout: wp
title:  从charles看系统 
---

了解一个产品到底做了什么，有哪些业务，一般通过抓包来看。我们使用 charles 对头条的 android 客户端将进行抓包，来看看头条这个 app 在背后到底做了什么？  

### 首屏 ###

第一次进入头条，我们通过 charles 看到一下请求：   

```
http://dm.toutiao.com/get_domains/v4/?
http://i.snssdk.com/directed_flow/detail/submit/?
http://ichannel.snssdk.com/check_version/v6/?
http://ichannel.snssdk.com/feedback/2/list/?
http://ichannel.snssdk.com/service/2/app_alert/?
http://is.snssdk.com/promotion/app/lt/?
http://isub.snssdk.com/2/user——carinfo/?
http://isub.snssdk.com/2/user/profile/v2/?
http://mon.snssdk.com/monitor/collect/?
http://s0z.pstatp.com/site/download/app/pl/news_article/61803/ss_plugin_config.json?
http://s3.pstatp.com/toutiao/app_web_article_online_updates/android_150_7647ac23d4381ca4df5607dbffbd0502.zip
https://dm.toutiao.com/get_domains/v4/?
https://ib.snssdk.com/service/2/app_log/?
https://is.snssdk.com/2/article/hot_words/?
https://is.snssdk.com/2/article/v58/refresh_tip/?
https://is.snssdk.com/activity/carrier_flow/query_flow/?
https://is.snssdk.com/api/ad/canvas/preload/v2/?
https://is.snssdk.com/api/msg/v1/unread/?
https://is.snssdk.com/article/category/get_subscribed/v2/?
https://is.snssdk.com/cloudpush/callback/register_device/?
https://is.snssdk.com/concern/v2/follow/my_follow/?
https://is.snssdk.com/dongtai/msg/list/v2/?
https://is.snssdk.com/dongtai/notification/list/?
https://is.snssdk.com/entry/subscription_list/v1
https://is.snssdk.com/location/suloin/?
https://is.snssdk.com/network/get_network/?
https://is.snssdk.com/search/suggest/homepage_suggest/?
https://is.snssdk.com/service/1/app_activity/?
https://is.snssdk.com/service/1/collect_settings/?
https://is.snssdk.com/service/14/app_ad/?
https://is.snssdk.com/service/settings/v2/?
https://is.snssdk.com/ttdiscuss/v2/ugc_video/video_auth/?
https://is.snssdk.com/user/tab/tabs/?
https://is.snssdk.com/wenda/v1/refresh/nativewidget/?
https://security.snssdk.com/api/plugin/config/v1/?
```

我们来看一下这些接口的用途：   

* get_domain: 跟域名相关的一些配置，https 开关，加解密开关等等。   
* directed_flow: 不清楚   
* check_version: 版本检查   
* feedback:  反馈列表   
* app_alert: 清楚  
* promotion/app/lt:  不清楚  
* user/info: 当前用户的信息  
* user/profile: 头条号/媒体的用户信息   
* monitor/collect: 监控收集   
* download/app/pl: 插件下载   
* app_log: 日志收集   
* article/hotword: 热搜  
* article/v58/refresh_tip: 更新提示   
* activity/carrier_flow/query_flow: 播放视频流量消耗提示   
* ad/canvas/preload: 不清楚  
* msg/v1/unread: 未读消息提醒   
* article/category/get_subscribed: 订阅列表   
* cloundpush/callback/register_device: 推送设备注册  
* concern/v2/follow/my_follow: 我的关注  
* dongtai/msg/list/v2: 动态消息列表  
* dongtai/notification/list: 动态通知    
* entry/subscription_list/v1:  不清楚   
* location/suloin: 当前的城市   
* network/get_network: 获取当前的网络   
* search/suggetst: 搜索推荐  
* service/1/app_activity/: 不清楚   
* service/1/collect_settings: 手机设置上报   
* service/14/app_ad: 广告相关   
* service/settings/v2:  获取服务配置   
* ttdiscuss/v2/ugc_video/video_auth: ugc/微头条提示   
* user/tab/tabs: 用户主页tab   
* wenda/v1/refresh/nativewidget: 问答频道数据   
* api/plugin/config/v1: 插件列表上报   


### 一步操作 ###

首页刷新，数据流  

* https://is.snssdk.com/api/news/feed/v58/    获取 推荐数据    

有一些频道包含其他请求，这里列一下：  

** 刷新 ** 头条号频道()：   

* https://is.snssdk.com/entry/subscription_list/v1/?   获取订阅的列表   

点击频道的加号：    

* https://is.snssdk.com/article/category/get_extra/v1/? 获取其他频道

本地频道(news_local):  

* https://ic.snssdk.com/stream/widget/local/weather/? 获取本地widget,天气，服务   

问答频道:   
* https://is.snssdk.com/wenda/v1/native/feedbrow/?category=question_and_answer

娱乐频道：   
*https://ic.snssdk.com/ugc/star/widget_data/ 头条明星指数榜   

房产频道:    
*https://ic.snssdk.com/stream/widget/template/house/?   房东频道widget  

电影频道:  
https://ic.snssdk.com/stream/widget/template/movie/?   电影的widget   

小说频道:  
https://ic.snssdk.com/novel_channel/?tt_daymode=1 : 小说频道页面   
https://ic.snssdk.com/novel_channel/recommend_book/:  推荐小说  

体育频道: 
https://ic.snssdk.com/stream/widget/template/sport_scroll/:  widget   

其他，科技(news_tech),热点(news_hot),财经(news_finance),视频(video), 汽车(news_car), 娱乐(news_entertainment)，娱乐(news_entertainment),房产频道(news_hourse),组图,社会(news_society),军事(news_millitary),国际频道(news_word),电影频道(movie),微头条(weitoutiao),段子(essay_joke),正能量(positive)等等

### 二步操作 ###

* /is.snssdk.com/2/article/information/v21 ，文章信息：相关文章，标签等   
* https://is.snssdk.com/article/v2/tab_comments/: 文章评论,


### 多步操作 ###

文章 like:  http://isub.snssdk.com/2/data/item_action/?   
评论点赞: https://is.snssdk.com/2/data/comment_action/?   
评论回复: https://is.snssdk.com/2/data/v3/post_message/   
评论删除:  https://is.snssdk.com/2/comment/v1/delete_comment/

评论回复点赞:  https://is.snssdk.com/2/comment/v1/digg_reply/   
评论详情: https://is.snssdk.com/2/comment/v1/detail/   
评论回复列表:  https://is.snssdk.com/2/comment/v1/reply_list/   
点赞列表:  https://is.snssdk.com/2/comment/v1/digg_list/   
评论回复回复： https://is.snssdk.com/2/comment/v1/create_reply/  
评论回复删除: https://is.snssdk.com/2/comment/v1/delete_reply  


拉黑用户：http://isub.snssdk.com/user_data/batch_action/  
拉黑用户: https://is.snssdk.com/user/block/create/  
关注用户: http://isub.snssdk.com/2/relation/follow/v2/   
关注推荐:  https://is.snssdk.com/user/relation/user_recommend/v1/supplement_recommends/   
关注:  https://is.snssdk.com/concern/v2/follow/my_follow/?   
取消关注：  http://isub.snssdk.com/2/relation/unfollow/   


等等等    

我们梳理了这些接口之后，然后简单根据操作的步骤分优先级(p0,p1)，然后可以大致勾勒出一个系统的整体架构出来。   

