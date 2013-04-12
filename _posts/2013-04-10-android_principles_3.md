---
layout: wp
title: Android UI 指南续2
comments: true
blogindex: true
---
接上文  
## 工具栏（Action Bar）  

工具栏提供了以下主要功能：  

+ 突出重要的功能，使其易操作  
+ 提供一致性导航和视图间切换  
+ 整理不常用的功能减少混乱?(Reduces clutter by providing an action overflow for rarely used actions.)   
+ 为APP提供特定的空间  


### 基本构成    
General Organization  
![action_bar_basics](http://developer.android.com/design/media/action_bar_basics.png)  

1. 应用图标  APP icon  
在首页显示APP的logo图标（也可以是其他图标），当不在首页时，务必在LOGO左侧显示返回标志(<)。
2. 视图控制 View control   
当应用程序需要让数据在不同视图下显示时，通过下拉菜单或者TAB进行视图切换。若应用不支持不同视图，也可以
使用它来显示非交互信息，如应用标题，长的简洁等。    
3. 功能按钮 (Action buttons)  
显示APP的最重要的功能，当功能项超过工具栏大小时将被自动移出。长按一个图标显示功能的名字。  
4. 功能溢出 (Action overflow)    
将不常用的功能放置到 action overflow里。

### 适应屏幕旋转和不同大小  
Adapting to Rotation and Different Screen Sizes  
在设计APP时应考虑旋转屏幕和不同大小的情况。  
可以通过"split action bars"方法解决宽屏的问题。如下图在横屏时将action bar合并：  
![action_bar_pattern_rotation](http://developer.android.com/design/media/action_bar_pattern_rotation.png)  

### Split Action Bars的布局  
Layout Considerations for Split Action Bars  
当需要通过多个action bar来分割时，通常有三种工具栏可选择：  
![action_bar_pattern_considerations](http://developer.android.com/design/media/action_bar_pattern_considerations.png)  
1 主工具栏(Main action bar)  ： 可提供顶部返回功能。  
2 顶部工具栏(Top action bar) :允许用户在不同视图间切换  
3 底部工具栏(Bottom bar)  :显示action 和 action overflow时用它。

### Contextual Action Bars(CAB)   
CAB是当用户在选择文本和列表项时的数据时显示的临时工具栏。  
![CAB](http://developer.android.com/design/media/action_bar_cab.png)  
在CAB，用户touch选中其他项，触发选中后的功能（如移除），之后CAB将消失；  

### View Controls  

如果APP数据用不同的视图显示，可以采用三种方法：标签(TABS),下拉菜单（SPINNERS）,抽屉式菜单（DRAWERS）  

当你希望用户快速地切换视图，尽可能多的显示视图，就选择TAB。  
#### 固定标签 Fixed tabs 
固定标签总是在屏幕上，不能被移动。当屏幕反转时，在主工具栏的TAB可以移动到顶部工具栏。  
![action_bar_pattern_default_tabs](http://developer.android.com/design/media/action_bar_pattern_default_tabs.png)  
固定标签支持2-3个APP视图间快速切换，它应该允许用户用左右滑动切换视图。  

#### 可移动标签 Scrollable tabs  
可移动标签总是放置在工具栏的整个空间上，当前活动的视图的放置在中间。它通过左右滑动移动标签进视图中。
当你的视图较多时，选择scrollable tabs.   
<pre>
<div class="layout-content-col span-7"><video width="400" class="with-shadow play-on-hover" autoplay="">
<source src="http://developer.android.com/design/media/tabs_scrolly.mp4" type="video/mp4">
<source src="http://developer.android.com/design/media/tabs_scrolly.webm" type="video/webm">
<source src="http://developer.android.com/design/media/tabs_scrolly.ogv" type="video/ogg">
</video>
</div></pre>



#### Spinners  
![spinner](http://developer.android.com/design/media/action_bar_pattern_spinner.png)  
spinner是一个下拉菜单。当你想要让某个视图总是占用屏幕；用户的需要切换视图显示同一数据，选择spinners。  

#### Drawers  
![drawer](http://developer.android.com/design/media/actionbar_drawer.png)  
抽屉式菜单是一个滑动-消失的菜单。用户可以用触摸顶部APP LOGO图标显示菜单，也可以在左边缘向右滑动显示，从drawer右边滑动让之消失。因为用户一般从顶部LOGO处打开菜单，因此，只有在顶层界面才适合使用它。当你想要让某个视图总是占用屏幕或菜单中间关联不大，而需要一起导航时选用抽屉菜单。  

### 工具按钮 Action buttons  
![](http://developer.android.com/design/media/action_bar_pattern_action_icons.png)
工具按钮包括了APP的最重要、最长用的功能项。为有效利用系统共建，系统会隐藏超过工具栏的功能项。记住：不要把无用的功能放到工具栏，我们使用满足FIT原则来选择功能项。  
**F-Frequent**：是否在一屏内使用7-10次的，是否连续使用多次，不使用是否会比较繁琐？  
**I—Important**:功能是否很酷，是个卖点？是否不常用是选择它会更加方便？  
**T-Typical**:是否在类似的APP中也常见？溢出屏幕后用户是否会感到奇怪？  
预定义了一些常见图标供[下载](http://developer.android.com/downloads/design/Android_Design_Icons_20120814.zip)选择。  

### Action overflow 
![action overflow](http://developer.android.com/design/media/action_bar_pattern_overflow.png)  
Action overflow提供那些不常用的工具项，它只会出现在没有物理MENU键的手机中，有MENU键的手机直接按MENU键显示overflow菜单。  

在Action bar上应该显示多少菜单项呢？  

+ 工具项不应该占据主工具栏的50%；底部工具栏可以全部使用。  
+ 屏幕宽度像素大小决定工具项个数。
	- <360 dp    = 2 icons
	- 360—499 dp = 3 icons
	- 500-599 dp = 4 icons
	- >600 dp    = 5 icons
  
![action bar tables](http://developer.android.com/design/media/action_bar_pattern_table.png)  
上图 o代表action, =代表overflow icon  

### share data  
当你的APP允许分享数据，如图片，电影等在工具栏使用分享按钮。分享项按使用频率在下拉菜单中排序，在其旁显示最长用项。  
![share button](http://developer.android.com/design/media/action_bar_pattern_share_pack.png)   


## 多面板布局（Multi-pane Layouts）  
设计app时应考虑屏幕大小和类型，确保APP在不同尺寸大小的APP间达到外观，布局上的平衡。  
Panels是实现屏幕适应性的有效方法。  它允许你绑定多个视图到一个混合视图中，当屏幕旋转到垂直方向，水平方向的面板被分割开适应小的屏幕。  

### 绑定多个视图到一个  
Combining Multiple Views Into One  
在小屏设备上通常将内容分为：主表格(master grid)和详细视图(detail view)，点击主表格中的一项进入详细视图。   
![multipane views](http://developer.android.com/design/media/multipane_views.png)  
因为平板的有更大屏幕，因此可以使用panel来联合相关的list和对应详细页到一个compound view中。  
![multipane_view_tablet](http://developer.android.com/design/media/multipane_view_tablet.png)  
通常使用右侧显示详细页，左侧显示列表。确保左侧列表选中时，有一定提示（如显示背景颜色等）表示左右对应关系。  

### Compound Views and Orientation Changes  
使用Compound Views时应考虑方向旋转的情况。当旋转屏幕使，应尽量将视图分割，可采用以下技术使功能保持不变。  

#### Stretch/compress     
伸缩。将左侧面板的列宽做适当伸缩，以适应布局。     
![stretch pane](http://developer.android.com/design/media/multipane_stretch.png)     
#### Stack  
堆放。重排panel适应屏幕。   
![stack pane](http://developer.android.com/design/media/multipane_stack.png)
#### Expand/collapse   
扩大和折叠。旋转屏幕时，左侧面板只显示重要的信息。   
![expand](http://developer.android.com/design/media/multipane_expand.png)   
#### Show/hide   
如果屏幕无法显示所有panel 信息，只显示右侧view，在TOP ICON中加返回到上级的按钮。   


## 切换视图（Swipe Views）   

有效的导航是一个好的设计的基础。纵然现在APP设计多是分层的。但水平导航也可以轻松替代垂直的分层，让数据间的访问更快速，更舒适。  

### Swiping Between Detail Views  
APP的数据通常按照主/从(master/detail)关系组织。用户通过数据列表进入详情页面。  
 ![swip views](http://developer.android.com/design/media/swipe_views.png)  
Master (left) and detail (right) views.  
在手机中，master和detail界面是分开的。因此用户不得不在看到detail之后重新返回master界面重新选择一项后进入detail，为避免这种重复动作(pogo-sticking)，是用户可连续的查看detail页面，可使用swipe手势在detail view间导航。  
![swip view](http://developer.android.com/design/media/swipe_views2.png)  
当视图内容超过屏幕宽度时，确保用户的swipe动作在视图内；当视图内容load完毕，新的swipe动作可以开始。另外，边缘swipe(edge swipes)进行快速导航是支持的。  

### swipe between tabs  
如果APP使用了TAB工具栏，使用swipe在不同TAB间切换  
<pre>
<div class="framed-galaxynexus-port-span-5">
<video class="play-on-hover" autoplay="">
<source src="http://developer.android.com/design/media/swipe_tabs.mp4" type="video/mp4">
<source src="http://developer.android.com/design/media/swipe_tabs.webm" type="video/webm">
<source src="http://developer.android.com/design/media/swipe_tabs.ogv" type="video/ogg">
</video>
</div>
</pre>

(to be continued...)  
