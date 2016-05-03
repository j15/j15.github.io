---
layout: post
title: "add new photos to the Simulator 向模拟器中加入新照片"
description: "Simulator"
category: 
tags: [Simulator]
---
{% include JB/setup %}

## add new photos to the Simulator 向模拟器中加入新照片
---

There are several ways you can add new photos to the *Simulator*.

有几种不同的方法。

You can go into *Safari* (on the Simulator) and search the internet for an image. Then press down on the image until a menu appears and choose Save Image:

第一种在 *Safari* 中用网址打开图片保存。

![saveToSimulator](/assets/images/The_iOS_Apprentice/MyLocations/saveToSimulator.png)

Instead of surfing the internet for images, you can also simply drop an image file on top of the Simulator window. This adds the picture to your library in the Photos app.

第二种直接拖拽。

![dropToSimulator](/assets/images/The_iOS_Apprentice/MyLocations/dropToSimulator.gif)

Finally, you can use the Terminal and the *simctl* command. Type all on one line:

第三种用命令行工具 *simctl*。

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl addphoto booted ~/Desktop/iOSDevLog.png
```
