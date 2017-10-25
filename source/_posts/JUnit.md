---
title: JUnit浅谈
date: 2016-07-19 11:57:33
categories: Java测试
tags:
	- JUnit
  - 测试
	- Java测试
---
# JUnit浅谈

- - - - -
[TOC]

# JUnit简介
JUnit是一个Java语言的单元测试框架。它由肯特·贝克和埃里希·伽玛（Erich Gamma）建立，逐渐成为源于Kent Beck的sUnit的xUnit家族中为最成功的一个。JUnit用于实施对应用程序的单元测试，加快程序编制速度，同时提高编码的质量。
这里是JUnit官网[http://junit.org/junit4/](http://junit.org/junit4/)
JUnit最新的版本是5，这里讨论的是JUnit4版本。


# 示例
## 集成
### Gradle
在build.gradle文件添加如下代码，运行`./gradle test`即可。

```groovy
apply plugin: 'java'
dependencies {
  testCompile 'junit:junit:4.12'
}
```
### Maven
在maven的xml文件中添加依赖。

```xml
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
  <scope>test</scope>
</dependency> 
```

## 代码示例
有一个执行加分的函数，代码逻辑如下:

```java
public class AddOperation {
    public int add(int x, int y) {
        return x + y;
    }
}
```
那么我们可以对这段代码进行测试。

```java
public class AddOperationTest {

    private AddOperation operation;

    @Before
    public void setUp() throws Exception {
        operation = new AddOperation();
    }

    @After
    public void tearDown() throws Exception {
        operation = null;
    }

    @Test
    public void add() {
        Assert.assertEquals("1+1=2", operation.add(1, 1), 2);
    }
}
```

# 注解使用说明

- @Test : 测试方法，测试程序会运行的方法，后边可以跟参数代表不同的测试，如(expected=XXException.class) 异常测试，(timeout=xxx)超时测试
- @Ignore : 被忽略的测试方法
- @Before: 每一个测试方法之前运行
- @After : 每一个测试方法之后运行
- @BeforeClass: 所有测试开始之前运行（被标记的方法必须是static的）
- @AfterClass: 所有测试结束之后运行（被标记的方法必须是static的）

示例：

```java
public class AddOperationTest {

    @BeforeClass
    public static void beforeClass() {
        System.out.println("BeforeClass");
    }

    @AfterClass
    public static void afterClass() {
        System.out.println("AfterClass");
    }

    @Before
    public void setUp() throws Exception {
        System.out.println("before");
    }

    @After
    public void tearDown() throws Exception {
        System.out.println("after");
    }

    @Test
    public void test1() {
        System.out.println("test1");
    }

    @Test
    public void test2() {
        System.out.println("test2");
    }

    @Test
    public void test3() {
        System.out.println("test3");
    }
}
```

运行结果如下:

```
BeforeClass
before
test1
after
before
test2
after
before
test3
after
AfterClass
```
BeforeClass和AfterClass只运行一次，相当于全局的初始化和回收方法，在每次执行一个测试方法的时候，会先执行before，做一次单独初始化，然后在执行测试方法，最后再执行after，做个回收功能。

# 高级使用
## 断言
JUnit对断言进行了扩展，可以很方便的使用。比如：

```java
import static org.hamcrest.CoreMatchers.allOf;
import static org.hamcrest.CoreMatchers.anyOf;
import static org.hamcrest.CoreMatchers.both;
import static org.hamcrest.CoreMatchers.containsString;
import static org.hamcrest.CoreMatchers.equalTo;
import static org.hamcrest.CoreMatchers.everyItem;
import static org.hamcrest.CoreMatchers.hasItems;
import static org.hamcrest.CoreMatchers.not;
import static org.hamcrest.CoreMatchers.sameInstance;
import static org.hamcrest.CoreMatchers.startsWith;
import static org.junit.Assert.assertArrayEquals;
import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertFalse;
import static org.junit.Assert.assertNotNull;
import static org.junit.Assert.assertNotSame;
import static org.junit.Assert.assertNull;
import static org.junit.Assert.assertSame;
import static org.junit.Assert.assertThat;
import static org.junit.Assert.assertTrue;

import java.util.Arrays;

import org.hamcrest.core.CombinableMatcher;
import org.junit.Test;

public class AssertTests {
  @Test
  public void testAssertArrayEquals() {
    byte[] expected = "trial".getBytes();
    byte[] actual = "trial".getBytes();
    assertArrayEquals("failure - byte arrays not same", expected, actual);
  }

  @Test
  public void testAssertEquals() {
    assertEquals("failure - strings are not equal", "text", "text");
  }

  @Test
  public void testAssertFalse() {
    assertFalse("failure - should be false", false);
  }

  @Test
  public void testAssertNotNull() {
    assertNotNull("should not be null", new Object());
  }

  @Test
  public void testAssertNotSame() {
    assertNotSame("should not be same Object", new Object(), new Object());
  }

  @Test
  public void testAssertNull() {
    assertNull("should be null", null);
  }

  @Test
  public void testAssertSame() {
    Integer aNumber = Integer.valueOf(768);
    assertSame("should be same", aNumber, aNumber);
  }

  // JUnit Matchers assertThat
  @Test
  public void testAssertThatBothContainsString() {
    assertThat("albumen", both(containsString("a")).and(containsString("b")));
  }

  @Test
  public void testAssertThatHasItems() {
    assertThat(Arrays.asList("one", "two", "three"), hasItems("one", "three"));
  }

  @Test
  public void testAssertThatEveryItemContainsString() {
    assertThat(Arrays.asList(new String[] { "fun", "ban", "net" }), everyItem(containsString("n")));
  }

  // Core Hamcrest Matchers with assertThat
  @Test
  public void testAssertThatHamcrestCoreMatchers() {
    assertThat("good", allOf(equalTo("good"), startsWith("good")));
    assertThat("good", not(allOf(equalTo("bad"), equalTo("good"))));
    assertThat("good", anyOf(equalTo("bad"), equalTo("good")));
    assertThat(7, not(CombinableMatcher.<Integer> either(equalTo(3)).or(equalTo(4))));
    assertThat(new Object(), not(sameInstance(new Object())));
  }

  @Test
  public void testAssertTrue() {
    assertTrue("failure - should be true", true);
  }
}
```
## Suite 
有时候我们需要写一些sdk给其他开发人员使用，但是需要保证质量，同时需要运行多个测试类。那么可以使用Suite来搞定。使用@RunWith注解来标注，然后指定哪些类需要一起测试。


```java
import org.junit.runner.RunWith;
import org.junit.runners.Suite;

@RunWith(Suite.class)
@Suite.SuiteClasses({
  TestFeatureLogin.class,
  TestFeatureLogout.class,
  TestFeatureNavigate.class,
  TestFeatureUpdate.class
})
public class FeatureTestSuite {
  // the class remains empty,
  // used only as a holder for the above annotations
}
```

## 测试异常
有时候，一个方法会抛出一些异常，当测试某个方法是否有异常，则使用如下方法。在@Test注解中申明需要抛出的异常，expected = XXXException.class。
比如如下代码，什么一个空的数组，然后获取第0个元素，那么会抛出数组越界异常。

```java
@Test(expected = IndexOutOfBoundsException.class) 
public void empty() { 
     new ArrayList<Object>().get(0); 
}
```
注意：使用expected字段后，则测试的内容必须抛出异常，否则任务该测试失败。

## 测试运行
JUnit获取一个类中所有的含有@Test方法的时候，返回的顺序和编写代码的顺序是不一致。那么可以使用一种简单方法来标注方法的排序，然后会按照这个顺序依次执行。

```java
import org.junit.FixMethodOrder;
import org.junit.Test;
import org.junit.runners.MethodSorters;

@FixMethodOrder(MethodSorters.NAME_ASCENDING)
public class TestMethodOrder {

    @Test
    public void testA() {
        System.out.println("first");
    }
    @Test
    public void testB() {
        System.out.println("second");
    }
    @Test
    public void testC() {
        System.out.println("third");
    }
}
```
## 断言匹配

有时候方法的返回值是一个不确定，但是有一定的规则的值，那么可以只用一些条件限制，来判断测试结果是否符合预期。示例如下：

```java
assertThat(x, is(3));
assertThat(x, is(not(4)));
assertThat(responseString, either(containsString("color")).or(containsString("colour")));
assertThat(myList, hasItem("3"));
```

## 错误忽略
有时候一些特殊原因，不需要关注一些测试失败的方法，那么可以临时添加@Ignore，忽略一些测试方法。

```java
@Ignore("Test is ignored as a demonstration")
@Test
public void testSame() {
    assertThat(1, is(1));
}
```

## 超时测试
有的方法需要运行很长时间，在核心功能上，性能会需要做些优化，在测试的时候可以指定运行的最大时间，如果超过指定的时间则失败。

```java
@Test(timeout=1000)
public void testWithTimeout() {
  ...
}
```

## 自定义参数
有时候，需要指定一组参数来做重复的测试，那么根据软件开发的规则，需要指定参数即可，可以使用如下方法，来配置参数。

```java
public class Fibonacci {
    public static int compute(int n) {
        int result = 0;

        if (n <= 1) { 
            result = n; 
        } else { 
            result = compute(n - 1) + compute(n - 2); 
        }

        return result;
    }
}

@RunWith(Parameterized.class)
public class FibonacciTest {
    @Parameters
    public static Collection<Object[]> data() {
        return Arrays.asList(new Object[][] {     
                 { 0, 0 }, { 1, 1 }, { 2, 1 }, { 3, 2 }, { 4, 3 }, { 5, 5 }, { 6, 8 }  
           });
    }

    private int fInput;
    private int fExpected;
    public FibonacciTest(int input, int expected) {
        fInput= input;
        fExpected= expected;
    }

    @Test
    public void test() {
        assertEquals(fExpected, Fibonacci.compute(fInput));
    }
}

```

## 假设

有时候开发者只需要关注一些业务逻辑，但是有时候一些外在的环境会导致测试代码的错误，但是这些错误有时候确实不是我所关心的。那么我么可以提供Assume这样类，跟Assert类似，只不过当运行Assume错误时，不会认为失败。

```java
 import static org.junit.Assume.*
    @Test
    public void filenameIncludesUsername() {
        assumeThat(File.separatorChar, is('/'));
        assertThat(new User("optimus").configFileName(), is("configfiles/optimus.cfg"));
    }

    @Test
    public void correctBehaviorWhenFilenameIsNull() {
       assumeTrue(bugFixed("13356"));  // bugFixed is not included in JUnit
       assertThat(parse(null), is(new NullDocument()));
    }
```

## Rule
Stop extending abstract test classes and start writing test rules.

[https://github.com/junit-team/junit4/wiki/Rules](https://github.com/junit-team/junit4/wiki/Rules)

## Theories 
Write tests that are more like scientific experiments using randomly generated data.

[https://github.com/junit-team/junit4/wiki/Theories](https://github.com/junit-team/junit4/wiki/Theories)

## Fixtures
Specify set up and clean up methods on a per-method and per-class basis.

[https://github.com/junit-team/junit4/wiki/Test-fixtures](https://github.com/junit-team/junit4/wiki/Test-fixtures)

## Categories 
Group your tests together for easier test filtering.
[https://github.com/junit-team/junit4/wiki/Categories](https://github.com/junit-team/junit4/wiki/Categories)

# 总结

单元测试对提高代码质量很有帮助，作者最近做一些sdk的开发，迭代次数较多，导致每次开发需要重新测试以前的功能，很费时费力，而且效果很不好，所以才下决心进行代码级别测试，努力提高代码质量。

本文参照JUnit的Github地址，做简单介绍，如需要详细了解，参考这里[https://github.com/junit-team/junit4/wiki](https://github.com/junit-team/junit4/wiki)。
