---
layout:     post
title:      "钉钉群自定义机器人消息Python封装"
subtitle:   "\"自定义聊天机器人 \""
date:       2018-01-23 16:00:00
author:     "Devin"
header-img: "img/leetcode-algorithm.jpg"
tags:
    - Python
    - Chatbot
---


# 一、钉钉群自定义机器人介绍
钉钉群机器人是钉钉群的一个高级扩展功能，然而使用起来却非常简单，只有注册一个钉钉账号即可，就可以将第三方服务的信息聚合到钉钉群中，实现信息的自动化同步，例如：通过聚合Github、Gitlab等源码管理服务，实现源码更新同步；通过聚合Trello、JIRA等项目协调服务，实现项目信息同步；同事，支持Webhook协议的自定义接入，支持更多可能性，例如：将运维报警提醒、自动化测试的结果报告提醒、工作、生活日程安排（上班打卡、下班吃饭、健身、读书、生日、纪念日...）等等的提醒，通过自定义机器人聚合到钉钉中。目前自定义机器人支持文本（text）、链接（link）、markdown三种消息格式、五种消息类型，详细信息请参考[自定义机器人官方文档](https://open-doc.dingtalk.com/docs/doc.htm?spm=0.0.0.0.0Sds7z&treeId=257&articleId=105733&docType=1)

# 二、安装使用
这么好用的功能，只有在群中添加好机器人，得到Webhoo地址，在命令行终端马上就可以一睹为快：
```
curl 'https://oapi.dingtalk.com/robot/send?access_token=xxxxxxxx' \
   -H 'Content-Type: application/json' \
   -d '
  {"msgtype": "text", 
    "text": {
        "content": "我就是我, 是不一样的烟火"
     }
  }' 
```
由于各种消息调用，官方只提供Java语言的封装，平时使用Python比较多，为了更方便平时自动化项目的使用，周末花了点时间用Python语言对各种消息类型进行了一一封装，代码已开源在GitHub上，同时也上传了PyPI。

1、项目源码地址如下：[DingtalkChatbot](https://github.com/zhuifengshen/DingtalkChatbot)
2、安装命令如下：
```
pip install DingtalkChatbot
```
3、支持功能如下：
- 支持Text消息；
- 支持Link消息；
- 支持Markdown消息；
- 支持ActionCard消息；
- 支持消息发送失败时自动通知；
- 支持Python2、Python3；

# 三、各消息类型使用示例

![](http://upload-images.jianshu.io/upload_images/139581-2f00607e29c4d9c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
from dingtalkchatbot.chatbot import DingtalkChatbot
# WebHook地址
webhook = 'https://oapi.dingtalk.com/robot/send?access_token=这里填写自己钉钉群自定义机器人的token'
# 初始化机器人小丁
xiaoding = DtalkChatbot(webhook)
# Text消息@所有人
xiaoding.send_text(msg='我就是小丁，小丁就是我！', is_at_all=True)
```

![](http://upload-images.jianshu.io/upload_images/139581-159adf21dba5ae2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
# Text消息之@指定用户
at_mobiles = ['这里填写需要提醒的用户的手机号码，字符串或数字都可以']
xiaoding.send_text(msg='我就是小丁，小丁就是我！', at_mobiles=at_mobiles)
```

![](http://upload-images.jianshu.io/upload_images/139581-9c791354c3768b15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
# Link消息
xiaoding.send_link(title='万万没想到，某小璐竟然...', text='故事是这样子的...', message_url='http://www.kwongwah.com.my/?p=454748", pic_url="https://pbs.twimg.com/media/CEwj7EDWgAE5eIF.jpg')
```

![](http://upload-images.jianshu.io/upload_images/139581-5838575692918ba6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
# Markdown消息@所有人
xiaoding.send_markdown(title='氧气文字', text='#### 广州天气\n'
                           '> 9度，西北风1级，空气良89，相对温度73%\n\n'
                           '> ![美景](http://www.sinaimg.cn/dy/slidenews/5_img/2013_28/453_28488_469248.jpg)\n'
                           '> ###### 10点20分发布 [天气](http://www.thinkpage.cn/) \n',
                           is_at_all=True)
```

![](http://upload-images.jianshu.io/upload_images/139581-40ca11d223edc08e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
    # Markdown消息@指定用户
    xiaoding.send_markdown(title='氧气文字', text='#### 广州天气 @用户手机号\n'
                           '> 9度，西北风1级，空气良89，相对温度73%\n\n'
                           '> ![美景](http://www.sinaimg.cn/dy/slidenews/5_img/2013_28/453_28488_469248.jpg)\n'
                           '> ###### 10点20分发布 [天气](http://www.thinkpage.cn/) \n',
                           at_mobiles=at_mobiles)
```

![](http://upload-images.jianshu.io/upload_images/139581-3262645fee8f40cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
# FeedCard消息类型
feedlink1 = FeedLink(title="氧气美女", message_url="https://www.dingtalk.com/", pic_url="https://unzippedtv.com/wp-content/uploads/sites/28/2016/02/asian.jpg")
feedlink2 = FeedLink(title="氧眼美女", message_url="https://www.dingtalk.com/", pic_url="https://unzippedtv.com/wp-content/uploads/sites/28/2016/02/asian.jpg")
feedlink3 = FeedLink(title="氧神美女", message_url="https://www.dingtalk.com/", pic_url="https://unzippedtv.com/wp-content/uploads/sites/28/2016/02/asian.jpg")
links = [feedlink1.get_data(), feedlink2.get_data(), feedlink3.get_data()]
xiaoding.send_feed_card(links)
```
![](http://upload-images.jianshu.io/upload_images/139581-bd17065ac75d8c9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
# ActionCard整体跳转消息类型
btns1 = [{"title": "查看详情", "actionURL": "https://www.dingtalk.com/"}]
actioncard1 = ActionCard(title='万万没想到，竟然...',
                             text='![选择](http://www.songshan.es/wp-content/uploads/2016/01/Yin-Yang.png) \n### 故事是这样子的...',
                             btns=btns1,
                             btn_orientation=1,
                             hide_avatar=1)
xiaoding.send_action_card(actioncard1)
```

![select_actioncard.png](http://upload-images.jianshu.io/upload_images/139581-fcdfddd89d6cd68f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
# ActionCard独立跳转消息类型（双选项）
btns2 = [{"title": "支持", "actionURL": "https://www.dingtalk.com/"}, {"title": "反对", "actionURL": "http://www.back china.com/news/2018/01/11/537468.html"}]
actioncard2 = ActionCard(title='万万没想到，竟然...',
                             text='![选择](http://www.songshan.es/wp-content/uploads/2016/01/Yin-Yang.png) \n### 故事是这样子的...',
                             btns=btns2,
                             btn_orientation=1,
                             hide_avatar=1)
xiaoding.send_action_card(actioncard2)
```

![](http://upload-images.jianshu.io/upload_images/139581-cc0bddb34f46ee95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
# ActionCard独立跳转消息类型（列表选项）
btns3 = [{"title": "支持", "actionURL": "https://www.dingtalk.com/"}, {"title": "中立", "actionURL": "https://www.dingtalk.com/"}, {"title": "反对", "actionURL": "https://www.dingtalk.com/"}]
    actioncard3 = ActionCard(title='万万没想到，竟然...',
                             text='![选择](http://www.songshan.es/wp-content/uploads/2016/01/Yin-Yang.png) \n### 故事是这样子的...',
                             btns=btns3,
                             btn_orientation=1,
                             hide_avatar=1)
xiaoding.send_action_card(actioncard3)
```

更多使用场景，尽情展开想象吧...
（如果对你有帮助的话，欢迎  > [**star**](https://github.com/zhuifengshen/DingtalkChatbot) <）
