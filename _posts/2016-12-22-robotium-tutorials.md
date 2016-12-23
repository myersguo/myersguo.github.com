---
layout: wp
title: robotium tutorials
---

前言：   
2016有很多未完成的`任务`,虽然，我无意深入去做android测试上的研究，但这篇笔记(文章)不能省，也不能不深入。    

之前写过一篇关于[instrumentation](https://myersguo.github.io/2016/10/10/About-Instrumentation.html)的介绍，这篇算作续集。   

### 用法 ###   

比如NotePadTest的AndroidManifest.xml中的配置：  

```
<instrumentation android:targetPackage="com.example.android.notepad" android:name="android.test.InstrumentationTestRunner" />
```

command line:   
`adb shell am instrument -w com.android.foo/android.test.InstrumentationTestRunner`  

`com.android.foo`是被测程序包名, android.test.InstrumentationTestRunner   

从本质上将，robotium仍然是基于instrumentation的测试，因此instrumentation的限制在robotium上都存在。那么robotium为我们提供了什么呢？    


### Solo ###    

我们先来看robotium的一个测试用例：    

```
public class NotePadTest extends ActivityInstrumentationTestCase2<NotesList>{

    private Solo solo;

    public NotePadTest() {
        super(NotesList.class);

    }

    public void testAddNote() throws Exception {
        solo.unlockScreen();
        solo.clickOnMenuItem("Add note");
        //Assert that NoteEditor activity is opened
        solo.assertCurrentActivity("Expected NoteEditor activity", "NoteEditor"); 
        //In text field 0, enter Note 1
        solo.enterText(0, "Note 1");
        solo.goBack(); 
        //Clicks on menu item
        solo.clickOnMenuItem("Add note");
        //In text field 0, type Note 2
        solo.typeText(0, "Note 2");
        //Go back to first activity
        solo.goBack(); 
        //Takes a screenshot and saves it in "/sdcard/Robotium-Screenshots/".
        solo.takeScreenshot();
        boolean notesFound = solo.searchText("Note 1") && solo.searchText("Note 2");
        //Assert that Note 1 & Note 2 are found
        assertTrue("Note 1 and/or Note 2 are not found", notesFound); 
    }
}
```

如果你用instrumentation怎么写呢？   

```
//以下是伪代码，未经运行测试:   
public class NotePadTest extends InstrumentationTestCase {
   public void testAddNote() {

      Instrumentation instrumentation = getInstrumentation();

      Intent intent = new Intent(Intent.ACTION_MAIN);
      intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
      intent.setClassName(instrumentation.getTargetContext(), 'com.example.android.notepad.NotesList');
      instrumentation.startActivitySync(intent);
      //获取当前activity
      Activity currentActivity = getInstrumentation().waitForMonitorWithTimeout(monitor, 5);
      //解锁
      currentActivity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_DISMISS_KEYGUARD);
      //点击菜单
      //1.打开菜单
      instrumentation.sendKeySync(KeyEvent.KEYCODE_MENU);
      //2.等待菜单打开
      ...
      //3. 查找菜单
       currentView = currentActivity.findViewById(XXX);
       //如果是文本，查找方式如何...
      //4. 点击菜单
      TouchUtils.clickView(this, currentView);
      
```

我们，不难看出，用instrumentation写测试用例，简直太『惨无人道』了。。。。    

robotium就是为了解决这个问题，让你轻松使用`instrumentation`，它怎么解决的？加入我们写一个robotium，希望它实现哪些功能呢？     

1.如何获取当前的activity.  
2.根据resource id,或文本来查找view，并进行点击，移动等操作。  
3.常用操作的封装，比如打开菜单，返回操作，截屏操作，滑动操作。  
4.一些校验。    


### 一探究竟 ###    

我们简单看一下[Solo](https://github.com/RobotiumTech/robotium/blob/master/robotium-solo/src/main/java/com/robotium/solo/Solo.java)这个类:   

```
public class Solo {

    protected final Asserter asserter;
    protected final ViewFetcher viewFetcher;
    protected final Checker checker;
    protected final Clicker clicker;
    protected final Presser presser;
    protected final Searcher searcher;
    protected final ActivityUtils activityUtils;
    protected final DialogUtils dialogUtils;
    protected final TextEnterer textEnterer;
    protected final Rotator rotator;
    protected final Scroller scroller;
    protected final Sleeper sleeper;
    protected final Swiper swiper;
    protected final Tapper tapper;
    protected final Illustrator illustrator;
    protected final Waiter waiter;
    protected final Setter setter;
    protected final Getter getter;
    protected final WebUtils webUtils;
    protected final Sender sender;
    protected final ScreenshotTaker screenshotTaker;
    protected final Instrumentation instrumentation;
    protected final Zoomer zoomer;
    protected final SystemUtils systemUtils;
    protected String webUrl = null;
    private final Config config;
    public final static int LANDSCAPE = ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE;   // 0
    public final static int PORTRAIT = ActivityInfo.SCREEN_ORIENTATION_PORTRAIT;     // 1
    public final static int RIGHT = KeyEvent.KEYCODE_DPAD_RIGHT;
    public final static int LEFT = KeyEvent.KEYCODE_DPAD_LEFT;
    public final static int UP = KeyEvent.KEYCODE_DPAD_UP;
    public final static int DOWN = KeyEvent.KEYCODE_DPAD_DOWN;
    public final static int ENTER = KeyEvent.KEYCODE_ENTER;
    public final static int MENU = KeyEvent.KEYCODE_MENU;
    public final static int DELETE = KeyEvent.KEYCODE_DEL;
    public final static int CLOSED = 0;
    public final static int OPENED = 1;
...
private Solo(Config config, Instrumentation instrumentation, Activity activity) {
        
        if(config.commandLogging){
            Log.d(config.commandLoggingTag, "Solo("+config+", "+instrumentation+", "+activity+")");
        }
        
        this.config = (config == null) ? new Config(): config;
        this.instrumentation = instrumentation;
        this.sleeper = new Sleeper(config.sleepDuration, config.sleepMiniDuration);
        this.sender = new Sender(instrumentation, sleeper);
        this.activityUtils = new ActivityUtils(config, instrumentation, activity, sleeper);
        this.viewFetcher = new ViewFetcher(instrumentation, sleeper);
        this.screenshotTaker = new ScreenshotTaker(config, instrumentation, activityUtils, viewFetcher, sleeper);
        this.dialogUtils = new DialogUtils(instrumentation, activityUtils, viewFetcher, sleeper);
        this.webUtils = new WebUtils(config, instrumentation,viewFetcher, sleeper);
        this.scroller = new Scroller(config, instrumentation, viewFetcher, sleeper);
        this.searcher = new Searcher(viewFetcher, webUtils, scroller, sleeper);
        this.waiter = new Waiter(instrumentation, activityUtils, viewFetcher, searcher,scroller, sleeper);
        this.getter = new Getter(instrumentation, activityUtils, waiter);
        this.clicker = new Clicker(activityUtils, viewFetcher,sender, instrumentation, sleeper, waiter, webUtils, dialogUtils);
        this.setter = new Setter(activityUtils, getter, clicker, waiter);
        this.asserter = new Asserter(activityUtils, waiter);
        this.checker = new Checker(viewFetcher, waiter);
        this.zoomer = new Zoomer(instrumentation);
        this.swiper = new Swiper(instrumentation);
        this.tapper =  new Tapper(instrumentation);
        this.illustrator = new Illustrator(instrumentation);
        this.rotator = new Rotator(instrumentation);
        this.presser = new Presser(viewFetcher, clicker, instrumentation, sleeper, waiter, dialogUtils);
        this.textEnterer = new TextEnterer(instrumentation, clicker, dialogUtils);
        this.systemUtils = new SystemUtils(instrumentation);
        initialize();
    }
...

```

这个几个final属性可以看做是工具类。方便内部调用。    

 
####  ActivityUtils ####    

封装了activity相关的方法，比如：获取当前activity,获取activitymonitor等。    


我们来看它的获取当前的activity:   

```
/**
     * Returns the current {@code Activity}.
     *
     * @param shouldSleepFirst whether to sleep a default pause first
     * @param waitForActivity whether to wait for the activity
     * @return the current {@code Activity}
     */

    public Activity getCurrentActivity(boolean shouldSleepFirst, boolean waitForActivity) {
        if(shouldSleepFirst){
            sleeper.sleep();
        }
        if(!config.trackActivities){
            return activity;
        }
        
        if(waitForActivity){
            waitForActivityIfNotAvailable();
        }
        if(!activityStack.isEmpty()){
            activity=activityStack.peek().get();
        }
        return activity;
    }
/**
     * Waits for an activity to be started if one is not provided
     * by the constructor.
     */

    private final void waitForActivityIfNotAvailable(){
        if(activityStack.isEmpty() || activityStack.peek().get() == null){

            if (activityMonitor != null) {
                Activity activity = activityMonitor.getLastActivity();
                while (activity == null){
                    sleeper.sleepMini();
                    activity = activityMonitor.getLastActivity();
                }
                addActivityToStack(activity);
            }
            else if(config.trackActivities){
                sleeper.sleepMini();
                setupActivityMonitor();
                waitForActivityIfNotAvailable();
            }
        }
    }
/**
     * This is were the activityMonitor is set up. The monitor will keep check
     * for the currently active activity.
     */

    private void setupActivityMonitor() {
        if(config.trackActivities){
            try {
                IntentFilter filter = null;
                activityMonitor = inst.addMonitor(filter, null, false);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
```

简单解释一下：   
首先需要初始化一个activity monitor,来监视activity(`inst.addMonitor`);   
monitor来获取最后一个activity:`Activity activity = activityMonitor.getLastActivity();`:   

>Retrieve the most recent activity class that was seen by this monitor.   

把这个activity加入堆栈中：`activitiesStoredInActivityStack.push(activity.toString());`和`activityStack.push(weakActivityReference);`   

当前的activity，就从堆栈中的顶部即是`
activity=activityStack.peek().get();`   

那有activity移除了，怎么办？所以，要有一个线程监听activity 才对：    

```
/**
     * This is were the activityStack listener is set up. The listener will keep track of the
     * opened activities and their positions.
     */

    private void setupActivityStackListener() {
        if(activityMonitor == null){
            return;
        }

        setRegisterActivities(true);

        activityThread = new RegisterActivitiesThread(this);
        activityThread.start();
    }
```

来看监听的详细代码：    


```
private static final class RegisterActivitiesThread extends Thread {

        public static final long REGISTER_ACTIVITY_THREAD_SLEEP_MS = 16L;
        private final WeakReference<ActivityUtils> activityUtilsWR;

        RegisterActivitiesThread(ActivityUtils activityUtils) {
            super("activityMonitorThread");
            activityUtilsWR = new WeakReference<ActivityUtils>(activityUtils);
            setPriority(Thread.MIN_PRIORITY);
        }

        @Override
        public void run() {
            while (shouldMonitor()) {
                monitorActivities();
                SystemClock.sleep(REGISTER_ACTIVITY_THREAD_SLEEP_MS);
            }
        }

        private boolean shouldMonitor() {
            ActivityUtils activityUtils = activityUtilsWR.get();

            return activityUtils != null && activityUtils.shouldRegisterActivities();
        }

        private void monitorActivities() {
            ActivityUtils activityUtils = activityUtilsWR.get();
            if (activityUtils != null) {
                activityUtils.monitorActivities();
            }
        }
    }
    void monitorActivities() {
        if(activityMonitor != null){
            Activity activity = activityMonitor.waitForActivityWithTimeout(2000L);

            if(activity != null){
                if (activitiesStoredInActivityStack.remove(activity.toString())){
                    removeActivityFromStack(activity);
                }
                if(!activity.isFinishing()){
                    addActivityToStack(activity);
                }
            }
        }
    }
```


每隔16ms检查一次，对当前的activity(`Activity activity = activityMonitor.waitForActivityWithTimeout(2000L);`)做检查：   
从stack中移除activity,如果activity没有结束的话，再把它加入stack中;   
如果stack中本来就没有此activity,则只会加入stack了；    

因此，能保证activity都在堆栈里面了。所以，获取当前堆栈只需要从stack中取就行了。   


#### ViewFetcher ####    

获取view,`getCurrentViews`,`getViews`,`getCurrentImageViews`:   


```
/**
     * Returns true if the view is sufficiently shown
     *
     * @param view the view to check
     * @return true if the view is sufficiently shown
     */

    public final boolean isViewSufficientlyShown(View view){
        final int[] xyView = new int[2];
        final int[] xyParent = new int[2];

        if(view == null)
            return false;

        final float viewHeight = view.getHeight();
        final View parent = getScrollOrListParent(view);
        view.getLocationOnScreen(xyView);

        if(parent == null){
            xyParent[1] = 0;
        }
        else{
            parent.getLocationOnScreen(xyParent);
        }

        if(xyView[1] + (viewHeight/2.0f) > getScrollListWindowHeight(view))
            return false;

        else if(xyView[1] + (viewHeight/2.0f) < xyParent[1])
            return false;

        return true;
    }

    /**
     * Returns the height of the scroll or list view parent
     * @param view the view who's parents height should be returned
     * @return the height of the scroll or list view parent
     */

    @SuppressWarnings("deprecation")
    public float getScrollListWindowHeight(View view) {
        final int[] xyParent = new int[2];
        View parent = getScrollOrListParent(view);
        final float windowHeight;

        if(parent == null){
            WindowManager windowManager = (WindowManager) 
                    instrumentation.getTargetContext().getSystemService(Context.WINDOW_SERVICE);

            windowHeight = windowManager.getDefaultDisplay().getHeight();
        }

        else{
            parent.getLocationOnScreen(xyParent);
            windowHeight = xyParent[1] + parent.getHeight();
        }
        parent = null;
        return windowHeight;
    }
````

上面的代码检查view是否sufficiently shown     


```
public ArrayList<View> getAllViews(boolean onlySufficientlyVisible) {
        final View[] views = getWindowDecorViews();
        final ArrayList<View> allViews = new ArrayList<View>();
        final View[] nonDecorViews = getNonDecorViews(views);
        View view = null;

        if(nonDecorViews != null){
            for(int i = 0; i < nonDecorViews.length; i++){
                view = nonDecorViews[i];
                try {
                    addChildren(allViews, (ViewGroup)view, onlySufficientlyVisible);
                } catch (Exception ignored) {}
                if(view != null) allViews.add(view);
            }
        }

        if (views != null && views.length > 0) {
            view = getRecentDecorView(views);
            try {
                addChildren(allViews, (ViewGroup)view, onlySufficientlyVisible);
            } catch (Exception ignored) {}

            if(view != null) allViews.add(view);
        }

        return allViews;
    }
```

#### Searcher ####     


搜索类,核心的查找方法。    







#### Checker ####    

一些检查类，比如：isButtonChecked：   

`return (waiter.waitForAndGetView(index, expectedClass).isChecked());`

`Waits for and returns a View.`



   
#### Swiper & Tapper  #### 

Swiper和Tapper都只有一个`generateSwipeGesture`方法,内部都调用`MotionEvent.obtain`来绘制手势操作。    




(未完待续)   





### 参考资料 ###    

[https://developer.android.com/reference/android/test/InstrumentationTestRunner.html](https://developer.android.com/reference/android/test/InstrumentationTestRunner.html)  
[https://github.com/RobotiumTech/robotium/wiki/Questions-&-Answers](https://github.com/RobotiumTech/robotium/wiki/Questions-&-Answers)   
[https://github.com/RobotiumTech/robotium/wiki/Robotium-Tutorials](https://github.com/RobotiumTech/robotium/wiki/Robotium-Tutorials)   

