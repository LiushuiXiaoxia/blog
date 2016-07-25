---
title: Android UIAutomator
date: 2016-07-25 21:25:22
categories: Android测试
tags:
	- Android UIAutomator
	- Android测试
	- 自动化测试
---


# Android UIAutomator浅谈

- - - - -

[TOC]

# 简介
Uiautomator是谷歌推出的，用于UI自动化测试的工具，也就是普通的手工测试，点击每个控件元素看看输出的结果是否符合预期。比如登陆界面分别输入正确和错误的用户名密码然后点击登陆按钮看看是否能否登陆以及是否有错误提示等。

注意：UI Automator测试框架是基于instrumentation的API，运行在Android JunitRunner 之上，同时UI Automator Test只运行在Android 4.3(API level 18)以上版本。

# 准备
集成UI Automator，首先需要保证App项目已经依赖了Gradle Testing。然后在gradle中添加如下依赖即可。

```groovy
dependencies {
    androidTestCompile 'com.android.support.test.uiautomator:uiautomator-v18:2.1.1'
}
```

# UI元素查找
在Android开发中，可以种findViewById来找到相应的控件，但是UI Automator是用来测试界面的，如果也是用相同的方式，那么则会依赖很大。UI Automator使用一种类似的方式来查找元素。UI Automator是根据元素的text、hint、contentDescription等属性进行查找的，为了使UI Automator能够获取到以上的属性值，请测试的UI界面是可以访问的，同时UI Automator也是可以访问这些控件元素。

可以使用uiautomatorviewer，来获取界面元素的层次关系以及各个元素的属性，然后UI Automator就可以根据这些属性信息查找到对应的控件元素。

获取步骤：
* 启动设备上的App
* 把设备连接到开发机器上
* 打开Android sdk目录，`<android-sdk>/tools/`。
* 启动`uiautomatorviewer`
然后可以操作`uiautomatorviewer`
* 点击Device ScreenShot，稍等一会，获取到了屏幕的快照，界面右侧是当前界面的布局结构以及属性信息。


**确保UI可以访问**
上面介绍过，UI Automator是根据元素的text、hint、contentDescription等属性进行查找的，所以需要尽可能给布局中的元素添加上面的属性。有时候程序员需要自定义一些控件，那么请实现`AccessibilityNodeProvider`，以确保可以正常使用。

## 访问UI控件
UI Automator 提供 `UiDevice` 类，这个类提供一些方法，可以获取设备的一些状态和属性，同时可以执行一系列动作来操作设备。下面是一个示例，演示怎么样货到 UiDevice 对象，以及按下Home键。

```java
import org.junit.Before;
import android.support.test.runner.AndroidJUnit4;
import android.support.test.uiautomator.UiDevice;
import android.support.test.uiautomator.By;
import android.support.test.uiautomator.Until;
...

@RunWith(AndroidJUnit4.class)
@SdkSuppress(minSdkVersion = 18)
public class ChangeTextBehaviorTest {

    private static final String BASIC_SAMPLE_PACKAGE
            = "com.example.android.testing.uiautomator.BasicSample";
    private static final int LAUNCH_TIMEOUT = 5000;
    private static final String STRING_TO_BE_TYPED = "UiAutomator";
    private UiDevice mDevice;

    @Before
    public void startMainActivityFromHomeScreen() {
        // Initialize UiDevice instance
        mDevice = UiDevice.getInstance(InstrumentationRegistry.getInstrumentation());

        // Start from the home screen
        mDevice.pressHome();

        // Wait for launcher
        final String launcherPackage = mDevice.getLauncherPackageName();
        assertThat(launcherPackage, notNullValue());
        mDevice.wait(Until.hasObject(By.pkg(launcherPackage).depth(0)),
                LAUNCH_TIMEOUT);

        // Launch the app
        Context context = InstrumentationRegistry.getContext();
        final Intent intent = context.getPackageManager()
                .getLaunchIntentForPackage(BASIC_SAMPLE_PACKAGE);
        // Clear out any previous instances
        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
        context.startActivity(intent);

        // Wait for the app to appear
        mDevice.wait(Until.hasObject(By.pkg(BASIC_SAMPLE_PACKAGE).depth(0)),
                LAUNCH_TIMEOUT);
    }
}
```
 然后可以使用` findObject() `方法来获取到界面的UI控件，如下所示，在一个设备上面找出符合规则的UI元素，然后再执行一些列动作。

```java
UiObject cancelButton = mDevice.findObject(new UiSelector()
        .text("Cancel"))
        .className("android.widget.Button"));
UiObject okButton = mDevice.findObject(new UiSelector()
        .text("OK"))
        .className("android.widget.Button"));

// Simulate a user-click on the OK button, if found.
if(okButton.exists() && okButton.isEnabled()) {
    okButton.click();
}
```
## 指定一个选择器
有时候，需要访问一个特定的UI组件，可以使用`UiSelector `。这个类提供一些列的方法，帮助找到特定的UI组件。
当有多个符合条件UI元素被查找到时候，第一个元素会不是使用。可以传递的一个UiSelector到构造方法，相当于链式访问。如果没有元素被找到，那么会抛出`UiAutomatorObjectNotFoundException `异常。
也可以使用`childSelector() `来帅选多个UiSelector对象，如下所示，ListView的子元素中有很多是相同的，可以根据这个方法，选择某一个子元素。
```java
UiObject appItem = new UiObject(new UiSelector()
        .className("android.widget.ListView")
        .instance(1)
        .childSelector(new UiSelector()
        .text("Apps")));
```
上面的查找还是很复杂，我们可以根据ResourceId 来替代文本查找的方式。文本找到的方式很脆弱，而且很容使测试失败，而且当语言环境变化了，文本查找就需要对照多个翻译版本的文本了。

# 执行动作
当我们根据文本查找找到对应的文本元素后，我们就可以随心所欲的进行操作了。

* click() 点击
* dragTo() 拖动当前元素
* setText() 设置文本
* swipeUp() 向上滑动，同理也有向下、向左、向右，swipeDown、swipeLeft、swipeRight。

UI Automator也可以提供一个Context，这样可以方便发送一个Intent或者是启动一个Activity。

```java
public void setUp() {
    // Launch a simple calculator app
    Context context = getInstrumentation().getContext();
    Intent intent = context.getPackageManager()
            .getLaunchIntentForPackage(CALC_PACKAGE);
    intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
            // Clear out any previous instances
    context.startActivity(intent);
    mDevice.wait(Until.hasObject(By.pkg(CALC_PACKAGE).depth(0)), TIMEOUT);
}
```
## 集合操作
可以使用` UiCollection`来模拟用户对一组UI控件进行操作，比如有时候界面上有个Listview。为了创建一个 UiCollection 对象，可以指定一个UiSelector搜索这个UI容器上满足这个条件的所有子元素。

下面代码演示如何在一个一组控件中进行操作。
```java
UiCollection videos = new UiCollection(new UiSelector()
        .className("android.widget.FrameLayout"));

// Retrieve the number of videos in this collection:
int count = videos.getChildCount(new UiSelector()
        .className("android.widget.LinearLayout"));

// Find a specific video and simulate a user-click on it
UiObject video = videos.getChildByText(new UiSelector()
        .className("android.widget.LinearLayout"), "Cute Baby Laughing");
video.click();

// Simulate selecting a checkbox that is associated with the video
UiObject checkBox = video.getChild(new UiSelector()
        .className("android.widget.Checkbox"));
if(!checkBox.isSelected()) checkbox.click();
```
## 操作可以滚动的UI元素
可以使用`UiScrollable `来模拟用户对界面的滚动操作（水平、垂直）,这个技术可以帮我们搞定，当某个界面没有显示的时候，我们可以滚动界面，把那个界面显示出来。

```java
UiScrollable settingsItem = new UiScrollable(new UiSelector()
        .className("android.widget.ListView"));
UiObject about = settingsItem.getChildByText(new UiSelector()
        .className("android.widget.LinearLayout"), "About tablet");
about.click();
```

# 校验结果
Ui Automator 是基于InstrumentationTestCase，同样InstrumentationTestCase是基于标准的JUnit Assert，那么我们也可以使用标准的JUnit Assert来判断结果。

下面代码片段演示一个计算器的代码，以及验证结果。
```java
private static final String CALC_PACKAGE = "com.myexample.calc";

public void testTwoPlusThreeEqualsFive() {
    // Enter an equation: 2 + 3 = ?
    mDevice.findObject(new UiSelector()
            .packageName(CALC_PACKAGE).resourceId("two")).click();
    mDevice.findObject(new UiSelector()
            .packageName(CALC_PACKAGE).resourceId("plus")).click();
    mDevice.findObject(new UiSelector()
            .packageName(CALC_PACKAGE).resourceId("three")).click();
    mDevice.findObject(new UiSelector()
            .packageName(CALC_PACKAGE).resourceId("equals")).click();

    // Verify the result = 5
    UiObject result = mDevice.findObject(By.res(CALC_PACKAGE, "result"));
    assertEquals("5", result.getText());
}
```

# Api

## UiDevice
```java
void	clearLastTraversedText()
// Clears the text from the last UI traversal event.
// 清楚上次UI遍历的事件？

boolean	click(int x, int y)
// Perform a click at arbitrary coordinates specified by the user
// 根据坐标点击

boolean	drag(int startX, int startY, int endX, int endY, int steps)
// Performs a swipe from one coordinate to another coordinate.
// 拖动

void	dumpWindowHierarchy(File dest)
// Dump the current window hierarchy to a File.
// dump当前的层次化结构到文件中

void	dumpWindowHierarchy(OutputStream out)
// Dump the current window hierarchy to an OutputStream.
// dump当前的层次化结构到流中

void	dumpWindowHierarchy(String fileName)
// This method is deprecated. Use dumpWindowHierarchy(File) or dumpWindowHierarchy(OutputStream) instead.
// dump当前的层次化结构到文件中

UiObject2	findObject(BySelector selector)
// Returns the first object to match the selector criteria.
// 根据BySelector查找

UiObject	findObject(UiSelector selector)
// Returns a UiObject which represents a view that matches the specified selector criteria.
// 根据UiSelector 查找

List<UiObject2>	findObjects(BySelector selector)
// Returns all objects that match the selector criteria.
// 根据BySelector查找

void	freezeRotation()
// Disables the sensors and freezes the device rotation at its current rotation state.
// 冻结旋转的状态

String	getCurrentActivityName()
// This method is deprecated. The results returned should be considered unreliable
// 获取当前Activity的名字，已经被废弃

String	getCurrentPackageName()
// Retrieves the name of the last package to report accessibility events.
// 获取当前package

int	getDisplayHeight()
// Gets the height of the display, in pixels.

int	getDisplayRotation()
// Returns the current rotation of the display, as defined in Surface

Point	getDisplaySizeDp()
// Returns the display size in dp (device-independent pixel) The returned display size is adjusted per screen rotation.

int	getDisplayWidth()
// Gets the width of the display, in pixels.

static UiDevice	getInstance()
// This method is deprecated. Should use getInstance(Instrumentation) instead. This version hides UiDevice's dependency on having an Instrumentation reference and is prone to misuse.
// 获取一个对象

static UiDevice	getInstance(Instrumentation instrumentation)
// Retrieves a singleton instance of UiDevice

String	getLastTraversedText()
// Retrieves the text from the last UI traversal event received.
// 获取上一次遍历的文本

String	getLauncherPackageName()
// Retrieves default launcher package name
// 获取运行的packagename

String	getProductName()
// Retrieves the product name of the device.

boolean	hasAnyWatcherTriggered()
// Checks if any registered UiWatcher have triggered.
// 检查是否有触发器触发

boolean	hasObject(BySelector selector)
// Returns whether there is a match for the given selector criteria.
// 是否有符合的条件的

boolean	hasWatcherTriggered(String watcherName)
// Checks if a specific registered UiWatcher has triggered.

boolean	isNaturalOrientation()
// Check if the device is in its natural orientation.

boolean	isScreenOn()
// Checks the power manager if the screen is ON.

boolean	openNotification()
// Opens the notification shade.
// 打开通知

boolean	openQuickSettings()
// Opens the Quick Settings shade.
// 打开设置

<R> R	performActionAndWait(Runnable action, EventCondition<R> condition, long timeout)
// Performs the provided action and waits for the condition to be met.

boolean	pressBack()
// Simulates a short press on the BACK button.

boolean	pressDPadCenter()
// Simulates a short press on the CENTER button.

boolean	pressDPadDown()
// Simulates a short press on the DOWN button.

boolean	pressDPadLeft()
// Simulates a short press on the LEFT button.

boolean	pressDPadRight()
// Simulates a short press on the RIGHT button.

boolean	pressDPadUp()
// Simulates a short press on the UP button.

boolean	pressDelete()
// Simulates a short press on the DELETE key.

boolean	pressEnter()
// Simulates a short press on the ENTER key.

boolean	pressHome()
// Simulates a short press on the HOME button.

boolean	pressKeyCode(int keyCode)
// Simulates a short press using a key code.

boolean	pressKeyCode(int keyCode, int metaState)
// Simulates a short press using a key code.

boolean	pressMenu()
// Simulates a short press on the MENU button.

boolean	pressRecentApps()
// Simulates a short press on the Recent Apps button.

boolean	pressSearch()
// Simulates a short press on the SEARCH button.

void	registerWatcher(String name, UiWatcher watcher)
// Registers a UiWatcher to run automatically when the testing framework is unable to find a match using a UiSelector.

void	removeWatcher(String name)
// Removes a previously registered UiWatcher.

void	resetWatcherTriggers()
// Resets a UiWatcher that has been triggered.

void	runWatchers()
// This method forces all registered watchers to run.

void	setCompressedLayoutHeirarchy(boolean compressed)
// Enables or disables layout hierarchy compression.

void	setOrientationLeft()
// Simulates orienting the device to the left and also freezes rotation by disabling the sensors.
// 设置旋转方向

void	setOrientationNatural()
// Simulates orienting the device into its natural orientation and also freezes rotation by disabling the sensors.

void	setOrientationRight()
// Simulates orienting the device to the right and also freezes rotation by disabling the sensors.

void	sleep()
// This method simply presses the power button if the screen is ON else it does nothing if the screen is already OFF.
// 关闭屏幕

boolean	swipe(int startX, int startY, int endX, int endY, int steps)
// Performs a swipe from one coordinate to another using the number of steps to determine smoothness and speed.

boolean	swipe(Point[] segments, int segmentSteps)
// Performs a swipe between points in the Point array.

boolean	takeScreenshot(File storePath, float scale, int quality)
// Take a screenshot of current window and store it as PNG The screenshot is adjusted per screen rotation
// 截屏
boolean	takeScreenshot(File storePath)
// Take a screenshot of current window and store it as PNG Default scale of 1.0f (original size) and 90% quality is used The screenshot is adjusted per screen rotation

void	unfreezeRotation()
// Re-enables the sensors and un-freezes the device rotation allowing its contents to rotate with the device physical rotation.

<R> R	wait(SearchCondition<R> condition, long timeout)
// Waits for given the condition to be met.

void	waitForIdle(long timeout)
// Waits for the current application to idle.

void	waitForIdle()
// Waits for the current application to idle.

boolean	waitForWindowUpdate(String packageName, long timeout)
// Waits for a window content update event to occur.

void	wakeUp()
// This method simulates pressing the power button if the screen is OFF else it does nothing if the screen is already ON.
// 点亮屏幕
```
## UiObject
```java
void	clearTextField()
// Clears the existing text contents in an editable field.
// 清空输入接口

boolean	click()
// Performs a click at the center of the visible bounds of the UI element represented by this UiObject.
// 点击

boolean	clickAndWaitForNewWindow()
// Waits for window transitions that would typically take longer than the usual default timeouts.
// 点击并等待新界面

boolean	clickAndWaitForNewWindow(long timeout)
// Performs a click at the center of the visible bounds of the UI element represented by this UiObject and waits for window transitions.
// 点击并等待新界面，设置等待时间

boolean	clickBottomRight()
// Clicks the bottom and right corner of the UI element
// 点击右下边

boolean	clickTopLeft()
// Clicks the top and left corner of the UI element

boolean	dragTo(UiObject destObj, int steps)
// Drags this object to a destination UiObject.
// 拖动

boolean	dragTo(int destX, int destY, int steps)
// Drags this object to arbitrary coordinates.

boolean	exists()
// Check if view exists.
// 判断是否存在


Rect	getBounds()
// Returns the view's bounds property.
// 返回边界

UiObject	getChild(UiSelector selector)
// Creates a new UiObject for a child view that is under the present UiObject.
// 根据条件获取子元素

int	getChildCount()
// Counts the child views immediately under the present UiObject.
// 获取子元素数量

String	getClassName()
// Retrieves the className property of the UI element.
// 获取当前元素的class name

String	getContentDescription()
// Reads the content_desc property of the UI element

UiObject	getFromParent(UiSelector selector)
// Creates a new UiObject for a sibling view or a child of the sibling view, relative to the present UiObject.

String	getPackageName()
// Reads the view's package property

final UiSelector	getSelector()
// Debugging helper.

String	getText()
// Reads the text property of the UI element

Rect	getVisibleBounds()
// Returns the visible bounds of the view.
// 获取可见边界

boolean	isCheckable()
// Checks if the UI element's checkable property is currently true.
// 是否可以点击

boolean	isChecked()
// Check if the UI element's checked property is currently true
// 是否已经选中

boolean	isClickable()
// Checks if the UI element's clickable property is currently true.

boolean	isEnabled()
// Checks if the UI element's enabled property is currently true.

boolean	isFocusable()
// Check if the UI element's focusable property is currently true.

boolean	isFocused()
// Check if the UI element's focused property is currently true

boolean	isLongClickable()
// Check if the view's long-clickable property is currently true

boolean	isScrollable()
// Check if the view's scrollable property is currently true

boolean	isSelected()
// Checks if the UI element's selected property is currently true.

boolean	longClick()
// Long clicks the center of the visible bounds of the UI element
// 长按

boolean	longClickBottomRight()
// Long clicks bottom and right corner of the UI element

boolean	longClickTopLeft()
// Long clicks on the top and left corner of the UI element

boolean	performMultiPointerGesture(PointerCoords... touches)
// Performs a multi-touch gesture.

boolean	performTwoPointerGesture(Point startPoint1, Point startPoint2, Point endPoint1, Point endPoint2, int steps)
// Generates a two-pointer gesture with arbitrary starting and ending points.

boolean	pinchIn(int percent, int steps)
// Performs a two-pointer gesture, where each pointer moves diagonally toward the other, from the edges to the center of this UiObject .

boolean	pinchOut(int percent, int steps)
// Performs a two-pointer gesture, where each pointer moves diagonally opposite across the other, from the center out towards the edges of the this UiObject.

boolean	setText(String text)
// Sets the text in an editable field, after clearing the field's content.
// 设置输入内容

boolean	swipeDown(int steps)
// Performs the swipe down action on the UiObject.

boolean	swipeLeft(int steps)
// Performs the swipe left action on the UiObject.

boolean	swipeRight(int steps)
// Performs the swipe right action on the UiObject.

boolean	swipeUp(int steps)
// Performs the swipe up action on the UiObject.

boolean	waitForExists(long timeout)
// Waits a specified length of time for a view to become visible.

boolean	waitUntilGone(long timeout)
// Waits a specified length of time for a view to become undetectable.
```

# 总结

**优点：**
* 可以对所有操作进行自动化，操作简单
* 不需要对被测程序进行重签名，且，可以测试所有设备上的程序，比如~某APP，比如~拨号，比如~发信息等等
* 对于控件定位，要比robotium简单一点点

**缺点：**
* Ui Automator需要android level 16以上才可以使用，因为在level 16及以上的API里面才带有uiautomator工具
* 如果想要使用resource-id定位控件，则需要level 18及以上才可以
* 对中文支持不好（不代表不支持，第三方jar可以实现）

# 链接

[https://developer.android.com/training/testing/ui-testing/uiautomator-testing.html](https://developer.android.com/training/testing/ui-testing/uiautomator-testing.html)``