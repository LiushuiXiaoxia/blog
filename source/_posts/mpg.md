---
title: MyBatis-Plus Gradle 代码生成插件了解一下
date: 2018-12-21 18:08:06
categories: 后端开发
tags:
    - mybatis plus
    - gradle plugin

---

# Mybatis Plus Gradle Generator

---

# 介绍

Web开发中使用mybatis比较多，[MyBatis Plus](https://mp.baomidou.com/)是一个比较好的扩展，并且还可以自动生成代码，比较方便。
但是MyBatis Plus 对maven比较友好，对Gradle就比较差了，现在Spring Boot也对Gradle支持比较好，在使用Gradle生成代码的时候就比较麻烦。

<!-- more -->

# 使用姿势

本插件需要结合Spring Boot项目一起使用，如下所示，首先添加插件依赖，当前版本为
[ ![Download](https://api.bintray.com/packages/liushuixiaoxia/maven/mpg/images/download.svg) ](https://bintray.com/liushuixiaoxia/maven/mpg/_latestVersion)

```groovy
buildscript {
    ext {
        springBootVersion = '2.1.1.RELEASE'
    }
    repositories {
        maven { url "http://maven.aliyun.com/nexus/content/groups/public/" }
        maven { url "https://dl.bintray.com/liushuixiaoxia/maven"  }
        mavenCentral()
        jcenter()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
        classpath("cn.mycommons:mpg:${mpgVesion}")
    }
}
```

然后配置相关属性即可，本配置可以参考[MyBatis Plus 代码生成官方文档](https://mp.baomidou.com/guide/generator.html)，基本配置和官方配置一样。

示例：表名为`tb_app_info`，前缀为`tb_`，生成的entity为`AppInfo`,mapper为`AppInfoMapper.xml`。

```groovy
apply plugin: 'mpg'
mpg {
    enable = true

    globalConfig {
        outputDir = projectDir.path + "/src/main/java"
        author = "Admin"
        open = false
        baseResultMap = true
    }

    dataSourceConfig {
        driverName = "com.mysql.jdbc.Driver"
        url = "jdbc:mysql://localhost:3306/mpg_test?useUnicode=true&characterEncoding=utf8&useSSL=false"
        username = "root"
        password = "admin"
    }

    packageConfig {
        parent = "cn.mycommons.springdemo.mpg"
        entity = "mybatis.entity"
        mapper = "mybatis.mapper"
    }

    xmlMapperConfig {
        path = projectDir.path + "/src/main/resources/mapper/"
        name = "AppInfoMapper.xml"
    }

    strategyConfig {
        include = ["tb_app_info"]
        tablePrefix = ["tb_"]
        entityLombokModel = true
        restControllerStyle = true
        superEntityClass = "cn.mycommons.basic.dto.BaseEntity"
        logicDeleteFieldName = "is_delete"
        superEntityColumns = ["create_by", "create_time", "update_by", "update_time", "remark", "is_delete"]
        entityTableFieldAnnotationEnable(true)
        controllerMappingHyphenStyle = true
        superControllerClass = "cn.mycommons.basic.controller.BaseController"
    }
}
```

下面为自定义配置，表示生成xmlMapper的文件路径。

```groovy
xmlMapperConfig {
    path = projectDir.path + "/src/main/resources/mapper/"
    name = "AppInfoMapper.xml"
}
```

然后执行Gradle Task `mpg` 即可。

![mpg](https://raw.githubusercontent.com/LiushuiXiaoxia/mybatis-plus-gradle-generator/master/doc/1.png)

# 相关链接

[MyBatis Plus](https://mp.baomidou.com/)

[MyBatis Plus 文档](https://mp.baomidou.com/config/)

[MyBatis Plus Gradle Plugin](https://github.com/LiushuiXiaoxia/mybatis-plus-gradle-generator)