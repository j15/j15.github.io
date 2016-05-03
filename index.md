---
layout: page
title: iOSDevLog!
tagline:
author: iOSDevLog
---
{% include JB/setup %}

# Welcome to [iOSDevLog](http://iosdevlog.com) Official Home Page!

# 欢迎来到 [iOS 开发日志](http://iosdevlog.com) 官方网站
---

历经九九八十一期的磨难，我的播客 《iOS 开发日志》 终于成功登上了 苹果的 Podcast。

<https://itunes.apple.com/cn/podcast/ios-kai-fa-ri-zhi/id1039229999?mt=2>

喜欢的朋友欢迎订阅噢!

gihub 仓库地址:

<https://github.com/iOSDevLog/iOSDevLog>

视频地址:

<http://pan.baidu.com/s/1pJ5qjwR>

WWDC 中文字幕:

<https://github.com/iOSDevLog/wwdc_chinese>

---

# 博客
---

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
