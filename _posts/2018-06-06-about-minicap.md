---
title: minicap 一瞥
layout: wp
comments: true
---

#### 本地环境 ####

android sdk, android ndk-build tools(可以直接通过 android sdk manager安装)   

设置环境变量:  

```
#ANDROID
export ANDROID_HOME='/Users/guoliangyong/Library/Android/sdk'
export PATH=$PATH:$ANDROID_HOME/platform-tools:$ANDROID_HOME/build-tools/26.0.0:$ANDROID_HOME/ndk-bundle
```

下载源码：   

```
git clone https://github.com/openstf/minicap.git
cd minicap && git submodule init && git submodule update
./run.sh autosize
```


#### 源码一瞥 ####


