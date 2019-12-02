---
title: Android KTX简介
date: 2018-02-07 15:59:38
tags: 
    - android
    - Kotlin
    - KTX
---

# Android KTX简介

---

## 介绍

昨天Google爸爸又发布了一个Android工具库，是使用Kotlin实现了。自从17年5月份声明支持Kotlin作为Android官方开发语言以来，Google爸爸对Kotlin的态度还是很积极的。
而且Jake大神后面也加入了Google，从博客的内容来看，也是这个类库也是出自Jake大神之手，所以特来拜读下。

<!-- more -->

## 主要功能

从官方简介来看，主要是对Android原始的Api做了一些扩展，方便开发调用，使代码更加自然和简单,下面列举几个例子，详细的API介绍可以参考官方API文档。

### 字符串转为URI

通常情况下为`Uri.parse(uriString)`，但是Android KTX 会为字符串添加一个扩展函数，使字符串更加自然地转换为 URI。

```kotlin
// Kotlin
val uri = Uri.parse(uriString)

// android KTX
val uri = uriString.toUri()
```

### SharedPreferences

SharedPreferences也经常使用，使用 Android KTX后，代码也简介不少。

```kotlin
// kotlin
sharedPreferences.edit()
    .putBoolean("key", value)
    .apply()

// KTX
sharedPreferences.edit {
    putBoolean("key", value)
}
```

### Path

两个路径之间的距离改变了100px

```kotlin
// kotlin
val pathDifference = Path(myPath1).apply {
    op(myPath2, Path.Op.DIFFERENCE)
}

canvas.apply {
  val checkpoint = save()
  translate(0F, 100F)
  drawPath(pathDifference, myPaint)
  restoreToCount(checkpoint)
}

// KTX
val pathDifference = myPath1 - myPath2

canvas.withTranslation(y = 100F) {
    drawPath(pathDifference, myPaint)
}
```

### View的onPreDraw监听

触发了视图中 onPreDraw 的回调

```
// kotlin
view.viewTreeObserver.addOnPreDrawListener(
    object : ViewTreeObserver.OnPreDrawListener {
        override fun onPreDraw(): Boolean {
            viewTreeObserver.removeOnPreDrawListener(this)
            actionToBeTriggered()
            return true
        }
    })

// KTX
view.doOnPreDraw {
     actionToBeTriggered()
}
```

## 代码接入

代码接入也很简单，首先项目代码必须接入kotlin，这里不做介绍，直接使用Android Studio创建基于Kotlin的项目即可，然后添加相关依赖，现在的版本还是`0.1`。

```gradle
repositories {
    google()
}

dependencies {
    implementation 'androidx.core:core-ktx:0.1'
}
```

## 原理介绍

透过现象看本质，这样使用起来就不会迷惑，而且遇到问题也能方便排查。

主要使用Kotlin语言的几个特性，了解了这些特性后，我们自己也能很方便的进行封装，这样就形成了我们自己的类库，便于自己技术的沉淀。

### Extensions

上面的第一个例子，uri的封装就是利用了这个，Kotlin的[官方文档](http://kotlinlang.org/docs/reference/extensions.html)也有介绍。

直接看源码就行了。

```kotlin
inline fun String.toUri(): Uri = Uri.parse(this)
```

其实就是对String做了一个扩展，如果使用Java的就很容易理解，如下所示，这种方式在日常开发中也很容易见到。

```java
public class StringUtil{

    public static Uri parse(String uriString) {
        return Uri.parse(uriString);
    }
}
```

### Lambdas

第二个例子主要使用了Lambdas这个特性，Kotlin文档在[这里](http://kotlinlang.org/docs/reference/lambdas.html)。

还是贴代码，首先对`SharedPreferences`做了扩展，然后这个扩展函数的参数是一个闭包，当函数最后一个参数是闭包的时候，函数的括号可以直接省略，然后在后面接上闭包就行了。

```kotlin
inline fun SharedPreferences.edit(action: SharedPreferences.Editor.() -> Unit) {
    val editor = edit()
    action(editor)
    editor.apply()
}
```

### Default Arguments

这个特性上面的例子没有，可以单独列举，如下所示。官方文档[介绍](http://kotlinlang.org/docs/reference/functions.html#default-arguments)。
也是就说，当一个函数中含有多个参数时候，不需要像Java中那样，依次赋值，可以仅仅赋需要的即可，Java中常见的解决的方法是方法重载，挨个传入默认值。

```kotlin
class ViewTest {
    private val context = InstrumentationRegistry.getContext()
    private val view = View(context)

    @Test
    fun updatePadding() {
        view.updatePadding(top = 10, right = 20)
        assertEquals(0, view.paddingLeft)
        assertEquals(10, view.paddingTop)
        assertEquals(20, view.paddingRight)
        assertEquals(0, view.paddingBottom)
    }
}
```

看`updatePadding`方法定义。

```kotlin
fun View.updatePadding(
    @Px left: Int = paddingLeft,
    @Px top: Int = paddingTop,
    @Px right: Int = paddingRight,
    @Px bottom: Int = paddingBottom
) {
    setPadding(left, top, right, bottom)
}
```

对于默认参数，还可以这样玩，比如在Java中，常见的有建造在模式，对每个参数进行赋值，然后创建一个对象，如果使用这种特性，不需要改变的值，可以直接用默认值表示，这样在编码的时候，就会显得很简洁。

## 相关链接

[官方博客](https://android-developers.googleblog.com/)

[Github链接](https://github.com/android/android-ktx/)

[API参考文档](https://android.github.io/android-ktx/core-ktx/)