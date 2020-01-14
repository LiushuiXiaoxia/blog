---
title: Docker快速删除
date: 2020-01-14 14:32:40
categories: Docker
tags:
    - Docker
---

# Docker快速删除

---

## Docker删除无效的容器

```bash
docker ps -a | grep Exited | awk '{print $1}' | xargs docker rm
```

## Docker删除无用镜像

```bash
docker images | grep none | awk '{print $3}' | xargs docker rmi
```

## 停用全部运行中的容器

```bash
docker stop $(docker ps -q)
```

## 删除全部容器

```bash
docker rm $(docker ps -aq)
```

## 停用并删除容器

```bash
docker stop $(docker ps -q) & docker rm $(docker ps -aq)
```