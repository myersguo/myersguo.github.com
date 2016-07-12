---
layout: wp
title: 测试管理相关
---

之前说的study lua & sqlite3都在一步步进行中，他们对测试都是基于代码架构的管理。而这里说一下，用用例的文本管理。   
一个公司都回有各种各样的组织，在jira上每个组织都会各种component，每个issue都关联到component下面。每个issue都可以定制字段，当然bug or testcase 可以有自己的属性定义。   

````
projects---
    tasks--introduction、features to be tested、test tasks 、 environmental needs,etc
    requirements---
        test suites---
            test case---Title、Description、Test Steps、Expected result、requirements
                run job--- fail or pass
```

更加丰富的细节\扩展如：   
test steps  ,status,assign, automation, test schedules,mark test result  

测试相关的feature是比较多的，但我们应该『不忘初心』，做到『简约而不简单』。   

* 用例的维护  
* 用例的执行   
* BUG的管理   

每个BUG都应该是管理一个用例或者说，BUG的描述过程就是一个用例。整个测试的管理，应该有以下矩阵：   

自动化脚本---用例---BUG

用例的执行可以关联自动化脚本，可以手工执行；   
执行的结果，如果是FAIL，需要提交BUG，如果是自动化执行，自动提交BUG，如果是手工执行，手工描述BUG。  

大的框架如此，设计到更多详细的分类，如：自动化如何实现落地，用例与自动化的交互（触发）方式——持续继承问题。我们未来再探讨。  


