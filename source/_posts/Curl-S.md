---
title: Curl不显示统计信息% Total % Received %
date: 2017-09-21 18:42:29
categories: 工具使用
tags:
    - Curl
---

# Curl不显示统计信息% Total % Received %

今天需要用curl测试服务器，用的是python调用的，最后发现结果中包含一些统计信息。

```py
# -*- coding:UTF-8-*-

import os

os.system('curl http://www.baidu.com')
```
结果为:

```
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2381  100  2381    0     0  25963      0 --:--:-- --:--:-- --:--:-- 25880
<!DOCTYPE html>
....
```
经过查找，需要添加一个参数`-s`，可以去除这些统计信息。

```
curl http://www.baidu.com -s
```