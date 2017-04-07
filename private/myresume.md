---
layout: wp
private: true
title: 郭亮勇
---


-------------------     ----------------------------

**出生日期：**                  1987.06.28  
**邮箱：**                     myersguo at gmail dot com    
**地址：**                     北京海淀   
**手机号：**                     130-5184-7507   
**Blog：**                     [https://myersguo.github.io/](https://myersguo.github.io/)  

-------------------     ----------------------------

教育经历
---------

2010-2013  华南师范大学	硕士	计算机应用技术专业  
2006-2010  延边大学    本科	计算机科学与技术专业


工作经验
----------

**小米科技责任有限公司:**                     2014.03——至今    

[小米网](http://www.mi.com/)平台研发部测试组测试开发工程师。测试自动化平台开发、维护，
配置管理、持续集成平台开发，APP 质量监控与工具开发


**艺龙旅行网**		2013.07——2014.03

[艺龙网](http://www.elong.com/)测试部测试开发工程师。测试开发环境自动化部署与搭建；性能测试与自动化开发

专业技能
--------------------
熟练掌握黑盒测试、白盒测试技术；  
掌握移动端测试开发技术：Robotium, Instrumentation, Uiautomator, Monkey 等  
掌握 PHP 开发，熟练使用 Yii、Yaf 框架  
熟悉 Golang Web 开发    
熟悉 MySQL, Redis 等常用存储技术    


项目经验
--------------------

**APP 质量监控平台**  

对 APP 进行质量监控，类似 [Instabug ](https://instabug.com/) 或 国内腾讯的[Bugly](https://bugly.qq.com/)。方便开发者定位问题，跟踪用户反馈。APP 端捕获异常(类似 [Acra](https://github.com/ACRA/acra) 方案), 上传异常到 Elasticsearch，质量平台采用 PHP + Yii 开发，对 APP 进行质量分析：  

* APP 接入管理  
* Crash 聚合分析， Crash 趋势  
* 用户反馈收集、质量跟踪（任务分配）

**自动造单系统**  

电商的测试用例的核心是订单。订单包括各种订单状态（支付、出库、打单、配货等），每次测试都需要一种类型的订单状态，因此需要有一个平台自动生成特定状态的订单。开发框架： PHP + Yii, 部分 Golang。   

* 项目分 WEB端 和 API 端，API端负责核心逻辑执行，WEB端负责前端数据管理与交互  
* 功能包括：订单创建、流转、定时任务管理等
	
**小米网配置管理系统**  

每一个项目都包含一个配置文件，用于记录应用需要的各种配置（服务开关、DB 账号密码、白名单等等），配置在不同环境（测试、预发布、线上），不同机房都不相同，且代码与配置分别发布。为此开发配置管理系统，用于配置的创建、编辑、发布。   

* 配置文件管理：创建、修改、同步、加密等     
* 配置相关功能：机房、站点、用户权限管理等  
* 异常行为监控：系统故障、配置变更等  

**其他**

* 基于 Android Adb Monkey 的[性能监控与统计分析](https://github.com/myersguo/PythonExamples/tree/master/adb)  
* 基于 Selenium + Appium 的 UI [自动自动化工具](https://github.com/myersguo/miuitest)  
* Jenkin [文件更新插件](https://github.com/myersguo/fileoperate_jenkins)  
* 小工具的开发维护：MOCK SERVER、时间戳、库存更改等等  
