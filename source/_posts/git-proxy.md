---
title: Mac Git终端代理问题
date: 2018-09-25 16:03:04
categories: 工具使用
tags:
    - git
    - proxy
    - socks5
---

# Mac Git终端代理问题

最近换了新电脑，使用的是自己的vpn，但是发现clone github上面的代码比较慢，经常失败，使用brew也会出错了，后面发现在终端上面使用的git不是走系统的代理，需要配置如下，即可。

```bash
git config --global http.https://github.com.proxy socks5://127.0.0.1:1086
```