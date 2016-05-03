---
layout: post
title: "197-200 wwdc2015 session 406. UI Testing in Xcode"
description: ""
category: wwdc
tags: [wwdc, ui, test]
---
{% include JB/setup %}

# UI Testing in Xcode
---

*Wil Turner Developer* Tools Brooke 

*Callahan Developer* Tools

## Overview
---

* UI testing
    * Find and interact with UI elements
    * Validate UI properties and state
* UI recording
* Test reports

![1](/assets/images/wwdc/2015/406/1.png)

## XCTest
---

### Xcode’s testing framework

* Test case subclasses
* Test methods
* Assertions
* Integrated with Xcode
* CI via Xcode Server and xcodebuild
* Swift and Objective-C

### Testing Matrix
---

![2](/assets/images/wwdc/2015/406/2.png)

## Accessibility
---

* Rich semantic data about
* UI UIKit and AppKit integration
* APIs for fine tuning
* UI tests interact with the app the way a user does

## Requirements
---

* UI testing depends on new OS features
    * iOS 9
    * OS X 10.11
* Privacy protection
    * iOS devices
        * Enabled for development
        * Connected to a trusted host running Xcode
    * OS X must grant permission to Xcode Helper
        * Prompted on first run

## Getting Started
---

* Xcode target type
* APIs
* UI recording

## UI Testing Xcode Targets
---

* UI tests have special requirements
    * Execute in a separate process
    * Permission to use Accessibility
* New Xcode target templates
    * Cocoa Touch UI Testing Bundle (iOS)
    * Cocoa UI Testing Bundle (OS X)
* "Target to be Tested" setting

![3](/assets/images/wwdc/2015/406/3.png)

## APIs
---

Three new classes

* XCUIApplication
* XCUIElement
* XCUIElementQuery

## UI Recording
---

* Interact with your app
* Recording generates the code
    * Create new tests
    * Expand existing tests

## What Did You See?
---

* Adding a UI testing target
* Using recording
    * Finding UI elements
    * Synthesizing user events
* Adding validation with XCTAssert

## UI Testing API
---

![4](/assets/images/wwdc/2015/406/4.png)

## Example
---

### Testing the Add button

{% highlight swift %}
// application:
let app = XCUIApplication()
app.launch()

// element and query:
let addButton = app.buttons["Add"]
addButton.tap()

// assertion:
XCTAssertEqual(app.tables.cells.count, 1)
{% endhighlight %}

![5](/assets/images/wwdc/2015/406/5.png)

## XCUIApplication
---

* Proxy for the tested application
    * Tests run in a separate process
* Launch
    * Always spawns a new process
    * Implicitly terminates any preexisting instance
* Starting point for finding elements

## XCUIElement
---

* Proxy for elements in application
* Types
    * Button, Cell, Window, etc.
* Identifiers
    * Accessibility identifier, label, title, etc.
* Most elements are found by combining type and identifier

## Element Hierarchy
---

![6](/assets/images/wwdc/2015/406/6.png)

## Element Uniqueness
---

* Every XCUIElement is backed by a query
* Query must resolve to exactly one match
    * No matches or multiple matches cause test failure 
    * Failure raised when element resolves query
* Exception
    * exists property

## Event Synthesis
---

* Simulate user interaction on elements
* APIs are platform-specific

{% highlight swift %}
button.click() // OS X
button.tap() // iOS
textField.typeText("Hello, World!") // iOS & OS X
{% endhighlight %}

## XCUIElementQuery
---

### API for specifying elements

* Queries resolve to collections of accessible elements
    * Number of matches:count
    * Specify by identifier: subscripting
    * Specify by index:elementAtIndex()

### How do queries work?

* Relationships
* Filtering

### Expressing relationships

* Descendants
* Children
* Containment

![7](/assets/images/wwdc/2015/406/7.png)

### Filtering

* Element type
    * Button, table, menu, etc.
* Identifiers
    * Accessibility identifier, label, title, etc.
* Predicates
    * Value, partial matching, etc.

## Combining Relationships and Filtering
---

### descendantsMatchingType()

So common, we provide convenience API for each type

{% highlight swift %}
let allButtons = app.descendantsMatchingType(.Button)
let allCellsInTable = table.descendantsMatchingType(.Cell)
let allMenuItemsInMenu = menu.descendantsMatchingType(.MenuItem)
{% endhighlight %}

can also:

{% highlight swift %}
let allButtons = app.buttons
let allCellsInTable = table.cells
let allMenuItemsInMenu = menu.menuItems
{% endhighlight %}

### childrenMatchingType()

Differentiates between any descendant and a direct child relationship

{% highlight swift %}
let allButtons = app.buttons // descendantsMatchingType(.Button)
let childButtons = navBar.childrenMatchingType(.Button)
{% endhighlight %}

### containingType()

Find elements by describing their descendants

{% highlight swift %}
let cellQuery = cells.containingType(.StaticText, identifier:"Groceries")
{% endhighlight %}

Predicate variant also available

![8](/assets/images/wwdc/2015/406/8.png)

## XCUIElementQuery
---

### Combining relationships and filtering

{% highlight swift %}
descendantsMatchingType()
childrenMatchingType()
containingType()
{% endhighlight %}

## Combining Queries
---

Queries can be “chained” together
Output of each query is the input of the next query

{% highlight swift %}
let labelsInTable = app.tables.staticTexts
{% endhighlight %}

![9](/assets/images/wwdc/2015/406/9.png)

## Getting Elements from Queries
---

* Subscripting
    * table.staticTexts["Groceries"]
* Index
    * table.staticTexts.elementAtIndex(0)
* Unique
    * app.navigationBars.element

## Evaluating Queries
---

* Queries are evaluated on demand
* XCUIElement
    * Synthesizing events
    * Reading property values
* XCUIElementQuery
    * Getting number of matches (.count)
    * Getting all matches (.allElementsBoundByAccessibilityElement)
* Re-evaluated when UI changes

## Queries and Elements
---

### Similar to URLs

* Creating a URL does not fetch a resource
    * URL could be invalid, error raised when requested
* Queries and elements
    * Just a specification for accessible elements in the tested application
    * Not resolved until needed

## API Recap
---

![10](/assets/images/wwdc/2015/406/10.png)

## Accessibility and UI Testing
---

![11](/assets/images/wwdc/2015/406/11.png)

### Debugging tips

* Not accessible
    * Custom view subclasses
    * Layers, sprites, and other graphics objects
* Poor accessibility data
* Tools
    * UI recording
    * Accessibility inspectors

### Improving data

* Interface Builder inspector
* API
    * UIAccessibility (iOS)
    * NSAccessibility (OS X)

![12](/assets/images/wwdc/2015/406/12.png)

## What Did You See?
---

* Advanced UI testing
* Correcting queries
* Looping over elements
* Improving accessibility

## Test Reports
---

* UI Refresh


* Show results for all tests
    * Pass/fail
    * Failure reason
    * Performance metrics
* Same UI in Xcode and in Xcode Server
* Per-device results for Xcode Server

![13](/assets/images/wwdc/2015/406/13.png)

### UI testing additions

* New data
* Screenshots
* Nested activities

### Nested activities

* UI testing APIs have several steps
* Typing into a textfield
    * Wait for the app to idle
    * Evaluate the textfield query
    * Synthesize the text input
    * Wait for the app to idle
* QuickLook for screenshots

![14](/assets/images/wwdc/2015/406/14.png)

# When to Use UI Testing
---

## Using UI Testing
---

* Complements unit testing
* Unit testing more precisely pinpoints failures
* UI testing covers broader aspects of functionality
* Find the right blend of UI tests and unit tests for your project

## Candidates for UI Testing
---

* Demo sequences
* Common workflows
* Custom views
* Document creation, saving, and opening

## Summary
---

* UI testing
    * Find and interact with UI elements
    * Validate UI properties and state
* UI recording
* Test reports

## More Information
---

Testing in Xcode Documentation

<http://developer.apple.com/testing>

Accessibility for Developers Documentation

<http://developer.apple.com/accessibility>

Apple Developer Forums

<http://developer.apple.com/forums>

Stefan Lesser

**Developer Tools Evangelist**

<slesser@apple.com>
