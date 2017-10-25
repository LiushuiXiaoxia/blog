---
title: 格式化Curl返回的Json字符
date: 2017-08-12 18:30:32
categories: 工具使用
tags:
    - Curl
    - Python
    - Json format
    - 工具使用
---
# 格式化Curl返回的Json字符

---

<!-- TOC -->

- [格式化Curl返回的Json字符](#格式化curl返回的json字符)
    - [Python 格式化](#python-格式化)
    - [Nodejs 格式化](#nodejs-格式化)

<!-- /TOC -->

经常会用到curl调试接口，服务器返回的是json，不过这些json是没有格式化的，不方便阅读。

经过搜索和实验，发现下面2中方式比较方便。

示例：

```bash
curl https://news-at.zhihu.com/api/4/news/latest
{"date":"20171014","stories":[{"title":"这些有故事的 DOTA 职业选手外号（国外篇）","ga_prefix":"101417","images":["https:\/\/pic3.zhimg.com\/v2-471f6f1170fcb7d491ba54404acaf30a.jpg"],"multipic":true,"type":0,"id":9651211},{"images":["https:\/\/pic1.zhimg.com\/v2-16e9abb39a4fb4dd56994c9db9378110.jpg"],"type":0,"id":9649645,"ga_prefix":"101416","title":"被你的宠物捉弄的的时候，你想过「动物是否会骗人」吗？"},{"images":["https:\/\/pic3.zhimg.com\/v2-5ab5db73049a413a6810677ad3817602.jpg"],"type":0,"id":9651376,"ga_prefix":"101415","title":"想明白 iPhone X 的人脸识别是怎么工作的，先得了解这道「光」"},{"images":["https:\/\/pic1.zhimg.com\/v2-c20f4551e1725eef3ac7cd502e9ed71c.jpg"],"type":0,"id":9649434,"ga_prefix":"101414","title":"不靠专利，拿什么保护发明？世博会的数据会告诉你"},{"images":["https:\/\/pic1.zhimg.com\/v2-a0a033debd47042be4554a3450fe9478.jpg"],"type":0,"id":9650760,"ga_prefix":"101413","title":"回头看这项世界顶级比赛的历史，对它的存在意义愈发迷茫"},{"images":["https:\/\/pic4.zhimg.com\/v2-0df458ee4784b68befc40c03ddf5bc67.jpg"],"type":0,"id":9646702,"ga_prefix":"101412","title":"大误 · 一次咨询"},{"images":["https:\/\/pic2.zhimg.com\/v2-9d0caf3ba46c60a15826e53ea8473a5d.jpg"],"type":0,"id":9634763,"ga_prefix":"101411","title":"黄金钩焖五花肉，绵软浓香的口感，其他菜绝对没法比"},{"images":["https:\/\/pic4.zhimg.com\/v2-c4964723821a835fb42a4f70bfa8ce6f.jpg"],"type":0,"id":9650778,"ga_prefix":"101410","title":"Ta 的一生可以写 20 本书，至于是男是女，已经不重要了"},{"images":["https:\/\/pic1.zhimg.com\/v2-054e30b7cec2624a849af1e358528cd8.jpg"],"type":0,"id":9634507,"ga_prefix":"101409","title":"中国有哪些不出名，但值得一去的山？"},{"images":["https:\/\/pic4.zhimg.com\/v2-95d371314244ca1ce74bfbd22ba0938f.jpg"],"type":0,"id":9651324,"ga_prefix":"101408","title":"- 实在想不到怎么出国更炫酷了\r\n- 喏，自己开飞机去"},{"images":["https:\/\/pic2.zhimg.com\/v2-06dd1c008cac334147cd81abf0bfefe1.jpg"],"type":0,"id":9649625,"ga_prefix":"101407","title":"早起来一份「班尼迪克蛋」，做个逼格满满的早餐网红"},{"images":["https:\/\/pic4.zhimg.com\/v2-b0e9676d36b7fd4b1822ba8cb93bf4f7.jpg"],"type":0,"id":9651424,"ga_prefix":"101407","title":"丁俊晖：那个满脸青春痘的少年球手，几度大起大落已是而立之年"},{"images":["https:\/\/pic3.zhimg.com\/v2-a065e8278298efb317b13d92084275f6.jpg"],"type":0,"id":9650104,"ga_prefix":"101407","title":"在你读过的童话中，是不是一对姐妹里坏的那个总是姐姐？"},{"images":["https:\/\/pic3.zhimg.com\/v2-db3702f9cee08897c8f7a174000f0ca2.jpg"],"type":0,"id":9651366,"ga_prefix":"101406","title":"瞎扯 · 如何正确地吐槽"}],"top_stories":[{"image":"https:\/\/pic3.zhimg.com\/v2-e5dc45c4698771e7001e1ae1c27ba8b6.jpg","type":0,"id":9651376,"ga_prefix":"101415","title":"想明白 iPhone X 的人脸识别是怎么工作的，先得了解这道「光」"},{"image":"https:\/\/pic3.zhimg.com\/v2-ce790cb7a633fbc7aa8e2303e3c1ce16.jpg","type":0,"id":9651424,"ga_prefix":"101407","title":"丁俊晖：那个满脸青春痘的少年球手，几度大起大落已是而立之年"},{"image":"https:\/\/pic2.zhimg.com\/v2-36e6e13f557c2dd29e65d8c23fa9cec5.jpg","type":0,"id":9634507,"ga_prefix":"101409","title":"中国有哪些不出名，但值得一去的山？"},{"image":"https:\/\/pic4.zhimg.com\/v2-79056fe95d9834f9c8b30957980b0193.jpg","type":0,"id":9649434,"ga_prefix":"101414","title":"不靠专利，拿什么保护发明？世博会的数据会告诉你"},{"image":"https:\/\/pic4.zhimg.com\/v2-45e71d718938e0a4e292b12e52269a07.jpg","type":0,"id":9650104,"ga_prefix":"101407","title":"在你读过的童话中，是不是一对姐妹里坏的那个总是姐姐？"}]}
```

## Python 格式化

在curl命令后面添加 ` | python -m json.tool` 即可。

如下所示

```bash
curl https://news-at.zhihu.com/api/4/news/latest | python -m json.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  3901  100  3901    0     0  33333      0 --:--:-- --:--:-- --:--:-- 33629
{
    "date": "20171014",
    "stories": [
        {
            "ga_prefix": "101417",
            "id": 9651211,
            "images": [
                "https://pic3.zhimg.com/v2-471f6f1170fcb7d491ba54404acaf30a.jpg"
            ],
            "multipic": true,
            "title": "\u8fd9\u4e9b\u6709\u6545\u4e8b\u7684 DOTA \u804c\u4e1a\u9009\u624b\u5916\u53f7\uff08\u56fd\u5916\u7bc7\uff09",
            "type": 0
        },
        {
            "ga_prefix": "101416",
            "id": 9649645,
            "images": [
                "https://pic1.zhimg.com/v2-16e9abb39a4fb4dd56994c9db9378110.jpg"
            ],
            "title": "\u88ab\u4f60\u7684\u5ba0\u7269\u6349\u5f04\u7684\u7684\u65f6\u5019\uff0c\u4f60\u60f3\u8fc7\u300c\u52a8\u7269\u662f\u5426\u4f1a\u9a97\u4eba\u300d\u5417\uff1f",
            "type": 0
        },
        {
            "ga_prefix": "101415",
            "id": 9651376,
            "images": [
                "https://pic3.zhimg.com/v2-5ab5db73049a413a6810677ad3817602.jpg"
            ],
            "title": "\u60f3\u660e\u767d iPhone X \u7684\u4eba\u8138\u8bc6\u522b\u662f\u600e\u4e48\u5de5\u4f5c\u7684\uff0c\u5148\u5f97\u4e86\u89e3\u8fd9\u9053\u300c\u5149\u300d",
            "type": 0
        },
        {
            "ga_prefix": "101414",
            "id": 9649434,
            "images": [
                "https://pic1.zhimg.com/v2-c20f4551e1725eef3ac7cd502e9ed71c.jpg"
            ],
            "title": "\u4e0d\u9760\u4e13\u5229\uff0c\u62ff\u4ec0\u4e48\u4fdd\u62a4\u53d1\u660e\uff1f\u4e16\u535a\u4f1a\u7684\u6570\u636e\u4f1a\u544a\u8bc9\u4f60",
            "type": 0
        },
        {
            "ga_prefix": "101413",
            "id": 9650760,
            "images": [
                "https://pic1.zhimg.com/v2-a0a033debd47042be4554a3450fe9478.jpg"
            ],
            "title": "\u56de\u5934\u770b\u8fd9\u9879\u4e16\u754c\u9876\u7ea7\u6bd4\u8d5b\u7684\u5386\u53f2\uff0c\u5bf9\u5b83\u7684\u5b58\u5728\u610f\u4e49\u6108\u53d1\u8ff7\u832b",
            "type": 0
        },
        {
            "ga_prefix": "101412",
            "id": 9646702,
            "images": [
                "https://pic4.zhimg.com/v2-0df458ee4784b68befc40c03ddf5bc67.jpg"
            ],
            "title": "\u5927\u8bef \u00b7 \u4e00\u6b21\u54a8\u8be2",
            "type": 0
        },
        {
            "ga_prefix": "101411",
            "id": 9634763,
            "images": [
                "https://pic2.zhimg.com/v2-9d0caf3ba46c60a15826e53ea8473a5d.jpg"
            ],
            "title": "\u9ec4\u91d1\u94a9\u7116\u4e94\u82b1\u8089\uff0c\u7ef5\u8f6f\u6d53\u9999\u7684\u53e3\u611f\uff0c\u5176\u4ed6\u83dc\u7edd\u5bf9\u6ca1\u6cd5\u6bd4",
            "type": 0
        },
        {
            "ga_prefix": "101410",
            "id": 9650778,
            "images": [
                "https://pic4.zhimg.com/v2-c4964723821a835fb42a4f70bfa8ce6f.jpg"
            ],
            "title": "Ta \u7684\u4e00\u751f\u53ef\u4ee5\u5199 20 \u672c\u4e66\uff0c\u81f3\u4e8e\u662f\u7537\u662f\u5973\uff0c\u5df2\u7ecf\u4e0d\u91cd\u8981\u4e86",
            "type": 0
        },
        {
            "ga_prefix": "101409",
            "id": 9634507,
            "images": [
                "https://pic1.zhimg.com/v2-054e30b7cec2624a849af1e358528cd8.jpg"
            ],
            "title": "\u4e2d\u56fd\u6709\u54ea\u4e9b\u4e0d\u51fa\u540d\uff0c\u4f46\u503c\u5f97\u4e00\u53bb\u7684\u5c71\uff1f",
            "type": 0
        },
        {
            "ga_prefix": "101408",
            "id": 9651324,
            "images": [
                "https://pic4.zhimg.com/v2-95d371314244ca1ce74bfbd22ba0938f.jpg"
            ],
            "title": "- \u5b9e\u5728\u60f3\u4e0d\u5230\u600e\u4e48\u51fa\u56fd\u66f4\u70ab\u9177\u4e86\r\n- \u558f\uff0c\u81ea\u5df1\u5f00\u98de\u673a\u53bb",
            "type": 0
        },
        {
            "ga_prefix": "101407",
            "id": 9649625,
            "images": [
                "https://pic2.zhimg.com/v2-06dd1c008cac334147cd81abf0bfefe1.jpg"
            ],
            "title": "\u65e9\u8d77\u6765\u4e00\u4efd\u300c\u73ed\u5c3c\u8fea\u514b\u86cb\u300d\uff0c\u505a\u4e2a\u903c\u683c\u6ee1\u6ee1\u7684\u65e9\u9910\u7f51\u7ea2",
            "type": 0
        },
        {
            "ga_prefix": "101407",
            "id": 9651424,
            "images": [
                "https://pic4.zhimg.com/v2-b0e9676d36b7fd4b1822ba8cb93bf4f7.jpg"
            ],
            "title": "\u4e01\u4fca\u6656\uff1a\u90a3\u4e2a\u6ee1\u8138\u9752\u6625\u75d8\u7684\u5c11\u5e74\u7403\u624b\uff0c\u51e0\u5ea6\u5927\u8d77\u5927\u843d\u5df2\u662f\u800c\u7acb\u4e4b\u5e74",
            "type": 0
        },
        {
            "ga_prefix": "101407",
            "id": 9650104,
            "images": [
                "https://pic3.zhimg.com/v2-a065e8278298efb317b13d92084275f6.jpg"
            ],
            "title": "\u5728\u4f60\u8bfb\u8fc7\u7684\u7ae5\u8bdd\u4e2d\uff0c\u662f\u4e0d\u662f\u4e00\u5bf9\u59d0\u59b9\u91cc\u574f\u7684\u90a3\u4e2a\u603b\u662f\u59d0\u59d0\uff1f",
            "type": 0
        },
        {
            "ga_prefix": "101406",
            "id": 9651366,
            "images": [
                "https://pic3.zhimg.com/v2-db3702f9cee08897c8f7a174000f0ca2.jpg"
            ],
            "title": "\u778e\u626f \u00b7 \u5982\u4f55\u6b63\u786e\u5730\u5410\u69fd",
            "type": 0
        }
    ],
    "top_stories": [
        {
            "ga_prefix": "101415",
            "id": 9651376,
            "image": "https://pic3.zhimg.com/v2-e5dc45c4698771e7001e1ae1c27ba8b6.jpg",
            "title": "\u60f3\u660e\u767d iPhone X \u7684\u4eba\u8138\u8bc6\u522b\u662f\u600e\u4e48\u5de5\u4f5c\u7684\uff0c\u5148\u5f97\u4e86\u89e3\u8fd9\u9053\u300c\u5149\u300d",
            "type": 0
        },
        {
            "ga_prefix": "101407",
            "id": 9651424,
            "image": "https://pic3.zhimg.com/v2-ce790cb7a633fbc7aa8e2303e3c1ce16.jpg",
            "title": "\u4e01\u4fca\u6656\uff1a\u90a3\u4e2a\u6ee1\u8138\u9752\u6625\u75d8\u7684\u5c11\u5e74\u7403\u624b\uff0c\u51e0\u5ea6\u5927\u8d77\u5927\u843d\u5df2\u662f\u800c\u7acb\u4e4b\u5e74",
            "type": 0
        },
        {
            "ga_prefix": "101409",
            "id": 9634507,
            "image": "https://pic2.zhimg.com/v2-36e6e13f557c2dd29e65d8c23fa9cec5.jpg",
            "title": "\u4e2d\u56fd\u6709\u54ea\u4e9b\u4e0d\u51fa\u540d\uff0c\u4f46\u503c\u5f97\u4e00\u53bb\u7684\u5c71\uff1f",
            "type": 0
        },
        {
            "ga_prefix": "101414",
            "id": 9649434,
            "image": "https://pic4.zhimg.com/v2-79056fe95d9834f9c8b30957980b0193.jpg",
            "title": "\u4e0d\u9760\u4e13\u5229\uff0c\u62ff\u4ec0\u4e48\u4fdd\u62a4\u53d1\u660e\uff1f\u4e16\u535a\u4f1a\u7684\u6570\u636e\u4f1a\u544a\u8bc9\u4f60",
            "type": 0
        },
        {
            "ga_prefix": "101407",
            "id": 9650104,
            "image": "https://pic4.zhimg.com/v2-45e71d718938e0a4e292b12e52269a07.jpg",
            "title": "\u5728\u4f60\u8bfb\u8fc7\u7684\u7ae5\u8bdd\u4e2d\uff0c\u662f\u4e0d\u662f\u4e00\u5bf9\u59d0\u59b9\u91cc\u574f\u7684\u90a3\u4e2a\u603b\u662f\u59d0\u59d0\uff1f",
            "type": 0
        }
    ]
}
```

如果不想显示curl的统计信息，可以参考这篇[文章](http://www.jianshu.com/p/7e696e1b14d3)，添加 `-s`参数即可。

```bash
curl https://news-at.zhihu.com/api/4/news/latest  -s | python -m json.tool
```

## Nodejs 格式化

用nvm安装一个json库，这里是库的[地址](https://www.npmjs.com/package/json) 文档。

安装`json`命令

```bash
npm install -g json
```

在curl命令后面添加 ` | json ` 即可。

如下所示：

```bash
curl https://news-at.zhihu.com/api/4/news/latest -s | json
{
  "date": "20171014",
  "stories": [
    {
      "title": "这些有故事的 DOTA 职业选手外号（国外篇）",
      "ga_prefix": "101417",
      "images": [
        "https://pic3.zhimg.com/v2-471f6f1170fcb7d491ba54404acaf30a.jpg"
      ],
      "multipic": true,
      "type": 0,
      "id": 9651211
    },
    {
      "images": [
        "https://pic1.zhimg.com/v2-16e9abb39a4fb4dd56994c9db9378110.jpg"
      ],
      "type": 0,
      "id": 9649645,
      "ga_prefix": "101416",
      "title": "被你的宠物捉弄的的时候，你想过「动物是否会骗人」吗？"
    },
    {
      "images": [
        "https://pic3.zhimg.com/v2-5ab5db73049a413a6810677ad3817602.jpg"
      ],
      "type": 0,
      "id": 9651376,
      "ga_prefix": "101415",
      "title": "想明白 iPhone X 的人脸识别是怎么工作的，先得了解这道「光」"
    },
    {
      "images": [
        "https://pic1.zhimg.com/v2-c20f4551e1725eef3ac7cd502e9ed71c.jpg"
      ],
      "type": 0,
      "id": 9649434,
      "ga_prefix": "101414",
      "title": "不靠专利，拿什么保护发明？世博会的数据会告诉你"
    },
    {
      "images": [
        "https://pic1.zhimg.com/v2-a0a033debd47042be4554a3450fe9478.jpg"
      ],
      "type": 0,
      "id": 9650760,
      "ga_prefix": "101413",
      "title": "回头看这项世界顶级比赛的历史，对它的存在意义愈发迷茫"
    },
    {
      "images": [
        "https://pic4.zhimg.com/v2-0df458ee4784b68befc40c03ddf5bc67.jpg"
      ],
      "type": 0,
      "id": 9646702,
      "ga_prefix": "101412",
      "title": "大误 · 一次咨询"
    },
    {
      "images": [
        "https://pic2.zhimg.com/v2-9d0caf3ba46c60a15826e53ea8473a5d.jpg"
      ],
      "type": 0,
      "id": 9634763,
      "ga_prefix": "101411",
      "title": "黄金钩焖五花肉，绵软浓香的口感，其他菜绝对没法比"
    },
    {
      "images": [
        "https://pic4.zhimg.com/v2-c4964723821a835fb42a4f70bfa8ce6f.jpg"
      ],
      "type": 0,
      "id": 9650778,
      "ga_prefix": "101410",
      "title": "Ta 的一生可以写 20 本书，至于是男是女，已经不重要了"
    },
    {
      "images": [
        "https://pic1.zhimg.com/v2-054e30b7cec2624a849af1e358528cd8.jpg"
      ],
      "type": 0,
      "id": 9634507,
      "ga_prefix": "101409",
      "title": "中国有哪些不出名，但值得一去的山？"
    },
    {
      "images": [
        "https://pic4.zhimg.com/v2-95d371314244ca1ce74bfbd22ba0938f.jpg"
      ],
      "type": 0,
      "id": 9651324,
      "ga_prefix": "101408",
      "title": "- 实在想不到怎么出国更炫酷了\r\n- 喏，自己开飞机去"
    },
    {
      "images": [
        "https://pic2.zhimg.com/v2-06dd1c008cac334147cd81abf0bfefe1.jpg"
      ],
      "type": 0,
      "id": 9649625,
      "ga_prefix": "101407",
      "title": "早起来一份「班尼迪克蛋」，做个逼格满满的早餐网红"
    },
    {
      "images": [
        "https://pic4.zhimg.com/v2-b0e9676d36b7fd4b1822ba8cb93bf4f7.jpg"
      ],
      "type": 0,
      "id": 9651424,
      "ga_prefix": "101407",
      "title": "丁俊晖：那个满脸青春痘的少年球手，几度大起大落已是而立之年"
    },
    {
      "images": [
        "https://pic3.zhimg.com/v2-a065e8278298efb317b13d92084275f6.jpg"
      ],
      "type": 0,
      "id": 9650104,
      "ga_prefix": "101407",
      "title": "在你读过的童话中，是不是一对姐妹里坏的那个总是姐姐？"
    },
    {
      "images": [
        "https://pic3.zhimg.com/v2-db3702f9cee08897c8f7a174000f0ca2.jpg"
      ],
      "type": 0,
      "id": 9651366,
      "ga_prefix": "101406",
      "title": "瞎扯 · 如何正确地吐槽"
    }
  ],
  "top_stories": [
    {
      "image": "https://pic3.zhimg.com/v2-e5dc45c4698771e7001e1ae1c27ba8b6.jpg",
      "type": 0,
      "id": 9651376,
      "ga_prefix": "101415",
      "title": "想明白 iPhone X 的人脸识别是怎么工作的，先得了解这道「光」"
    },
    {
      "image": "https://pic3.zhimg.com/v2-ce790cb7a633fbc7aa8e2303e3c1ce16.jpg",
      "type": 0,
      "id": 9651424,
      "ga_prefix": "101407",
      "title": "丁俊晖：那个满脸青春痘的少年球手，几度大起大落已是而立之年"
    },
    {
      "image": "https://pic2.zhimg.com/v2-36e6e13f557c2dd29e65d8c23fa9cec5.jpg",
      "type": 0,
      "id": 9634507,
      "ga_prefix": "101409",
      "title": "中国有哪些不出名，但值得一去的山？"
    },
    {
      "image": "https://pic4.zhimg.com/v2-79056fe95d9834f9c8b30957980b0193.jpg",
      "type": 0,
      "id": 9649434,
      "ga_prefix": "101414",
      "title": "不靠专利，拿什么保护发明？世博会的数据会告诉你"
    },
    {
      "image": "https://pic4.zhimg.com/v2-45e71d718938e0a4e292b12e52269a07.jpg",
      "type": 0,
      "id": 9650104,
      "ga_prefix": "101407",
      "title": "在你读过的童话中，是不是一对姐妹里坏的那个总是姐姐？"
    }
  ]
}
```