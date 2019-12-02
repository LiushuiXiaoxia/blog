---
title: Jenkins can't parse argument number changelog.url 问题
date: 2018-11-26 00:17:13
categories: Jenkins
tags:
    - Jeknins
---

最近使用了Jenkins，碰到一问题，提示如下。

```java
org.apache.commons.jelly.JellyTagException: jar:file:/Users/xiaqiulei/.jenkins/war/WEB-INF/lib/jenkins-core-2.147.jar!/hudson/model/UpdateCenter/CoreUpdateMonitor/message.jelly:53:20: <j:otherwise> can't parse argument number: changelog.url
	at org.apache.commons.jelly.impl.TagScript.handleException(TagScript.java:726)
	at org.apache.commons.jelly.impl.TagScript.run(TagScript.java:281)
	at org.apache.commons.jelly.impl.ScriptBlock.run(ScriptBlock.java:95)
	at org.apache.commons.jelly.TagSupport.invokeBody(TagSupport.java:161)
	at org.apache.commons.jelly.tags.core.ChooseTag.doTag(ChooseTag.java:38)
	at org.apache.commons.jelly.impl.TagScript.run(TagScript.java:269)
	at org.apache.commons.jelly.impl.ScriptBlock.run(ScriptBlock.java:95)
	at org.kohsuke.stapler.jelly.ReallyStaticTagLibrary$1.run(ReallyStaticTagLi
```

排查了好久，才发现问题，原因是升级了一个插件导致的，经过一个多小时的排查，终于找到了，是jeknins中文语言包，卸载或者降级即可。

<!-- more -->

```
Localization: Chinese (Simplified)
Jenkins 及其插件的简体中文语言包。
```

但是页面出现了问题，就不能接入插件管理的页面，可以直接打开jenkins目录，删除插件文件，然后重启服务即可。

我的电脑是mac，插件地址如下，删除`localization-zh-cn`和`localization-zh-cn.jpi`即可。

```
~/.jenkins/plugins
```

当然还有一种办法可以解决，jenkins系统管理页面是进入不了，如下所示

```
http://localhost:8080/manage
```

但是可以直接访问地址的

```
http://localhost:8080/pluginManager/
```