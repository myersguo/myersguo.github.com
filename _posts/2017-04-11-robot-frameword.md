---
layout: wp
title: robot framework
---

### 前言 ###

在开始介绍 [robot framework](http://robotframework.org/) 之前，我们先看下她的作者 Pekka Klärck 的这篇 [slide](https://www.slideshare.net/pekkaklarck/introduction-to-test-automation)，自动化测试有以下几种方法：  

* 录制和回放(Record and playback)   
    易用，不需编程经验；测试覆盖不全，难于管理，无法在系统之前开始   
* 线性脚本( Linear script)   
    非结构化的单个测试脚本，eg:   
![script testing](/public/images/script_test.png)
    方便编写，可扩展，但不够灵活，难于维护，不适合大型项目   
* 模块化脚本 ( Modular scripting, Driver scripting ) 
   ![module script](/public/images/module_scripting_arc.png)    
   ![module script](/public/images/module_scripting.png)    
    将测试模块化成**基础库**+**驱动脚本**   
    重用代码，便于管理，驱动脚本易于编写,但也有问题：基础库的开发和维护成本，测试数据混合在测试库中，新的测试支持需要添加新的测试库   
* 数据驱动测试 （ Data-driven testing )   
   ![data-driven testing](/public/images/data_driven_script.png)  
    编写测试用例，就是编写数据（一般是 Excel 表格), 这样，功能测试人员不需要编程经验，测试开发负责用例驱动的维护工作，但这样用例比较简单，测试驱动维护比较复杂    
* 关键字驱动测试 ( Keyword-driven testing )   
   ![keyword_driven_testing](/public/images/keyword_driven_testing.png)  
   基础库是**关键字函数**，关键字的驱动由框架执行，用例变成了包含关键字的数据。   
   和数据驱动一样，但它通过关键字来支持更多的测试，由于**不需要再去维护driver script和基础库**(它们都由framework取代)，因此维护起来也很方便，但难在框架的搭建      

在开始之前，当然要想好方向。每个方向都有优劣。简单、合适、有效最重要。  

数据驱动，关键字驱动的目的都是解放双手，用描述语言将用例实现，灵活性上肯定和模块化脚本没办法比，但作为大型项目的测试，用关键字驱动的框架维护的成本相对于模块的脚本更低一些。    

OK,让我们来一窥`robot framework`的究竟。   

### 简介 ###

![robot architecture](/public/images/robot_architecture.png)   


Robot Framework 是基于 Python 的可扩展的关键字驱动的( keyword-driven test automation framework ) 的[验收测试驱动的开发模式, ATDD ](https://en.wikipedia.org/wiki/Acceptance_test%E2%80%93driven_development)。它非常方便使用：   

* 支持表格式语法   
* 支持创建自定义的关键字   
* 测试报告和测试日志的支持   
* 独立于测试应用   
* 提供了简单的 API 扩展，方便使用 Python或 Java创建测试库   
* 提供了命令行工具，以及 XML 格式的结果，方便与其他持续集成平台结合   
* 支持 Selenium web testing, Java GUI testing, 启动其他进程， Telnet, SSH等等   
* 支持创建(数据驱动, data-driven)测试用例  
* 支持对测试用例进行归档、标记，分别执行   
* 方便版本控制：测试集合仅仅是文件和目录   
* 模块化架构设计，方便创造多种接口驱动的测试   

可以看出，它包含很多设计思想：   
测试报告与持续集成   
特定语法的用例，关键字模式   
测试用例集合与版本控制方法   


### [安装](https://github.com/robotframework/robotframework/blob/master/INSTALL.rst) ###


```
pip install robotframework
python -m pip install robotframework
```

验证一下安装是否成功：  

```
python -m robot --version
```

安装其他辅助库：  

```
pip install -U wxPython
pip install robotframework-ride
pip install robotframework-selenium2library
pip install robotframework-archivelibrary
pip install robotframework-SSHLibrary
pip install robotframework-ftplibrary
```

### quickstart ###

```
git clone https://github.com/robotframework/QuickStartGuide.git   
```



### 用例的写法 ###

robot framework 的用例管理使用文件来管理的。   
每一个测试用用例都是一个测试文件,每个文件可以包含多个测试用例(文件就是一个 testsuit)；  
测试集合( test suite ) 是测试用例的目录。测试集合下可包含子目录；   
测试用例的格式是 tabular format。  
测试数据支持 html, tsv, reST, txt, robot(plain text);    


先看一个简单的测试用例：    


```
# file: robot_example.robot
*** Settings ***
Documentation  一个简单的测试用例

*** Test Cases ***
StartCase
    Log     "Start TestCase"
```


```
python -m robot robot_example.bot
```

当前目录下生成测试报告: report.html, log.html   


![report report](/public/images/robot_report.png)  
![report log](/public/images/robot_log.png)  



**我们来看下用例写法的规范(test case syntax)**

*** Test Cases ***
测试用例名称
    关键字  参数    参数

关键字之后至少两个空格才是参数，关键字中可以有一个空格   


**为了增加灵活性，robot framework 还具有可编程功能**，你可以自己定制关键字，可以定义变量，实现循环，判断等等。   


```
# file: robot_example.robot
*** Settings ***
Suite Setup   Log     "开始测试"
Suite TearDown  Log     "结束测试"
Documentation  一个简单的测试用例

*** Test Cases ***
StartCase
    Log     "Start TestCase"
WHO
    ${name} =   Get User Name       myersguo
    Log   得到用户 ${name}
Loop
    :FOR    ${index}    IN RANGE    10
    \   Run Keyword If    ${index} == 5   Exit For Loop
    \   Log     ${index}
#自定义关键字
*** Keywords ***
Get User Name
    [Arguments]     ${arg}
    [Return]    ${arg}

```


上面的包括：自定义关键字，循环(FOR)，条件判断 (Run Keyword If),



### 原理探究 ###

robot\run.py

```
    def run_cli(arguments, exit=True):
        return RobotFramework().execute_cli(arguments, exit=exit)

class RobotFramework(Application):
...

robot\utils\application.py

class Application(object):
   ...
    def execute_cli(self, cli_arguments, exit=True):
        with self._logger:
            self._logger.info('%s %s' % (self._ap.name, self._ap.version)
            options, arguments = self._parse_arguments(cli_arguments)
            rc = self._execute(arguments, options)
        if exit:
            self._exit(rc)
        return rc 
    def _execute(self, arguments, options):
        try:
            rc = self.main(arguments, **options)
        except DataError as err:
            return self._report_error(err.message, help=True)
        except (KeyboardInterrupt, SystemExit):
            return self._report_error('Exception stopped by user.', rc=STOPPED_BY_USER) 
        except:
            error, details = get_error_details(execlude_robot_traces=False)
            return self._report_error('Unexpected error: %s' % error, details, rc=FRAMEWORK_ERROR)
        else:
            return rc, 0
#robot.py

class Robot(RobotFramework):
    def main(self, datasources, **options):
        settings = RobotSettings(options)
        LOGGER.register_console_logger(**settings.console_output_config)
        LOGGER.disable




```











### 参考资料 ###

[http://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html](http://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html)   
[https://www.slideshare.net/pekkaklarck/introduction-to-test-automation](https://www.slideshare.net/pekkaklarck/introduction-to-test-automation)  
[http://www.infoq.com/cn/news/2012/06/robot-author-suggest-autotest](http://www.infoq.com/cn/news/2012/06/robot-author-suggest-autotest)   
[https://github.com/robotframework/HowToWriteGoodTestCases/blob/master/HowToWriteGoodTestCases.rst](https://github.com/robotframework/HowToWriteGoodTestCases/blob/master/HowToWriteGoodTestCases.rst)  
[https://blog.codecentric.de/en/2016/01/robot-framework-tutorial-2016-keywords/](https://blog.codecentric.de/en/2016/01/robot-framework-tutorial-2016-keywords/)   

