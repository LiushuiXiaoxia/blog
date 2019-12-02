---
title: Android上使用Lombok
date: 2017-7-13 18:49:04
categories: Libs
tags:
    - Android
    - Lombok
    - AutoValue
---

# Android上使用Lombok

---

## 简介

最近几天尝试了一把后端的工作，发现后端同学使用了一个第三库——[Lombok](https://projectlombok.org/)，用了一下，感觉还不错，特来介绍一下，感觉和以前介绍过的[AutoValue](https://github.com/LiushuiXiaoxia/AutoValueDemo)挺像的。

Lombok 官网上面有个几分钟的视频，接单介绍了Lombok的用途，使用方法很简单，只需要依赖对应的jar文件，然后在对应的Java文件上使用注解即可。

<!-- more -->

先看个例子，下面是常见的一个Java一个实体类，含有field、setter、getter、equals、hashcode、toString方法。

```java
public class User {

    private int id;

    private String name;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        User user = (User) o;

        if (id != user.id) return false;
        return name != null ? name.equals(user.name) : user.name == null;
    }

    @Override
    public int hashCode() {
        int result = id;
        result = 31 * result + (name != null ? name.hashCode() : 0);
        return result;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}
```

如果使用了Lombok，就很简单了，直接定义好字段，然后添加一个注解`@Data`即可，其他方法，工具自动生成，虽然上面的方法我们也是用工具生成的，但是如果要添加或者删除字段，还是要修改代码的，如果直接使用注解的方式，那么还是简单的，无需修改任何方法。

```java
@Data
public class UserLombok {

    private int id;

    private String name;
}
```

## 注解简介

Lombok 主要使用就是通过添加注解，来自动生成代码，主要包含两类，一种是Stable类型，一种是Experimental。前面表示稳定的注解，后面表示实验类型的，可能会被移除。本文主要介绍Stable类型，Experimental由于使用较少，不做讲解。

**Stable**

* val

Finally! Hassle-free final local variables.

* @NonNull

or: How I learned to stop worrying and love the NullPointerException.

* @Cleanup

Automatic resource management: Call your close() methods safely with no hassle.

* @Getter/@Setter

Never write public int getFoo() {return foo;} again.

* @ToString

No need to start a debugger to see your fields: Just let lombok generate a toString for you!

* @EqualsAndHashCode

Equality made easy: Generates hashCode and equals implementations from the fields of your object..

* @NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor

Constructors made to order: Generates constructors that take no arguments, one argument per final / non-nullfield, or one argument for every field.

* @Data

All together now: A shortcut for @ToString, @EqualsAndHashCode, @Getter on all fields, and @Setter on all non-final fields, and @RequiredArgsConstructor!

* @Value

Immutable classes made very easy.

* @Builder

... and Bob's your uncle: No-hassle fancy-pants APIs for object creation!

* @SneakyThrows

To boldly throw checked exceptions where no one has thrown them before!

* @Synchronized

synchronized done right: Don't expose your locks.

* @Getter(lazy=true)

Laziness is a virtue!

* @Log

Captain's Log, stardate 24435.7: "What was that line again?"


**Experimental**

* var

Modifiable local variables with a type inferred by assigning value.

* @Accessors

A more fluent API for getters and setters.

* @ExtensionMethod

Annoying API? Fix it yourself: Add new methods to existing types!

* @FieldDefaults

New default field modifiers for the 21st century.

* @Delegate

Don't lose your composition.

* @Wither

Immutable 'setters' - methods that create a clone but with one changed field.

* onMethod= / onConstructor= / onParam=

Sup dawg, we heard you like annotations, so we put annotations in your annotations so you can annotate while you're annotating.

* @UtilityClass

Utility, metility, wetility! Utility classes for the masses.

* @Helper

With a little help from my friends... Helper methods for java.

## Android 集成

项目根目录下面新建配置文件 lombok.config，同时填上对应的配置项，Java项目不需要，Android和Java还是有点区别的，不配置有的注解使用不了，编译不过。

![](http://upload-images.jianshu.io/upload_images/1520343-31aacd4b694f9269.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

lombok.config

```
lombok.anyConstructor.suppressConstructorProperties=true
```

然后在对应的项目中添加gradle依赖就行了。

```gradle
dependencies {
    provided "org.projectlombok:lombok:1.16.18"
    compile 'org.glassfish:javax.annotation:10.0-b28'
}
```

可以在Android Studio中安装lombok插件。

![](http://upload-images.jianshu.io/upload_images/1520343-516b57dee4009639.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样可以很方便的看到类中生成的方法

![](http://upload-images.jianshu.io/upload_images/1520343-34217ef31a06e86e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 注解说明

下面简单说明注解的使用方法（如需了解详细使用，请参阅官方文档），以及使用注解后类中生成的方法。

### val

定义一个final类型的变量，并且可以不写类型。

如:

```java
public class ValExample {

    public String example() {
        val example = new ArrayList<String>();
        example.add("Hello, World!");
        val foo = example.get(0);
        return foo.toLowerCase();
    }

    public void example2() {
        val map = new HashMap<Integer, String>();
        map.put(0, "zero");
        map.put(5, "five");
        for (val entry : map.entrySet()) {
            System.out.printf("%d: %s\n", entry.getKey(), entry.getValue());
        }
    }
}
```

class字节码：

```java
public class ValExample {
    public ValExample() {
    }

    public String example() {
        ArrayList<String> example = new ArrayList();
        example.add("Hello, World!");
        String foo = (String)example.get(0);
        return foo.toLowerCase();
    }

    public void example2() {
        HashMap<Integer, String> map = new HashMap();
        map.put(Integer.valueOf(0), "zero");
        map.put(Integer.valueOf(5), "five");
        Iterator var2 = map.entrySet().iterator();

        while(var2.hasNext()) {
            Entry<Integer, String> entry = (Entry)var2.next();
            System.out.printf("%d: %s\n", new Object[]{entry.getKey(), entry.getValue()});
        }
    }
}
```

### @NonNull

非空值判断，如果为空，则抛出异常

如：

```java
public class NonNullExample {

    public static int length(@NonNull String string) {
        return string.length();
    }
}
```

class字节码

```java
public class NonNullExample {
    public NonNullExample() {
    }

    public static int length(@NonNull String string) {
        if(string == null) {
            throw new NullPointerException("string");
        } else {
            return string.length();
        }
    }
}
```

### @Cleanup

可以自动调用close方法

如

```java
public class CleanupExample {

    public static void main(String[] args) throws IOException {
        @Cleanup InputStream in = new FileInputStream(args[0]);
        @Cleanup OutputStream out = new FileOutputStream(args[1]);
        byte[] b = new byte[10000];
        while (true) {
            int r = in.read(b);
            if (r == -1) break;
            out.write(b, 0, r);
        }
    }
}
```

class字节码

```java
public class CleanupExample {
    public CleanupExample() {
    }

    public static void main(String[] args) throws IOException {
        FileInputStream in = new FileInputStream(args[0]);

        try {
            FileOutputStream out = new FileOutputStream(args[1]);

            try {
                byte[] b = new byte[10000];

                while(true) {
                    int r = in.read(b);
                    if(r == -1) {
                        return;
                    }

                    out.write(b, 0, r);
                }
            } finally {
                if(Collections.singletonList(out).get(0) != null) {
                    out.close();
                }

            }
        } finally {
            if(Collections.singletonList(in).get(0) != null) {
                in.close();
            }

        }
    }
}
```

### @Getter/@Setter

自动生成setter、getter方法

```java
// GetterSetterExample.java
public class GetterSetterExample {

    @Getter
    @Setter
    private int age;

    @Setter(AccessLevel.PROTECTED)
    private String name;
}

// GetterSetterExample.class
public class GetterSetterExample {
    private int age;
    private String name;

    public GetterSetterExample() {
    }

    public int getAge() {
        return this.age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    protected void setName(String name) {
        this.name = name;
    }
}
```


### @ToString

自动生成toString方法

```java
// ToStringExample.java
@ToString(exclude = "id")
public class ToStringExample {

    private int id;

    private String name;

    private String passwd;

    public ToStringExample(int id, String name, String passwd) {
        this.id = id;
        this.name = name;
        this.passwd = passwd;
    }
}

// ToStringExample.class
public class ToStringExample {
    private int id;
    private String name;
    private String passwd;

    public ToStringExample(int id, String name, String passwd) {
        this.id = id;
        this.name = name;
        this.passwd = passwd;
    }

    public String toString() {
        return "ToStringExample(name=" + this.name + ", passwd=" + this.passwd + ")";
    }
}
```

### @EqualsAndHashCode

自动生成equals和hashcode方法。

```java
// EqualsAndHashCodeExample.java
@EqualsAndHashCode
public class EqualsAndHashCodeExample {

    private int id;

    private String name;

    public EqualsAndHashCodeExample(int id, String name) {
        this.id = id;
        this.name = name;
    }
}

// EqualsAndHashCodeExample.class
public class EqualsAndHashCodeExample {
    private int id;
    private String name;

    public EqualsAndHashCodeExample(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public boolean equals(Object o) {
        if(o == this) {
            return true;
        } else if(!(o instanceof EqualsAndHashCodeExample)) {
            return false;
        } else {
            EqualsAndHashCodeExample other = (EqualsAndHashCodeExample)o;
            if(!other.canEqual(this)) {
                return false;
            } else if(this.id != other.id) {
                return false;
            } else {
                Object this$name = this.name;
                Object other$name = other.name;
                if(this$name == null) {
                    if(other$name != null) {
                        return false;
                    }
                } else if(!this$name.equals(other$name)) {
                    return false;
                }

                return true;
            }
        }
    }

    protected boolean canEqual(Object other) {
        return other instanceof EqualsAndHashCodeExample;
    }

    public int hashCode() {
        int PRIME = true;
        int result = 1;
        int result = result * 59 + this.id;
        Object $name = this.name;
        result = result * 59 + ($name == null?43:$name.hashCode());
        return result;
    }
}
```

### @NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor

自动生成相关的构造函数

```java
// ConstructorExample.java
@ToString
@NoArgsConstructor
@AllArgsConstructor(access = AccessLevel.PUBLIC)
public class ConstructorExample<T> {

    private String args;

    @ToString
    @RequiredArgsConstructor(staticName = "of")
    public static class StaticMethodsExample {

        @NonNull
        private String field;
    }
}

// ConstructorExample.class
public class ConstructorExample<T> {
    private String args;

    public String toString() {
        return "ConstructorExample(args=" + this.args + ")";
    }

    public ConstructorExample() {
    }

    public ConstructorExample(String args) {
        this.args = args;
    }

    public static class StaticMethodsExample {
        @NonNull
        private String field;

        public String toString() {
            return "ConstructorExample.StaticMethodsExample(field=" + this.field + ")";
        }

        private StaticMethodsExample(@NonNull String field) {
            if(field == null) {
                throw new NullPointerException("field");
            } else {
                this.field = field;
            }
        }

        public static ConstructorExample.StaticMethodsExample of(@NonNull String field) {
            return new ConstructorExample.StaticMethodsExample(field);
        }
    }
}
```

### @Builder

自动生成构造者模式方法

```java
// BuilderExample.java

@Builder
@Data
public class BuilderExample {

    private String name;

    private int age;

    @Singular
    private Set<String> occupations;
}
```

class文件太长，就不贴了，下面是调用方式。

```java
// test builder
BuilderExample builderExample = BuilderExample.builder()
    .name("admin")
    .age(10)
    .occupation("aaa")
    .occupation("bbb")
    .build();

Log.i(TAG, "onCreate: " + builderExample);
```

### @SneakyThrows

自动生成异常抛出代码

```java
// SneakyThrowsExample.java

public class SneakyThrowsExample implements Runnable {

    @SneakyThrows(UnsupportedEncodingException.class)
    public String utf8ToString(byte[] bytes) {
        return new String(bytes, "UTF-8");
    }

    @SneakyThrows
    public void run() {
        throw new Throwable();
    }
}

// SneakyThrowsExample.class
public class SneakyThrowsExample implements Runnable {
    public SneakyThrowsExample() {
    }

    public String utf8ToString(byte[] bytes) {
        try {
            return new String(bytes, "UTF-8");
        } catch (UnsupportedEncodingException var3) {
            throw var3;
        }
    }

    public void run() {
        try {
            throw new Throwable();
        } catch (Throwable var2) {
            throw var2;
        }
    }
}
```

### @Synchronized

自动生成线程同步代码

```java
// SynchronizedExample.java
public class SynchronizedExample {

    private final Object readLock = new Object();

    @Synchronized
    public static void hello() {
        System.out.println("world");
    }

    @Synchronized
    public int answerToLife() {
        return 42;
    }

    @Synchronized("readLock")
    public void foo() {
        System.out.println("bar");
    }
}

// SynchronizedExample.class
public class SynchronizedExample {
    private static final Object $LOCK = new Object[0];
    private final Object $lock = new Object[0];
    private final Object readLock = new Object();

    public SynchronizedExample() {
    }

    public static void hello() {
        Object var0 = $LOCK;
        synchronized($LOCK) {
            System.out.println("world");
        }
    }

    public int answerToLife() {
        Object var1 = this.$lock;
        synchronized(this.$lock) {
            return 42;
        }
    }

    public void foo() {
        Object var1 = this.readLock;
        synchronized(this.readLock) {
            System.out.println("bar");
        }
    }
}
```

### @Getter(lazy=true)

延迟初始化

```java
// GetterLazyExample.java
public class GetterLazyExample {

    @Getter(lazy = true)
    private final double[] cached = expensive();

    private double[] expensive() {
        double[] result = new double[1000000];
        for (int i = 0; i < result.length; i++) {
            result[i] = Math.asin(i);
        }
        return result;
    }
}

// GetterLazyExample.class
public class GetterLazyExample {

    private final AtomicReference<Object> cached = new AtomicReference();

    public GetterLazyExample() {
    }

    private double[] expensive() {
        double[] result = new double[1000000];

        for(int i = 0; i < result.length; ++i) {
            result[i] = Math.asin((double)i);
        }

        return result;
    }

    public double[] getCached() {
        Object value = this.cached.get();
        if(value == null) {
            AtomicReference var2 = this.cached;
            synchronized(this.cached) {
                value = this.cached.get();
                if(value == null) {
                    double[] actualValue = this.expensive();
                    value = actualValue == null?this.cached:actualValue;
                    this.cached.set(value);
                }
            }
        }

        return (double[])((double[])(value == this.cached?null:value));
    }
}
```

### @Log

自动生成日志对象，不过都是J2EE方面的，Android端用途不大。

[官方示例](https://projectlombok.org/features/log)

## 原理

自从Java 6起，javac就支持“JSR 269 Pluggable Annotation Processing API”规范，只要程序实现了该API，就能在javac运行的时候得到调用。

举例来说，现在有一个实现了"JSR 269 API"的程序A,那么使用javac编译源码的时候具体流程如下：

1. javac对源代码进行分析，生成一棵抽象语法树(AST)

2. 运行过程中调用实现了"JSR 269 API"的A程序

3. 此时A程序就可以完成它自己的逻辑，包括修改第一步骤得到的抽象语法树(AST)

4. javac使用修改后的抽象语法树(AST)生成字节码文件

详细的流程图如下：

![](http://upload-images.jianshu.io/upload_images/1520343-35db5d2109a5f950.gif?imageMogr2/auto-orient/strip)

## 总结

综上所述，使用了lombok可以简化Java代码，因为是在编译期处理所以可能会增加点时间，不过对于Android来说，可以尝试一下，不过17年Google IO已经推荐使用Kotlin开发Android了，lombok中好多功能在Kotlin中已经实现了，如果项目暂时还不想使用Kotlin开发，继续使用Java的可以尝试一下。

**缺点：**

使用lombok虽然能够省去手动创建代码的麻烦，但是却大大降低了源代码文件的可读性和完整性，降低了阅读源代码的舒适度。

## 相关链接

[Lombok官网](https://projectlombok.org/)

[AutoValue相关](https://github.com/LiushuiXiaoxia/AutoValueDemo)

[android基础之依赖注入问题](http://www.voidcn.com/blog/sangsa/article/p-5981116.html)

[Lombok的使用和原理](http://blog.csdn.net/dslztx/article/details/46715803)