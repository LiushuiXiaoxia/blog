---
title: Kotlin之let,apply,with,run函数区别
date: 2017-04-15 19:37:39
categories: Kotlin
tags:
    - Kotlin
---

# Kotlin之let,apply,with,run函数区别

---

很长一段时间内都一直使用Kotlin这门语言，也只是纯粹使用简单语法，最近有时候写的代码，编辑器自动提示使用let等函数，然后就专门花点时间研究了下。


## let

首先let()的定义是这样的，默认当前这个对象作为闭包的it参数，返回值是函数里面最后一行，或者指定return

```kotlin
fun <T, R> T.let(f: (T) -> R): R = f(this)
```

简单示例:

```kotlin
fun testLet(): Int {
    // fun <T, R> T.let(f: (T) -> R): R { f(this)}
    "testLet".let {
        println(it)
        println(it)
        println(it)
        return 1
    }
}
//运行结果
//testLet
//testLet
//testLet
```

可以看看最后生成的class文件，代码已经经过格式化了，编译器只是在我们原先的变量后面添加了let里面的内容。

```
public static final int testLet() {
    String str1 = "testLet";
    String it = (String)str1;
    int $i$a$1$let;
    System.out.println(it);
    System.out.println(it);
    System.out.println(it);
    return 1;
}
```

来个复杂一定的例子

```kotlin
fun testLet(): Int {
    // fun <T, R> T.let(f: (T) -> R): R { f(this)}
    "testLet".let {
        if (Random().nextBoolean()) {
            println(it)
            return 1
        } else {
            println(it)
            return 2
        }
    }
}
```

编译过后的class文件

```
public static final int testLet() {
    String str1 = "testLet";
    String it = (String)str1;
    int $i$a$1$let;
    if (new Random().nextBoolean())
    {
        System.out.println(it);
        return 1;
    }
    System.out.println(it);
    return 2;
}
```


## apply

apply函数是这样的，调用某对象的apply函数，在函数范围内，可以任意调用该对象的任意方法，并返回该对象

```kotlin
fun <T> T.apply(f: T.() -> Unit): T { f(); return this }
```

代码示例

```kotlin
fun testApply() {
    // fun <T> T.apply(f: T.() -> Unit): T { f(); return this }
    ArrayList<String>().apply {
        add("testApply")
        add("testApply")
        add("testApply")
        println("this = " + this)
    }.let { println(it) }
}

// 运行结果
// this = [testApply, testApply, testApply]
// [testApply, testApply, testApply]
```

编译过后的class文件

```
  public static final void testApply()
  {
    ArrayList localArrayList1 = new ArrayList();
    ArrayList localArrayList2 = (ArrayList)localArrayList1;
    int $i$a$1$apply;
    ArrayList $receiver;
    $receiver.add("testApply");
    $receiver.add("testApply");
    $receiver.add("testApply");
    String str = "this = " + $receiver;
    System.out.println(str);
    localArrayList1 = localArrayList1;
    ArrayList it = (ArrayList)localArrayList1;
    int $i$a$2$let;
    System.out.println(it);
  }
```

## with

with函数是一个单独的函数，并不是Kotlin中的extension，所以调用方式有点不一样，返回是最后一行，然后可以直接调用对象的方法，感觉像是let和apply的结合。

```kotlin
fun <T, R> with(receiver: T, f: T.() -> R): R = receiver.f()
```

代码示例:

```kotlin
fun testWith() {
    // fun <T, R> with(receiver: T, f: T.() -> R): R = receiver.f()
    with(ArrayList<String>()) {
        add("testWith")
        add("testWith")
        add("testWith")
        println("this = " + this)
    }.let { println(it) }
}
// 运行结果
// this = [testWith, testWith, testWith]
// kotlin.Unit
```
class文件

```
 public static final void testWith()
  {
    Object localObject = new ArrayList();
    ArrayList localArrayList1 = (ArrayList)localObject;
    int $i$a$1$with;
    ArrayList $receiver;
    $receiver.add("testWith");
    $receiver.add("testWith");
    $receiver.add("testWith");
    String str = "this = " + $receiver;
    System.out.println(str);
    localObject = Unit.INSTANCE;
    Unit it = (Unit)localObject;
    int $i$a$2$let;
    System.out.println(it);
  }

```

## run

run函数和apply函数很像，只不过run函数是使用最后一行的返回，apply返回当前自己的对象。

```kotlin
fun <T, R> T.run(f: T.() -> R): R = f()
```

代码示例

```kotlin
fun testRun() {
    // fun <T, R> T.run(f: T.() -> R): R = f()
    "testRun".run {
        println("this = " + this)
    }.let { println(it) }
}
// 运行结果
// this = testRun
// kotlin.Unit
```

class文件

```
  public static final void testRun()
  {
    Object localObject = "testRun";
    String str1 = (String)localObject;
    int $i$a$1$run;
    String $receiver;
    String str2 = "this = " + $receiver;
    System.out.println(str2);
    localObject = Unit.INSTANCE;
    Unit it = (Unit)localObject;
    int $i$a$2$let;
    System.out.println(it);
  }
```

# 总结

怎么样，是不是看晕了，没关系，我们来总结下。


|函数名     | 定义                                                        | 参数           | 返回值  | extension | 其他 |
|----------|:----------------------------------------------------------:|:-------------:| -----:| -----:|-----:|
| let      |fun <T, R> T.let(f: (T) -> R): R = f(this)                  | it            | 闭包返回 | 是   |     |
| apply    |fun <T> T.apply(f: T.() -> Unit): T { f(); return this }    | 无，可以使用this | this   | 是  |  |
| with     |fun <T, R> with(receiver: T, f: T.() -> R): R = receiver.f()| 无，可以使用this | 闭包返回 | 否  | 调用方式与其他不同 |
| run      |fun <T, R> T.run(f: T.() -> R): R = f()                     | 无，可以使用this | 闭包返回 | 是  |  |