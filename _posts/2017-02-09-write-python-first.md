---
layout: wp
title: write python first
---

### 安装虚拟环境 ###

>
>[虚拟环境](http://docs.python-guide.org/en/latest/dev/virtualenvs/)解决不同的项目依赖版本不同的问题。保持本地全局包的干净。  
>它创建独立的(isolated)python环境。


```
sudo yum install python-devel
sudo pip install virtualenv
cd my_project
virtualenv -v -p /home/work/app/python27/bin/python env
touch requirements.txt
#激活当前项目虚拟环境
source env/bin/activate
```

可以获取本地环境的依赖：  

`pip freeze > requirements.txt`  



### 未完待续 ###





### 参考资料 ###

[http://docs.python-guide.org/en/latest/dev/virtualenvs/](http://docs.python-guide.org/en/latest/dev/virtualenvs/)  
[http://pythonguidecn.readthedocs.io/zh/latest/dev/virtualenvs.html](http://pythonguidecn.readthedocs.io/zh/latest/dev/virtualenvs.html)  


