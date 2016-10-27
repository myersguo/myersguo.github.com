---
layout: wp
title: think in test
---

###  测试感悟 ### 

昨天给部门领导做测试工作组汇报，测试开发同学讲了各种测试工具，测试管理系统，压测系统；功能（黑盒）测试同学讲了业务测试的质量把控（需求管理、工具与效率，白盒测试，现场测试，安全测试）方方面面。   

领导听完，领导分享了他对『做事』的理解，讲了他近几年轮换工作岗位的一些感慨，总结到：做测试就要把测试这件事做好，测试的衡量标准就是项目上线的质量，漏测率是测试工作的核心指标。因此，他想看到项目测试的数据指标。   

领导说的非常对，但仔细思考一下你会发现测试应该测测试的衡量标准是什么不是一句话就完了，而要系统的说。     


我在刚毕业时，在大公司的测试和一个不知名公司的开发的职业选择中选择了做测试开发。工作半年发现工作内容即非纯开发，又不结束测试，对测试完全不理解。到底什么才是测试？八个月后我换了份现在的工作，到新公司做业务测试，这才接触到了『真正的测试』——把业务吃透，用鼠标来构造输入条件和输出结果，用肉眼来比较预期与结果。  


不用关心代码的逻辑，只针对业务流程，把用户的行为操作一边，对逻辑进行验证，这就是业务测试。   

是吗？不，不止如此。   

测试的细节是什么呢？   
用户交互有哪些多少种？数据的存储是什么样子的？浏览器加载的性能指标如何收集和统计？慢日志如何定位？代码的逻辑有哪些没有测试到？那些无法手工测试？   
这些测试怎么做？   

用户的交互可以用机器来模拟，数据的验证可以用机器来验证，性能测试与统计也可以用机器来实现。开发用编程来实现业务，测试用编程来实现验证。开发的代码叫做业务代码，测试的代码就是测试用例。  

产品质量的评判标准是测试用例的执行结果。不管用例是怎么执行的，手工或者代码执行，或者混合执行。   

发布上线看的是测试用例的执行报告。手工测试不是给出一个可以上线的一句话，而且要有测试用例执行表；代码测试一般自动汇总测试结果。   

不可测试的代码不是好代码。我近期惊讶于一些开原项目的测试工作，做的太好了。比如sqllite，php,phpunit,openresty这些项目的测试覆盖度相当高。   

我们关心项目测试，关心漏测率。但过程是我们怎么测试，怎么高效地、可度量的测试，这一直是测试领悟的探索，是技术性的探索，不是空口白话的探索！ 

在测试这条道路上，不乏优秀的探索者。我们来看看开源项目PHP是怎么测试的：   

看PHP的第一个测试用例(php-src/tests/basic/001.phpt):   

```
--TEST--
Trivial "Hello World" test
--FILE--
<?php echo "Hello World"?>
--EXPECT--
Hello World
```

是不是很简单，但统计一下PHP的测试用例有多少呢？在5.3版本中有大概有784个用例。   

  
看sqlite是怎么测试的，网上有一篇『How SQLite Is Tested』，里面详细介绍了sqlite3的测试内容：  

```
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
```

还有更多。。。    

测试的衡量目标摆在那里，如何达到这个目的，我们的前辈们已经探索出了很多优秀的方案，这些方案我们自己也会继续探索下去。。。  



