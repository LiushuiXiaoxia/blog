---
title: MockWebServer
date: 2016-07-25 21:31:16
tags:
	- Web Server Test
	- 测试
---

# MockWebServer

- - - - -

# 简介

[MockWebServer Github地址](https://github.com/square/okhttp/tree/master/mockwebserver)

按照官方文档定义，MockWebServer是一个可脚本化的用于测试HTTP客户端的Web服务器。
主要用于测试你的应用在进行HTTP、HTTPS请求时是否按照预期的行为动作。使用该工具，你可以验证应用的请求是否符合预期，你可以选择返回的响应。

MockWebServer包含了所有的HTTP栈，所以可以测试所有的事。甚至可以直接将真实Web服务器中的HTTP响应内容复制过来，以创建相应的测试用例。此外，还可以测试应用在糟糕的网络环境下的表现，比如500错误或者响应返回缓慢。

<!-- more -->

# 示例

MockWebServer使用方法和Mockito类似，按照如下步骤。

- 编写模拟脚本
- 运行应用程序代码
- 验证做出的请求是否符合预期

下面是官方给的示例代码：

```java
public void test() throws Exception() {
    // 创建一个 MockWebServer
    MockWebServer server = new MockWebServer();

    // 设置响应
    server.enqueue(new MockResponse().setBody("hello, world!"));
    server.enqueue(new MockResponse().setBody("sup, bra?"));
    server.enqueue(new MockResponse().setBody("yo dog"));

    // 启动服务
    // Start the server.
    server.start();

    // 设置服务端的URL，客户端请求中使用
    HttpUrl baseUrl = server.url("/v1/chat");

    // 运行你的应用程序代码，进行HTTP请求
    // 响应会按照上面设置中放入队列的顺序被返回
    Chat chat = new Chat(baseUrl);

    chat.loadMore();
    assertEquals("hello, world!", chat.message());

    chat.loadMore();
    chat.loadMore();
    assertEquals(""
                + "hello, world!\n"
                + "sup, bra?\n"
                + "yo dog", chat.message());

    // 可选：确认你的应用做出了正确的请求
    RecordedRequest request1 = server.takeRequst();
    assertEquals("/v1/chat/messages/", request1.getPath());
    assertNotNull(request1.getHeader("Authorization"));

    RecordedRequest request2 = server.takeRequest();
    assertEquals("/v1/chat/message/2", request.getPath());

    RecordedRequest request3 = server.takeRequest();
    assertEquals("/v1/chat/message/3", request.getPath());

    // 关闭服务，因为不能重用
    server.shutdown();
}
```
单元测试时候，可以把 server 作为一个字段，然后在 tearDown() 方法中关闭服务。

# Api接口

## 模拟Response(MockResponse)
MockResponse 可以默认返回http code是200的response，相依可以设置字符串、输入流、字节数组，设置可以设置Header。

```java
MockResponse response = new MockResponse()
    .addHeader("Content-Type", "application/json,charset=utf-8")
    .addHeader("Cache-Control", "no-cache")
    .setBody("{}");
```

MockResponse还可以模拟低速率网络的情况。这一点在测试超时和交互式测试时非常有用。

```java
response.throttleBody(1024, 1, TimeUnit.SECONDS);
```

## 记录请求（RecordedRequest）
校验请求的请求方法、路径、HTTP版本、请求体、请求头。

```java
RecordedRequest request = server.takeRequest();
assertEquals("POST /v1/chat/send HTTP/1.1", request.getRequestLine());
assertEquals("application/json; charset=utf-8", request.getHeader("Content-Type"));
assertEquals("{}", request.getUtf8Body());
```

## 转发器（Dispatcher）
默认情况下 MockWebServer 使用队列来指定响应。另外，可以根据需要使用另外一种响应策略，可以通过转发器来处理器，可以通过请求的路径来选择转发策略。比如，我们可以过滤请求替代 server.enqueue()。

```java
final Dispatcher dispatcher = new Dispatcher() {

    @Override
    public MockResponse dispatch(RecordedRequest request) throws InterruptedException {

        if (request.getPath().equals("/v1/login/auth/")){
            return new MockResponse().setResponseCode(200);
        } else if (request.getPath().equals("v1/check/version/")){
            return new MockResponse().setResponseCode(200).setBody("version=9");
        } else if (request.getPath().equals("/v1/profile/info")) {
            return new MockResponse().setResponseCode(200).setBody("{\\\"info\\\":{\\\"name\":\"Lucas Albuquerque\",\"age\":\"21\",\"gender\":\"male\"}}");
        }
        return new MockResponse().setResponseCode(404);
    }
};
server.setDispatcher(dispatcher);
```

# 集成
- Gradle

```groovy
testCompile 'com.squareup.okhttp3:mockwebserver:(insert latest version)'
```

- Maven

```xml
<dependency>
  <groupId>com.squareup.okhttp3</groupId>
  <artifactId>mockwebserver</artifactId>
  <version>(insert latest version)</version>
  <scope>test</scope>
</dependency>
```



