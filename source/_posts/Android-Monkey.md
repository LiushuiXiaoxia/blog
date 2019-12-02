---
title: Android Monkey整理
date: 2016-07-19 20:52:19
categories: Android测试
tags:
	- Android Monkey
	- Android测试
	- Monkey测试
---

# Android Monkey整理

- - - - -

# 简介

Monkey是Android中的一个命令行工具，可以运行在模拟器里或实际设备中。它向系统发送伪随机的用户事件流(如按键输入、触摸屏输入、手势输入等)，实现对正在开发的应用程序进行压力测试。Monkey测试是一种为了测试软件的稳定性、健壮性的快速有效的方法。

<!-- more -->

Monkey命令包含下面4个命令选项。
- 常规选项
- 运行约束
- 事件类型和频率
- 调试选项

当命令运行的时候，它会产生随机的事件到Android系统，然后监听系统的测试情况，同时对一些条件进行特俗对待。

- 当你对一个或者多个设备进行测试的时候，如果app跳转大其他app，则会停止运行
- 当程序发送了奔溃或者是一些列没有处理的异常，命令会停止同时报告错误
- 当程序发送ANR的时候，命令也会停止并报告错误


# 基本使用
基本语法如下：

```sh
adb shell monkey [options] <event-count>
```
如果不指定options，Monkey将以无反馈模式启动，并把事件任意发送到安装在目标环境中的全部包。下面是一个更为典型的命令行示例，它启动指定的应用程序，并向其发送500个伪随机事件：

```sh
adb shell monkey -p your.package.name -v 500
```

# 命令语法

## General	

> --help
> Prints a simple usage guide.
> 列出简单的用法。
> 
> -v
> Each -v on the command line will increment the verbosity level.
> Level 0 (the default) provides little information beyond startup notification, test completion, and final results. Level 1 provides more details about the test as it runs, such as individual events being sent to your activities. Level 2 provides more detailed setup information such as activities selected or not selected for testing.
> 
> 命令行的每一个 -v 将增加反馈信息的级别。
> Level 0(缺省值)除启动提示、测试完成和最终结果之外，提供较少信息。
> Level 1提供较为详细的测试信息，如逐个发送到Activity的事件。
> Level 2提供更加详细的设置信息，如测试中被选中的或未被选中的Activity。

## Events	
> -s <seed>
> Seed value for pseudo-random number generator. If you re-run the Monkey with the same seed value, it will generate the same sequence of events.
> 用于指定伪随机数生成器的seed值，如果seed相同，则两次Monkey测试所产生的事件序列也相同的。
> 
> --throttle <milliseconds>
> Inserts a fixed delay between events. You can use this option to slow down the Monkey. If not specified, there is no delay and the events are generated as rapidly as possible.
> 在事件之间插入固定延迟。通过这个选项可以减缓 Monkey 的执行速度。如果不指定该选项，Monkey 将不会被延迟，事件将尽可能快地被产成。
> 
> --pct-touch <percent>
> Adjust percentage of touch events. (Touch events are a down-up event in a single place on the screen.)
> 调整触摸事件的百分比(触摸事件是一个 down-up 事件，它发生在屏幕上的某单一位置)。
> 
> --pct-motion <percent>
> Adjust percentage of motion events. (Motion events consist of a down event somewhere on the screen, a series of pseudo-random movements, and an up event.)
> 调整动作事件的百分比(动作事件由屏幕上某处的一个 down 事件、一系列的伪随机事件和一个 up 事件组成)。
> 
> --pct-trackball <percent>
> Adjust percentage of trackball events. (Trackball events consist of one or more random movements, sometimes followed by a click.)
> 调整轨迹事件的百分比(轨迹事件由一个或几个随机的移动组成，有时还伴随有点击)。
> 
> --pct-nav <percent>
> Adjust percentage of "basic" navigation events. (Navigation events consist of up/down/left/right, as input from a directional input device.)
> 调整“基本”导航事件的百分比(导航事件由来自方向输入设备的 up/down/left/right 组成)。
> 
> --pct-majornav <percent>
> Adjust percentage of "major" navigation events. (These are navigation events that will typically cause actions within your UI, such as the center button in a 5-way pad, the back key, or the menu key.)
> 调整“主要”导航事件的百分比(这些导航事件通常引发图形界面中的动作，如：5-way键盘的中间按键、回退按键、菜单按键)
> 
> --pct-syskeys <percent>
> Adjust percentage of "system" key events. (These are keys that are generally reserved for use by the system, such as Home, Back, Start Call, End Call, or Volume controls.)
> 调整“系统”按键事件的百分比(这些按键通常被保留，由系统使用，如 Home、Back、Start Call、End Call 及音量控制键)。
> 
> --pct-appswitch <percent>
> Adjust percentage of activity launches. At random intervals, the Monkey will issue a startActivity() call, as a way of maximizing coverage of all activities within your package.
> 调整启动 Activity 的百分比。在随机间隔里，Monkey 将执行一个 startActivity() 调用，作为最大程度覆盖包中全部Activity 的一种方法。
> 
> --pct-anyevent <percent>
> Adjust percentage of other types of events. This is a catch-all for all other types of events such as keypresses, other less-used buttons on the device, and so forth.
> 调整其它类型事件的百分比。它包罗了所有其它类型的事件，如：按键、其它不常用的设备按钮、等等。

## Constraints	
> -p <allowed-package-name>
> If you specify one or more packages this way, the Monkey will only allow the system to visit activities within those packages. If your application requires access to activities in other packages (e.g. to select a contact) you'll need to specify those packages as well. If you don't specify any packages, the Monkey will allow the system to launch activities in all packages. To specify multiple packages, use the -p option multiple times — one -p option per package.
> 
> 如果用此参数指定了一个或几个包，Monkey 将只允许系统启动这些包里的 Activity。如果你的应用程序还需要访问其它包里的 Activity (如选择取一个联系人)，那些包也需要在此同时指定。如果不指定任何包，Monkey 将允许系统启动全部包里的 Activity。要指定多个包，需要使用多个 -p 选项，每个 -p 选项只能用于一个包。
> 
> 指定一个包： adb shell monkey -p com.htc.Weather 100
> 说明：com.htc.Weather为包名，100是事件计数（即让Monkey程序模拟100次随机用户事件）。
> 指定多个包：adb shell monkey -p com.htc.Weather –p com.htc.pdfreader -p com.htc.photo.widgets 100
> 不指定包：adb shell monkey 100
> 说明：Monkey随机启动APP并发送100个随机事件。
> 
> -c <main-category>
> If you specify one or more categories this way, the Monkey will only allow the system to visit activities that are listed with one of the specified categories. If you don't specify any categories, the Monkey will select activities listed with the category Intent.CATEGORY_LAUNCHER or Intent.CATEGORY_MONKEY. To specify multiple categories, use the -c option multiple times — one -c option per category.
> 如果用此参数指定了一个或几个类别，Monkey 将只允许系统启动被这些类别中的某个类别列出的 Activity。如果不指定任何类别，Monkey 将选 择下列类别中列出的 Activity： Intent.CATEGORY_LAUNCHER 或Intent.CATEGORY_MONKEY。要指定多个类别，需要使用多个 -c 选项，每个 -c 选 项只能用于一个类别。


## Debugging
> --dbg-no-events
> When specified, the Monkey will perform the initial launch into a test activity, but will not generate any further events. For best results, combine with -v, one or more package constraints, and a non-zero throttle to keep the Monkey running for 30 seconds or more. This provides an environment in which you can monitor package transitions invoked by your application.
> 设置此选项，Monkey将执行初始启动，进入到一个测试Activity，然后不会再进一步生成事件。为了得到最佳结果，把它与-v、一个或几个包约 束、以及一个保持Monkey运行30秒或更长时间的非零值联合起来，从而提供一个环境，可以监视应用程序所调用的包之间的转换。
> 
> --hprof
> If set, this option will generate profiling reports immediately before and after the Monkey event sequence. This will generate large (~5Mb) files in data/misc, so use with care. See Traceview for more information on trace files.
> 设置此选项，将在Monkey事件序列之前和之后立即生成profiling报告。这将会在data/misc中生成大文件(~5Mb)，所以要小心使用它。
> 
> --ignore-crashes
> Normally, the Monkey will stop when the application crashes or experiences any type of unhandled exception. If you specify this option, the Monkey will continue to send events to the system, until the count is completed.
> 通常，当应用程序崩溃或发生任何失控异常时，Monkey将停止运行。如果设置此选项，Monkey将继续向系统发送事件，直到计数完成。
> 
> --ignore-timeouts
> Normally, the Monkey will stop when the application experiences any type of timeout error such as a "Application Not Responding" dialog. If you specify this option, the Monkey will continue to send events to the system, until the count is completed.
> 通常，当应用程序发生任何超时错误(如“Application Not Responding”对话框)时，Monkey 将停止运行。如果设置此选项，Monkey 将继续向系统发送事件，直到计数完成。
> 
> --ignore-security-exceptions
> Normally, the Monkey will stop when the application experiences any type of permissions error, for example if it attempts to launch an activity that requires certain permissions. If you specify this option, the Monkey will continue to send events to the system, until the count is completed.
> 通常，当应用程序发生许可错误(如启动一个需要某些许可的 Activity )时，Monkey 将停止运行。如果设置了此选项，Monkey 将继续向系统发送事件，直到计数完成。
> 
> --kill-process-after-error
> Normally, when the Monkey stops due to an error, the application that failed will be left running. When this option is set, it will signal the system to stop the process in which the error occurred. Note, under a normal (successful) completion, the launched process(es) are not stopped, and the device is simply left in the last state after the final event.
> 通常，当 Monkey 由于一个错误而停止时，出错的应用程序将继续处于运行状态。当设置了此选项时，将会通知系统停止发生错误的进程。注意，正常的(成功的)结束，并没有停止启动的进程，设备只是在结束事件之后，简单地保持在最后的状态。
> 
> --monitor-native-crashes
> Watches for and reports crashes occurring in the Android system native code. If --kill-process-after-error is set, the system will stop.
> 监视并报告 Android 系统中本地代码的崩溃事件。如果设置了 --kill-process-after-error，系统将停止运行。
> 
> --wait-dbg
> Stops the Monkey from executing until a debugger is attached to it.
> 停止执行中的 Monkey，直到有调试器和它相连接。

# 相关链接

[https://developer.android.com/studio/test/monkey.html](https://developer.android.com/studio/test/monkey.html)
[http://www.cnblogs.com/yyangblog/archive/2011/03/10/1980068.html](http://www.cnblogs.com/yyangblog/archive/2011/03/10/1980068.html)
[http://blog.csdn.net/huangbiao86/article/details/8490743](http://blog.csdn.net/huangbiao86/article/details/8490743)