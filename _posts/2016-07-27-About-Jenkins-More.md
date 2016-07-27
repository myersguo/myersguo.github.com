---
layout: wp
title: Jenkins As Deploy System
---

使用jenkins作为发布系统，要实现以下功能：   

1. 打包部署。  
2. 配置更新。   
3. 远程控制。   

项目打包及将源码编译成可运行的文件（PHP项目不需要此步)，编译后的文件部署到目标机(target)上。然后目标机执行启动脚本。   

如果项目依赖于某些配置或者系统依赖于配置文件（如vhost,property files, host）那么需要更新目标机上的配置文件。即，单发配置。   

发布配置后，可能需要重新启动目标机的某个服务，因此执行SHELL命令，作为发布执行的必备功能。   

以上，可以用jenkins来实现：  

SHELL脚本PUSH JENKINS MASTER的workspace到目标机上。    
文件更新插件负责创建配置文件，SHELL脚本将配置文件更新到远程服务器上。    
expect send bash command to remote SERVER.   



