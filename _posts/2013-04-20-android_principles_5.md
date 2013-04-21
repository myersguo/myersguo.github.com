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
### Guidelines  

