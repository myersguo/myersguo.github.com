---
layout: wp
title: Android UI 指南
comments: true
---

# Android User Interface Guidelines（Android UI设计指南简译）    

Android用户体验团队写了关于Android开发的交互设计和视觉设计规范，为设计更优秀的应用提供了设计类型，设计方法。  

一、[设计概览](http://developer.android.com/design/index.html)   
Android设计的三个目标是： 
  
+ 赏心悦目（Enchant me)：app不只追求外观漂亮，更追求多层面的优秀。如运行的流畅，布局和排版的简洁有用。力争做到简洁、高效、美观。  
	
	1. 眼前一亮（Delight me in surprising ways)
	精美的外观，动画，恰到好处的声音。  
	![Dlight me in surprising ways](http://developer.android.com/design/media/principles_delight.png)

	2. 拟物化功能(Real objects are more fun than buttons and menus),拟物的点击比才能更容易理解。  
	![Real objects are more fun than buttons and menus](http://developer.android.com/design/media/principles_real_objects.png)  
	
	3. 多种选择(Le me make it mine)：用户喜欢装扮自己    
	 ![get to know me](http://developer.android.com/design/media/principles_make_it_mine.png) 

	4. 理解用户（Get to konw me):给用户选择，简化用户操作    
	![Get to know me](http://developer.android.com/design/media/principles_get_to_know_me.png)  

+ 简单明了(Simplify my life)：app可让生活更加简单化。打开app的第一眼就能展现它的主要功能。将繁琐的过程简化，让不同年龄和文化的人都能轻松掌握，而不会被繁多的选择迷惑。  

	1. 简化语句(Keep it brief)  
	![Keep it brief](http://developer.android.com/design/media/principles_keep_it_brief.png)  

	2. 用图表达(Pictures are faster than words)  
	![Pictures are faster than words](http://developer.android.com/design/media/principles_pictures.png)  

	3. 自作主张(Decide for me but let me have the final say)替用户想好可能的操作    
	![Decide for me but let me have the final say](http://developer.android.com/design/media/principles_decide_for_me.png)    

	4. 善于隐藏(Only show what I need when I need it)将需要的功能隐藏，但让用户知道在那里  
	![Only show what I need when I need it](http://developer.android.com/design/media/principles_information_when_need_it.png)  

	5. 准确的导航(I should always know where I am)让用户知道自己在那里，如何返回      
	![I should always know where I am](http://developer.android.com/design/media/principles_navigation.png)  

	6. 保存重要信息(Never lose my stuff)及时保存和同步用户的创造/配置内容，不要丢失  
	![never lose my stuff](http://developer.android.com/design/media/principles_never_lose_stuff.png)  

	7. 视觉和功能一致(If it looks the same, it should act the same):一致的视觉表现，其功能也应相似  
	![it looks the save,it should act the same](http://developer.android.com/design/media/principles_looks_same.png)  

	8. 非诚勿扰(Only interrupt me if it's important)除非必要，慎用提醒/打扰用户  
	![Only interrupt me if it's important](http://developer.android.com/design/media/principles_important_interruption.png)  


+ 令我痴狂(Make me amazing)：app不止步于有用，而且激起人发觉它的欲望，并展现出它的与众不同、华丽优美。     
	
	1. 藏有技巧(Give me tricks that work everywhere)让用户挖掘一些表面看不到的使用技巧，比如快捷键，手势操作等。  
	![Give me tricks that work everywhere](http://developer.android.com/design/media/principles_tricks.png)
	
	2. 温馨提示(It's not my fault)清楚地告诉用户出错的补救方式。  
	![It's not my fault](http://developer.android.com/design/media/principles_error.png)  

	3. 鼓舞我心(Sprinkle encouragement):给用户鼓励，用用户更有成就感  
	  ![Sprinkle encouragement](http://developer.android.com/design/media/principles_sprinkle_encouragement.png)  

	4. 屌丝的逆袭(Do the heavy lifting for me)将复杂的功能替用户实现。如，照片自动曝光，照片滤镜等。  
	![Do the heavy lifting for me](http://developer.android.com/design/media/principles_heavy_lifting.png)  

	5. 重点突出(Make important things fast),比如照相最重要的功能是按快门，音乐是播放与暂停。  
	![Make important things fast](http://developer.android.com/design/media/principles_make_important_fast.png)  


二、UI概览(Overview)  
app的设计建立在系统UI基础上。系统的界面有：主页，全局导航，提醒。所有的app应保持整个系统体验的一致性和有趣性。  

1. 桌面、所有应用、最近应用  
![home](http://developer.android.com/design/media/ui_overview_home_screen.png)  
	桌面(Home screen):存放应用程序的快捷方式，屏幕直接左右滑动切换。底部托盘存放最长用功能。托盘中间点击后显示所有应用。  
![All apps screen](http://developer.android.com/design/media/ui_overview_all_apps.png)  
	所用程序(All apps screen)显示安装的所有app,通过拖动app图标，可以将其放知道任意界面。  
![Recents screen](http://developer.android.com/design/media/ui_overview_recents.png)  
	最近使用的app提供了运行的多个任务的切换途径。其按照最近使用时间排序，点击打开，向左或右拖动移除。  

2. 系统工具栏   
系统工具栏有：设备状态1，提醒通知，设备导航2。通常系统工具栏和APP一同显示，但一些应用如（看书、看电影等）需要全屏，隐藏系统栏。从设备状态栏向下拉查看更多通知，向左/右移动移除通知。在平板(tablet)中状态栏和导航合并到一起。    
![systerm_bar](http://developer.android.com/design/media/ui_overview_system_ui.png)  


3. 普通APP UI  
通常APP包括主要的系统工具栏，内容区等。 
![common app ui](http://developer.android.com/design/media/ui_overview_app_ui.png)
1为主工具栏。它是APP的控制中心，提供切换APP功能试图/层次，以及包含其他工具栏。  
2为视图控制，切换APP的不同内容和功能的界面。  
3为APP的内容展示区  
4为次工具栏。显示了主工具栏外的更多功能按钮。丰富主工具栏的功能。  

三、设计(STYLE) 
 
1. 设备适配  
android 设备众多，屏幕大小不一。APP应该是可伸缩的(Be flexible)、布局优化的(Optimize layouts)、即可能适配的(Assets for all)  
![](http://developer.android.com/design/media/devices_displays_density.png)  

2. 主题(Themes)  
主题是Android一致性设计的应用。风格就是APP的设计元素的，如颜色、厚度、字体等。为提高APP间的凝聚力。Android 提供了3中主题（Holo Light:全亮 , Holo Dark：全黑  ,Holo Light with dark action bars：内容亮，工具栏黑）可供选择。选择系统主题可更好的满足你的需求和设计美学。若想自定义主题，在系统基础之上定义是更好的方法。  
![holo light](http://developer.android.com/design/media/themes_holo_light.png)  
![holo dark](http://developer.android.com/design/media/themes_holo_dark.png)	  
![Talk in Holo Light with dark action bar.](http://developer.android.com/design/media/themes_holo_inverse.png)    

3. 按键反馈   
在用户按键之后，使用颜色和光照(color and illumination )，来加强手势效果,让用户清楚APP已经接收了用户的按键。  
![touch feedback](http://developer.android.com/design/media/touch_feedback_reaction_response.png)  
![states](http://developer.android.com/design/media/touch_feedback_states.png)
当一些操作比较复杂时，用反馈方法提醒(与用户交流)。比如在最近使用的应用中，当左右滑动时，用变暗表示移除。   
![communication](http://developer.android.com/design/media/touch_feedback_manipulation.png)
再比如用户在APP界限边界滚动时，APP用突出边框表示无法滚动。  
![communication](http://developer.android.com/design/media/touch_feedback_communication.png)  

4. 多屏设计  
Android 设备大小及屏幕密度众多。(Devices vary not only in physical size, but also in screen density (DPI).)我们可以假象，只有两种设备：小于600dp的手机，大于600pd的平板。DPI只有LDPI(120dpi), MDPI(120dpi), HDPI(160dpi), and XHDPI(320dpi)几种。选取这几种进行设计并优化布局。  
![dpi](http://developer.android.com/design/media/metrics_diagram.png)
*UI通常以48dp为基准进行设计。UI元素之间间隔为8dp.  
![48dp](http://developer.android.com/design/media/metrics_48.png)
48dp转化为物理单位为9mm，一般7-10mm是最适合触摸的。
![why 48dp](http://developer.android.com/design/media/metrics_closeup.png)  
![ex](http://developer.android.com/design/media/metrics_forms.png)    
 
5. 字体与颜色  
android系统需要传统的排版技术(缩放、大小、对齐等）支持。Android4.0引入了新的字体Roboto。系统默认颜色为textColorPrimary and textColorSecondary:  
![default_color](http://developer.android.com/design/media/typography_defaults.png)  
系统的大小可自定义，但最好使用系统限制大小，如下:  
![type_size](http://developer.android.com/design/media/typography_sizes.png)  
颜色主要用来强调。在不同组件间选择合适的对比色。  
<div class="color-row-container">
      <ul class="color-row" style="list-style-type: none;">
        <li style="background-color: rgb(51, 181, 229);"><span style="color: rgb(51, 181, 229);">#33B5E5</span></li>
        <li style="background-color: rgb(170, 102, 204);"><span style="color: rgb(170, 102, 204);">#AA66CC</span></li>
        <li style="background-color: rgb(153, 204, 0);"><span style="color: rgb(153, 204, 0);">#99CC00</span></li>
        <li style="background-color: rgb(255, 187, 51);"><span style="color: rgb(255, 187, 51);">#FFBB33</span></li>
        <li style="background-color: rgb(255, 68, 68);"><span style="color: rgb(255, 68, 68);">#FF4444</span></li>
      </ul>
</div>

蓝色是android默认的强调色(Blue is the standard accent color in Android's color palette)。每种颜色在需要时都应该有黑色阴影。


6. 图标  
图标提供了功能、状态、APP的快速、直观的表现。  
**起动器**：  
起动器的图标在桌面和所有程序界面显示。由于界面背景可更换，因此起动器图标应设计成在任意背景下可见。  
![laucher ico](http://developer.android.com/design/media/iconography_launcher_focal.png)  
起动器的图标大小必须为48x48 dp，在Google Play上必须为512x512 pixels.  
可用鲜明的表现，如3D，透视等，让图标更直观易见。   
![ico launcher](http://developer.android.com/design/media/iconography_launcher_example2.png)  
**工具条**  
工具条APP显示的是APP重要的功能。每一个ICON应能用一个具有象征性的图来清晰表达出功能含义。  
[Download the Action Bar Icon Pack](http://developer.android.com/downloads/design/Android_Design_Icons_20120814.zip),在这里定义了一些常用功能图标，如刷新、分享等。  
功能图标大小应为32x32 dp；视觉上大小应为24x24 dp，全部32x32 dp.  
![size](http://developer.android.com/design/media/iconography_actionbar_focal.png)  
类型：象形、偏平化，细节不要过多，带有尖或平滑形状；若图形太薄，填充焦点空间；线条的宽度和空隙应该为2dp.
>Style:Pictographic, flat, not too detailed, with smooth curves or sharp shapes. If the graphic is thin, rotate it 45° left or right to fill the focal space. The thickness of the strokes and negative spaces should be a minimum of 2 dp.    
![icon style](http://developer.android.com/design/media/iconography_actionbar_style.png)  
**内容中图标**  
大小：16x16 dp.Optical square, 12x12 dp  
![icon small focal](http://developer.android.com/design/media/iconography_small_focal.png)  
类型：中立、偏平、简单，更好的象征性。  
![samll style](http://developer.android.com/design/media/iconography_small_style.png)  
颜色选择非中性(non-neutral )颜色：    
![colors](http://developer.android.com/design/media/iconography_small_colors.png)  

**提醒图标**  ：简单、偏平化
大小：24x24 dp，Optical square, 22x22 dp，颜色：必须为白色.  
![notificatio ico](http://developer.android.com/design/media/iconography_notification_style.png)  

7 [描述](http://developer.android.com/intl/zh-CN/design/style/writing.html)  
应用程序中的文字描述应该：  
简洁、简单、友好、重要的优先、仅描述必要的、避免重复。  

PS：本人非专业设计师，描述可能有误，请慎重参考。  
PS2，本文译自http://developer.android.com/intl/zh-CN/design/index.html
中的[Get Started](http://developer.android.com/intl/zh-CN/design/index.html)，[Style](http://developer.android.com/intl/zh-CN/design/style/index.html)两部分其他部分见下一篇。


参考链接：
[1]http://developer.android.com/intl/zh-CN/design/index.html
