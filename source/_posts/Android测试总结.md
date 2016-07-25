---
title: Android测试总结
date: 2016-07-25 21:35:18
tags:
	- Android测试
	- 测试
---


# Android测试总结

---

[TOC]
# 简介
最近整理了Android测试方便的只是，主要涉及代码测试和自动化测试。

# 代码测试
## Junit
[JUnit浅谈](http://mycommons.cn/2016/07/19/JUnit%E6%B5%85%E8%B0%88/)
## Mockito
[Mockito浅谈](http://mycommons.cn/2016/07/19/Mockito%E6%B5%85%E8%B0%88/)
## Mockwebserver
[MockWebServer](http://mycommons.cn/2016/07/25/MockWebServer/)

# Android自动化测试
## Android monkey
[Android Monkey整理](http://mycommons.cn/2016/07/19/Android-Monkey%E6%95%B4%E7%90%86/)
## Android monkeyrunner
[Android monkeyrunner整理](http://mycommons.cn/2016/07/25/AndroidMonkeyRunner/)
## Android UIAutomator
[Android UIAutomator浅谈](http://mycommons.cn/2016/07/25/Android-UIAutomator/)
## Android Espresso
[Android Espresso浅谈](http://mycommons.cn/2016/07/25/Android-Espresso/)

## 自动化测试示例

下面示例一个Android项目，就是一个简单的登录页面，依次使用上面介绍的自动化测试方案测试界面。

首先是界面布局:
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout android:id="@+id/activity_main"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="20dp"
    tools:context="cn.mycommons.testcase.MainActivity">

    <EditText
        android:id="@+id/edtUserName"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:hint="Input user name" />

    <EditText
        android:id="@+id/edtPasswd"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:hint="Input password"
        android:contentDescription="Input password"
        android:inputType="textPassword" />

    <Button
        android:id="@+id/btnLogin"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:text="Login" />
</LinearLayout>
```
其次是页面代码：
```java
package cn.mycommons.testcase;

import android.content.Intent;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    EditText edtUserName;
    EditText edtPasswd;
    Button btnLogin;

    String userName;
    String passwd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        edtUserName = (EditText) findViewById(R.id.edtUserName);
        edtPasswd = (EditText) findViewById(R.id.edtPasswd);
        btnLogin = (Button) findViewById(R.id.btnLogin);

        btnLogin.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                userName = edtUserName.getText().toString();
                passwd = edtPasswd.getText().toString();
                if (check(userName, passwd)) {
                    doLogin(userName, passwd);
                }
            }
        });
    }

    boolean check(String userName, String passwd) {
        do {
            if (userName.length() < 5) {
                showToast("User name invalidate");
                break;
            }
            if (passwd.length() < 5) {
                showToast("Password invalidate");
                break;
            }
            return true;
        } while (false);
        return false;
    }

    void doLogin(String userName, String passwd) {
        if ("admin".equals(userName) && "admin".equals(passwd)) {
            showToast("Login success");
            startActivity(new Intent(this, SuccessActivity.class));
        } else {
            showToast("Login fail");
        }
    }

    void showToast(String msg) {
        Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
    }
}
```

### Monkey
Monkey只是检查使用下shell命令即可。
```sh
adb shell monkey -p cn.mycommons.testcase -v 50000
```

###  Monkey Runner
Monkey Runner提供的是一个python文件，然后调用monkeyrunner命令即可。
```sh
$ monkeyrunner test_case.py
```

```python
# Imports the monkeyrunner modules used by this program
from com.android.monkeyrunner import MonkeyRunner, MonkeyDevice

# Connects to the current device, returning a MonkeyDevice object
print 'wait for device connection.'
device = MonkeyRunner.waitForConnection()
print 'connect device success.'

# Takes a screenshot
result = device.takeSnapshot()
print 'takeSnapshot success.'
# Writes the screenshot to a file
result.writeToFile('./test_case1.png','png')
print 'save image to file success.'

# input user name
device.touch(200,380,'DOWN_AND_UP')
print 'touch user name'
for x in xrange(1,100):
	device.press("KEYCODE_DEL",'DOWN_AND_UP')
print 'delete user name'
device.type("admin")
print 'input admin to user name'

# input passwd 
device.touch(200,500,'DOWN_AND_UP')
print 'touch password'
for x in xrange(1,100):
	device.press("KEYCODE_DEL",'DOWN_AND_UP')
print 'delete password'
device.type("admin")
print 'input admin to password'

# press login button
device.touch(200,680,'DOWN_AND_UP')
print 'press login button'

MonkeyRunner.sleep(2)

# Takes a screenshot
result = device.takeSnapshot()
print 'takeSnapshot success.'
# Writes the screenshot to a file
result.writeToFile('./test_case2.png','png')
print 'save image to file success.'
```
### UiAutomator
```java
package cn.mycommons.testcase;

import android.content.Context;
import android.content.Intent;
import android.os.Build;
import android.support.test.InstrumentationRegistry;
import android.support.test.filters.SdkSuppress;
import android.support.test.runner.AndroidJUnit4;
import android.support.test.uiautomator.By;
import android.support.test.uiautomator.UiDevice;
import android.support.test.uiautomator.UiObject;
import android.support.test.uiautomator.UiObjectNotFoundException;
import android.support.test.uiautomator.UiSelector;
import android.support.test.uiautomator.Until;
import org.hamcrest.Matchers;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import static org.junit.Assert.assertThat;

@RunWith(AndroidJUnit4.class)
@SdkSuppress(minSdkVersion = Build.VERSION_CODES.JELLY_BEAN_MR2)
public class MainActivityTest {

    private static final String BASIC_SAMPLE_PACKAGE = "cn.mycommons.testcase";
    private static final int LAUNCH_TIMEOUT = 5000;
    private static final String STRING_TO_BE_TYPED = "UiAutomator";

    private UiDevice mDevice;

    @Before
    public void before() {
        mDevice = UiDevice.getInstance(InstrumentationRegistry.getInstrumentation());
        mDevice.pressHome();

        // Wait for launcher
        final String launcherPackage = mDevice.getLauncherPackageName();
        assertThat(launcherPackage, Matchers.notNullValue());
        mDevice.wait(Until.hasObject(By.pkg(launcherPackage).depth(0)), LAUNCH_TIMEOUT);

        Context context = InstrumentationRegistry.getContext();
        final Intent intent = context.getPackageManager().getLaunchIntentForPackage(BASIC_SAMPLE_PACKAGE);
        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
        context.startActivity(intent);

        // Wait for the app to appear
        mDevice.wait(Until.hasObject(By.pkg(BASIC_SAMPLE_PACKAGE).depth(0)), LAUNCH_TIMEOUT);
    }

    @Test
    public void testLogin1() throws UiObjectNotFoundException {
        UiObject login = mDevice.findObject(new UiSelector().text("Login"));
        UiObject edtUserName = mDevice.findObject(new UiSelector().text("Input user name"));
        UiObject edtPasswd = mDevice.findObject(new UiSelector().descriptionContains("Input password"));

        edtUserName.clearTextField();
        edtPasswd.clearTextField();

        edtUserName.setText("admin");
        edtPasswd.setText("admin");
        login.click();

        mDevice.waitForWindowUpdate(BuildConfig.FLAVOR, 3000);
    }

    @Test
    public void testLogin2() throws UiObjectNotFoundException {
        UiObject login = mDevice.findObject(new UiSelector().text("Login"));
        UiObject edtUserName = mDevice.findObject(new UiSelector().text("Input user name"));
        UiObject edtPasswd = mDevice.findObject(new UiSelector().descriptionContains("Input password"));

        edtUserName.clearTextField();
        edtPasswd.clearTextField();
        edtUserName.setText("");
        edtPasswd.setText("");

        login.click();
    }

    @Test
    public void testLogin3() throws UiObjectNotFoundException {
        UiObject login = mDevice.findObject(new UiSelector().text("Login"));
        UiObject edtUserName = mDevice.findObject(new UiSelector().text("Input user name"));
        UiObject edtPasswd = mDevice.findObject(new UiSelector().descriptionContains("Input password"));

        edtUserName.clearTextField();
        edtPasswd.clearTextField();
        edtUserName.setText("abc");
        edtPasswd.setText("");

        login.click();
    }

    @Test
    public void testLogin4() throws UiObjectNotFoundException {
        UiObject login = mDevice.findObject(new UiSelector().text("Login"));
        UiObject edtUserName = mDevice.findObject(new UiSelector().text("Input user name"));
        UiObject edtPasswd = mDevice.findObject(new UiSelector().descriptionContains("Input password"));

        edtUserName.clearTextField();
        edtPasswd.clearTextField();
        edtUserName.setText("");
        edtPasswd.setText("abc");

        login.click();
    }

    @Test
    public void testLogin5() throws UiObjectNotFoundException {
        UiObject login = mDevice.findObject(new UiSelector().text("Login"));
        UiObject edtUserName = mDevice.findObject(new UiSelector().text("Input user name"));
        UiObject edtPasswd = mDevice.findObject(new UiSelector().descriptionContains("Input password"));

        edtUserName.clearTextField();
        edtPasswd.clearTextField();
        edtUserName.setText("abc");
        edtPasswd.setText("abc");

        login.click();
    }

    @Test
    public void testLogin6() throws UiObjectNotFoundException {
        UiObject login = mDevice.findObject(new UiSelector().text("Login"));
        UiObject edtUserName = mDevice.findObject(new UiSelector().text("Input user name"));
        UiObject edtPasswd = mDevice.findObject(new UiSelector().descriptionContains("Input password"));

        edtUserName.clearTextField();
        edtPasswd.clearTextField();
        edtUserName.setText("abcedf");
        edtPasswd.setText("abc");

        login.click();
    }

    @Test
    public void testLogin7() throws UiObjectNotFoundException {
        UiObject login = mDevice.findObject(new UiSelector().text("Login"));
        UiObject edtUserName = mDevice.findObject(new UiSelector().text("Input user name"));
        UiObject edtPasswd = mDevice.findObject(new UiSelector().descriptionContains("Input password"));


        edtUserName.clearTextField();
        edtPasswd.clearTextField();
        edtUserName.setText("abcedf");
        edtPasswd.setText("abcedf");

        login.click();
    }
}
```
### Espressor
```java
package cn.mycommons.testcase;

import android.support.test.espresso.Espresso;
import android.support.test.espresso.action.ViewActions;
import android.support.test.espresso.matcher.ViewMatchers;
import android.support.test.rule.ActivityTestRule;
import android.support.test.runner.AndroidJUnit4;
import org.junit.Rule;
import org.junit.Test;
import org.junit.runner.RunWith;

@RunWith(AndroidJUnit4.class)
public class MainActivityEspressoTest {

    @Rule
    public ActivityTestRule<MainActivity> testRule = new ActivityTestRule<>(MainActivity.class);

    @Test
    public void testLogin() {
        Espresso.onView(ViewMatchers.withContentDescription(R.id.edtUserName)).perform(ViewActions.typeText("admin"));
        Espresso.onView(ViewMatchers.withId(R.id.edtPasswd)).perform(ViewActions.typeText("admin"));
        Espresso.onView(ViewMatchers.withId(R.id.btnLogin)).perform(ViewActions.click());
    }

    @Test
    public void testLogin1() {
        Espresso.onView(ViewMatchers.withId(R.id.edtUserName)).perform(ViewActions.typeText(""));
        Espresso.onView(ViewMatchers.withId(R.id.edtPasswd)).perform(ViewActions.typeText(""));
        Espresso.onView(ViewMatchers.withId(R.id.btnLogin)).perform(ViewActions.click());
    }

    @Test
    public void testLogin2() {
        Espresso.onView(ViewMatchers.withId(R.id.edtUserName)).perform(ViewActions.typeText("abc"));
        Espresso.onView(ViewMatchers.withId(R.id.edtPasswd)).perform(ViewActions.typeText(""));
        Espresso.onView(ViewMatchers.withId(R.id.btnLogin)).perform(ViewActions.click());
    }

    @Test
    public void testLogin3() {
        Espresso.onView(ViewMatchers.withId(R.id.edtUserName)).perform(ViewActions.typeText(""));
        Espresso.onView(ViewMatchers.withId(R.id.edtPasswd)).perform(ViewActions.typeText("abc"));
        Espresso.onView(ViewMatchers.withId(R.id.btnLogin)).perform(ViewActions.click());
    }

    @Test
    public void testLogin4() {
        Espresso.onView(ViewMatchers.withId(R.id.edtUserName)).perform(ViewActions.typeText("abc"));
        Espresso.onView(ViewMatchers.withId(R.id.edtPasswd)).perform(ViewActions.typeText("abc"));
        Espresso.onView(ViewMatchers.withId(R.id.btnLogin)).perform(ViewActions.click());
    }

    @Test
    public void testLogin5() {
        Espresso.onView(ViewMatchers.withId(R.id.edtUserName)).perform(ViewActions.typeText("abcdef"));
        Espresso.onView(ViewMatchers.withId(R.id.edtPasswd)).perform(ViewActions.typeText("abcdef"));
        Espresso.onView(ViewMatchers.withId(R.id.btnLogin)).perform(ViewActions.click());
    }
}
```
## 自动化测试总结
* Monkey
准确来说，这不算是自动化测试，因为其只能产生随机的事件，无法按照既定的步骤操作；

* Monkeyrunner
优点：操作最为简单，可以录制测试脚本，可视化操作；
缺点：主要生成坐标的自动化操作，移植性不强，功能最为局限，上面代码中已经显示出来，完全使用的数字坐标，移植到另外一个设备，则不能运行。

*  UiAutomator
优点：可以对所有操作进行自动化，操作简单；
缺点：Android版本需要高于4.3，无法根据控件ID操作，相对来说功能较为局限，但也够用了；

* Espresso
优点：主要针对某一个APK进行自动化测试，APK可以有源码，也可以没有源码，功能强大；
缺点：针对APK操作，因此操作相对复杂；

**总结：**由上面介绍可以有这样的结论：测试某个APK，可以选择Espresso；测试过程可能涉及多个APK，选择UiAutomator；一些简单的测试，选择Monkeyrunner；