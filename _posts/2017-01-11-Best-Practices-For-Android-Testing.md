---
layout: wp
title: Best Practies For Android Testing(译)
---

## 简介 ##  

Android测试是基于JUnit的。你可以在JVM或者Android设备上执行测试。  

### 测试类型 ###  

项目的测试工程必须和源码属于不同的目录。一般有以下测试类型：   

#### 单元测试(Unit tests) ####   

单元测试是APP测试体系中的函数测试方法，在每次BUILD后执行每一个单独的逻辑中的测试,能快速定位和修复软件集成后可能引起的问题。  

单元测试通常是对代码的最小单元模块的测试，可以是函数，类或组件等。  

* 本地单元测试(Local unit tests)：
  module-name/src/test/java/...
  这些测试在本地JVM下执行，一般不使用Android API。  

* Instrumented tests:  
  module-name/src/androidTest/java/...  
  这些测试一般在Android设备(或虚拟机)上执行。测试代码生成一个test apk和被测程序在同一设备执行。测试apk和被测apk在同一进程下，因此test apk能调用和修改被测程序的方法。    

![image](/public/test-types_2x.png)  


#### 集成测试(Intergration Tests) ####     

* 单APP测试：espresso  
* 跨APP测试： UI Automator  


## 测试工具 & API ##  

### Junit ###  

你可以直接使用JUnit4的相关API类来实现测试开始，结束，断言等方法。  

* @Before:测试开始前执行   
* @After: 测试结束后执行   
* @Test: 测试方法  
* @Rule: 测试规则定义  
* @BeforeClass:Class的静态方法，每次初始化Class只执行一次。  
* @AfterClass:Class的静态成员，Class内的所有测试都执行完毕后执行   
* @Test(timeout=):测试用例参数化  

例子，在根目录的build.grade文件中添加依赖：  

```
dependencies {
    testCompile: 'junit:junit:4.12',
    testCompile: 'org.mockito:mockito-core:1.10.19'
}
```

```
import org.junit.Test;
import java.util.regex.Pattern;
import static org.junit.Assert.assertFalse;
import static org.junit.Assert.assertTrue;

public class EmailValidatorTest {

    @Test
    public void emailValidator_CorrectEmailSimple_ReturnsTrue() {
        assertThat(EmailValidator.isValidEmail("name@email.com"), is(true));
    }
    ...
}
```

### Instrumented Unit Test ###

添加依赖： 

```
dependencies {
    androidTestCompile 'com.android.support:support-annotations:24.0.0'
    androidTestCompile 'com.android.support.test:runner:0.5'
    androidTestCompile 'com.android.support.test:rules:0.5'
    // Optional -- Hamcrest library
    androidTestCompile 'org.hamcrest:hamcrest-library:1.3'
    // Optional -- UI testing with Espresso
    androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2.2'
    // Optional -- UI testing with UI Automator
    androidTestCompile 'com.android.support.test.uiautomator:uiautomator-v18:2.1.2'
}
```




### Android 测试框架/库 ###  

![android test framework](/public/android_test_framework.png)   

* AndroidJUnitRunner  
* Espresso  
* UI Automator  


### 断言类 ###   

Android测试框架继承自JUnit,因此可以直接使用JUnit的测试的断言方法。 

### Monkey & monkeyrunner ###  

* Monkey:  
  android提供的可以发送伪随机按键、触摸、手势等到设备的信息流的命令行工具.  
* monkeyrunner:  
  包含一系列测试API和测试命令方法，包括：连接设备，截图，图片比较，事件操作等等。  


## APP测试技术(自动化、单元、性能） ##  

### Automating User Interface Tests ###   




## 参考资料 ##  

[https://developer.android.com/training/testing/index.html](https://developer.android.com/training/testing/index.html)  
[http://tmq.qq.com/2016/09/mobile-app-test-automation-framework/](http://tmq.qq.com/2016/09/mobile-app-test-automation-framework/)  
[https://developer.android.com/studio/test/index.html](https://developer.android.com/studio/test/index.html)  


