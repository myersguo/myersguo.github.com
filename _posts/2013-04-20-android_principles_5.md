---
layout: wp
title: Android UI 指南续5
comments: true
blogindex: false
---
接上文    
## Backwards Compatibility  兼容性  
android 3.0的变化包括：  

+ 不使用硬件按钮导航(Back,Menu,Search,Home),而使用虚拟导航控件(Back,Home,Recents)  
+ 在操作栏固定菜单  

android 4.0把这些给平板的变化带入手机平台。  

### Adapting Android 4.0 to Older Hardware and Apps  
使4.0兼容3.0系统  
#### Phones with virtual navigation controls  
虚拟导航控制的手机 
3.0及以后版本的APP在操作栏显示功能项。当功能项过多或不重要时，放到更多菜单中。  
![virtual nav](http://developer.android.com/design/media/compatibility_virtual_nav.png)    

#### Phones with physical navigation keys  
物理按键的手机  
有物理按键的手机不在手机屏幕下显示虚拟导航。相反，overflow(更多）允许从menu键显示。菜单样式同上，但显示在底部。  
![physical buttons](http://developer.android.com/design/media/compatibility_physical_buttons.png)  

#### Legacy apps on phones with virtual navigation controls  
使用虚拟控件控制旧应用。当你运营基于2.3或更早版本的应用时，虚拟控件的更多选项相当于物理硬件的menu菜单，按下将弹出menu项。  

## Accessibility 可用性  
android的一个使命就是整理全世界的信息并让它普遍易用和有用。Accessibility是衡量产品是否被各种可能的使用方式成功应用的标准。我们的远景之一就是让所有人包括哪些视力、听力等有缺陷的人能够都能使用。  
[Universal design](https://www.google.com/#hl=en&q=universal+design&fp=1)是指产品设计为所有人可用。android 设计模式参照universal design模式。按照模式标准将帮助你的APP满足基本的可用标准。  

### Android's Accessibility Tools   
Android提供了一些可用性工具。  

+ TalkBack 是预安装的屏幕阅读服务。通过返回声音来标明应用操作（如启动，事件，通知等）的结果。  
+ Explore by Touch，与TalkBack共同工作，允许你通过触摸屏幕，使用feedback进行交互。  
+ Accessibility settings 允许修改设备的显示和声音选项，比如增加字体大小，改变文字的阅读速度等。  

### Guidelines  指导  
android的设计原则中“让我知道我在哪儿？”是可用性的关注点。当用户通过APP浏览时，他们需要得到反馈，知道他们在哪里。合理的信息架构设计非常重要。大多数用户通过交互的视觉和触觉反馈了解所在位置(标签，颜色，图标，反馈)，视觉缺陷用户通过文字描述和放大来增加可视度。  

在设计APP时，要考虑标签和符号可以通过语音控制。考虑用户能够通过视觉和听觉进行交互。像APP其他设计方面一样，APP结构应该简单、高雅、健壮（simple,elegant,robust）。下面是一些指导准则：  

#### Make navigation intuitive  
直观的导航。良好的设计，清晰的任务流程，最小的导航步骤，确保这些任务通过控件完成。  

#### Use recommended touch target sizes  
使用推荐的大小设置。48dp是推荐的屏幕触控大小。  

#### Label visual UI elements meaningfully  
视觉元素的标签的意义。有一些标签名没有名字（这些组件可能是buttons,icons,tabs with icons,icons with state）。需要依据contentDescreption属性来设置标签。    
![label visual UI elements meanningfully](http://developer.android.com/design/media/accessibility_contentdesc.png)    
1.group 群组。 2.all contacts 联系人 3.favorites 收藏 4.search 搜索。 5.action overflow button. 更多菜单。
6.when starred:remove from favorites;when not starred:add to favorites,加星控制 7.action overflow。更多菜单。 8 text message 文本内容 9.video chat，视频  

#### Provide alternatives to affordances that time out  
提供时间到后的情境支持。APP的图标和控件可能在屏幕不动后一段时间后消失。例如，播放视频5 seconds后播放按钮将逐渐消失。  
因为talkback工作在控件获得焦点时，因此，当它们消失后，用户就不能使用talkback服务。因此，要确保，高优先级的任务流不依赖于time out controls.如果控件允许一个重要的动作，确保用户能够重新打开控件。你也可选择不使time-out controls消失。   

#### Use standard framework controls or enable TalkBack for custom controls  
使用标准的框架控件或允许talkback支持的控件  
标准的android框架控件和accessibility服务一起工作，并有上下文描述。  
一个常被忽视的控件问题是字体的大小。用户能够打开系统的大字设置，让用户在APP中使用系统默认的字体大小选择。允许系统字体大小应用于APP中。   
做好大字体的空间适配。  

### Pure Android 真正的Android  
大多数开发者的APP是跨平台的。但确保你的APP在不同的不太遵守不同的规则。一种平台的设计思路在另一种平台不一定使用。“design once,ship anywhere”可能在开始节省了时间，但是有让用户原理的风险。考虑一下设计思路避免常见错误和缺陷：  
#### Don't mimic UI elements from other platforms  
不要照搬其他平台的UI。各大平台提供了各自风格的控件。如，一些平台鼓励使用圆角button，另一些鼓励标题栏使用渐变。虽然目的一致，但设计功能差别很大。当你设计android app时不要照搬其他平台的控件。      
![migrating ui elements](http://developer.android.com/design/media/migrating_ui_elements.png)   
图为Android, iOS and Windows Phone 7控件。  

#### Don't carry over platform-specific icons  
平台通常为常用功能提供了图标选择，如分享，新建，删除等。当你在android平台设计时，请使用android的图标。   
![migrating icons](http://developer.android.com/design/media/migrating_icons.png)    
图为Android, iOS and Windows Phone 7的图标。  

#### Don't use bottom tab bars  
不要使用底部标签栏。其他平台通过底部标签栏切换视图。android的惯例是，在顶部放置放置标签栏切换视图，在底部可以放置一些工具栏。  
你应该像其他app一样避免非一致的体验，避免混淆“actions and view switching”。  
![tab bars](http://developer.android.com/design/media/migrating_ios_dialers.png)  

#### Don't hardcode links to other apps  
不要使用硬链接到其他APP。在需要使用其他APP完成某种功能如分享，浏览网页等时，不要使用hard-coded到特定APP，而使用android自带的intent API加载activity chooser，让用户来选择使用哪个APP。  
![activity chooser](http://developer.android.com/design/media/migrating_intents.png)  

#### Don't use labeled back buttons on action bars  
不要在操作栏使用带标题的返回按钮。android使用操作栏的app icon来返回上一级，使用navigation bar返回上一个记录屏幕。  
![back button](http://developer.android.com/design/media/migrating_ios_galleries.png)  
Android action bar with up caret vs. iOS labeled "Back" button.  

#### Don't use right-pointing carets on line items  
列表项中不使用向右箭头。其他平台使用right-pointing carets来表明有下一级；android 不适用此标志，避免用户的误猜。  
![right-pointing carets](http://developer.android.com/design/media/migrating_ios_settings.png)  
Android settings without right-pointing carets in line items vs. iOS settings.

  

  




