---
layout: post
title: "Collection Views Quick Start"
description: ""
category: Pro_iOS_Table_Views_and_Collection_Views
tags: [Pro_iOS_Table_Views_and_Collection_Views]
---
{% include JB/setup %}

# Pro iOS Table Views and Collection Views: Using Swift 2

Copyright © 2015 by Tim Duckett

For information on translations, please e-mail <rights@apress.com>, or visit <www.apress.com>.

![1](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/1.png)

![2](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/2.png)

![3](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/3.png)

![4](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/4.png)

### Collection View Cells

Collection view cells are instances of UICollectionReusableView or its subclasses. They have one of three roles:

* Item cells, which are created as instances of UICollectionViewCell. These are analogous to the cells of a table view, and are used to display the main data items. In a gallery app, for example, the cells are likely to display thumbnail images of the photos in an album.

* Supplementary views **(追加视图)**, which are instances of UICollectionReusableView. These are entirely optional, and can have a variety of purposes. In grid-type layouts, such as a photo gallery, they’re often used to provide metadata about sections by acting as headers and footers. In more complex layouts, they can be used to display additional information about items.

* Decoration views **(装饰视图)**, which are also instances of UICollectionReusableView. These are independent of the collection view’s model and don’t display any data. Typically they’re often used to display graphical elements such as backgrounds or section highlights.

![5](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/5.png)

### The Supporting Objects

The collection view control itself is pretty dumb; it relies on the support of four other objects in order to display its data:

* Model
* Datasource 
* Delegate
* Layout

![6](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/6.png)

![7](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/7.png)

![8](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/8.png)

![9](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/9.png)

![10](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/10.png)

![11](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/11.png)

![12](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/12.png)

![13](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/13.png)

![14](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/14.png)

![15](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/15.png)

![16](/assets/images/Pro_iOS_Table_Views_and_Collection_Views/3/16.png)


## Summary

In this chapter, you created a very basic collection view stage by stage:

* To start, you created some data to display in the collection view.
* Then, using Interface Builder, you created an instance of UICollectionView in the window.
* The view controller conformed to the UICollectionViewDataSource and UICollectionViewDelegate protocols so that it could provide the data for the collection view and responses to interaction.
* You implemented the code required to create cells for the collection view.
* You tweaked the layout to control how items are laid out within the bounds of the collection view.
