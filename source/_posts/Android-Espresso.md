---
title: Android Espresso
date: 2016-07-25 21:26:56
tags:
	- Android Espresso
	- Android测试
	- 自动化测试
---


# Android Espresso浅谈

- - - - -

# 简介

Espresso 也是一款自动化测试的框架，和UiAutomator类似。
基本上使用流程和UiAutomator类似。

<!-- more -->

**步骤：**

* 查找元素：找到UI上测试的元素位置，比如找到一个按钮
* 执行操作：给某个元素执行一个动作，比如触发按钮的点击事件
* 检查结果：判断做出的动作是否符合期望，比如按钮点击后，是否符合我的期望

# 准备
集成Espresso，首先需要保证App项目已经依赖了Gradle Testing。然后在gradle中添加如下依赖即可。

```groovy
dependencies {
    androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2.1'
}
```
# 创建Test Case
创建一个Espresso测试用例可以按照如下步骤。
* 找到你想测试的Activity，然后使用`onView`或者`onData`来查找UI元素
* 模拟用户用户点击，可以调用 `ViewInteraction.perform() `or` DataInteraction.perform()`,为了顺序的给同一个组件执行一些列的动作，可以使用链式的调用方式调用，中间使用逗号分隔，相当于传入一个动作数组。

下面是官方网站给出的一个例子，
```java
onView(withId(R.id.my_view))            // withId(R.id.my_view) is a ViewMatcher
        .perform(click())               // click() is a ViewAction
        .check(matches(isDisplayed())); // matches(isDisplayed()) is a ViewAssertion
```
## 使用ActivityTestRule创建Espresso
接下来的步骤是，使用ActivityTestRule来创建Espresso测试用例，下面是代码示例。`@RunWith(AndroidJUnit4.class)`设置测试代码怎么运行，`@Rule`来标注一个测试的Rule。

```java
package com.example.android.testing.espresso.BasicSample;

import org.junit.Before;
import org.junit.Rule;
import org.junit.Test;
import org.junit.runner.RunWith;

import android.support.test.rule.ActivityTestRule;
import android.support.test.runner.AndroidJUnit4;
...

@RunWith(AndroidJUnit4.class)
@LargeTest
public class ChangeTextBehaviorTest {

    private String mStringToBetyped;

    @Rule
    public ActivityTestRule<MainActivity> mActivityRule = new ActivityTestRule<>(
            MainActivity.class);

    @Before
    public void initValidString() {
        // Specify a valid string.
        mStringToBetyped = "Espresso";
    }

    @Test
    public void changeText_sameActivity() {
        // Type text and then press the button.
        onView(withId(R.id.editTextUserInput))
                .perform(typeText(mStringToBetyped), closeSoftKeyboard());
        onView(withId(R.id.changeTextBt)).perform(click());

        // Check that the text was changed.
        onView(withId(R.id.textToBeChanged))
                .check(matches(withText(mStringToBetyped)));
    }
}
```

## 使用ActivityInstrumentationTestCase2来创建Espresso
相对于上的测试用例，也可以使用ActivityInstrumentationTestCase2来创建Esprsso测试用例。
下面是代码示例：
```java
import android.support.test.InstrumentationRegistry;
public class MyEspressoTest extends ActivityInstrumentationTestCase2<MyActivity> {

    private MyActivity mActivity;

    public MyEspressoTest() {
        super(MyActivity.class);
    }

    @Before
    public void setUp() throws Exception {
        super.setUp();
        injectInstrumentation(InstrumentationRegistry.getInstrumentation());
        mActivity = getActivity();
    }
}
```
# 访问UI元素
对比与UiAutomator，Espresso可以直接根据id来访问元素，同样Espresso也是可以根据文本信息匹配进行访问的。
Espresso 提供` onView() `方法来访问UI元素，然后在执行一种操作，最后再进行验证。
下面的一个代码示例，表示访问一个EditText，输入一些内容，关闭输入法，最后点击按钮。
```java
public void testChangeText_sameActivity() {
    // Type text and then press the button.
    onView(withId(R.id.editTextUserInput)).perform(typeText(STRING_TO_BE_TYPED), closeSoftKeyboard());
    onView(withId(R.id.changeTextButton)).perform(click());
}
```
使用`withId()`进行Id访问，也可以使用`withText()`进行文本匹配。
```java
Espresso.onView(ViewMatchers.withText("Sign-in"));
Espresso.onView(ViewMatchers.withId(R.id.button_signin));
```
结合 Hamcrest库中的`Matchers`，可以使用`allOf()`来组合选择多个UI元素。
```java
onView(allOf(withId(R.id.button_signin), withText("Sign-in")));
onView(allOf(withId(R.id.button_signin), not(withText("Sign-out"))));
```
# AdapterView
当查找`AdapterView`之类的组件的时候，它的子元素都是动态生成的，如果要访问这些类的子元素，使用`onView`就会起不了作用。
Espresso提供`onData`方法来获取` DataInteraction `对象，然后在来访问目标元素。Espresso处理加载目标元素到当前层次结构。

**注意**
`onData() `方法不检查找到的元素是否匹配，它只是检查当前层次结构，如果不匹配会抛出`NoMatchingviewExption`异常。

下面代码演示，使用`onData`方法加载指定字符串数组的，找到对应的元素。
```java
onData(allOf(is(instanceOf(Map.class)),hasEntry(equalTo(LongListActivity.ROW_TEXT), is(str))));
```
`
# 执行动作
调用` ViewInteraction.perform()`和 `DataInteraction.perform() `，可以模拟用户执行UI元素的操作。可以指定一个或者多个动作，Espresso会按照指定的顺序，依次发送动作事件，这些动作是线程安全的。
` ViewActions` 可以提供一些列常用的方法，我们可以利用写方法来操作UI元素。

* ViewActions.click():  点击事件
* ViewActions.typeText(): 输入指定的文字内容
* ViewActions.scrollTo():  滑动
* ViewActions.pressKey():  按下按键
* ViewActions.clearText():  清空文本

# 校验结果

调用` ViewInteraction.check() `和` DataInteraction.check() `方法，可以判断UI元素的状态，如果断言失败，会抛出`AssertionFailedError`异常。
比如：
* doesNotExist: 断言某一个view不存在
* matches:  断言某个view存在，且符合一列的匹配
* selectedDescendentsMatch :断言指定的子元素存在，且他们的状态符合一些列的匹配

如下所示，代码表示查找的元素是否符合指定的字符串。
```java
public void testChangeText_sameActivity() {
    // Type text and then press the button.
    // Check that the text was changed.
    onView(withId(R.id.textToBeChanged)).check(matches(withText(STRING_TO_BE_TYPED)));
}

```

# 链接
[https://developer.android.com/training/testing/ui-testing/espresso-testing.html](https://developer.android.com/training/testing/ui-testing/espresso-testing.html)