---
layout: post
title: "The iOS Apprentice 2: Checklists - That’s a wrap!"
description: ""
category: raywinderlich
tags: [raywinderlich]
---
{% include JB/setup %}

## That’s a wrap! 收工
---

Things should be starting to make sense by now. I’ve thrown you into the deep end by writing an entire app from scratch, and we’ve touched on a number of advanced topics already, but hopefully you were able to follow along quite well with what I’m doing. Kudos for sticking with it until the end!

现在应该开始有意义的东西。我把你扔到从头编写整个应用程序,我们已经谈到了一些高级主题,但是希望您能够跟随很好,清楚知道我在做什么。坚持不懈，直到成功!

It’s OK if you’re still a bit fuzzy on the details. Sleep on it for a bit and keep tinkering with the code. Programming requires its own way of thinking and you won’t learn that overnight. Don’t be afraid to do this tutorial again from the start – it will make more sense the second time around!

如果你仍然有点模糊的细节，没关系。睡上一会儿,保持修补代码。编程需要自己的思维方式,你不会在一夜之间明白。不要害怕再次从头开始做这个教程，重复，再三会更有意义!

This lesson focused mainly on UIKit and its most important controls and patterns. In the next lesson we’ll take a few steps back to talk more about the Swift language itself and of course you’ll build another cool app.

这节课主要关注UIKit和最重要的控制和模式。在接下来的课我们后退几步并进一步讨论您将构建的Swift语言本身,当然另外一个很酷的应用程序。

Here is the final storyboard for Checklists:

这是清单应用最终版的故事板:

![12-1](/assets/images/The_iOS_Apprentice/Checklists/12-1.png)

I had trouble fitting that on my screen!

我遇到了麻烦,在我的屏幕上!

Take a well-deserved break, and when you’re ready continue on to the next tutorial, where you’ll make an app that knows its place! !

当之无愧要休息,当你准备好继续到下一个教程中,你将开发一个新应用程序,知道它的位置!!

Haven’t had enough yet? Here are some challenges to sink your teeth into:

还没够?这里有一些挑战:

Exercise: Put the due date in a label on the table view cells under the text of the to-do item.

练习:把截止日期在一个标签表视图Cell的文本下的待办事项

Exercise: Sort the to-do items list based on the due date. This is similar to what you did with the list of Checklists except that now you’re sorting ChecklistItem objects and you’ll be comparing NSDate objects instead of strings. (NSDate does not have a localizedStandardCompare method but it does have a regular compare).

练习:待办事项列表排序基于截止日期。这类似于你的列表清单所做的只是现在你排序ChecklistItem对象,你会比较NSDate对象而不是字符串。(NSDate没有localizedStandardCompare方法但它确实有一个常规的比较)。