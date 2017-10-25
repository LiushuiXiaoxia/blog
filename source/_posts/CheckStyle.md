---
title: CheckStyle出现Unable to create a Checker configLocation问题
date: 2017-03-29 18:53:31
categories: 代码分析
tags: 
    - 代码分析
    - check style
---

# CheckStyle出现Unable to create a Checker: configLocation问题

---

最近使用了Android Studio 3.0版本，同时Gradle版本由3.3升级到了3.5版本。

突然发现原先项目中静态代码分析工具——CheckStyle不能使用了，出现类似如下的错误。

```
1: Task failed with an exception.
-----------
* What went wrong:
Execution failed for task ':app:checkstyle'.
> Unable to create Root Module: configLocation {xxxx/xxxx/checkstyle/checkstyle.xml}, classpath {null}.
```

经过查找资料获得，大致是这样的，Gradle3.5版本中默认使用的是CheckStyle7.2版本，3.3版本中使用的是5.9版本，有兼容性错误。

原文是这样的:

```
The specific problem here is that OperatorWrap did not support the METHOD_REF token until Checkstyle 7.2. Technically this configuration file is invalid unless you specify Checkstyle version 7.2 or higher.

With Gradle 3.3 (default Checkstyle 5.9), that configuration was still not correct, but you did not receive an error due to looser validation (in Checkstyle, not Gradle). That version only failed on tokens that were completely unknown, not those that were only valid for other rules.
```

所以在官方暂时没有解决方案的情况，可以在Gradle中指定CheckStyle的版本。

如下所示

```
apply plugin: 'checkstyle'

checkstyle {
    toolVersion = '5.9'
}

task checkstyle(type: Checkstyle) {
    configFile file("checkstyle.xml")

    ignoreFailures false
    showViolations true

    source 'src'
    include '**/*.java'
    exclude '**/gen/**', '**/test/**', '**/build/**'

    classpath = files()
}
```

[官方地址](https://discuss.gradle.org/t/checkstyle-checker-cannot-be-created-with-gradle-3-5/22474)