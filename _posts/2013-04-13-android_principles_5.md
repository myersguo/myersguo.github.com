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

### Design Patterns  
设计模式。  
#### Checkbox 复选框  
某项选中或不选中时使用。  
![复选框](http://developer.android.com/design/media/settings_checkbox.png)   
#### Multiple choice 单选   
多个选择项只选择一项。    
![单选框](http://developer.android.com/design/media/settings_multiple_choice.png)   
#### Slider 滑动条     
连续的选项。      
![滑动条](http://developer.android.com/design/media/settings_slider.png)    
#### Date/time 时间设置     
需要选择时间时使用        
![时间设置](http://developer.android.com/design/media/settings_date_time.png)
#### Subscreen navigation 子窗口导航    
当需要进入子窗口或序列子窗口设置时选择。单一子窗口的title和导航label相同；序列子窗口的title使用序列窗口的设置的第一步描述(入下图)。            
![时间设置](http://developer.android.com/design/media/settings_subscreen_navigation.png)    
#### List subscreen 列表子窗口      
在设置等价意义的列表项时使用。label提供项的名称，secondary text被用来提供项的状态信息。(如下图，使用status使用一个icon来加强视觉感受)          
![list screen](hhttp://developer.android.com/design/media/settings_list_subscreen.png)    
#### Master on/off switch 主开关控制   
在设置分类的允许控制时使用。放置在action bar的第一项。当switch关闭，列表项清空。
![master on/off](http://developer.android.com/design/media/settings_master_on_off.png)  
当然，也可以在导航菜单中放置switch开关，但最好确保用户不常进入子窗口，负责用户会常直接触动开关。  
![master on/off](http://developer.android.com/design/media/settings_master_on_off_2.png)   
#### Individual on/off switch 单项开关  
在需要详细描述的功能开关时使用。开关只出现在子窗口，避免用户不了解详情下触发。  
![individual on off](http://developer.android.com/design/media/settings_individual_on_off.png)  
#### Dependency 依赖    
功能项是否可用由另一项控制。控制项放在功能项上侧。功能项包含一个status line时，在不可用时提示：Unavailable,reason.若控能项多于3个使用master on/off来控制。         
![dependency](http://developer.android.com/design/media/settings_dependency.png)   
### Defaults   
要非常重视默认值的选择。因为你的选择决定了用户对APP的第一印象，他们更期望APP的初始设置是命中人心的。以下选择是需要考虑的： 
 
- 哪些选择是用户可能选择的。  
- 哪些选择是最中立的  
- 哪些选择是没有风向，最没争议，不过分的？ 
- 哪些选择使用最少的电量和数据量  
- 哪些选择最符合设计原则：不弄掉用户数据；不打扰用户  
### Writing Guidelines 写作指导  
#### Label clearly and concisely 标题清晰明了  
空间有限下写出清晰的标题是个挑战。以下指导让你的标题简洁、丰富、直观。    

- 使用常用句式(如，仅在第一个单词或特殊字词时大写）  
- 不要直接使用指导性的动词，如："设置”，“修改”，“编辑”，“管理”，“使用”，“选择”(set,change,edit,modify,manage,use,select,choose)，用户知道下一步是操作 。   
- 标题不要以...设置为结尾。因为它是多余的。  
- 在一组中设置，项标题不要重复组名或子屏幕标题。  
- 不要用“不要”等否定词开头(donn't never)。例如使用"block"替换"donn't allow"  
- 不要使用太多术语，除非目标用户能理解。尽量使用简单的用语。  
- 不要提及用户。比如提醒设置，使用“提醒”，而非“提醒我”。  
 
#### Secondary text below is for status, not description…  
副标题来表示状态，而非描述信息。    
使用副标题的好处是：  

+ 用户能一眼看出当前设置的值。  
+ 它应用了设计原则：保持简洁。  

#### …unless it's a checkbox setting   
在使用复选框(checkbox）时，使用副标题是个例外：不表示status，而表示description。复选框不需要status。复选框不像其他控件可以直接显示，它需要描述信息表示它的更多含义。  
当然，若checkbox的标题已经标明意思，无需添加secondary text。按照下列方法添加副标题：  
 
+ 用一句话表达，不要在结尾家标点符号    
+ 当选中项时，改变描述语句。如"allow data exchange"选中后更改为“allows data exchange”   
+ 不要在标题中重复使用单子。如：个人设置，个人主页，个人信箱。  
+ 不要提及用户。如不要使用：个人设置。  
+ 若必须提及用户。使用"you"而非"I"。    


#### Writing examples  
副标题文案示例。  
before:Use tactile feedback  
after: Vibrate on touch   
在此复选框title中，去掉“Use"，使用更直接的表达。  
**before:**   
Screen timeout  
Adjust the delay before the screen automatically turns off  
**after:**    
Sleep  
After 10 minutes of inactivity   
在此多选框中, 使用更友好的标题，用status替换description。状态表示sleep是在10min的静止后，加入简单描述，防止用户曲解。  
before:  
Change screen lock  
Change or disable pattern, PIN, or password security  
after:  
Screen lock  
Pattern  
这里设置导向子窗口，使用当前的设置状态来为secondary text。标题去掉"change"这种动词。  
before:  
NFC  
Use Near Field Communication to read and exchange tags  
after:  
NFC  
Allow data exchange when the phone touches another device    
虽然NFC是一个技术名词，但这里保留，因为：找不到其他可以替代的词；用户会越来越熟悉这个词。   
我们用更加通俗的语言替换了原来科技用语。   

## Help  
虽然我们希望当你按照设计规范进行设计，几乎每一个人都能无障碍的使用APP，但这几乎不可能。   
一些用户可能会遇到各种问题。他们会在APP中能有帮助信息，若不能很快找到使用帮助，他们将离开APP并不会回来。   

### Designing Help into Your App    
#### Don't show unsolicited help, except in very limited cases   
通常，你希望用户快速掌握，发现APP的应用功能。你也许会在用户第一次使用应用时，展示一个一次性的介绍页或启动画面(one-time introductory slideshow,video,splash screen)来帮助用户认识APP。    
但通常，我们不建议采用这样设计，因为：    

+ 它打断用户的。用户通常想快速启动APP，因此任何放到面前的都是阻碍或烦恼(obstacle or annoyance),不管你是否是好意，他不需要它，通常会忽略它的存在。  
+ 它是不必要的。如果你关注你的APP设计，就不要仅提供一个帮助页，要尽量使用UI解决问题。应用Android的design patterns,styles,building blocks.你就能降低用户的学习成本。    

只有一种情况向新用户提供主动的启动页帮助信息：  
指导用户使用手势来完成某个功能。    

+ High value。没有它，用户不知道常用的屏幕操作满足需求。  
+ Available only through a gesture。没有按钮和菜单，只能通过手势来操作。   

#### Follow the standard design for navigating to help    
在应用的每个屏幕，在隐藏菜单提供帮助项，且把它放到最下面位置。     
![help overflow](http://developer.android.com/design/media/help_overflow.png)    
这样用户能够轻松找到帮助页。    
### Assume that every call for help is urgent    
在帮助页你可能想提供更多信息，如版权，服务条款，隐私协议等。    
让用户在帮助页面访问这些内容，但最好将这些用户不急切想要知道的帮助信息的放到更多菜单中。    
![help](http://developer.android.com/design/media/help_better.png)    
不要提供一个对话框让用户选择帮助项。直接展示用户关心的用户项（中），一些用户不关心的信息放置到更多菜单中。     
#### Principles for Writing On-Screen Help Content      
帮助内容的设计原则：    
#### Help is part of the UI    
帮助是UI的扩展，而非UI的描述。界面的内容应该符合[Writing Style](http://developer.android.com/design/style/writing.html)规范以得到完整的体验（ end-to-end experience）。  
#### Make every pixel count    
节省像素空间。没必要把APP的所有细节都包含，尤其是一言能看出来的功能。能够覆盖主要的信息即可。    
#### Pictures are faster than words  
描述关键UI元素，提供按步指引时，考虑使用图文形式(text with icons),局部采用截屏。用更少的话解释事情，用户能够更快理解。  
#### Help me scan, not read    
浏览而非阅读。用户不会把帮助从头读到尾，只会浏览重要的包含他们需要的信息。因此，在排版上使用加粗标题，分类，列表，表格，分段，这些对浏览友好的排版方法。若内容过多，份屏滑动浏览。    
#### Take me straight to the answer    
最快的浏览方式就是答案就在眼前，无需浏览。可以考虑在用户在浏览帮助项时提供相关帮助项（上下文帮助项）。若使用此方法，确保能够访问整个help内容。  
 



