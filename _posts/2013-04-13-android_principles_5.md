---
layout: wp
title: Android UI 指南续4
comments: true
blogindex: false
---
接上文  

## Widgets  
小工具是HOME屏的自定义支持功能。用户可以一眼("at-a-glance")观察到APP的数据。  
###Widget types
首先思考一下，你想要什么类型的工具：  
####Information widgets  
信息工具通常显示对用户有用的，并随着时间而关注的重要信息元素。比如，天气、时钟、体育比赛等。按下信息组件通常打开APP显示详细的组件信息。  
![weather widget](http://developer.android.com/design/media/widgets_info.png)
####Collection widgets  
收集组件，显示相同类型的信息，如相册APP的多张图片，新闻APP的多篇文章，通信工具的邮件/信息列表等。收集组件通常关注两种情况：收集信息，打开信息详情页。  
![collection widget](http://developer.android.com/design/media/widgets_collection_gmail.png)  
####Control widgets  
控件APP通常用来在APP外直接操作APP，就像是远程操纵一样。例如，音乐APP widgets可以不打开APP，进行音乐播放、暂停、跳过等。  
与这些widget可能不会打开详情页。 
![control widget](http://developer.android.com/design/media/widgets_control.png)  
#### Hybrid widgets  
混合widget。以某一种为中心，其他多种widget混合的类型。如下图，音乐播放器widget，主要是一个control widget，但是，它也显示当前播放的信息。将control widget和information widget结合是很方便的。  
![hybrid widget](http://developer.android.com/design/media/widgets_hybrid.png)  
### Widget limitations  
在设计APP widget时必须清楚，widget不是"mini-app"，它有很多限制。  
####Gestures  
widget和home屏是在一起，这限制了很多手势支持（如，app可能支持横向导航，但这已经被HOME屏占用了）。所以，widget只支持  

+ 点击 Touch  
+ 竖向滑动 Vertical swipe

![widgets getures](http://developer.android.com/design/media/widgets_gestures.png)  

#### Elements  
由于手势限制，一个控件(building blocks)不能被使用。  

### 设计规范  Design guidelines  
####Widget content  
widget是APP把最新，最有趣的内容展示的设计。就像报纸的广告页一样，它应该考虑将信息内容浓缩，并可以通过到到达APP的信息详情处。好比widget是快餐，而app是丰富的一顿饭。请确保app显示比widget更多的信息。    
####Widget navigation
除了提供信息外，widget也可以提供一些APP中的功能，如：  

+ 创造内容。比如写一个文档，发送消息  
+ 打开APP程序：除了提供到信息详情页，提供直接到APP HOME页的导航就不比打开HOME页再打开APP。用APP ICON显示在widget，这也可以标明widget属于哪一个APP。  
  
####Widget resizing
在版本3.1，android的widget可以调整大小，它允许用户调整widget的宽、高。你可以决定是否任意调整大小，还是只允许某个方向。你不需给固定大小的widget提供调节能力。   
当通过调整大小能够满足一下，最好提供调节功能：  

+ 看到更多的信息  
+ 可以调整widget的布局  

调节的策略依赖于widget的类型。list 或者 grid的搜集型通常直接调节大小即可，这样只会扩大其滚动的区域大小。infromation widget就需要一点经验，你必须通过resize operation让widget的内容和大小适应。  
![widget resize](http://developer.android.com/design/media/widgets_resizing02.png)  
上图示例，用户通过放大显示更多信息。不同的大小决定显示内容的不同，小部件显示最重要的，大部件显示更丰富的信息。  
####Layout considerations
布局考虑。  
通过不同大小的设备来设计widget的布局是负责的，请以下考虑以下几点：  

+ 不同设备具有不同的大小、数量、空间，因此设计widget适应多种设备是必须的考虑的。  
+ 事实上，系统调整大小的单位是dp，因此，实施resize策略时，通过"size buckets"而不是表格尺寸更好。  

####Widget configuration  
有时候，widget需要经过配置之后才可以使用。如Gmail需要提供用户帐号，相片widget需要指定相册。  
widget的配置需要在被放置到home页时就显示，保持widget配置简洁，只显示2-3个配置项。不要在全屏配置，使用系统对话框，哪怕需要多个。  
一旦设置之后，不需要被再次设置，因此不需要再次显示setup或configuration项。  
![widget configure](http://developer.android.com/design/media/widgets_config.png)  

## Setting  
设置，可以让用户控制APP的行为，这样：  

+  APP不必在使用过程中多次打扰用户。
+  用户感觉自己能够控制APP。  

### Flow and Structure  
#### Provide access to Settings in the action overflow   
设置不应该突出显示，因为它不经常被使用。即使在action bar上有多余空间，页不要放置setting。保持setting显示在更多(overflow)中，放在所有项的后面，help的前面。
![setting overflow](http://developer.android.com/design/media/settings_overflow.png)   
#### Avoid the temptation to make everything a setting   
由于setting是一个指导项，不论有多少项都不会出现在主UI界面。它看起来像个堆积杂货的地方，你不会话费太多的时间，但并不意味着你可以东西都往里放。用户仍然对设置抱有期待。因此在进行设置设计时应考虑：  
![setting flowchart](http://developer.android.com/design/media/settings_flowchart.png)  
即将那些非文本介绍的、不经常改变的、不同用户有不同选择的配置成设置。   
#### If you still have lots of settings, group related settings together    
人类能够HOLD住的项是7±2个。如果你要放置超过10项，用户就很难浏览，理解，设置。  
你可以使用把设置项分成组。

未完待续中...
### Design Patterns  
#### Checkbox  
#### Multiple choice  
#### Slider   
#### Date/time  
#### Subscreen navigation  
#### List subscreen  
#### Master on/off switch  
#### Individual on/off switch  
#### Dependency  
### Defaults  
### Writing Guidelines  
#### Label clearly and concisely  
#### Secondary text below is for status, not description…  
#### …unless it's a checkbox setting  
#### Writing examples  



