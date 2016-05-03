---
layout: post
title: "The iOS Apprentice 2: Checklists 02 - Array"
description: ""
category: raywinderlich
tags: [raywinderlich]
---
{% include JB/setup %}

这一期主要分以下几点 

1. 添加上期忘记内容 
1. 数组介绍 
1. 创建Model Class 
1. 使用数组 
1. 重构，使代码更简洁清晰 
1. 忘记了替换 items.count

# Array
---

![2-1](/assets/images/The_iOS_Apprentice/Checklists/2-1.png)

![2-2](/assets/images/The_iOS_Apprentice/Checklists/2-2.png)

## Class
---

{% highlight swift %}
class ChecklistItem {
    var text = ""
    var checked = false
}
{% endhighlight %}

## Syntax
---

{% highlight swift %}
var items = [ChecklistItem]()
{% endhighlight %}

is **shorter** for

{% highlight swift %}
var items: [ChecklistItem]

required init?(coder aDecoder: NSCoder) {
    items = [ChecklistItem]()

    // ...
    super.init(coder: aDecoder)
}
{% endhighlight %}

## 


