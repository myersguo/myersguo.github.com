---
layout: wp
title: Android UI 指南续
comments: true
blogindex: true
---

# Android User Interface Guidelines  
（Android UI设计指南简译）    

接[上文](http://myersguo.github.io/2013/04/10/android_principles.html)   

# 一、设计模式(Patterns)  
[参考](http://developer.android.com/design/patterns/index.html)  

## 1. 手势操作  
Android允许用户通过手势操作APP提供的对象进行交互。Android提供了**7种触摸操作**。    
![Touch](http://developer.android.com/design/media/gesture_touch.png)  
**按**：触发默认的操作  
操作：按下-抬起  
![Long press](http://developer.android.com/design/media/gesture_longtouch.png)  
**长按**：允许长按显示上下文菜单；进入选择模式，可在一个视图里选择一到多项功能。  
操作：按下-等待-抬起   
![Swip](http://developer.android.com/design/media/gesture_swipe.png)  
**滑动**：移动内容到视图外或在同级视图间切换  
操作：按下-移动-抬起  
![Drag](http://developer.android.com/design/media/gesture_drag.png)  
**拖动**：在同一视图内重排数据或移动数据到容器中（比如Home页面的文件夹）  
操作：长按-移动-抬起  
![Double Touch](http://developer.android.com/design/media/gesture_doubletouch.png)  
**双击**：放大内容区；也做为选中文字菜单的另一种选择。  
操作：快速按两下  
![Pinch Open](http://developer.android.com/design/media/gesture_pinchopen.png)  
**挤开**：放大内容  
操作：两个手指按下，向外移动，抬起  
![Pinch close](http://developer.android.com/design/media/gesture_pinchclose.png)  
**挤住**：缩小内容  
操作：两个手指按下，向内移动，抬起  

## 2. APP结构   ##
![General Structure](http://developer.android.com/design/media/app_structure_overview.png)  
通常Android app包括顶层视图和详细/编辑视图(top level and detail/edit views)。若导航层级比较负责，在顶层和详细视图间使用分类视图(catergory views).  

### 顶层视图(Top level views)   
顶层视图是APP不同视图的内容区，可显示相同数据的不同显示或显示所有的功能模块。
顶层视图需要特别的关注。因为这是用户进入APP后看到的第一屏。  
设计时，问问自己：“用户最想要做的是什么？”，然后把他们放到开始屏中。  

1. 用内容装饰(Put content forward)  
需要APP将内容显示在首屏，而不仅仅只做导航。选择迷人的布局和合适的数据类型适应屏幕大小。  
![Google App Strore](http://developer.android.com/design/media/app_structure_market.png)   

2. 放置功能导航(Set up action bars for navigation and actions)  
![Gmail](http://developer.android.com/design/media/app_structure_gmail.png)  	
所有的屏幕都应放置功能按钮，提供始终如一的导航，将最重要的功能显示。注意事项：  
	>在工具栏显示APP图标和名称。  
	>若需要多个视图，添加视图控制按钮  
	>若APP允许用户创建内容，应考虑在第一屏加入该功能(consider making the content accessible right from the top level)
	>若APP内容可检索，在第一屏加入搜索按钮和图标  
		
3. (Create an identity for your app)  
创建独特的APP，通过数据的排列方式，交互方式。尤其是多媒体应用程序，尽量展示特别的布局，摆脱千篇一律的列表视图。  
![The 3D carousel](http://developer.android.com/design/media/app_structure_music_lndscp.png)  

### 分类视图(Category views)  
让你更输入了解数据
	
1. 使用TAB(Use tabs to combine category selection and data display)
当类别接近且数量不多时，使用可滚动的TAB，使数据保持在用户的实现中。可切换的TAB最好不要超过5-7个。  
![SWIFT TABS](http://developer.android.com/design/media/app_structure_scrolltabs.png)  
若分类不类似，使用固定的导航，确保所有的视图可在视图中切换。  
![FIXED TABLES](http://developer.android.com/design/media/app_structure_fixedtabs.png)   

2. 使用快速导航(Allow cutting through hierarchies)  
在数据项，允许通过下拉或分割项菜单(drop-downs or split list items)显示列表或表格视图进行快捷操作。     
![drop down list](http://developer.android.com/design/media/app_structure_shortcut_on_item.png)  
上图中，可直接播放音乐，而不用进入详细信息页面。   

3. (Acting upon multiple data items)  
当在详细页允许用户进行某种操作（如删除）时，考虑在分类列表页允许数据选中。    

### 详细视图(Detail/edit view)    
数据展示    
	
1. 布局(Layout)   
考虑用户关系的详细信息组织布局。   

2. Lights-out mode  
沉浸式的内容体验（如多媒体，游戏）是全屏的最佳方式。但这不意味着不用提供其他的操作，如分享，评论，搜索等。
如果用户长时间没有交互动作，自动让工具栏变暗，系统UI进入情景模式——我们称之为熄灯模式（Light-out mode）。  
![Google Books'](http://developer.android.com/design/media/app_structure_book_detail_page_flip.png)  

3. 语序详细视图切换(Make navigation between detail views efficient）
![Gmail using swipe views to navigate from detail view to detail view](http://developer.android.com/design/media/app_structure_gmail_swipe.png)  
在用户需要连续查看内容项时，使用swipe views或 thumbnail view controls技术，允许用户在多个内容之间切换查看。  


## 3. 导航  
一致性导航是整个用户体验的基本要求。  

### 顶部与返回（up vs back）   
顶部按钮用来在不同层级视图见导航。比如从界面A点击某项后进入界面B，则B应该在顶部放置返回到A的按钮。
app的HOME页不应该显示一个返回按钮(<)。  
系统的返回按钮是按照显示的时间顺序返回，而不是关系界面间的层级关系，因此系统的返回可能返回到系统HOME页或其他APP页面。       
![up vs back](http://developer.android.com/design/media/navigation_up_vs_back_gmail.png)  
除了在视图间切换，返回按钮还可以：取消浮动的窗口；取消上下文菜单(contextual action bars)；隐藏IME KeyBoard.  

### APP内部导航  
####多入口导航(Navigating to screens with multiple entry points)  
有时，可以从多个点进入视图，这时，顶部返回按钮应指示的是参考界面（如类别界面）。  
#### 同一界面内的变化(Changing view within a screen)
在一个界面内更改内容，不更改顶部和返回功能的作用，即只改变视图(view)不创建新的导航历史。  
如：  

+ 使用TABS或左右滑动切换视图    
+ 下拉滑动  
+ 过滤/排序列表
+ 改变字符显示方式（如放大，缩小）  

#### 在同级界面间导航 (Navigating between sibling screens)    
当APP支持从列表进入详细界面时，通常支持从某项详细页进入另一项详细页。  
![Navigating between sibling screens](http://developer.android.com/design/media/navigation_between_siblings_gmail.png)   
仔细看上图中改变视图不改变顶部和返回功能(切换详情页，按返回同样返回列表页)。  
![navigation_between_siblings_market](http://developer.android.com/design/media/navigation_between_siblings_market1.png)  
上图中，详情页的切换是通过相关项(related detail)进入，不是由联系的列表(referringlist)进入。因此，会创建导航历史，引起返回按钮变化。  
![navigation_between_siblings_market2](http://developer.android.com/design/media/navigation_between_siblings_market2.png)  
如上图，顶部返回按钮应该能够随着详情页而具体的变化。  


### 从小工具和提醒栏进入（Navigation into Your App via Home Screen Widgets and Notifications）
从这两种情况进入时，UP按钮应该考虑：  

+ 如果目的界面一般从某个特殊界面进入，UP应该返回至那个界面。  
+ 否则，UP应该返回至APP的HOME页面。  

系统的返回按钮页应该让导航看上去更加具有预见性——在返回栈中插入返回的路径。如下GMAIL例子：   
![Gmail](http://developer.android.com/design/media/navigation_from_outside_back.png)  

#### 间接提醒(Indirect notifications)  
当app需要提供多个事件提醒时，可以提供一种中间提醒页(interstitial screen)。这个页面简单描述事件，并提供进入详细页的路径，这种提醒叫做间接提醒( indirect notifications)  
不同于标准的直接提醒，间接提醒的返回会返回到用户触发事件前的页面。但一旦用户进入详情页，顶部和底部返回就和上面描述的标准提醒的功能一致了。  
![navigation_indirect_notification](http://developer.android.com/design/media/navigation_indirect_notification.png)  

#### 弹出式提醒(Pop-up notifications)  
弹出式提醒不通过提醒下拉(the notification drawer)直接出现在用户眼前。这仅仅被用在那些需要实时的，必须的事件上。
例如，实时通话。弹出式提醒和间接提醒的返回情况是一致的。  

### 应用间导航(Navigation Between Apps)  
Android系统允许APP之间的调用。如一个APP可能需要照相，可调用相机，拍照后返回调用的APP。这对开发者具有巨大的好处——可以
充分利用他人的成果；而用户只需享受这无缝、一致的体验即可。  
为了更好的理解"app-to-app"导航，需要理解android框架行为：  
#### 活动，任务和意图（Activities, tasks, and intents）  
**activities**定了了程序的界面和所有功能.应用是由你创造的和其他app创建的activities组成。     
**任务**是用户为完成某一目标而设定的一系列activities。一个简单的任务可使用一个app的activities，也可选择其他APP的。  
**意图**是app发送给其他app的信号，来请求某个动作。意图可指定相应的APP。比如，打开网页的意图，用户可选择可相应该意图的多个APP，并指定默认APP来相应该意图。  

![navigation_between_apps](http://developer.android.com/design/media/navigation_between_apps_inward.png)    
如上图，启动Play Store app后**任务(task)A**创建，通过app内导航进入详细页，用户仍在相同task内，触发分享提示操作，对话
框列出注册了相应该意图(intent)的APP。当用户通过Gmail分享时，Gmail的activity被添加进任务A，没有新的任务产生。
从当前活动中点击返回按钮可经过一系列返回到任务的系列操作中。  
![navigation_between_apps_up](http://developer.android.com/design/media/navigation_between_apps_up.png)  
如上图，当触发了添加进任务的activity的顶部菜单时，表明用户想要进入Gmail,这时Gmail的conversation list activity出现了，新的任务B被创建。新的任务一般根植于Home界面，所以触发返回将从conversation list返回HOME页。  

顶部和返回的功能实现，请参考：[Implementing Ancestral Navigation](http://developer.android.com/training/implementing-navigation/ancestral.html) and [Implementing Temporal Navigation](http://developer.android.com/training/implementing-navigation/temporal.html).   

（未完待续）
