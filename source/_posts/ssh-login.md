---
title: ssh自动登录
date: 2018-11-25 15:53:35
categories: 工具使用
tags: 
    - ssh
    - sshpass
---

# ssh自动登录

---

登录服务器的时候，经常使用`ssh`进行远程的登录，经常输入密码，比较麻烦，可以使用`sshpass`配合`iTerm2`简化操作。
 
## 安装sshpass

首先下载`sshpass`，不同系统可能不一样，大致差不多，可以直接使用包管理工具安装。我用的是mac os, 可直接使用`brew`。

```
brew install sshpass
Error: No available formula for sshpass  
We won't add sshpass because it makes it too easy for novice SSH users to  ruin SSH's security.
```

说明不安全，可不管，直接下载安装。

```
brew install https://raw.githubusercontent.com/kadwanev/bigboybrew/master/Library/Formula/sshpass.rb
```

## sshpass使用姿势

### 直接远程连接某主机

```
sshpass -p {密码} ssh {用户名}@{主机IP}
```

### 远程连接指定ssh的端口

```
sshpass -p {密码} ssh -p ${端口} {用户名}@{主机IP} 
```

### 从密码文件读取文件内容作为密码去远程连接主机
```
sshpass -f ${密码文本文件} ssh {用户名}@{主机IP}
```

### 从远程主机上拉取文件到本地

```
sshpass -p {密码} scp {用户名}@{主机IP}:${远程主机目录} ${本地主机目录}
```

### 将主机目录文件拷贝至远程主机目录

```
sshpass -p {密码} scp ${本地主机目录} {用户名}@{主机IP}:${远程主机目录} 
```

### 远程连接主机并执行命令

```
sshpass -p {密码} ssh -o StrictHostKeyChecking=no {用户名}@{主机IP} 'rm -rf /tmp/test'
-o StrictHostKeyChecking=no ：忽略密码提示
```

## iTerm2结合使用

参考上面，使用`sshpass -p {密码} ssh {用户名}@{主机IP}`即可满足我们的需求，每次只要运行同样的命令即可。如:

```
sshpass -p root ssh  admin@192.168.1.100
```

使用`iTerm2`中的`Profile`，添加一条新的配置，如下所示。

![](/images/ssh_login_iterm2.png)

命令如下，sshpass和ssh需要指定具体路径。

```
/usr/local/bin/sshpass -p root /usr/bin/ssh  admin@192.168.1.100
```