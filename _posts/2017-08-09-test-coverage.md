---
layout: wp
title: 代码覆盖率实践
---

>把测试覆盖作为质量目标没有任何意义，而我们应该把它作为一种发现未被测试覆盖的代码的手段。  
>代码覆盖率的意义
>分析未覆盖部分的代码，从而反推在前期测试设计是否充分，没有覆盖到的代码是否是测试设计的盲点，为什么没有考虑到？需求/设计不够清晰，测试设计的理解有误，工程方法应用后的造成的策略性放弃等等，之后进行补充测试用例设计。
>检测出程序中的废代码，可以逆向反推在代码设计中思维混乱点，提醒设计/开发人员理清代码逻辑关系，提升代码质量。
>代码覆盖率高不能说明代码质量高，但是反过来看，代码覆盖率低，代码质量不会高到哪里去，可以作为测试自我审视的重要工具之一。


### 安装 ###

wget https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-6.5.zip   
unzip sonarqube-6.5.zip   
下载 sonar-scanner-cli ，并添加到环境变量中   
启动  
`./sonar.sh console`  

可以更改DB配置:   

```
mysql> create database sonar character set utf8 collate utf8_general_ci; 
mysql> create user 'sonar' identified by 'sonar';
mysql> GRANT ALL ON sonar.* TO 'sonar'@'%' IDENTIFIED BY 'sonar';
mysql> GRANT ALL ON sonar.* TO 'sonar'@'localhost' IDENTIFIED BY 'sonar';
mysql> FLUSH PRIVILEGES;
```

vi conf/sonar.properties   

```
sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
sonar.jdbc.url=jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance
```


安装 python coverage 工具:   

pip install coverage   

测试一下：   

```
#test.py
import unittest
import os

def load_tests(loader, standard_tests, pattern):
    # top level directory cached on loader instance
    this_dir = os.path.dirname(__file__)
    package_tests = loader.discover(start_dir=this_dir, pattern='test_*.py')
    standard_tests.addTests(package_tests)
    return standard_tests

if __name__ == '__main__':
    unittest.main()
```

```
coverage run --source=. test.py
coverage xml -i
coverage report -m
```

ok, 我们执行单元测试然后上报 sonarqube:   

```
sonar-scanner   -Dsonar.projectKey=test   -Dsonar.sources=.   -Dsonar.host.url=http://localhost:9000   -Dsonar.login=7dae49d1e5f558a56bfa61813c2bf01399b11e57 -Dsonar.python.coverage.reportPath=./coverage.xml
```

结果:   

访问 http://localhost:9000/ 来测试一下    

![sonar scanner coverage](/public/images/sonarqube.png)


### 覆盖率实践 ###

我们这里以 django 项目为例，来说明一下如何测试它的代码覆盖率。  

执行 `coverage run --source=. manage.py runserver --noreload`  

注意，我说 ** 注意** ，这里一定要加 --noreload, 不然 coverage 检测不到，为什么呢？  

ok,  你可以连接本地的服务，然后通过浏览器开始访问，进行测试。测试完毕后，停止服务，然后执行：    

```
coverage report -m 
coverage xml
```

生成覆盖率报告，然后通过 `sonar-scanner` 来扫描并上报项目的检测结果：   

```
sonar-scanner   -Dsonar.projectKey=platform   -Dsonar.sources=.   -Dsonar.host.url=http://localhost:9000   -Dsonar.login=7dae49d1e5f558a56bfa61813c2bf01399b11e57 -Dsonar.python.coverage.reportPath=./coverage.xml  -Dsonar.language="py"
```

最终的结果这里不展示截图了。   

### coverage 原理 ###

那么， python coverage 是怎么做的呢？   

下载 coverage 的代码.   

```
import re
import sys

from coverage.cmdline import main

if __name__ == '__main__':
    sys.argv[0] = re.sub(r'(-script\.pyw?|\.exe)?$', '', sys.argv[0])
    sys.exit(main())
```

coverage 的执行入口是 `main` 这里，我们看  `cmdline,py` 的 `main` 其实是调用了 `collector.py` 下的 start方法, 它怎么做的呢？   

```
tracer = pytracer
tracer.start()
...
sys.settrace(self._trace)

```


到这里，我们明白了， [sys.settrace](https://docs.python.org/2/library/sys.html#sys.settrace), 原来是调用了系统的 `debug`功能:   

>Set the system’s trace function, which allows you to implement a Python source code debugger in Python. The function is thread-specific; for a debugger to support multiple threads, it must be registered using settrace() for each thread being debugged.  

#### 延伸一下 ####   

coverage  是调用系统的 debug 功能，相当于插桩，而其他覆盖率工具，`jacoco` 也是通过注入插桩的方式实现的。    


