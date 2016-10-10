---
layout: wp
title: android理解instrumentation
---


### instrumentation  

先看activity的[启动过程](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/app/Activity.java#4501)：  

```
    @Override
    public void startActivity(Intent intent, @Nullable Bundle options) {
        if (options != null) {
            startActivityForResult(intent, -1, options);
        } else {
            // Note we want to go through this call for compatibility with
            // applications that may have overridden the method.
            startActivityForResult(intent, -1);
        }
    }


```
继续看startActivityForResult:  

```
    public void startActivityForResult(@RequiresPermission Intent intent, int requestCode,
            @Nullable Bundle options) {
        if (mParent == null) {
            Instrumentation.ActivityResult ar =
                mInstrumentation.execStartActivity(
                    this, mMainThread.getApplicationThread(), mToken, this,
                    intent, requestCode, options);
            if (ar != null) {
                mMainThread.sendActivityResult(
                    mToken, mEmbeddedID, requestCode, ar.getResultCode(),
                    ar.getResultData());
            }
            if (requestCode >= 0) {
                // If this start is requesting a result, we can avoid making
                // the activity visible until the result is received.  Setting
                // this code during onCreate(Bundle savedInstanceState) or onResume() will keep the
                // activity hidden during this time, to avoid flickering.
                // This can only be done when a result is requested because
                // that guarantees we will get information back when the
                // activity is finished, no matter what happens to it.
                mStartedActivity = true;
            }
            cancelInputsAndStartExitTransition(options);
            // TODO Consider clearing/flushing other event sources and events for child windows.
        } else {
            if (options != null) {
                mParent.startActivityFromChild(this, intent, requestCode, options);
            } else {
                // Note we want to go through this method for compatibility with
                // existing applications that may have overridden it.
                mParent.startActivityFromChild(this, intent, requestCode);
            }
        }
    }

```

这里我们看到activity的启动是由 instrumentation的execStartActivity执行的。  
这种设计即委托观察者模式。  

```
    public ActivityResult execStartActivity(
            Context who, IBinder contextThread, IBinder token, Activity target,
            Intent intent, int requestCode, Bundle options) {
        IApplicationThread whoThread = (IApplicationThread) contextThread;
        Uri referrer = target != null ? target.onProvideReferrer() : null;
        if (referrer != null) {
            intent.putExtra(Intent.EXTRA_REFERRER, referrer);
        }
        if (mActivityMonitors != null) {
            synchronized (mSync) {
                final int N = mActivityMonitors.size();
                for (int i=0; i<N; i++) {
                    final ActivityMonitor am = mActivityMonitors.get(i);
                    if (am.match(who, null, intent)) {
                        am.mHits++;
                        if (am.isBlocking()) {
                            return requestCode >= 0 ? am.getResult() : null;
                        }
                        break;
                    }
                }
            }
        }
        try {
            intent.migrateExtraStreamToClipData();
            intent.prepareToLeaveProcess(who);
            int result = ActivityManagerNative.getDefault()
                .startActivity(whoThread, who.getBasePackageName(), intent,
                        intent.resolveTypeIfNeeded(who.getContentResolver()),
                        token, target != null ? target.mEmbeddedID : null,
                        requestCode, 0, null, options);
            checkStartActivityResult(result, intent);
        } catch (RemoteException e) {
            throw new RuntimeException("Failure from system", e);
        }
        return null;
    }

```  

自动化测试,instrumentaiontestcase:  

```
testactivity = (testClass)getInstrumentation().startActivitySync(intent);
text = testactivity.findViewById(R.id.text1);
assertEquals("hello,world", text.getText().toString());

...不知道源码也可以的。
/** This testcase starts the same activity and sending a key event to that activity under test*/
private void testcase3() {
Intent intent = new Intent(Intent.ACTION_MAIN);
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
intent.putExtra(“STRING”, “Hai”);
intent.setClassName(getTargetContext(), "MyActivity.class");

MyActivity activity = (MyActivity)startActivitySync(_intent);
act = new ActivityRunnable(activity);
runOnMainSync(act);

```


举个例子(from:http://stackoverflow.com/questions/1759626/how-do-you-test-an-android-application-across-multiple-activities)   



```
package com.mycompany;

import android.app.*;
import android.content.*;
import android.test.*;
import android.test.suitebuilder.annotation.*;
import android.util.*;
import android.view.*;
import android.widget.*;

import static org.hamcrest.core.Is.*;
import static org.hamcrest.core.IsNull.*;
import static org.hamcrest.core.IsInstanceOf.instanceOf;
import static org.junit.Assert.*;
import static com.mycompany.R.id.*;

public class LoginTests extends InstrumentationTestCase {

   @MediumTest
   public void testAValidUserCanLogIn() {

      Instrumentation instrumentation = getInstrumentation();

      // Register we are interested in the authentication activiry...
      Instrumentation.ActivityMonitor monitor = instrumentation.addMonitor(AuthenticateActivity.class.getName(), null, false);

      // Start the authentication activity as the first activity...
      Intent intent = new Intent(Intent.ACTION_MAIN);
      intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
      intent.setClassName(instrumentation.getTargetContext(), AuthenticateActivity.class.getName());
      instrumentation.startActivitySync(intent);

      // Wait for it to start...
      Activity currentActivity = getInstrumentation().waitForMonitorWithTimeout(monitor, 5);
      assertThat(currentActivity, is(notNullValue()));

      // Type into the username field...
      View currentView = currentActivity.findViewById(username_field);
      assertThat(currentView, is(notNullValue()));
      assertThat(currentView, instanceOf(EditText.class));
      TouchUtils.clickView(this, currentView);
      instrumentation.sendStringSync("MyUsername");

      // Type into the password field...
      currentView = currentActivity.findViewById(password_field);
      assertThat(currentView, is(notNullValue()));
      assertThat(currentView, instanceOf(EditText.class));
      TouchUtils.clickView(this, currentView);
      instrumentation.sendStringSync("MyPassword");

      // Register we are interested in the welcome activity...
      // this has to be done before we do something that will send us to that
      // activity...
      instrumentation.removeMonitor(monitor);
      monitor = instrumentation.addMonitor(WelcomeActivity.class.getName(), null, false);

      // Click the login button...
      currentView = currentActivity.findViewById(login_button;
      assertThat(currentView, is(notNullValue()));
      assertThat(currentView, instanceOf(Button.class));
      TouchUtils.clickView(this, currentView);

      // Wait for the welcome page to start...
      currentActivity = getInstrumentation().waitForMonitorWithTimeout(monitor, 5);
      assertThat(currentActivity, is(notNullValue()));

      // Make sure we are logged in...
      currentView = currentActivity.findViewById(welcome_message);
      assertThat(currentView, is(notNullValue()));
      assertThat(currentView, instanceOf(TextView.class));
      assertThat(((TextView)currentView).getText().toString(), is("Welcome, MyUsername!"));
   }
}

```

### uiautomator  

简单看一下uiautomator,首先，instrumentation 用来做UI测试受制于android的安全策略，本质上是一个APP测试，需要和被测APP在相同签名下，相同的进程下，才能互相信任,获取控件状态。因此，如果不知道源码，不要对APP重签名；另外，instrumentaion比较底层，封装程度不高，因此不适合直接拿来测试。从android API LEVEL 16开始，android引入了新的测试框架uiautomator,它对UI测试做了高度封装，同时不依赖于instrumentation,不需要做签名校验，可直接通过系统调用，获取被测应用控件。非常方便。以后详细介绍。





### 参考资料  
[android samples](https://android.googlesource.com/platform/development/+/master/samples?autodive=0%2F%2F%2F)  
[instrumentationtestcase source code](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/test/InstrumentationTestCase.java)  
[instrumentation 源码](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/app/Instrumentation.java)  
[Activity 源码](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/app/Activity.java)  
[uiautomatorTestCase源码](https://android.googlesource.com/platform/frameworks/testing/+/master/uiautomator/library/testrunner-src/com/android/uiautomator/testrunner/UiAutomatorTestCase.java)    
[uidevice 源码](https://android.googlesource.com/platform/frameworks/testing/+/master/uiautomator/library/core-src/com/android/uiautomator/core/UiDevice.java)    
[uiautomatorbridge源码](https://android.googlesource.com/platform/frameworks/uiautomator/+/master/src/com/android/uiautomator/core/UiAutomatorBridge.java)  
[automationg performance tests](http://www.slideshare.net/snailee/automating-performance-tests-for-android-applications)  

