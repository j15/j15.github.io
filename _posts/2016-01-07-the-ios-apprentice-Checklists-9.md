---
layout: post
title: "The iOS Apprentice 2: Checklists 09 - NSUserDefaults"
description: ""
category: raywinderlich
tags: [raywinderlich]
---
{% include JB/setup %}

## Using NSUserDefaults to remember stuff 用 NSUserDefaults 记一些东西
---

NSUserDefaults works like a dictionary, which is a collection object for storing key- value pairs. You’ve already seen the array collection, which stores an ordered list of objects. The dictionary is another very common collection that looks like this:

NSUserDefaults 像字典,它是一个对象存储键-值对的集合。您已经看到数组集合,存储对象的有序列表。字典是另一个非常常见的集合:

![9-1](/assets/images/The_iOS_Apprentice/Checklists/9-1.png)


To be fair, NSUserDefaults isn’t a true dictionary, but it acts like one.

平心而论,NSUserDefaults不是一个真正的字典,但它像字典一样。


This is what you are going to do:

* On the segue from the main screen (*AllListsViewController*) to the checklist screen (*ChecklistViewController*), you write the row index of the selected list into NSUserDefaults. This is how you’ll remember which checklist was active.（记住下标）

    You could have saved the name of the checklist instead of the row index, but what would happen if two checklists have the same name? Unlikely, but not impossible. Using the row index guarantees that you’ll always select the proper one.(名字会有冲突，最好有唯一标识)

* When the user presses the back button to return to the main screen, you have to remove this value from NSUserDefaults again. It is common to set a value such as this to -1 to mean “no value”.（返回时清空记录，例如 -1）

    Why -1? You start counting rows at 0, so you can’t use 0. Positive numbers are also out of the question, unless you use a huge number such as 1000000 – it’s very unlikely the user will make that many checklists. -1 is not a valid row index; and because it’s a negative value it looks weird, making it easy to spot during debugging.

    (If you’re wondering why you’re not using an optional for this – good question! – the answer is that NSUserDefaults cannot handle optionals. Sad face.)（为何不用 可空类型？ 因为 NSUserDefaults 不支持）

* If the app starts up and the value from NSUserDefaults isn’t -1, the user was previously viewing the contents of a checklist and you have to manually perform a segue to the ChecklistViewController for the corresponding row.（如果有不为 -1 的值，需要手动跳转到上次打开界面）

> In *AllListsViewController.swift*, change **tableView(didSelectRowAtIndexPath**) to the following:

{% highlight swift %}
override func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
    
    /* new */
    NSUserDefaults.standardUserDefaults().setInteger(indexPath.row, forKey: "ChecklistIndex")
    /* end */

    let checklist = dataModel.lists[indexPath.row]
    performSegueWithIdentifier("ShowChecklist", sender: checklist)
}
{% endhighlight %}

> Add the delegate protocol to the class line in *AllListsViewController.swift*:

{% highlight swift %}
class AllListsViewController: 
UITableViewController, 
ListDetailViewControllerDelegate,
/* new */
UINavigationControllerDelegate
/* end */
{% endhighlight %}


> Add the delegate method to the bottom of *AllListsViewController.swift*:

{% highlight swift %}
func navigationController(navigationController: UINavigationController, willShowViewController viewController: UIViewController, animated: Bool) {
    if viewController === self {
        NSUserDefaults.standardUserDefaults().setInteger(-1, forKey: "ChecklistIndex")
    } 
}
{% endhighlight %}

If you use ==, you’re checking whether two variables have the same value. With === you’re checking whether two variables refer to the exact same object.

* ==: 两个相等号，判断值是否相等
* ===: 三个相等号，判断是否是同一个对象，地址相等

> Add the **viewDidAppear()** method to *AllListsViewController.swift*:

{% highlight swift %}
override func viewDidAppear(animated: Bool) {
    super.viewDidAppear(animated)
    
    navigationController?.delegate = self
    
    let index = NSUserDefaults.standardUserDefaults().integerForKey("ChecklistIndex")
    
    if index != -1 {
        let checklist = dataModel.lists[index] 
        performSegueWithIdentifier("ShowChecklist", sender: checklist)
    }
}
{% endhighlight %}

(You could also have written navigationController! instead of ?. The difference between the two is that ! will crash the app if this view controller would ever be shown outside of a UINavigationController, while ? won’t crash but simply ignore the rest of that line.)

(你也可以写了 navigationController! 而不是 ?。两者的区别是,如果 ViewController 不在 UINavigationController 中 !会崩溃的应用,?不会崩溃只是忽略后面的代码)。

## Defensive programming 防御性编程
---

> Now do the following: Stop the app and reset the Simulator using the menu item *Simulator → Reset Contents and Settings*.

Then run the app again from within Xcode and watch it crash:

    fatal error: Array index out of range

The app crashes in viewDidAppear() on the line:

    let checklist = dataModel.lists[index]


Fortunately, NSUserDefaults will let you set default values for the default values. Yep, you read that correctly. Let’s do that in the DataModel object.

幸运的是,NSUserDefaults会让你设置默认值为默认值。是的,你看的没错。

> Add the following method inside *DataModel.swift*:

{% highlight swift %}
func registerDefaults() {
    let dictionary = [ "ChecklistIndex": -1 ]
    NSUserDefaults.standardUserDefaults().registerDefaults(dictionary)
}
{% endhighlight %}


The square bracket notation is not only used to make arrays but also dictionaries. The difference is that for a dictionary it always looks like,
   
    [ key1: value1, key2: value2, . . . ]

while an array is just:

    [ value1, value2, value3, . . . ]


NSUserDefaults will use the values from this dictionary if you ask it for a key but it cannot find anything under that key.

如果找不到值对应的键，NSUserDefaults将使用这个字典的值

> Change *DataModel.swift*’s **init()** to call this new method:

{% highlight swift %}
init() {
    loadChecklists()
    /* new */
    registerDefaults()
    /* end */
}
{% endhighlight %}

> Run the app again and now it should no longer crash.

In fact, let’s move all of the NSUserDefaults stuff into *DataModel*.

> Add the following to DataModel.swift:

{% highlight swift %}
var indexOfSelectedChecklist: Int {
    get {
        return NSUserDefaults.standardUserDefaults().integerForKey( "ChecklistIndex")
    }
    set {
        NSUserDefaults.standardUserDefaults().setInteger(newValue,
forKey: "ChecklistIndex")
    } 
}
{% endhighlight %}

Update the code in *AllListsViewController.swift* to use this new computed property:

{% highlight swift %}
override func viewDidAppear(animated: Bool) {
    super.viewDidAppear(animated)
    navigationController?.delegate = self
    let index =
        /* new */
        dataModel.indexOfSelectedChecklist if index != -1
        /* end */
    {
        let checklist = dataModel.lists[index]
        performSegueWithIdentifier("ShowChecklist", sender: checklist)
    }
}
{% endhighlight %}


{% highlight swift %}
override func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
    /* new */
    dataModel.indexOfSelectedChecklist = indexPath.row
    /* end */

    let checklist = dataModel.lists[indexPath.row]
    performSegueWithIdentifier("ShowChecklist", sender: checklist)
}
{% endhighlight %}

{% highlight swift %}
func navigationController(navigationController: UINavigationController!, willShowViewController viewController: UIViewController!,
animated: Bool) {
    if viewController === self {
        /* new */
        dataModel.indexOfSelectedChecklist = -1
        /* end */
    }
}
{% endhighlight %}

> Run the app again and – if you’re lucky? – it will crash with:

    fatal error: Array index out of range


{% highlight swift %}
set {
    NSUserDefaults.standardUserDefaults().setInteger(newValue, forKey: "ChecklistIndex")
        /* new */
        NSUserDefaults.standardUserDefaults().synchronize()
        /* end */
}
{% endhighlight %}

In our case, you can easily fix *AllListsViewController*’s **viewDidAppear()** method to deal with this situation.

> Change viewDidAppear() to:

{% highlight swift %}
override func viewDidAppear(animated: Bool) {
    super.viewDidAppear(animated)
    navigationController?.delegate = self

    let index = dataModel.indexOfSelectedChecklist
    if index >= 0 && index < dataModel.lists.count {
        let checklist = dataModel.lists[index]
        performSegueWithIdentifier("ShowChecklist", sender: checklist)
    }
}
{% endhighlight %}

## The first-run experience
---

> Change the **registerDefaults()** method in *DataModel.swift* (don’t miss the comma after “ChecklistIndex”):

{% highlight swift %}
func registerDefaults() {
    let dictionary = [ "ChecklistIndex": -1
        /* new */
        ,"FirstTime": true
        /* end */
    ] 
    NSUserDefaults.standardUserDefaults().registerDefaults(dictionary)
}
{% endhighlight %}

> Still in *DataModel.swift*, add a new **handleFirstTime()** method:

{% highlight swift %}
func handleFirstTime() {
    let userDefaults = NSUserDefaults.standardUserDefaults() 
    let firstTime = userDefaults.boolForKey("FirstTime")

    if firstTime {
        let checklist = Checklist(name: "List") 
        lists.append(checklist)
        indexOfSelectedChecklist = 0 
        userDefaults.setBool(false, forKey: "FirstTime") 
        userDefaults.synchronize()
    }
}
{% endhighlight %}

> Call this new method from *DataModel*’s **init()**:

{% highlight swift %}
init() {
    loadChecklists()
    registerDefaults()
    /* new */
    handleFirstTime()
    /* end */
}
{% endhighlight %}