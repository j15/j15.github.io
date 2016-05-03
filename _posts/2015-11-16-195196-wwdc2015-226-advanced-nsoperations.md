---
layout: post
title: "195-196 wwdc2015 session 226 Advanced NSOperations"
description: ""
category: wwdc
tags: [wwdc, NSOperations]
---
{% include JB/setup %}

## WWDC App
---

* Download Settings
* Download Schedule
* Download News
* Download Video list
* Mark Favorites
* Leave Feedback
* Log In
* Developer Status
* Download Pass
* Download Photos
* Watch Videos
* Push Notifications
* Access CloudKit
* Location Permission
* Display Alerts Upload
* Analytics

# Agenda
---

* Core Concepts
* Beyond the Basics
* Sample Code

## Core Concepts
---

* High-level dispatch_queue_t
* Cancellation
* maxConcurrentOperationCount

Serial Queues

{% highlight swift %}
maxConcurrentOperationCount = 1
{% endhighlight %}

Concurrent Queues

{% highlight swift %}
maxConcurrentOperationCount = Default
{% endhighlight %}

## NSOperation
---

* High-level dispatch_block_t
* Long-running task
* Subclassable

## Lifecycle of an NSOperation
---

![1](/assets/images/wwdc/2015/226/1.png)

### Cancellation
---

{% highlight swift %}
var cancelled: Bool { get }
{% endhighlight %}

* Only changes state
* Subclasses dictate meaning
* Susceptible to race conditions

{% highlight swift %}
operationA.cancel()
{% endhighlight %}

### Readiness
---

{% highlight swift %}
var ready: Bool { get }
{% endhighlight %}

Execute when ready

## Serial Operation Queues
---

* Readiness

## Dependencies
---

* “First this, then that”
* Strict ordering
* Implemented via readiness
* Not limited by queues

{% highlight swift %}
let operationA = ...
let operationB = ...
operationB.addDependency(operationA)
{% endhighlight %}

* Operation deadlock

## WWDC App Favorites
---

![2](/assets/images/wwdc/2015/226/2.png)

## WWDC App Startup
---

![3](/assets/images/wwdc/2015/226/3.png)

## Abstraction
---


* Operations abstract logic
* Simplifies logic changes
* WWDC app
    * Custom backend CloudKit
* Grand Central Dispatch?

# Beyond the Basics
---

## UI Operations
---

### Not just the background

* Authentication Videos
* Alerts
* “Modal” UI

## Block Operations
---

![4](/assets/images/wwdc/2015/226/4.png)

### It’s blocks all the way down

* NSOperation to execute a block
* Dependencies for blocks
* Leaving feedback

## WWDC App
---

* Block operations

## Generating Dependencies
---

### When two operations love each other...

* “Never execute this until after executing that”
* Saving a favorite
* Downloading pass
* Anything requiring login

### WWDC app favorites

![5](/assets/images/wwdc/2015/226/5.png)

## Extending Readiness
---

### Adding requirements

* “Only execute this if...”
* Examples

    * Network is reachable
    * Access to user location
    * User is “logged in”

## Composing Operations
---

### Downloading and parsing

![6](/assets/images/wwdc/2015/226/6.png)

### I heard you like operations...

* Downloading and parsing
* Fetching favorites

## WWDC App
---

### Fetching favorites

{% highlight swift %}
override public func execute() {
	let userRecordID: CKRecordID = ...
	let predicate = NSPredicate(format: "creatorUserRecordID = %@", userRecordID)
	let query = CKQuery(recordType: "SessionFavorite", predicate: predicate)
	let queryOperation = CKQueryOperation(query)
	executeQueryOperation(queryOperation)
}

private func executeQueryOperation(queryOperation: CKQueryOperation) {
	queryOperation.recordFetchedBlock = ...
	queryOperation.queryCompletionBlock = { cursor, error in
		if let error = error {
			self.finishWithError(error)
		} else if let cursor = cursor {
			let fetchMoreRecords = CKQueryOperation(cursor)
				self.executeQueryOperation(fetchMoreRecords)
		} else {
			self.processFetchedRecords()
		}
	}
	database.addOperation(queryOperation)
}
{% endhighlight %}

## Mutual Exclusivity
---

### There can be only one

* Alerts
* Videos
* Loading database

### One alert at a time

![7](/assets/images/wwdc/2015/226/7.png)

## Sample Code
---

* Earthquakes and Operations <developer.apple.com/wwdc>

## Operation
---

* NSOperation subclass
* Adds “conditions”
* Adds “observers”
* Example operations

    * Groups
    * URLSessionTask
    * Location
    * Delay

## OperationCondition
---

* Generates dependencies
* Defines mutual exclusivity
* Checks for satisfied conditions
* Example conditions
    * MutuallyExclusive<T>
    * Reachability
    * Permissions

## OperationObserver
---

* Notified about significant events
    * Operation start
    * Operation end
    * Operation generation

* Example observers
    * Timeouts
    * Background tasks
    * Network activity indicator

# Summary
---

* Operations abstract logic
* Dependencies clarify intent
* Describe complex behaviors
* Enables powerful patterns

## Related Sessions
---

* Building Responsive and Efficient Apps with GCD CloudKit Tips and Tricks
* Advanced CloudKit
* Nob Hill Pacific Heights （WWDC14）
