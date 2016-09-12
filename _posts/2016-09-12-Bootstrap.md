---
layout: wp
title: 大话bootstrap
---

bootstrap4.0截止到今天(2016.09.12)仍在4.0 alpha阶段，增加了工具类的支持(相比它，uikit早就[支持了](https://github.com/uikit/uikit/blob/develop/src/less/core/utility.less#L33))。我们来看一下：  

### 工具类 ###

#### 空间类 ####  

命名方式： {property}-{sides}-{size}  
其中，property为  
m——代表margin  
p——代表padding  

sides为方向的首字母：  
t——代表top,  b代表bottom, l: left, r: right; x: 代表 left and right; y: 上下； a : all   

size: 代表大小：  
1: 1rem; 2: 1.5rem;  3: 3rem;  


#### 文字 #### 

命名方式： text-property-sides  
eg: text-xs-left: 表示extra small(<544px)下左对齐。  
text-sm-left: 标识small(<768px)下左对齐；类似的:text-md-right:middle(<992px)下右对齐。   

##### 大小写转化#####
 
text-lowercase: 小写显示；  
text-uppercase: 大写显示；  
text-capitalize: 首字母大写。   

##### 文字背景等 #####  

text-muted:  
text-primary:  
text-success:  
text-info:  
text-warning:  
text-danger:  

font-weight-bold,font-weight-normal,font-italic  

bg-primary,bg-success, bg-info, bg-warning, bg-danger, bg-inverse  


##### 位置  ####  


```
<div class="pull-xs-left">Float left on all viewport sizes</div><br>
<div class="pull-xs-right">Float right on all viewport sizes</div><br>
<div class="pull-xs-none">Don't float on all viewport sizes</div><br>

<div class="pull-sm-left">Float left on viewports sized SM (small) or wider</div><br>
<div class="pull-md-left">Float left on viewports sized MD (medium) or wider</div><br>
<div class="pull-lg-left">Float left on viewports sized LG (large) or wider</div><br>
<div class="pull-xl-left">Float left on viewports sized XL (extra-large) or wider</div><br>

```


参考：  
bootstrap grid:[http://v4-alpha.getbootstrap.com/layout/grid/](http://v4-alpha.getbootstrap.com/layout/grid/)  
bootstrap组件：[http://v4-alpha.getbootstrap.com/components/utilities/](http://v4-alpha.getbootstrap.com/components/utilities/)
