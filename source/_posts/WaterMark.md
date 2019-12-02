---
title: 自己写个小工具——图片水印生成器
date: 2017-07-29 18:36:02
categories: 工具使用
tags:
    - 图片水印
    - WaterMark
    - Homebrew WaterMark
---

# 自己写个小工具——图片水印生成器

---

## 介绍

做技术好几年了，最近想总结一下，写了几篇文章，不过发现经常有转载的地方，所以想做个图片水印，简书上面的图片是没有水印的，所以就自己写了个工具，供大家使用。

这里是[地址https://github.com/LiushuiXiaoxia/WaterMark](https://github.com/LiushuiXiaoxia/WaterMark)，水印生成器，可以给指定图片文件或者目录添加水印，水印支持自定义文本、位置、颜色、大小。

<!-- more -->

## 安装

```bash
brew tap LiushuiXiaoxia/watermark
brew install watermark
```

执行`watermark -h`，显示帮助信息，说明安装成功。

```bash
$ watermark -h
usage: watermark.py [-h] -f FILE -t TEXT -o OUT [-c COLOR] [-s SIZE]
                    [-p POSITION]

optional arguments:
  -h, --help            show this help message and exit
  -f FILE, --file FILE  image path or directory
  -t TEXT, --text TEXT  water mart text
  -o OUT, --out OUT     image output directory
  -c COLOR, --color COLOR
                        text color, red、blue and so on
  -s SIZE, --size SIZE  text size
  -p POSITION, --position POSITION
                        text position, left_top、left_bottom、right_top、ri
                        ght_bottom、center，default is center,
```

## 使用说明

参数说明:

```bash
  -h, --help                    帮助信息
  -f FILE, --file FILE          图片路径或者图片目录
  -t TEXT, --text TEXT          水印文本
  -o OUT, --out OUT             图片输出目录
  -c COLOR, --color COLOR       水印文本颜色，可以是red、blue、white等
  -s SIZE, --size SIZE          文本大小
  -p POSITION, --position       文本位置可以是left_top、left_bottom、right_top、right_botto center，默认是center
```

比如举例一个图片，文件路径是`image/test.png`

![](http://upload-images.jianshu.io/upload_images/1520343-5f4470a63d69266a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行命令如下

```bash
watermark -f image/test.png -t "流水不腐小夏" -o new -c red -s 23 -p left_top
```

则效果如下

![](http://upload-images.jianshu.io/upload_images/1520343-00d90e4050966973.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当然如果你有整个文件夹要处理，可以直接指定一个目录。

```bash
watermark -f image -t "流水不腐小夏" -c black  -o new  -p left_top
```

![](http://upload-images.jianshu.io/upload_images/1520343-ef41155203606de6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 其他

如有想法或者意见，欢迎交流。

本程序暂时只支持Mac平台，如果Window平台需要运行，可以直接python代码。

## TODO LIST

* 支持自定义字体

* 支持更多平台