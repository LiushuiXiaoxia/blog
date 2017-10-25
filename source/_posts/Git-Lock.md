---
title: 关于Git的错误
date: 2017-04-15 18:56:48
categories: 工具使用
tags:
    - Git
---

# 关于Git的错误

写完代码以后，不能继续提交，错误显示是这样的。

```
fatal: Unable to create '/Users/Demo/.git/index.lock': File exists.

Another git process seems to be running in this repository, e.g.
an editor opened by 'git commit'. Please make sure all processes
are terminated then try again. If it still fails, a git process
may have crashed in this repository earlier:
remove the file manually to continue.
```

大致意思是，有个其他进程在操作这个仓库，所以无法继续提交。

只需要删除`index.lock`文件即可。

```sh
rm .git/index.lock
```