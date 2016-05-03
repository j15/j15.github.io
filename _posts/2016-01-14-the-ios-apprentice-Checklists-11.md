---
layout: post
title: "The iOS Apprentice 2: Checklists 11 - Local Notifications"
description: ""
category: raywinderlich
tags: [raywinderlich]
---
{% include JB/setup %}

## Extra feature: local notifications 额外的功能:本地通知
---

I hope you’re still with me! We have discussed in great detail view controllers, navigation controllers, storyboards, segues, table views and cells, and the data model.

我希望你还是和我在一起!我们已经讨论了详细视图控制器、导航控制器,故事板,segue,表视图和Cell,和数据模型。

These are all essential topics to master if you want to build iOS apps because almost every app uses these building blocks.

如果你想建立iOS应用程序,这些都是最主要最基本主题.因为几乎每一个应用程序都使用这些构建块。

In this section you’re going to expand the app to add a new feature: local notifications. A local notification allows the app to schedule a reminder to the user that will be displayed even when the app is not running.

在本节中,您将扩展应用程序添加一个新特性:本地通知。本地通知允许应用程序即使没有运行也可以安排提醒用户。

You will add a “due date” field to the ChecklistItem object and then remind the user about this deadline with a local notification.

您将添加一个“截止日期”字段到ChecklistItem对象然后通过本地通知提醒用户这个最后期限。

If this sounds like fun, then keep reading. :-)

如果这听起来很有趣,然后继续阅读。:-)

The steps for this section are as follows:

这一节的步骤如下:

* Try out a local notification just to see how it works
* 尝试本地通知,看看它是如何工作的
* Allow the user to pick a due date for to-do items.
* 允许用户选择在待办事项中选择一个日期。
* Create a date picker control.
* 创建一个日期选择器控制。
* Schedule local notifications for the to-do items, and update them when the user changes the due date.
* 安排本地通知要做的项目,当用户更改和更新他们的截止日期。

Before you wonder about how to integrate this in the app, let’s just schedule a local notification and see what happens.

之前你想知道如何集成这个应用,我们就安排本地通知,看看会发生什么。

By the way, local notifications are different from push notifications. Push allows your app to receive messages about external events, such as your favorite team winning the World Series.

顺便说一下,本地通知不同于推送通知。不推送通知允许应用程序接收消息关于外部事件,比如你最喜欢的团队赢得了世界系列赛。

Local notifications are more similar to an alarm clock: you set a specific time and then it “beeps”.

本地通知更类似于闹钟:你设定一个具体的时间,然后它“beeps”响。

As of iOS 8, an app is only allowed to show local notifications after it has asked the user for permission. If the user denies permission, then any local notifications for your app simply won’t appear. You only need to ask for permission once, so let’s do that first.

iOS 8,应用程序只允许要求获取用户许可后才能显示本地通知。如果用户拒绝权限,那么任何本地应用程序的通知根本不会出现。你只需要请求批准一次,所以让我们先这样做。

> Open *AppDelegate.swift* and add the following code to the method **application(didFinishLaunchingWithOptions)**, just before the return true line:

{% highlight swift %}
let notificationSettings = UIUserNotificationSettings(forTypes: [.Alert, .Sound], categories: nil)
UIApplication.sharedApplication().registerUserNotificationSettings( notificationSettings)
{% endhighlight %}

Recall that **application(didFinishLaunchingWithOptions)** is called when the app starts up. It is the entry point for the app, the first place in the code where you can do something after the app launches.

回想一下,**application(didFinishLaunchingWithOptions)**在应用程序启动时被调用。它是程序的入口点,这段代码中,你可以做一些应用程序启动。

Because you’re just playing with these local notifications now, this is a good place to ask for permission.

因为你只是测试本地通知,这是一个好地方获取许可。

You tell iOS that the app wishes to send notifications of type “alert” with a sound effect. Later you’ll put this code into a more appropriate place.

你告诉iOS应用程序希望发送通知类型的“警报”声音效果。以后你会把这段代码放到一个更合适的地方。

    *Things that start with a dot*

    Throughout the app you’ve seen things like .None, .Checkmark, and .Default – and now .Alert and .Sound. These are enumeration symbols.
    
    An enumeration, or enum for short, is a data type that consists of a list of possible symbols and their values.
    
    For example, the UIUserNotificationType enum contains the symbols:

    .None
    .Badge
    .Sound
    .Alert

    You can combine these names in an array to define what sort of notifications the app will show to the user. Here you’ve chosen the combination of an alert and a sound effect by writing [.Alert, .Sound].

    It’s easy to spot when an enum is being used because of the dot in front of the symbol name. This is actually shorthand notation; you could also have written it like this:

    let notificationSettings = UIUserNotificationSettings(forTypes: [UIUserNotificationType.Alert, UIUserNotificationType.Sound], categories: nil)

    Fortunately, Swift is smart enough to realize that .Alert and .Sound are from the enum UIUserNotificationType, so you can save yourself some keystrokes.

> Run the app. You should immediately get a popup asking for permission:

![11-1](/assets/images/The_iOS_Apprentice/Checklists/11-1.png)

Tap *OK*. The next time you run the app you won’t be asked again; iOS remembers what you chose.

点击 *OK*。下次你再次运行应用程序你不会再次询问,iOS会记得你选择什么。

(If you tapped Don’t Allow – naughty! – then you can always reset the Simulator to get the permissions dialog again. You can also change the notification options in the Settings app.)

(如果你利用不允许 --- :( 那么你必需重置模拟器获才能再次显示权限对话框。您还可以更改通知选项在设置应用程序。)

> Stop the app and add the following code to **didFinishLaunchingWithOptions**:

{% highlight swift %}
let date = NSDate(timeIntervalSinceNow: 10)
let localNotification = UILocalNotification()
localNotification.fireDate = date
localNotification.timeZone = NSTimeZone.defaultTimeZone()
localNotification.alertBody = "I am a local notification!"
localNotification.soundName = UILocalNotificationDefaultSoundName
UIApplication.sharedApplication().scheduleLocalNotification(localNotification)
{% endhighlight %}

This creates a new local notification. It will fire exactly 10 seconds after the app has started.

这将创建一个新的本地通知。它将在应用已经开始10秒后触发。

A local notification is scheduled in the future using an NSDate object, which specifies a certain date and time. You use the timeIntervalSinceNow initializer to create an NSDate object that points at a time 10 seconds into the future.

When you create the UILocalNotification object you give it the NSDate object as its “fire date”. You also set the time zone, so the system automatically adjusts the fire date when the device travels across different time zones (for you frequent flyers).

你也设置时区,所以在不同的时区旅行时系统会自动调整设备日期(频繁飞行？)。

Local notifications can appear in different ways. Here you set a text so that an alert message will be shown when the notification fires. You also set a sound.

本地通知可以以不同的方式出现。你设置一个文本,以便将显示一个警告消息时,通知启动。你也设置一个声音。

Finally, you tell the UIApplication object to schedule the notification.


    A word on UIApplication
    
    You haven’t used this object before, but every app has one and it deals with application-wide functionality.
    
    You won’t directly use UIApplication a lot, except for special features such as local notifications.
    
    The app also provides a delegate object for UIApplication to handle messages concerning the app as a whole, such as applicationDidEnterBackground() that you’ve seen earlier and application(didFinishLaunchingWithOptions) that you’re using now.
    
    In this app, the delegate for UIApplication is the – aptly named – AppDelegate object. Every app needs one and the Xcode project templates always include an app delegate object ready-to-go.

> Add the following method to *AppDelegate.swift*:

{% highlight swift %}
func application(application: UIApplication, didReceiveLocalNotification notification: UILocalNotification) {
    print("didReceiveLocalNotification \(notification)")
}
{% endhighlight %}


This method will be invoked when the local notification is posted and the app is still running or in a suspended state in the background.

这个方法时将应用程序仍在运行或在后台挂起状态调用本地通知。

You won’t do anything here except log a message to the debug pane. Depending on the type of app it may make sense to react to the notification, for example to show a message to the user or to refresh the screen.

你不会做任何事除了在消息调试面板打印日志。取决于应用程序的类型通知也许是有意义的反应,例如向用户显示一条消息或刷新屏幕。

> Run the app. Immediately after it has started, exit to the home screen (use the *Hardware → Home* menu item on the Simulator).

Wait 10 seconds... I know, it seems like an eternity! After an agonizing 10 seconds a message should pop up in Notification Center:

等待10秒……我知道,这似乎是一个永恒!一个痛苦的10秒后消息应该出现在通知中心:

![11-2](/assets/images/The_iOS_Apprentice/Checklists/11-2.png)

> Tap the notification to go back to the app.

As the app opens, the debug area shows that didReceiveLocalNotification() is called with the notification object. It displays something like this:

{% highlight swift %}    
didReceiveLocalNotification <UIConcreteLocalNotification: 0x7fbabb513720>{fire date = Friday, July 18, 2015 at 8:25:57 PM Central European Summer Time, time zone = Europe/Amsterdam (GMT+2) offset 7200 (Daylight), repeat interval = 0, repeat count = UILocalNotificationInfiniteRepeatCount, next fire date = (null), user info = (null)}
{% endhighlight %}

Why did I want you to exit to the home screen? iOS will only show an alert with the notification message if the app is not currently active.

为什么我要你退出到主屏幕?如果应用没有目前活跃iOS将只显示一个警告通知消息。

> Stop the app and run it again. Now don’t press Home and just wait.

After 10 seconds you should see the “didReceiveLocalNotification” log message in the debug area but no alert is shown. (The notification does show up in Notification Center, though.)

When your app is active and in the foreground, it is supposed to handle any fired notifications in its own manner.

活跃和前台应用程序时,它应该以自己的方式处理任何通知。

All right, now you know that it works, you should restore AppDelegate.swift to its former state because you don’t really want to schedule a new notification every time the user starts the app.

好吧,现在你知道它的工作方式,你应该恢复AppDelegate。迅速到前的状态,因为你不想用户每次启动应用程序时安排一个新的通知。

> Remove all the local notification code from didFinishLaunchingWithOptions and change it back to the way it was.

You can keep the didReceiveLocalNotification() method, as it will come in handy when debugging the local notifications.

### Extending the data model 扩展数据模型
---

Let’s think about how the app will handle these notifications. Each ChecklistItem will get a due date field (an NSDate object) and a Bool that says whether the user wants to be reminded of this item or not.

让我们想想这个应用程序将如何处理这些通知。每个ChecklistItem将截止日期字段(NSDate对象)和一个Bool说明用户是否想起这个事项。

Users might not want to be reminded of everything, so you shouldn’t schedule local notifications for those items. Such a Bool is often called a flag. Let’s name it shouldRemind.

用户可能不希望被提醒所有事项,所以你不应该安排当地的通知这些事项。 这样一个Bool通常被称为一个标志。 我们的名字它shouldRemind。

You will add settings for these two new fields to the Add/Edit Item screen and make it look like this:

您将添加设置这两个新字段*Add/Edit*项屏幕,让它看起来像这样:

![11-3](/assets/images/The_iOS_Apprentice/Checklists/11-3.png)

The due date field will require some sort of date picker control. iOS comes with a cool date picker view that you’ll add into the table view.

截止日期字段需要某种形式的日期选择器控制。 iOS带有一个很酷的日期选择器视图中,您将添加到表格视图。

First, let’s figure out how and when to schedule the notifications. I can think of the following situations:

首先,让我们考虑如何以及何时安排通知。 我能想到的以下情况:

* When the user adds a new ChecklistItem object that has the shouldRemind flag set, you must schedule a new notification.
* 当用户添加了一个新的ChecklistItem对象设置了shouldRemind标志,必须安排一个新的通知。
* When the user changes the due date on an existing ChecklistItem, the old notification should be cancelled (if there is one) and a new one scheduled in its place (if shouldRemind is still set).
* 当用户更改现有ChecklistItem截止日期,旧的通知应被取消(如果有的话)和一个新的计划在其位置(如果shouldRemind仍设置)。
* When the user toggles the shouldRemind flag from on to off, the existing notification should be cancelled. The other way around, from off to on, should schedule a new notification.
* 当用户切换shouldRemind标志从开到关,现有的通知应被取消。 反过来,从关到开,应该安排一个新的通知。
* When the user deletes a ChecklistItem, its notification should be cancelled if it had one.
* 当用户删除ChecklistItem,应该取消了,如果有一个通知。
* When the user deletes an entire Checklist, all the notifications for those items should be cancelled.
* 当用户删除一个完整的清单,这些事项的所有通知应被取消。

This list makes it obvious that you don’t need just a way to schedule new notifications but also a way to cancel them.

这个列表是显而易见的,你不需要来安排新的通知也取消它们的方法。

You should probably also check that you don’t create notifications for to-do items whose due dates are in the past. I’m sure iOS is smart enough to ignore those notifications, but let’s be good citizens anyway.

您还应该检查你不创建待办事项的通知项在过去的截止日期。 我相信iOS是足够聪明来忽略这些通知,让我们成为好公民。

UIApplication has a method cancelLocalNotification() that allows you to cancel a notification that was previously scheduled. That method requires you to give it a UILocalNotification object. Somehow you must associate the ChecklistItem object with a UILocalNotification in order to cancel that notification.

UIApplication有cancelLocalNotification()方法,允许您取消原定的通知。 这种方法需要你给它一个UILocalNotification对象。 你必须把ChecklistItem对象与UILocalNotification为了取消通知。

It is tempting to put the UILocalNotification object as a variable in ChecklistItem, so you always know what it is, but imagine what happens when the app goes to the background. You save the ChecklistItem object to the Checklists.plist file – but what about the UILocalNotification object?

人们很容易把UILocalNotification ChecklistItem对象作为一个变量,所以你总是知道它是什么,但是想象一下当应用背景。 你将ChecklistItem对象保存到Checklists.plist文件——但UILocalNotification对象呢?

As it happens, the UILocalNotification conforms to the NSCoding protocol so you could serialize it along with the ChecklistItem object into the plist file. However, that is asking for trouble.

碰巧,UILocalNotification符合NSCoding协议你可以序列化它随着Checklists.plist文件中。 然而,这是自找麻烦。

These UILocalNotification objects are owned by the operating system, not by your app. When the app starts again, it is very well possible that iOS uses different objects to represent the same notifications. You cannot unfreeze these objects from the plist file and expect iOS to recognize them.

这些UILocalNotification对象属于操作系统,而不是应用程序。在应用程序启动时,很有可能iOS使用不同的对象来表示相同的通知。 你不能解冻这些对象从plist文件并期望iOS认出他们来。

So let’s not store the UILocalNotification objects directly.

所以我们不要存储UILocalNotification直接对象。

What will work better is to give the UILocalNotification a reference to the associated ChecklistItem. Each local notification has a dictionary named userInfo that you can use to store your own values.

给UILocalNotification 关联到 ChecklistItem 的引用将更好地工作。 每个当地通知字典命名存储用户信息,您可以使用您自己的值。

You will not use this dictionary to store the ChecklistItem object itself, for the same reason as above: when the app closes and later starts again, it will get new ChecklistItem objects. Even though they look and behave exactly the same as the old ChecklistItems (because you froze and unfroze them), they are likely to be placed elsewhere in memory and the references inside the UILocalNotifications will be broken.

你不会使用这本字典存储ChecklistItem对象本身,出于同样的原因,如上图:当应用程序关闭后重新开始,它将获得新的ChecklistItem对象。 即使他们的外观和行为一模一样老ChecklistItems(因为你冻结,解冻他们),他们可能会被放置在内存和UILocalNotifications内部的引用将被打破。

Instead of direct references, you will use a numeric identifier. You will give each ChecklistItem object a unique numeric ID. Assigning numeric IDs to objects is a common approach when creating data models – it is very similar to giving records in a relational database a numeric primary key, if you’re familiar with that sort of thing.

而不是直接引用,您将使用一个数字标识符。 你会给每个ChecklistItem对象一个独特的数字ID。分配对象的数字ID是一个常见的方法在创建数据模型,它非常类似于给在关系数据库中记录一个数字主键,如果你熟悉之类的。

You’ll save this item ID in the Checklists.plist file and also store it in the userInfo dictionary of the UILocalNotification. Then you can easily find the notification when you have the ChecklistItem object, or the ChecklistItem object when you have the notification.

你会保存这个清单项ID。 plist文件并将其存储在用户信息UILocalNotification的字典。 然后你可以很容易的找到通知当你有ChecklistItem对象,或ChecklistItem对象时通知。

This will work even after the app has terminated and all the original objects have long been destroyed.

这将工作即使在程序终止,所有原来的对象一直被销毁。

> Make these changes to *ChecklistItem.swift*:


{% highlight swift %}
var dueDate = NSDate()
var shouldRemind = false
var itemID: Int
{% endhighlight %}

Note that you called it itemID and not simply “id”. The reason is that id is a special keyword in Objective-C, and this could cause trouble if you ever wanted to mix your Swift code with Objective-C code.

请注意,您不仅仅称之为itemID和“id”。 原因是id是一个特殊的关键字在Objective-C中,这可能导致麻烦如果你曾经想把 Swift 代码和Objective-C代码。

The dueDate and shouldRemind variables have initial values, but itemID does not. That’s why you had to specify the type for itemID – it’s an Int – but not for the other two variables.

dueDate和shouldRemind变量初始值,但itemID没有。 这就是为什么你必须指定itemID——这是Int类型——但不是其他两个变量。

Swift is smart enough to infer that dueDate cannot be anything but an NSDate, and that shouldRemind should be a Bool.

Swift 是足够聪明来推断dueDate除了NSData，不能是任何东西,shouldRemind应该是一个布尔值。

You have to extend init?(coder) and encodeWithCoder() in order to be able to load and save these new properties along with the ChecklistItem objects.

你应该扩展 **init?(coder)** 和 **encodeWithCoder()**为了能够加载和保存这些新的属性随着ChecklistItem对象。

> Change these methods in *ChecklistItem.swift*:

{% highlight swift %}
required init?(coder aDecoder: NSCoder) {
    text = aDecoder.decodeObjectForKey("Text") as! String 
    checked = aDecoder.decodeBoolForKey("Checked")

    /* new */
    dueDate = aDecoder.decodeObjectForKey("DueDate") as! NSDate
    shouldRemind = aDecoder.decodeBoolForKey("ShouldRemind")
    itemID = aDecoder.decodeIntegerForKey("ItemID")
    /* end */

    super.init()
}
{% endhighlight %}

{% highlight swift %}
func encodeWithCoder(aCoder: NSCoder) {
    aCoder.encodeObject(text, forKey: "Text")
    aCoder.encodeBool(checked, forKey: "Checked")

    /* new */
    aCoder.encodeObject(dueDate, forKey: "DueDate")
    aCoder.encodeBool(shouldRemind, forKey: "ShouldRemind")
    aCoder.encodeInteger(itemID, forKey: "ItemID")
    /* end */
}
{% endhighlight %}

For dueDate you call decodeObjectForKey() and encodeObject(), but for shouldRemind it is decode/encodeBool() and for itemID it is decode/encodeInteger(). Why do you need different methods to encode and decode these things?

This is necessary because the NSCoder system is written in Objective-C and that language makes a distinction between primitive types and objects.

这是必要的,因为NSCoder系统在Objective-C语言编写原始类型和对象之间的区别。

In Objective-C, Int, Float, and Bool are primitive types. Everything else, such as String and NSDate, is an object. That is different from Swift, which basically treats everything as an object. But because you’re talking to an Objective-C framework here, you need to play by the rules of Objective-C.

Great, that takes care of saving and loading existing objects.

Xcode has spotted one remaining error: init() still needs to give itemID a value. That makes sense: you also have to assign an ID to new ChecklistItem objects, which happens in init().

Xcode 提醒出错

> Make the following changes to **init()**:

{% highlight swift %}
override init() {
    /* new */
    itemID = DataModel.nextChecklistItemID()
    /* end */
    
    super.init()
}
{% endhighlight %}

This asks the DataModel object for a new item ID whenever the app creates a new ChecklistItem object.

Now let’s add this new nextChecklistItemID() method to DataModel. As you can guess from its name this method will return a new, unique ID every time you call it.

> Hop on over to *DataModel.swift* and add this new method:

{% highlight swift %}
class func nextChecklistItemID() -> Int {
    let userDefaults = NSUserDefaults.standardUserDefaults()
    let itemID = userDefaults.integerForKey("ChecklistItemID") userDefaults.setInteger(itemID + 1, forKey: "ChecklistItemID")
    userDefaults.synchronize()

    return itemID
}
{% endhighlight %}

You’re using your old friend NSUserDefaults again.

你再次使用你的老朋友NSUserDefaults。

This method gets the current “ChecklistItemID” value from NSUserDefaults, adds one to it, and writes it back to NSUserDefaults. It returns the previous value to the caller.

The method also does userDefaults.synchronize() to force NSUserDefaults to write these changes to disk immediately, so they won’t get lost if you kill the app from Xcode before it had a chance to save.

This is important because you never want two or more ChecklistItems to get the same ID.

这是很重要的,因为你永远不希望两个或两个以上的ChecklistItems得到相同的ID。

> Add a default value for “ChecklistItemID” to the registerDefaults() method (note the added comma after “FirstTime”):

{% highlight swift %}
func registerDefaults() {
    let dictionary = [ "ChecklistIndex": -1,
                    "FirstTime": true,
                    /* new */
                    "ChecklistItemID": 0
                    /* end */
                    ]
    NSUserDefaults.standardUserDefaults().registerDefaults(dictionary)
}
{% endhighlight %}

The first time nextChecklistItemID() is called it will return the ID 0. The second time it is called it will return the ID 1, the third time it will return the ID 2, and so on. The number is incremented by one each time. You can call this method a few billion times before you run out of unique IDs.

第一次nextChecklistItemID()就会返回ID 0。 它被称为第二次它将返回ID 1,第三次它将返回ID 2,等等。 每次都增加了一个数量。 你可以调用这个方法几十亿次的惟一id。

For a quick test to see if assigning these IDs works, you can put them inside the text that is shown in the ChecklistItem cell label. This is just a temporary thing for testing purposes, as users couldn’t care less about the internal identifier of these objects.

一个快速测试看看分配这些id,您可以把它们在ChecklistItem Cell标签所示的文本。 这只是一个临时的测试目的,用户不关心这些对象的内部标识符。

> In *ChecklistViewController.swift*, change the **configureTextForCell** method to:

{% highlight swift %}
func configureTextForCell(cell: UITableViewCell, withChecklistItem item: ChecklistItem) {
    let label = cell.viewWithTag(1000) as! UILabel
    
    //label.text = item.text
    /* new */
    label.text = "\(item.itemID): \(item.text)"
    /* end */
}
{% endhighlight %}

I have commented out the original line because you want to put that back later. The new one uses \( ... ) to add the to-do item’s itemID property into the text.

> Before you run the app, make sure to reset the Simulator first or throw away Checklists.plist from the app’s Documents directory.

You have changed the format of the Checklists.plist file again and reading an incompatible file may cause weird crashes.

你改变了清单的格式。 plist文件再读一个不兼容的文件可能会引起奇怪的崩溃。

> Run the app and add some checklist items. Each new item should get a unique identifier. Exit to the home screen (to make sure everything is saved properly) and stop the app.

Run the app again and add some new items; the IDs for these new items should start counting at where they left off.

再次运行应用程序,并添加一些新项目,这些新项目的id应该开始计数在他们离开的地方。

OK, that takes care of the IDs. Now lets add the “due date” and “should remind” fields to the Add/Edit Item screen.

(Keep configureTextForCell() the way it is for the time being; that will come in handy with testing the notifications.)

> Add the following outlets to *ItemDetailViewController.swift*:

![11-4](/assets/images/The_iOS_Apprentice/Checklists/11-4.png)

{% highlight swift %}
@IBOutlet weak var shouldRemindSwitch: UISwitch!
@IBOutlet weak var dueDateLabel: UILabel!
{% endhighlight %}

> Open the storyboard and select the Table View in the Item Detail View Controller (the one that says “Add Item”).

> Add a new section to the table. The easiest way to do this is to increment the Sections field in the Attributes inspector. This duplicates the existing section and cell.

> Remove the Text Field from the new cell. Drag a new Table View Cell from the Object Library and drop it below this one, so that the second section has two rows.

You will now design the new cells to look as follows:

你现在将设计新的Cell看起来如下:

![11-5](/assets/images/The_iOS_Apprentice/Checklists/11-5.png)

> Add a Label to the first cell and give it the text Remind Me. Set the font to System, size 16.

> Also drag a Switch control into the cell. Hook it up to the shouldRemindSwitch outlet on the view controller. In the Attributes inspector, set its State to Off so it is no longer green.

> Pin the Switch to the top and right edges of the table view cell. This makes sure the control will be visible regardless of the width of the device’s screen.

> The third cell has two labels: Due Date on the left and the label that will hold the actual chosen date on the right. You don’t have to add these labels yourself: simply set the Style of the cell to Right Detail and rename Title to Due Date.

> The label on the right should be hooked up to the dueDateLabel outlet. (You may need to click it a few times before it is selected and you can make the connection.)

You may need to move the Remind Me label and the switch around a bit to align them nicely with the labels from the “due date” cell. Tip: select the “Due Date” and “Detail” labels and look in the Size inspector what their margins are (should be 15 points from the edges).

您可能需要移动提醒我标签和开关有点使他们很好地与标签的“截止” Cell。 提示:选择“截止日期”和“详情”的标签,大小检查器看看他们的边缘(应该从边缘15点)。

Let’s write the code for this.

> Add a new dueDate instance variable to *ItemDetailViewController.swift*:

> 添加一个新的dueDate实例变量 ItemDetailViewController.swift :

{% highlight swift %}
var dueDate = NSDate()
{% endhighlight %}

For a new ChecklistItem item, the due date is right now, i.e. NSDate(). That sounds reasonable but by the time the user has filled in the rest of the fields and pressed Done, that due date will be in the past.

对于一个新的ChecklistItem项,截止日期就是现在，例如NSDate()。 这听起来合理,但当用户填写其余的字段并点击“完成”,截止日期将在过去。

But you do have to suggest something here. An alternative default value could be this time tomorrow, or ten minutes from now, but in most cases the user will have to pick their own due date anyway.

但是你必须表明一些东西。 另一个默认值可以明天这个时候,或从现在开始的十分钟,但在大多数情况下,用户必须选择自己的截止日期。

> Change **viewDidLoad()** to the following:

{% highlight swift %}
override func viewDidLoad() {
    super.viewDidLoad()
    
    if let item = itemToEdit {
        title = "Edit Item"
        textField.text = item.text
        doneBarButton.enabled = true

        /* new */
        shouldRemindSwitch.on = item.shouldRemind
        dueDate = item.dueDate
        /* end */
    }
    /* new */
    updateDueDateLabel()
    /* end */
}
{% endhighlight %}

If there already is an existing ChecklistItem object, you set the switch control to on or off, depending on the value of the object’s shouldRemind property. If the user is adding a new item, the switch is initially off (you did that in the storyboard).

如果已经有一个现有的ChecklistItem对象,设置打开或关闭的开关控制,根据对象的shouldRemind属性值。 如果用户添加一个新条目,切换最初状态(你在故事板中设置过)。

You also get the due date from the ChecklistItem.

你也得到ChecklistItem的截止日期。

> The updateDueDateLabel() method is new. Add it to the file:

> updateDueDateLabel()方法是新的,将其添加到文件:

{% highlight swift %}
func updateDueDateLabel() {
    let formatter = NSDateFormatter()
    formatter.dateStyle = .MediumStyle
    formatter.timeStyle = .ShortStyle
    dueDateLabel.text = formatter.stringFromDate(dueDate)
}
{% endhighlight %}

To convert the NSDate value to text, you use the NSDateFormatter object.

为了把NSDate值转换为文本,您使用NSDateFormatter对象。

The way it works is very straightforward: you give it a style for the date component and a separate style for the time component, and then ask it to format the NSDate object.

它的工作方式非常简单:你给它一个日期组件和一个单独的风格的风格的时间组件,然后问它NSDate对象格式。

You can play with different styles here but space in the label is limited so you can’t fit in the full month name, for example.

你可以在这里玩不同风格，但标签空间是有限的所以你不能适应整个月的名字,例如。

The cool thing about NSDateFormatter is that it takes the current locale into consideration so the time will look good to the user no matter where she is on the globe.

NSDateFormatter最酷的地方是,无论她在地球上什么地方使当前语言环境的时间看起来不错。

> The last thing to change in this file is the done() action. Change it to:

> 最后一件事就是改变在这个文件的 done() 操作。 把它改成:

{% highlight swift %}
@IBAction func done() {
    if let item = itemToEdit {
        item.text = textField.text!

        /* new */ 
        item.shouldRemind = shouldRemindSwitch.on
        item.dueDate = dueDate
        /* end */

        delegate?.itemDetailViewController(self, didFinishEditingItem: item)
    } else {
        let item = ChecklistItem()
        item.text = textField.text!
        item.checked = false

        /* new */ 
        item.shouldRemind = shouldRemindSwitch.on
        item.dueDate = dueDate
        /* end */
        
        delegate?.itemDetailViewController(self, didFinishAddingItem: item)
    }
}
{% endhighlight %}

    Note: Maybe you’re wondering why you’re using an instance variable for the dueDate but not for shouldRemind.
    You don’t need one for shouldRemind because it’s easy to get the state of the switch control: you just look at its on property, which is either true or false.
    However, it is hard to read the chosen date back out of the dueDateLabel because the label stores text (a String), not an NSDate. So it’s easier to keep track of the chosen date separately in an NSDate instance variable.

Here you put the value of the switch control and the dueDate instance variable back into the ChecklistItem object when the user presses the Done button.

当用户按下“完成”按钮时，在这里你把开关控制和dueDate实例变量的值返回ChecklistItem对象

> Run the app and change the position of the switch control. The app will remember this setting when you terminate it (but be sure to exit to the home screen first).

> 运行应用程序并改变开关控制的位置。 应用程序会记得这个设置终止时间(但一定要退出到主屏幕)。

The due date row doesn’t really do anything yet, however. In order to make that work, you first have to create a date picker.

截止日期行实际上并没有做任何事,所以，为了使这项工作,首先必须创建一个日期选择。

### The date picker 日期选择器
--- 

The date picker is not a new view controller. Tapping the Due Date row will insert a new UIDatePicker component directly into the table view, just like what happens in the built-in Calendar app.

![11-6](/assets/images/The_iOS_Apprentice/Checklists/11-6.png)

> Add a new instance variable to ItemDetailViewController.swift, to keep track of whether the date picker is currently visible:

{% highlight swift %}
var datePickerVisible = false
{% endhighlight %}

> And add the showDatePicker() method:  

{% highlight swift %}
func showDatePicker() {
    datePickerVisible = true
    let indexPathDatePicker = NSIndexPath(forRow: 2, inSection: 1)
    
    tableView.insertRowsAtIndexPaths([indexPathDatePicker], withRowAnimation: .Fade)
}
{% endhighlight %}

This sets the new instance variable to true, and tells the table view to insert a new row below the Due Date cell. This new row will contain the UIDatePicker.

The question is: where does the cell for this new date picker row come from? You can’t put it into the table view as a static cell already because then it would always be visible. You only want to show it after the user taps the Due Date row.

Xcode 7 has a cool new feature that lets you add additional views to a scene that are not immediately visible. That’s a great solution to this problem!

> Open the storyboard and go to the Add Item scene. From the Object Library, pick up a new Table View Cell. Don’t drag it into the view controller itself but into the scene dock at the top:

![11-7](/assets/images/The_iOS_Apprentice/Checklists/11-7.png)

After dragging, the storyboard should look like this:

![11-8](/assets/images/The_iOS_Apprentice/Checklists/11-8.png)

The new Table View Cell object belongs to the scene but it is not (yet) part of the scene’s table view.

The cell is a bit too small to fit a date picker, so first you’ll make it a bit bigger.

> Select the Table View Cell and in the Size inspector set the Height to 217. The date picker is 216 points tall, plus one point for the separator line at the bottom of the cell.

> In the Attributes inspector, set Selection to None so this cell won’t turn gray when you tap on it.

> From the Object Library, drag a Date Picker into the cell. It should fit exactly.

> Use the Pin menu to glue the Date Picker to the four sides of the cell. Turn off Constrain to margins and then select the four bars to make them red (they all should be 0).

When you’re done, the new cell looks like this:

![11-9](/assets/images/The_iOS_Apprentice/Checklists/11-9.png)

So how do you get this cell into the table view? First, make two new outlets and connect them to the cell and the date picker, respectively. That way you can refer to these views from code.

> Add these lines to ItemDetailViewController.swift:

Back in the storyboard, take a look at that scene dock again. Besides an icon for the table view cell you just added it also has a round yellow icon. This represents the view controller.

> To connect the outlet, simply Ctrl-drag from that yellow icon to the icon for the Table View Cell, and select the datePickerCell outlet:

![11-10](/assets/images/The_iOS_Apprentice/Checklists/11-10.png)

> To connect the date picker, Ctrl-drag from the yellow icon to the big blue Date Picker above it and select the datePicker outlet.

Great! Now that you have outlets for the cell and the date picker inside it, you can write the code to add them to the table view.

Normally you would implement the tableView(cellForRowAtIndexPath) method, but remember that this screen uses a table view with static cells. Such a table view does not have a data source and therefore does not use cellForRowAtIndexPath.

If you look in ItemDetailViewController.swift you won’t find that method anywhere. However, with a bit of trickery you can override the data source for a static table view and provide your own methods.

> Add the tableView(cellForRowAtIndexPath) method:

{% highlight swift %}
override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    if indexPath.section == 1 && indexPath.row == 2 {
        return datePickerCell
    } else {
        return super.tableView(tableView, cellForRowAtIndexPath: indexPath)
    } 
}
{% endhighlight %}

Danger: You shouldn’t really mess around too much with this method when it’s being used by a static table view, because it may interfere with the inner workings of those static cells. But if you’re careful you can get away with it.

The if-statement checks whether cellForRowAtIndexPath is being called with the index-path for the date picker row. If so, it returns the new datePickerCell that you just designed. This is safe to do because the table view from the storyboard doesn’t know anything about row 2 in section 1, so you’re not interfering with an existing static cell.

For any index-paths that are not the date picker cell, this method will call through to super (which is UITableViewController). This is the trick that makes sure the other static cells still work.

> You also need to override tableView(numberOfRowsInSection):

{% highlight swift %}
override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    if section == 1 && datePickerVisible {
        return 3
    } else {
        return super.tableView(tableView, numberOfRowsInSection: section)
    }
}
{% endhighlight %}

If the date picker is visible, then section 1 has three rows. If the date picker isn’t visible, you can simply pass through to the original data source.

> Likewise, you also need to provide the tableView(heightForRowAtIndexPath) method:

{% highlight swift %}
override func tableView(tableView: UITableView, heightForRowAtIndexPath indexPath: NSIndexPath) -> CGFloat {
    if indexPath.section == 1 && indexPath.row == 2 {
        return 217
    } else {
        return super.tableView(tableView, heightForRowAtIndexPath: indexPath)
    }
}
{% endhighlight %}

So far the cells in your table views all had the same height (44 points), but this is not a hard requirement. By providing the heightForRowAtIndexPath method you can give each cell its own height.

The UIDatePicker component is 216 points tall, plus 1 point for the separator line, making for a total row height of 217 points.

The date picker is only made visible after the user taps the Due Date cell, which happens in tableView(didSelectRowAtIndexPath).

> Add that method:

{% highlight swift %}
override func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
    tableView.deselectRowAtIndexPath(indexPath, animated: true)
    textField.resignFirstResponder()
    if indexPath.section == 1 && indexPath.row == 1 {
        showDatePicker()
    }
}
{% endhighlight %}

This calls showDatePicker() when the index-path indicates that the Due Date row was tapped. It also hides the on-screen keyboard if that was visible.

At this point you have most of the pieces in place, but the Due Date row isn’t actually tap-able yet. That’s because ItemDetailViewController.swift already has a willSelectRowAtIndexPath method that always returns nil, causing taps on all rows to be ignored.

> Change tableView(willSelectRowAtIndexPath) to:

{% highlight swift %}
override func tableView(tableView: UITableView, willSelectRowAtIndexPath indexPath: NSIndexPath) -> NSIndexPath? {
    if indexPath.section == 1 && indexPath.row == 1 {
        return indexPath
    } else {
        return nil
    }
}
{% endhighlight %}

Now the Due Date row responds to taps, but the other rows don’t.

> Run the app to try it out. Add a new checklist item and tap the Due Date row.

Whoops. The app crashes. After some investigating I found that when you override the data source for a static table view cell, you also need to provide the delegate method tableView(indentationLevelForRowAtIndexPath).

That’s not a method you’d typically use, but because you’re messing with the data source for a static table view you do need to override it. I told you this was a little tricky.

> Add the tableView(indentationLevelForRowAtIndexPath) method:

{% highlight swift %}
override func tableView(tableView: UITableView, var indentationLevelForRowAtIndexPath indexPath: NSIndexPath) -> Int {
    if indexPath.section == 1 && indexPath.row == 2 {
        indexPath = NSIndexPath(forRow: 0, inSection: indexPath.section)
    }
    
    return super.tableView(tableView, indentationLevelForRowAtIndexPath: indexPath)
}
{% endhighlight %}

The reason the app crashed on this method was that the standard data source doesn’t know anything about the cell at row 2 in section 1 (the one with the date picker), because that cell isn’t part of the table view’s design in the storyboard.

So after inserting the new date picker cell the data source gets confused and it crashes the app. To fix this, you have to trick the data source into believing there really are three rows in that section when the date picker is visible.

    Note: I snuck something new in here too. Notice how the second parameter of the method has the keyword var in front of it?
    In Swift, the parameters of a method are constants. You can read their values but you can’t put new values into these parameters.
    By declaring a parameter as var, however, the parameter acts as a local variable and you can assign it new values. Here that saves you from having to make a separate local variable to keep track of the new index path.

> Run the app again. This time the date picker cell shows up where it should:

![11-11](/assets/images/The_iOS_Apprentice/Checklists/11-11.png)

Interacting with the date picker should really change the date in the Due Date row but currently this has no effect (try it out: spin the wheels).

You have to listen to the date picker’s “Value Changed” event. That event gets sent whenever the wheels settle on a new value. For that, you need to add a new action method.

> Add the dateChanged() method to ItemDetailViewController.swift:

{% highlight swift %}
@IBAction func dateChanged(datePicker: UIDatePicker) {
    dueDate = datePicker.date
    updateDueDateLabel()
}
{% endhighlight %}

This is pretty simple. It updates the dueDate instance variable with the new date and then updates the text on the Due Date label.

> In the storyboard, Ctrl-drag from the Date Picker to the view controller and select the dateChanged: action method. Now everything is properly hooked up. (You can verify that the action method is indeed connected to the date picker’s

Value Changed event by looking at the Connections inspector.)

> Run the app to try it out. When you turn the wheels on the date picker, the text in the Due Date row updates too. Cool.

However, when you edit an existing to-do item, the date picker does not show the date from that item. It always starts on the current date and time.

> Add the following line to the bottom of showDatePicker():

{% highlight swift %}
datePicker.setDate(dueDate, animated: false)
{% endhighlight %}

This gives the proper date to the UIDatePicker component.

> Verify that it works: click on the (i) button from an existing to-do item, preferably one you made a while ago, and confirm that the date picker shows the same date and time as the Due Date label. Excellent!

Speaking of the label, it would be nice if this becomes highlighted when the date picker is active. You can use the tint color for this (that’s also what the Calendar app does).

> Change showDatePicker() one last time:

![11-12](/assets/images/The_iOS_Apprentice/Checklists/11-12.png)

![11-13](/assets/images/The_iOS_Apprentice/Checklists/11-13.png)

{% highlight swift %}
func showDatePicker() {
    datePickerVisible = true
    let indexPathDateRow = NSIndexPath(forRow: 1, inSection: 1) 
    let indexPathDatePicker = NSIndexPath(forRow: 2, inSection: 1)

    if let dateCell = tableView.cellForRowAtIndexPath(indexPathDateRow) {
        dateCell.detailTextLabel!.textColor =
    }

    tableView.beginUpdates() tableView.insertRowsAtIndexPaths([indexPathDatePicker], withRowAnimation: .Fade) 
    tableView.reloadRowsAtIndexPaths([indexPathDateRow], withRowAnimation: .None)
    tableView.endUpdates()

    datePicker.setDate(dueDate, animated: false)
}
{% endhighlight %}

This sets the textColor of the detailTextLabel to the tint color. It also tells the table view to reload the Due Date row. Without that, the separator lines between the cells don’t update properly.

Because you’re doing two operations on the table view at the same time – inserting a new row and reloading another – you need to put this in between calls to beginUpdates() and endUpdates(), so that the table view can animate everything at the same time.

> Run the app. The date now appears in blue:

![11-14](/assets/images/The_iOS_Apprentice/Checklists/11-14.png)

When the user taps the Due Date row again, the date picker should disappear. If you try that right now the app will crash – what did you expect! – which obviously won’t win it many favorable reviews.

> Add the new hideDatePicker() method:

{% highlight swift %}
func hideDatePicker() {
    if datePickerVisible {
        datePickerVisible = false
        let indexPathDateRow = NSIndexPath(forRow: 1, inSection: 1)
        let indexPathDatePicker = NSIndexPath(forRow: 2, inSection: 1)

        if let cell = tableView.cellForRowAtIndexPath(indexPathDateRow) {
            cell.detailTextLabel!.textColor = UIColor(white: 0, alpha: 0.5)
        }

        tableView.beginUpdates()
        tableView.reloadRowsAtIndexPaths([indexPathDateRow],     withRowAnimation: .None) 
        tableView.deleteRowsAtIndexPaths([indexPathDatePicker], withRowAnimation: .Fade)
        tableView.endUpdates()
    }
}
{% endhighlight %}

This does the opposite of showDatePicker(). It deletes the date picker cell from the table view and restores the color of the date label to medium gray.

> Change tableView(didSelectRowAtIndexPath) to toggle between the visible and hidden states:

{% highlight swift %}
override func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
    tableView.deselectRowAtIndexPath(indexPath, animated: true)
    textField.resignFirstResponder()

    if indexPath.section == 1 && indexPath.row == 1 {
        if !datePickerVisible {
            showDatePicker() }
    }
}
{% endhighlight %}

![11-15](/assets/images/The_iOS_Apprentice/Checklists/11-15.png)

There is another situation where it’s a good idea to hide the date picker: when the user taps inside the text field.

It won’t look very nice if the keyboard partially overlaps the date picker, so you might as well hide it. The view controller is already the delegate for the text field, making this easy.

> Add the textFieldDidBeginEditing() method:

{% highlight swift %}
func textFieldDidBeginEditing(textField: UITextField) {
    hideDatePicker()
}
{% endhighlight %}

And with that you have a cool inline date picker!

> Run the app and verify that hiding the date picker works, also when you activate the text field.

### Scheduling the local notifications 安排本地通知
--- 

One of the principles of object-oriented programming is that objects can do as much as possible themselves. Therefore, it makes sense that the ChecklistItem object can schedule its own notifications.

> Add the following method to ChecklistItem.swift:

{% highlight swift %}
func scheduleNotification() {
    if shouldRemind && dueDate.compare(NSDate()) != .OrderedAscending {
        print("We should schedule a notification!")
    }
}
{% endhighlight %}

This compares the due date on the item with the current date. You can always get the current time by making a new NSDate object with NSDate().

The statement dueDate.compare(NSDate()) compares the two dates and returns one of the following values from the NSComparisonResult enum:

* OrderedAscending: dueDate comes before the current date and time. In other words, it is in the past.
* OrderedSame: dueDate is exactly equal to the current date and time (Good luck trying to get this one! You’d have to time it down to the second.)
* OrderedDescending: dueDate represents a time after the current date and time, so it lies in the future.

If the due date is in the past, the print() will not be performed.

Note the use of the && “and” operator. You only print the text when the Remind Me switch is set to “on” and the due date is in the future.

You will call this method when the user presses the Done button after adding or editing a to-do item.

> Change the done() action in ItemDetailViewController.swift:

{% highlight swift %}
@IBAction func done() {
    if let item = itemToEdit {
        item.text = textField.text!
        item.shouldRemind = shouldRemindSwitch.on
        item.dueDate = dueDate
// new
        item.scheduleNotification()
// end
        delegate?.itemDetailViewController(self, didFinishEditingItem: item)
    } else {
        let item = ChecklistItem()
        item.text = textField.text!
        item.checked = false
        item.shouldRemind = shouldRemindSwitch.on
        item.dueDate = dueDate
// new
        item.scheduleNotification() 
// end
        delegate?.itemDetailViewController(self, didFinishAddingItem: item)
    }
}
{% endhighlight %}

Only the lines with item.scheduleNotification() are new.

> Run the app and try it out. Add a new item, set the switch to ON but don’t change the due date. Press Done.

There should be no message in the debug area because the due date has already passed (it is several seconds in the past by the time you press Done).

> Add another item, set the switch to ON, and choose a due date in the future. When you press Done now, there should be a print in the debug area (“We should schedule a notification!”).

Now that you’ve verified the method is called in the proper place, let’s actually schedule a new UILocalNotification object. First consider the case of a new to-do item being added.

> In ChecklistItem.swift, change scheduleNotification() to:

{% highlight swift %}
func scheduleNotification() {
    if shouldRemind && dueDate.compare(NSDate()) != .OrderedAscending {
        let localNotification = UILocalNotification() 
        localNotification.fireDate = dueDate
        localNotification.timeZone = NSTimeZone.defaultTimeZone() 
        localNotification.alertBody = text
        localNotification.soundName = 
        UILocalNotificationDefaultSoundName 
        localNotification.userInfo = ["ItemID": itemID]
        UIApplication.sharedApplication().scheduleLocalNotification(localNotification)
        print("Scheduled notification \(localNotification) for itemID \(itemID)")
    }
}
{% endhighlight %}

You’ve seen this code before when you tried out local notifications for the first time.

You create a UILocalNotification object and give it the ChecklistItem’s dueDate and text. You also add a userInfo dictionary with the item’s ID as the only contents. That is how you’ll be able to find this notification later in case you need to cancel it.

Xcode is less impressed with this new code and gives a bunch of error messages.

What is wrong here? UILocalNotification is an object provided by UIKit – you can tell by the “UI” in its name. However, ChecklistItem hasn’t used any other UIKit code until now.

The only framework objects it has used, NSCoder and NSDate, came from another framework, Foundation.

> To tell ChecklistItem about UIKit, you need to add the following line to the top of the file, below the other import:

{% highlight swift %}
import UIKit
{% endhighlight %}

Now the errors disappear like snow in the sun.

There’s another small problem, though. If you’ve reset the Simulator recently (and you probably have when you made the most recent data model changes) then the app no longer has permission to send local notifications.

> Try it out. Run the app, add a new checklist item, set the due date a minute into the future, and press Done.

The debug pane should print out an error:

{% highlight swift %}
Attempting to schedule a local notification <UIConcreteLocalNotification ...
with an alert but haven't received permission from the user to display alerts
{% endhighlight %}

Even if you didn’t get this error, you can’t assume the app has permission anymore.

When you were just messing around at this beginning of this section, you placed the permission code in the AppDelegate and ran it immediately upon launch. That’s not recommended.

Don’t you just hate those apps that prompt you for ten different things before you’ve even had a chance to properly look at them? Let’s be a bit more user friendly with our own app!

> Add the following method to ItemDetailViewController.swift:

{% highlight swift %}
@IBAction func shouldRemindToggled(switchControl: UISwitch) {
    textField.resignFirstResponder()
    if switchControl.on {
        let notificationSettings = UIUserNotificationSettings(forTypes: [.Alert , .Sound], categories: nil) 
        UIApplication.sharedApplication().registerUserNotificationSettings(notificationSettings)
    }
}
{% endhighlight %}

When the switch is toggled to ON, this prompts the user for permission to send local notifications. Once the user has given permission, the app won’t put up a prompt again.

> Open the storyboard and connect the shouldRemindToggled: action to the switch control.

> Test it out. Run the app, add a new checklist item, set the due date a minute into the future, press Done and exit to the home screen.

Wait one minute (patience...) and the notification should appear. Pretty cool!

![11-16](/assets/images/The_iOS_Apprentice/Checklists/11-16.png)

    Note: The date picker doesn’t show you seconds but they still are there (just watch the print output). If you set the due date to 10:16 PM when it’s currently 10:15:54 PM, you’ll have to wait until exactly 10:16:54 for the event to fire. It would probably be a better user experience if you always set the seconds to 0, but that’s a topic for another day.

The local notification when the app is in the background

That takes care of the case where you’re adding a new notification. There are two situations left: 1) the user edits an existing item, and 2) the user deletes an item. Let’s do editing first.

When the user edits an item, the following situations can occur:

* Remind Me was switched off and is now switched on. You have to schedule a new notification.
* Remind Me was switched on and is now switched off. You have to cancel the existing notification.
* Remind Me stays switched on but the due date changes. You have to cancel the existing notification and schedule a new one.
* Remind Me stays switched on but the due date doesn’t change. You don’t have to do anything.
* Remind Me stays switched off. Here you also don’t have to do anything.

Of course, in all those situations you’ll only schedule the notification if the due date is in the future.

Phew, that’s quite a list. It’s always a good idea to take stock of all possible scenarios before you start programming because this gives you a clear picture of everything you need to tackle.

It may seem like you need to write a lot of logic here to deal with all these situations, but actually it turns out to be quite simple.

First you’ll look if there is an existing notification for this to-do item. If there is, you simply cancel it. Then you determine whether the item should have a notification and if so, you schedule a new one.

That should take care of all the above situations, even if sometimes you simply could have left the existing notification alone. The algorithm is crude, but effective.

> Add the following to the top of scheduleNotification() in ChecklistItem.swift:

{% highlight swift %}
func scheduleNotification() {
    let existingNotification = notificationForThisItem()
    if let notification = existingNotification {
        print("Found an existing notification \(notification)") 
        UIApplication.sharedApplication().cancelLocalNotification(notification)
    }
    // ...
{% endhighlight %}

This calls a new method notificationForThisItem(), which you’ll add in a second.

If that method returns a valid UILocalNotification object (i.e. not nil), then you dump some debug info using print() and ask the UIApplication object to cancel this notification.

> Add the new notificationForThisItem() method:

{% highlight swift %}
func notificationForThisItem() -> UILocalNotification? {
    let allNotifications = UIApplication.sharedApplication().scheduledLocalNotifications!
    for notification in allNotifications {
        if let number = notification.userInfo?["ItemID"] as? Int where number == itemID {
            return notification
        }
    }
    
    return nil
}
{% endhighlight %}

This asks UIApplication for a list of all scheduled notifications. Then it loops through that list and looks at each notification one-by-one.

The notification should have an “ItemID” value inside the userInfo dictionary. If that value exists and equals the itemID property, then you’ve found a notification that belongs to this ChecklistItem.

If none of the local notifications match, or if there aren’t any to begin with, the method returns nil.

It’s possible that the UILocalNotification object does not have a userInfo dictionary – it’s an optional – which is why you write notification.userInfo? with a question mark to access it (optional chaining).

Likewise, there is no guarantee the dictionary actually contains a value under the “ItemID” key or that this value is an Int, so you need to use an optional type cast with as? to retrieve the Int object and if let to check the result for nil.

Of course, for the notifications that you scheduled these conditions are always true, but Swift cannot make such assumptions. Therefore you have to build those assumptions into your source code using if let and as?.

    Note: Remember “where”? Previously you used this with a for in loop but you can also use it inside an if-statement.

    An alternative way to write the above if-statement is as follows:

    if let number = notification.userInfo?["ItemID"] as? Int {
        if number == itemID {
            return notification
        }
    }

    That gives you two if-statements that are “nested” inside each other. There’s nothing wrong with that, but it’s shorter to tack that second check as a where clause onto the first if.

This is a common pattern that you’ll see in a lot of code. Something returns an array of items and you loop through the array to find the first item that matches what you’re looking for, in this case the item ID. Once you’ve found it, you can exit the loop.

> Run the app and try it out. Add a to-do item with a due date a few days into the future. A new notification will be scheduled.

> Edit the item and change the due date. The old notification will be removed and a new one scheduled for the new date.

You can tell that this happens from the print() output.

> Edit the to-do item again but now set the switch to OFF. The old notification will be removed and no new notification will be scheduled.

> Edit again and don’t change anything; no new notification will be scheduled because the switch is still off. This should also work if you terminate the app in between.

There is one last case to handle: deletion of the ChecklistItem object. This can happen in two ways:

1. the user can delete an individual item using swipe-to-delete,
2. the user can delete an entire checklist in which case all its ChecklistItem objects are also deleted.

An object is notified when it is about to be deleted using the deinit message. You can simply implement this method, look if there is a scheduled notification for this item and then cancel it.

> Add the following to the bottom of ChecklistItem.swift:

{% highlight swift %}
deinit {
    if let notification = notificationForThisItem() {
        print("Removing existing notification \(notification)") 
        UIApplication.sharedApplication().cancelLocalNotification(notification)
    }
}
{% endhighlight %}

That’s all you have to do. The special deinit method will be invoked when you delete an individual ChecklistItem but also when you delete a whole Checklist – because all its ChecklistItems will be destroyed as well, as the array they are in is deallocated.

> Run the app and try it out. First schedule some notifications far into the future (so they won’t be fired when you’re testing) and then remove that to-do item or its entire checklist. You should now see the “Removing existing notification” message in the debug area.

Once you’re convinced everything works, you can remove the print() statements. They are only temporary for debugging purposes. You probably don’t want to leave them in the final app. (They won’t hurt any, but the end user can’t see those messages anyway.)

> Also remove the item ID from the label in the ChecklistViewController – that was only used for debugging.