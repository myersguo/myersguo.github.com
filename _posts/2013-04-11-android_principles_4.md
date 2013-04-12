---
layout: wp
title: Android UI 指南续3
comments: true
blogindex: false
---
接上文  
## 选中（Selection）  
Android 3.0之前中的长按手势时显示上下文菜单(contextual actions)来选择数据。如下图：  
![selection content menu](http://developer.android.com/design/media/selection_context_menu.png)  
Android 3.0开始，使用CAB(contextual action bar)来选择数据。  
![CAB](http://developer.android.com/design/media/selection_cab_big.png)
当数据被选中时,CAB显示临时的工具按钮，CAB显示时可进行：   

+ 选择其他的数据  
+ 按下CAB上的按钮，操作选中的数据后CAB自动消失 
+ 按返回按钮或CAB的选中项时CAB消失，选中的数据项也取消高亮  

![selection of CAB](http://developer.android.com/design/media/selection_cab_example.png)  
你可以决定CAB上功能按钮，哪些显示，哪些隐藏到overflow中。  
当用户选中某项时，CAB的功能菜单应该动态更改。  
![CAB selection](http://developer.android.com/design/media/selection_adjusting_actions.png)  

## 确认和告知    
(Confirming & Acknowledging)  
用confirm和acknowledge通过文字来和用户沟通是一个好的表达方式。它可以解决用户后悔执行操作的情况。    
![confirm](http://developer.android.com/design/media/confirm_ack_confirming.png)  
Confirming是询问用户是否真正想要执行一个操作。一般情况下，确认是一个警告或危险操作通知。  
![acknowledge](http://developer.android.com/design/media/confirm_ack_acknowledge.png)  
Acknowledge是显示用户刚才操作成功的提示信息。它消除系统任务执行不确定性的疑虑。一般，Acknowledge伴随着一个undo选择。  

### 什么时候confirm和acknowledge由用户决定  
并不是所有的操作都需要一个confrim或acknowledge。是否需要遵循以下原则：  
![confrim act flowchart](http://developer.android.com/design/media/confirm_ack_flowchart.png)  
重大损害-->Confirm    
用以误操作(图标太近，用户连续操作)->  
  ->造成不可恢复的眼中后果->Confirm   
  ->造成可恢复的眼中后果->acknowledge    
操作没有明显的反馈->acknowlege  

### Confirming  
![confrim ex](http://developer.android.com/design/media/confirm_ack_ex_books.png)  
提醒用户删除之后不能回复。alert.  

### Acknowledging  
![acknowledge](http://developer.android.com/design/media/confirm_ack_ex_draftsave.png)    
在上图示例中，从写信中返回，草稿将会保存，并提示用户。  
![acknowledge delete](http://developer.android.com/design/media/confirm_ack_draft_deleted.png)  
上图示中删除后，提醒用户可回复。  

### No Confirmation or Acknowledgment  
![no conf or ack](http://developer.android.com/design/media/confirm_ack_ex_plus1.png)  
+1按钮，误操作也不会有损害，只需再次touch即可；同时，视觉上有提醒反馈，无需acknowledge  


## Notifications  
通知系统允许APP向用户推送事件消息，如对话，日历提醒等。提醒的设计是告知用户重要信息的新的方式。  
Jelly Bean的新特性：  

+ 提醒能包含功能按钮让用户立即处理它  
+ 对大小和布局更加适应，可包含更多细节信息    
+ 优先级标志让重要提醒优先显示  

### Anatomy of a notification  

####基本布局 Base Layout
一个提醒的基本元素：  

+ 发送提醒的应用程序ICON或发送者ICON
+ 提醒的标题和内容  
+ 时间戳  
+ 当主图标是发送者图标时，显示第二个图标标明应用程序   

![notifications](http://developer.android.com/design/media/notifications_pattern_anatomy.png)  

#### 扩展布局Expanded layouts  
在jelly bean系统中，提醒支持扩展布局(系统提供两种布局,text or image)显示更多信息，如若干行文字，预览的图片。用户通过两个手指pinch或滑动来显示更详细通知。    
![expand layout](http://developer.android.com/design/media/notifications_pattern_expandable.png)

#### 操作 Actions   
从jelly bean开始,android支持可选的功能项显示在notification的底部。通常使用特殊的功能来加快人机交互。    
![actions](http://developer.android.com/design/media/notifications_pattern_two_actions.png)
好的action选择根据：  

+ 必要的，常用的，基本的内容处理功能  
+ 实时的  
+ 不重复的  

坏的提醒action是:  

+ 含糊的  
+ 与提醒重复，如read,open  

你可以最多放置3个包含name和icon的按钮。向base layout添加action将显示expandable提醒。  

### 设计原则 Design guidelines  
#### 个性化 Make it personal  
![make it personnal](http://developer.android.com/design/media/notifications_pattern_personal.png)  
在提醒中添加发送者的头像和应用程序的图标  
#### 准确导航 Navigate to the right place  
通常用户在点击通知信息后进入APP的信息所在详情页(detail view),也可能是一个summary view。若用户被带到层级设计的APP中的非首层，请在app的back stack(返回栈）中插入导向APP HOME页的记录。    
#### 准确管理提醒的优先级 Correctly set and manage notification priority  
从jelly bean开始，android支持notification优先级。  

<table>
  <tbody><tr>
    <th><strong>Priority</strong></th>
    <th><strong>Use</strong></th>
  </tr>
  <tr>
    <td>MAX</td>
    <td>Use for critical and urgent notifications that alert the user to a condition that is time-critical or needs to be resolved before they can continue with a particular task.</td>
  </tr>
  <tr>
    <td>HIGH</td>
    <td>Use high priority notifications primarily for important communication, such as message or chat events with content that is particularly interesting for the user.</td>
  </tr>
  <tr>
    <td>DEFAULT</td>
    <td>The default priority. Keep all notifications that don't fall into any of the other categories at this priority level.</td>
  </tr>
  <tr>
    <td>LOW</td>
    <td>Use for notifications that you still want the user to be informed about, but that rate low in urgency.</td>
  </tr>
  <tr>
    <td>MIN</td>
    <td>Contextual/background information (e.g. weather information, contextual location information). Minimum     priority notifications will not show in the status bar. The user will only discover them when they expand the notification tray.</td>
  </tr>
</tbody></table>  

![notification flag](http://developer.android.com/design/media/notifications_pattern_priority.png)  

#### 折叠通知 Stack your notifications  
如果APP创建通知时有相同的通知存在，则应该折叠通知  
不要：  
![stack notification](http://developer.android.com/design/media/notifications_pattern_additional_fail.png)  
而要：  
![stack notification](http://developer.android.com/design/media/notifications_pattern_additional_win.png)  

可以通过expanded digest layout来显示折叠的通知：  
![stack notifaction show](http://developer.android.com/design/media/notifications_expand_contract_msg.png)  

#### 让通知可选 Make notifications optional  
用户应该总是能够控制通知是否显示，或显示的方式（比如声音，震动等）  
#### 使用明显的标记 Use distinct icons  
用户应该可以从通知栏中一览看出通知的类型。  
应该：从android apps中查看其它的通知标志，设计新的显而易见的标志；使用准确的ICON属性设置，Holo Dark风格的action icons;保持icon简洁。  
不该：使用多种颜色区分其他APP。  

#### Pluse the notification LED appropriately  
需要android设备都配有提醒灯。在屏幕暗时，那些级别(MAX,HIGH,DEFAULT)应该使灯闪亮。LED应该用白色闪亮而非其他颜色，除非用户自定义之。  

### Building notifications that users care about  
APP应让人感到顺畅、舒适。提醒的一个APP的声音，体现了APP的个性。不需要的，不重要的提醒不应该给用户。  
####When to display a notification   
理解用户关注点在哪里。提醒系统应升级成用户最关系的内容。但更应该清楚，提醒在某种程度打断了用户的工作。设计你的提醒时，问自己是否他们是最重要的必须的打扰。如果是，请允许用户进入提醒设置，选择提醒的优先级。  
优秀的APP只在用户呼叫时应答，但难免需要打扰用户的时候。  
提醒应该是：时间敏感的(time sensitive events)，尤其是即时的。  
![notification](http://developer.android.com/design/media/notifications_pattern_real_time_people.png)  
#### When not to display a notification  
以下情况不要提醒  
+ 不相关的信息。如不相关的社交信息更新  
+ APP已经打开，不要在通知栏产生消息  
+ 低级的技术操作，如同步，保存，更新等不要打扰用户  
+ 如果一些错误应用程序可自动修复，不需要提醒用户 
+ 不要咸的没事冒出来   
+ 不要为了显摆一下APP的存在感而提醒  

### 通知的交互 Interacting With Notifications  
![notification phone](http://developer.android.com/design/media/notifications_pattern_phone_icons.png)  
通知显示按图标显示在通知栏处，在通知的drawer被下拉后，通知按时间排序。左右滑动可移除通知  
![notifaction tablet](http://developer.android.com/design/media/notifications_pattern_tablet.png)   
在平板电脑中，通知栏位于底部工具栏，点击通知栏任意处呼出通知。  
#### Ongoing notifications  
![ongoing notifaction](http://developer.android.com/design/media/notifications_pattern_ongoing_music.png)   
进行中的通知用来告知用户系统后台正在运行着该程序。例如，音乐播放器在后台运行时，在通知栏显示，可进行播放，暂停的控制。  
#### Dialogs and toasts are for feedback not notification  
当APP不再当前屏幕时，不应该用Dialogs 和 toasts来进行通知。

(未完待续...)
 

 
