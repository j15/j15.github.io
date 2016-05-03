---
layout: post
title: "The iOS Apprentice 2: Checklists 08 - Improved Data Model"
description: ""
category: raywinderlich
tags: [raywinderlich]
---
{% include JB/setup %}

## Putting to-do items into the checklists
---

Everything you’ve done in the previous section is all well and good, but checklists don’t actually contain any to-do items yet.

在前几期中所做过的一切都很好,但实际上核对清单不包含任何待办事项。

So far, the list of to-do items and the actual checklists have been separate from each other.

到目前为止,待办事项的列表项和实际核对清单已经彼此分开。

Let’s change the data model to look like this:

让我们像下面这样改变数据模型:

![8-1](/assets/images/The_iOS_Apprentice/Checklists/8-1.png)

There will still be the lists array that contains all the Checklist objects, but each of these Checklists will have its own array of ChecklistItem objects.

仍然会有列表数组包含所有核对清单的对象,但这些核对清单将拥有自己的ChecklistItem对象的数组。

> Add a new **property** to *Checklist.swift*:

{% highlight swift %}
class Checklist: NSObject {
    var name = ""
    var items = [ChecklistItem] ()
    // ...
{% endhighlight %}

keeps everything nice and compact

* 简洁紧凑

{% highlight swift %}
var items = [ChecklistItem] ()
{% endhighlight %}

There you declared the array and initialized it in two different steps:

这里把上面一条语句拆分成 *声明* 一个数组并 *初始化*。

* 清晰明了:

{% highlight swift %}
var items: [ChecklistItem]

required init?(coder aDecoder: NSCoder) {
    items = [ChecklistItem] ()
    /* ... */
}
{% endhighlight %}

* 坚持完整:

{% highlight swift %}
var items: [ChecklistItem] = [ChecklistItem] ()
{% endhighlight %}

I personally don’t like this way of declaring variables because it violates the “DRY” principle – *Don’t Repeat Yourself*. Fortunately, thanks to Swift’s type inference, you can save yourself some keystrokes.

我个人不喜欢这种方式声明变量,因为它违反了“DRY”的原则(不要重复自己)。幸运的是,多亏了 *Swift* 的类型推断,你可以节省一些按键。

> Remove the **items** instance variable from *ChecklistViewController.swift*

> Then make the following changes in this source file:


![8-2](/assets/images/The_iOS_Apprentice/Checklists/8-2.png)

![8-3](/assets/images/The_iOS_Apprentice/Checklists/8-3.png)

![8-4](/assets/images/The_iOS_Apprentice/Checklists/8-4.png)

> Delete the following methods from *ChecklistViewController.swift*. (Tip: You may want to set aside the code from these methods in a temporary file somewhere; shortly you’ll be using them again in a slightly modified form.)

> 从 *ChecklistViewController.swift* 删除下列方法。（小贴士：你应该把这些方法复制到什么地方，我们之后还要使用，可以直接拖到 Xcode Code Snippet）

* func documentsDirectory()
* func dataFilePath()
* func saveChecklistItems()
* func loadChecklistItems()


> Remove the lines that call **saveChecklistItems()**.

> Also delete **init?(coder)**.

> Press *⌘+B* to make sure the app builds without errors.

### Fake it util you make it 填充数据直到完工
---

> Add the following to the bottom of *AllListsViewController.swift*’s **init?(coder)**:

{% highlight swift %}
for list in lists {
    let item = ChecklistItem()
    item.text = "Item for \(list.name)" 
    list.items.append(item)
}
{% endhighlight %}

Using loops will often save you a lot of time. You could have written this code as follows:

使用循环往往会节省你很多时间。否则你可能要写如下这段代码:

{% highlight swift %}
var item = ChecklistItem()
item.text = "Item for Birthdays" 
lists[0].items.append(item)

item = ChecklistItem()
item.text = "Item for Groceries"
lists[1].items.append(item)

item = ChecklistItem()
item.text = "Item for Cool Apps"
lists[2].items.append(item)

item = ChecklistItem()
item.text = "Item for To Do"
lists[3].items.append(item)
{% endhighlight %}

![8-5](/assets/images/The_iOS_Apprentice/Checklists/8-5.png)

> Add the following to AllListsViewController.swift (you may want to copy this from that temporary file, but be sure to make the highlighted changes):

![8-6](/assets/images/The_iOS_Apprentice/Checklists/8-6.png)

{% highlight swift %}
func documentsDirectory() -> String {
    let paths = NSSearchPathForDirectoriesInDomains(.DocumentDirectory, .UserDomainMask, true)
    return paths[0]
}

func dataFilePath() -> String {
    return (documentsDirectory() as NSString).stringByAppendingPathComponent("Checklists.plist")
}

func saveChecklists() {
    let data = NSMutableData()
    let archiver = NSKeyedArchiver(forWritingWithMutableData: data)
    archiver.encodeObject(lists, forKey: "Checklists")
    archiver.finishEncoding()
    data.writeToFile(dataFilePath(), atomically: true)
}

func loadChecklists() {
    let path = dataFilePath()
    if NSFileManager.defaultManager().fileExistsAtPath(path) {
        if let data = NSData(contentsOfFile: path) {
            let unarchiver = NSKeyedUnarchiver(forReadingWithData: data)
            lists = unarchiver.decodeObjectForKey("Checklists") as! [Checklist] 
            unarchiver.finishDecoding()
        }
    }
}
{% endhighlight %}

> Change **init?(coder) to**:

{% highlight swift %}
required init?(coder aDecoder: NSCoder) {
    lists = [Checklist]()
    super.init(coder: aDecoder)
    loadChecklists()
}
{% endhighlight %}

> Add the NSCoding protocol in *Checklist.swift*:

{% highlight swift %}
class Checklist: NSObject , NSCoding {
{% endhighlight %}

> Add those methods to *Checklist.swift*:

{% highlight swift %}
required init?(coder aDecoder: NSCoder) {
    name = aDecoder.decodeObjectForKey("Name") as! String
    items = aDecoder.decodeObjectForKey("Items") as! [ChecklistItem]
    super.init()
}

func encodeWithCoder(aCoder: NSCoder) {
    aCoder.encodeObject(name, forKey: "Name")
    aCoder.encodeObject(items, forKey: "Items")
}
{% endhighlight %}

> Important: Before you run the app, remove the old Checklists.plist file from the Simulator’s Documents folder.

### Doing saves differently
---

![8-7](/assets/images/The_iOS_Apprentice/Checklists/8-7.png)

Parents and their children

There are three situations in which an app can terminate:

1. While the user is running the app. This doesn’t happen very often anymore, but earlier versions of iOS did not support multitasking apps. Receiving an incoming phone call, for example, would kill the currently running app. As of iOS 4, the app will simply be suspended in the background when that happens. There are still situations where iOS may forcefully terminate a running app, for example if the app becomes unresponsive or runs out of memory.
2. When the app is suspended in the background. Most of the time iOS keeps these apps around for a long time. Their data is frozen in memory and no computations are taking place. (When you resume a suspended app, it literally continues from where it left off.) Sometimes the OS needs to make room for an app that requires a lot of working memory – often a game – and then it simply kills the suspended apps and wipes them from memory. The apps are not notified of this.
3. The app crashes. There are ways to detect crashes but handling them can be very tricky. Trying to deal with the crash may actually make things worse. The best way to avoid crashes is to make no programming mistakes! :-)

有三种使用程序终止的方案:

1. 内存不足
2. 挂起的给运行中的腾空间
3. 程序崩溃

Fortunately for us, iOS will inform the app about significant changes such as, “you are about to be terminated”, and, “you are about to be suspended”.

*application delegate*

幸运的是,iOS将通知应用等重大变化,“你要终止”和“你要暂停”。

In fact, if you look inside *AppDelegate.swift*, you’ll see the methods:

    func applicationDidEnterBackground(application: UIApplication)

and:

    func applicationWillTerminate(application: UIApplication)

Add this new method to *AppDelegate.swift*:

{% highlight swift %}
func saveData() {
    let navigationController = window!.rootViewController as! UINavigationController
    let controller = navigationController.viewControllers[0]
    controller.saveChecklists()
}
{% endhighlight %}

![8-8](/assets/images/The_iOS_Apprentice/Checklists/8-8.png)

![8-9](/assets/images/The_iOS_Apprentice/Checklists/8-9.png)

> Change the **applicationDidEnterBackground()** and **applicationWillTerminate()** methods to call **saveData()**:

{% highlight swift %}
func applicationDidEnterBackground(application: UIApplication) {
    saveData()
}

func applicationWillTerminate(application: UIApplication) {
    saveData()
}
{% endhighlight %}

Xcode’s Stop button

Important note: When you press Xcode’s Stop button, the application delegate will not receive the *applicationWillTerminate()* notification. Xcode kills the app without mercy.

特别注意: 当你点击 Xcode 上面的 Stop 按钮时，应用程序将不会收到 applicationWillTerminate() 通知。Xcode 将毫不客气的干掉应用。

Therefore, to test the saving behavior, always simulate a tap on the home button to make the app go into the background before you press Stop. If you don’t press *Shift+⌘+H* first, you’ll lose your data. Caveat developer.

警告开发者

因此，如果想要测试保存行为，在按 Stop 之前问题要先按 Home 键使应用进后台。如果不先按 *Shift+⌘+H*，你将会丢失数据。

### Improving the data model
---

> Add a new file to the project using the Swift File template. Save it as *DataModel.swift* (you don’t need to make this a subclass of anything).

> Change *DataModel.swift* to the following:

{% highlight swift %}
import Foundation

class DataModel {
    var lists = [Checklist]()
}
{% endhighlight %}

> Cut the following methods out of *AllListsViewController.swift* and paste them into *DataModel.swift*:

* func documentsDirectory()
* func dataFilePath()
* func saveChecklists()
* func loadChecklists()

> Add an init() method to DataModel.swift:

{% highlight swift %}
init() {
    loadChecklists()
}
{% endhighlight %}

Also, you don’t have to call *super.init()* because DataModel does not have a superclass (it is not built on NSObject).

Switch to *AllListsViewController.swift* and make the following changes:

> Remove the **lists instance** variable.
> Remove the **init?(coder)** method.
> Add a new instance variable:

{% highlight swift %}
var dataModel: DataModel!
{% endhighlight %}

Xcode still finds a number of errors in *AllListsViewController.swift*. You can no longer reference the lists variable directly, because it no longer exists. Instead, you’ll have to ask the DataModel for its lists property.

> Everywhere the code for *AllListsViewController* says **lists**, replace this with **dataModel.lists**. You need to do this in the following methods:

* tableView(numberOfRowsInSection)
* tableView(cellForRowAtIndexPath)
* tableView(didSelectRowAtIndexPath)
* tableView(commitEditingStyle, forRowAtIndexPath)
* tableView(accessoryButtonTappedForRowWithIndexPath)
* listDetailViewController(didFinishAddingChecklist)
* listDetailViewController(didFinishEditingChecklist)


> In *AppDelegate.swift*, add a new property:

{% highlight swift %}
let dataModel = DataModel()
{% endhighlight %}

> Simplify the *saveData()* method to just this:

{% highlight swift %}
func saveData() {
    dataModel.saveChecklists()
}
{% endhighlight %}

Crash

> Change that method to:

{% highlight swift %}
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
    let navigationController = window!.rootViewController as! UINavigationController
    let controller = navigationController.viewControllers[0] as! AllListsViewController
    controller.dataModel = dataModel

    return true
}
{% endhighlight %}

> Do a clean build *(Product → Clean)* and run the app. Verify that everything still works. Great!