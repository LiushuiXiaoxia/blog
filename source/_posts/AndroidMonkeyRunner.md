---
title: AndroidMonkeyRunner
date: 2016-07-25 21:22:56
categories: Android测试
tags:
	- Android Monkey Runner
	- Android测试
	- 自动化测试
---

#  Android monkeyrunner整理

- - - - -

[TOC]

# 简介
monkeyrunner即android SDK中自带的工具之一，此工具提供API可按制android设备或模拟器。
monkeyrunner提供了一个API，使用此API写出的程序可以在Android代码之外控制Android设备和模拟器。通过monkeyrunner，您可以写出一个Python程序去安装一个Android应用程序或测试包，运行它，向它发送模拟击键，截取它的用户界面图片，并将截图存储于工作站上。
monkeyrunner工具的主要目的是用于测试功能/框架水平上的应用程序和设备，或用于运行单元测试套件，但您当然也可以将其用于其它目的。

# 特性
- 多设备控制：monkeyrunner API可以跨多个设备或模拟器实施测试套件。您可以在同一时间接上所有的设备或一次启动全部模拟器（或统统一起），依据程序依次连接到每一个，然后运行一个或多个测试。您也可以用程序启动一个配置好的模拟器，运行一个或多个测试，然后关闭模拟器。

- 功能测试：monkeyrunner可以为一个应用自动化功能测试。为您提供按键或触摸事件的输入数值，然后观察输出结果的截屏。       

- 回归测试：monkeyrunner可以运行某个应用，并将其结果截屏与既定已知正确的结果截屏相比较，以此测试应用的稳定性。       

- 可扩展的自动化：由于monkeyrunner是一个API工具包，您可以基于Python模块和程序开发一整套系统，以此来控制Android设备。除了使用monkeyrunner    API之外，您还可以使用标准的Python    os和subprocess模块来调用如adb这样的Android工具。    


# 使用方法

## Monkeyrunner API
主要包括三个模块
1、MonkeyRunner:这个类提供了用于连接monkeyrunner和设备或模拟器的方法，它还提供了用于创建用户界面显示提供了方法。
2、MonkeyDevice:代表一个设备或模拟器。这个类为安装和卸载包、开启Activity、发送按键和触摸事件、运行测试包等提供了方法。
3、MonkeyImage:这个类提供了捕捉屏幕的方法。这个类为截图、将位图转换成各种格式、对比两个MonkeyImage对象、将image保存到文件等提供了方法。

```py
#引用导入API
from com.android.monkeyrunner import <module> 
```

## 运行
命令语法为：

```bat
monkeyrunner -plugin <plugin_jar> <program_filename> <program_options>
``` 
方式一：在CMD命令窗口直接运行monkeyrunner
方式二：使用Python编写测试代码文件，在CMD中执行monkeyrunner Findyou.py运行
不论使用哪种方式，您都需要调用SDK目录的tools子目录下的monkeyrunner命令。

**注意**
在运行monkeyrunner之前必须先运行相应的模拟器或连接真机，否则monkeyrunner无法连接到设备

运行模拟器有两种方法：
1、通过eclipse中执行模拟器
 2、在CMD中通过命令调用模拟器
这里介绍通过命令，在CMD中执行模拟器的方法
`emulator -avd test`
上面命令中test是指模拟器的名称。
 
附：
问题：CMD运行提示monkeyrunner不是内部或外部命令，也不是可运行的程序或批处理文件。
解决：电脑环境变量未配置，将monkeyrunner所在目录配在环境变量里。
变量名：Path
变量值：`${ANDROID_HOME}\platform-tools`

# 示例——卸载旧的APP，安装新的APP
**准备**
a. 连接安卓真机设备
b. 运行CMD，检测是否连接成功
**命令**

```sh
adb devices
```
## 方法一
1.打开CMD，运行monkeyrunner
2.进入monkeyrunner的shell命令交互模式后，逐条输入以下命令

```python
from com.android.monkeyrunner import MonkeyRunner, MonkeyDevice, MonkeyImage
device = MonkeyRunner.waitForConnection()
device.removePackage('xx.oo.package')
device.installPackage('xx.oo.apk')
```
注：每条命令的作用，请见方法二中的注解

## 方法二

*  编写Python测试代码

```py
# 引入本程序所用到的模块
from com.android.monkeyrunner import MonkeyRunner, MonkeyDevice, MonkeyImage

# 连接手机设备
device = MonkeyRunner.waitForConnection()

# 截图
result = device.takeSnapshot()

# 将截图保存到文件 
result.writeToFile('./shot01.png','png')

# 卸载APP
device.removePackage('xx.oo.package')
print ('Uninstall xx.oo.package Success!')

# 暂停5秒
MonkeyRunner.sleep(5)

# 截图
result = device.takeSnapshot()
result.writeToFile('./shot02.png','png')

# 安装新的APP
device.installPackage('xx.oo.apk')
print ('Install xx.oo.apk Success!')

# 截图
result = device.takeSnapshot()
result.writeToFile('./shot03.png','png')
```
**注：**拷贝运行时请去掉中文注释；或者在开头加入 #coding=utf-8 

* 运行脚本

```sh
Monkeyrunner Test1.py
```
* 检查手机app是否已更新

# API
Monkey Runner主要API有下面三种，代码package为`com.android.monkeyrunner`。
* MonkeyRunner 
弹出警告框、选择列表、帮助文档、输入、暂停、等待连接设备等。
* MonkeyDevice
主要操作设备的，包含安装、卸载，发送广播，启动应用等。
* MonkeyImage
主要操作图片，包含图片保存、对比、格式转化、获取像素点等。


## MonkeyRunner

```java
void	alert (string message, string title, string okTitle)
// Displays an alert dialog to the process running the current program.
// 弹出一个确认框，在pc上面

integer	choice (string message, iterable choices, string title)
// Displays a dialog with a list of choices to the process running the current program.
// 弹出一个选择框，在pc上面

void	help (string format)
// Displays the monkeyrunner API reference in a style similar to that of Python's pydoc tool, using the specified format.

string	input (string message, string initialValue, string title, string okTitle, string cancelTitle)
// Displays a dialog that accepts input.
// 弹出一个输入框，在pc上面

void	sleep (float seconds)
// Pauses the current program for the specified number of seconds.

MonkeyDevice	waitForConnection (float timeout, string deviceId)
// Tries to make a connection between the monkeyrunner backend and the specified device or emulator.
// 待定设备连接
```
## MonkeyDevice
可以使用`newdevice = MonkeyRunner.waitForConnection()`获取MonkeyDevice对象

```java
void	broadcastIntent (string uri, string action, string data, string mimetype, iterable categories dictionary extras, component component, iterable flags)
//Broadcasts an Intent to this device, as if the Intent were coming from an application.
// 向设备发送一个广播

void	drag (tuple start, tuple end, float duration, integer steps)
// Simulates a drag gesture (touch, hold, and move) on this device's screen.
// 拖动

object	getProperty (string key)
//Given the name of a system environment variable, returns its value for this device. The available variable names are listed in the detailed description of this method.
//object	getSystemProperty (string key)
//The API equivalent of adb shell getprop <key>. This is provided for use by platform developers.
// 获取一个属性

void	installPackage (string path)
//Installs the Android application or test package contained in packageFile onto this device. If the application or test package is already installed, it is replaced.
//dictionary	instrument (string className, dictionary args)
//Runs the specified component under Android instrumentation, and returns the results in a dictionary whose exact format is dictated by the component being run. The component must already be present on this device.
// 安装一个apk，参数是apk的路径

void	press (string name, dictionary type)
//Sends the key event specified by type to the key specified by keycode.
// 发送一个按钮事件

void	reboot (string into)
//Reboots this device into the bootloader specified by bootloadType.
// 重启设备

void	removePackage (string package)
//Deletes the specified package from this device, including its data and cache.
//根据apk的package删除app

object	shell (string cmd)
//Executes an adb shell command and returns the result, if any.
// 执行一个shell命令

void	startActivity (string uri, string action, string data, string mimetype, iterable categories dictionary extras, component component, flags)
//Starts an Activity on this device by sending an Intent constructed from the supplied arguments.
//MonkeyImage	takeSnapshot()
//Captures the entire screen buffer of this device, yielding a MonkeyImage object containing a screen capture of the current display.
// 启动一个activity

void	touch (integer x, integer y, integer type)
//Sends a touch event specified by type to the screen location specified by x and y.
// 发送触摸事件

void	type (string message)
//Sends the characters contained in message to this device, as if they had been typed on the device's keyboard. This is equivalent to calling press() for each keycode in message using the key event type DOWN_AND_UP.
// 向设备输入一些字符串

void	wake ()
//Wakes the screen of this device.
// 唤醒设备
```

## MonkeyImage
使用`newimage = MonkeyDevice.takeSnapshot()`获取MonkeyImage对象

```java
string	convertToBytes (string format)
// Converts the current image to a particular format and returns it as a string that you can then access as an iterable of binary bytes.
// 转换格式

tuple	getRawPixel (integer x, integer y)
// Returns the single pixel at the image location (x,y), as an a tuple of integer, in the form (a,r,g,b).
// 获取像素值

integer	getRawPixelInt (integer x, integer y)
// Returns the single pixel at the image location (x,y), as a 32-bit integer.
// 获取像素值

MonkeyImage	getSubImage (tuple rect)
// Creates a new MonkeyImage object from a rectangular selection of the current image.
// 获取像素值
// 根据举行获取一个新的对象

boolean	sameAs (MonkeyImage other, float percent)
// Compares this MonkeyImage object to another and returns the result of the comparison. The percent argument specifies the percentage difference that is allowed for the two images to be "equal".
// 比较2各图片是相同

void	writeToFile (string path, string format)
// Writes the current image to the file specified by filename, in the format specified by format.
// 保存图片
```


# 总结
## 优点：
Monkeyrunner可以运行多个device，能够截屏，能够提供按键和输入事件。可以基于python进行扩展开发，功能强大。
## 缺点：
Monkeyrunner获取控件是根据坐标进行定位的，不同的设备的坐标不一样，获取不稳定。

## Monkey与Monkeyrunner差别
- Monkey
Monkey工具直接运行在设备或模拟器的adb shell中，生成用户或系统的伪随机事件流。
- Monkeyrunner
Monkeyrunner工具是在工作站上通过API定义的特定命令和事件控制设备或模拟器。


# 相关链接
[https://developer.android.com/studio/test/monkeyrunner/index.html](https://developer.android.com/studio/test/monkeyrunner/index.html)
[http://www.cnblogs.com/findyou/p/3420936.html](http://www.cnblogs.com/findyou/p/3420936.html)