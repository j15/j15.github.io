---
layout: post
title: "The iOS Apprentice 2: Checklists 03 - Data Model"
description: ""
category: raywinderlich
tags: [raywinderlich]
---
{% include JB/setup %}

## Adding new items to the checklist
---

![3-0](/assets/images/The_iOS_Apprentice/Checklists/3-0.png)

What you’ll do in this section:

我们这一期要做什么

* Add a navigation controller
* 添加一个导航栏控制器
* Put the Add button into the navigation bar
* 把一个 *添加按钮* 放到导航栏上
* Add a fake item to the list when you press the Add button
* 当你点击 *添加按钮* 时添加一个假数据
* Delete items with swipe-to-delete
* 当 *滑动删除* 时删除事项
* Open the Add Item screen that lets the user type the text for the item
* 打开 *添加事项* 界面使用户可以输入事项

## Navigation controllers
---

![3-1](/assets/images/The_iOS_Apprentice/Checklists/3-1.png)

![3-2](/assets/images/The_iOS_Apprentice/Checklists/3-2.png)

![3-3](/assets/images/The_iOS_Apprentice/Checklists/3-3.png)

![3-4](/assets/images/The_iOS_Apprentice/Checklists/3-4.png)

![3-5](/assets/images/The_iOS_Apprentice/Checklists/3-5.png)

![3-6](/assets/images/The_iOS_Apprentice/Checklists/3-6.png)

![3-7](/assets/images/The_iOS_Apprentice/Checklists/3-7.png)

![3-8](/assets/images/The_iOS_Apprentice/Checklists/3-8.png)

### Add a new action method to *ChecklistViewController.swift*:

{% highlight swift %}
@IBAction func addItem() {
}
{% endhighlight %}

![3-9](/assets/images/The_iOS_Apprentice/Checklists/3-9.png)

![3-10](/assets/images/The_iOS_Apprentice/Checklists/3-10.png)

![3-11](/assets/images/The_iOS_Apprentice/Checklists/3-11.png)

### Let’s give **addItem()** something to do. Back in *ChecklistViewController.swift*, fill out the body of that method:

{% highlight swift %}
@IBAction func addItem() {
    // update Model
    let newRowIndex = items.count
    let item = ChecklistItem()
    item.text = "I am a new row"
    item.checked = false
    items.append(item)
    
    // update View
    let indexPath = NSIndexPath(forRow: newRowIndex, inSection: 0)
    let indexPaths = [indexPath] 
    tableView.insertRowsAtIndexPaths(indexPaths, withRowAnimation: .Automatic)
}
{% endhighlight %}

### To recap, you 总结

1. created a new ChecklistItem object 添加一个新的 ChecklistItem 对象
1. added it to the data model, and 把它添加到 *数据模型*，并且
1. inserted a new cell for it in the table view. 插入一个新行到 TableView。

![3-12](/assets/images/The_iOS_Apprentice/Checklists/3-12.png)

## Deleting rows
---

![3-13](/assets/images/The_iOS_Apprentice/Checklists/3-13.png)

{% highlight swift %}
override func tableView(tableView: UITableView,
commitEditingStyle editingStyle: UITableViewCellEditingStyle, forRowAtIndexPath indexPath: NSIndexPath) {
    // 1
    items.removeAtIndex(indexPath.row)
    // 2
    let indexPaths = [indexPath] tableView.deleteRowsAtIndexPaths(indexPaths,
}
{% endhighlight %}


1. remove the item from the data model, and 从 *数据模型* 中删除一项，并且
1. delete the corresponding row from the table view. 从 TableView 中删除与之相符的一行。
