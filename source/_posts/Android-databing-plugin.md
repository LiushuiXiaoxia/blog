---
title: AndroidStudio插件：布局文件转化Databinding
date: 2017-02-25 19:42:55
categories: Plugin
tags:
    - Andorid Studio plugin
    - Databinding
---

# AndroidStudio插件：布局文件转化Databinding

---
# 序

这几天一直在做代码重构，因为Android是提供Databinding的MVVM架构，所以就想把代码里面所有的Butterknife代码换成Databinding形式。

[Github地址](https://github.com/LiushuiXiaoxia/DatabindingConvert)

# 简介

一个简单的Android Studio 插件，把当前的布局文件转化成Databinding的形式。

如，当一个布局文件如下所示:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

    <android.support.design.widget.TabLayout
            android:id="@+id/tabLayout"
            style="@style/tabLayoutStyle"
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:background="@color/colorPrimary"/>

    <android.support.v4.view.ViewPager
            android:id="@+id/vpMainContainer"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
</LinearLayout>
```

转化过就是这样(注意：还没有实现代码转化后的格式化，需要手动格式化)：

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <data>

        <import type="" />

        <variable
            name=""
            type="" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <android.support.design.widget.TabLayout
            android:id="@+id/tabLayout"
            style="@style/tabLayoutStyle"
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:background="@color/colorPrimary" />

        <android.support.v4.view.ViewPager
            android:id="@+id/vpMainContainer"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    </LinearLayout>
</layout>
```

# 操作示范

![](https://raw.githubusercontent.com/LiushuiXiaoxia/DatabindingConvert/master/doc/1.gif)

# 插件下载

[DatabindingConvert_v0.1.jar](https://github.com/LiushuiXiaoxia/DatabindingConvert/blob/master/DatabindingConvert_v0.1.jar?raw=true)

# 后续优化

* 布局代码转换后，自动格式化代码
* 创建带模板的Databinding文件

# 版本更新

* v0.0.1 完成基本的布局转换