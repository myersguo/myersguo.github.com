---
layout: wp
title: 今天看到的
comments: true
---

### 开源社区让我震惊   

今天看到两个关于开源社区的内容：
1. how sqlite tested: (https://www.sqlite.org/testing.html)
sqlite的代码包含的测试用例有“Millions and millions of test cases”
看它官方是怎么写的：   
>
	Three independently developed test harnesses
	100% branch test coverage in an as-deployed configuration
	Millions and millions of test cases
	Out-of-memory tests
	I/O error tests
	Crash and power loss tests
	Fuzz tests
	Boundary value tests
	Disabled optimization tests
	Regression tests
	Malformed database tests
	Extensive use of assert() and run-time checks
	Valgrind analysis
	Undefined behavior checks
	Checklists

这让我很震惊，sqlite3这个项目太认真了，难怪android等很多大的项目的内部数据存储选择sqlite,这么可靠的测试，怎么能不拥有高质量呢？   

2) jenkins   
因为业务的特殊需要，需要了解jenkins的插件开发逻辑，去jenkins官网看了下，被jenkins社区完善的项目管理震惊到了。   
jenkins的bug管理列表:https://issues.jenkins-ci.org/secure/Dashboard.jspa 
第一次进入的时候会有一个介绍：https://issues.jenkins-ci.org/secure/WelcomeToJIRASoftware.jspa

联想到我现在所在的部门，所进行的工作。很多项目根本没有testcase,没有流程控制，bug的填写也不是很规范。
来，我们看一下jenkins的bug列表的bug填写:  

https://issues.jenkins-ci.org/browse/JENKINS-36111?filter=-1&jql=resolution%20%3D%20Unresolved%20order%20by%20updated%20DESC

另外，参考一下apache的：  
https://issues.apache.org/jira/secure/Dashboard.jspa   
https://issues.apache.org/jira/browse/FOP-28?filter=12311124&jql=issuetype%20%3D%20Bug%20ORDER%20BY%20updated%20ASC%2C%20key%20DESC




