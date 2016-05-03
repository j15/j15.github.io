---
layout: post
title: "The iOS Apprentice Getting Started 1-2"
description: ""
category: raywinderlich
tags: [raywinderlich]
---
{% include JB/setup %}

# The Bull’s Eye game
---

![1-1](/assets/images/The_iOS_Apprentice/Getting_Started/1-1.png)

* Put text on the screen, such as the “Score:” and “Round:” labels. Some of this text changes over time, for example the score, which increases when the player scores points.
* Put a slider on the screen and make it go between the values 1 and 100.
* Read the value of the slider after the user presses the Hit Me button.
* Generate a random number at the start of each round and display it on the screen. This is the target value.
* Compare the value of the slider to that random number and calculate a score based on how far off the player is. You show this score in the alert popup.
* Put the Start Over button on the screen. Make it reset the score and put the player back into the first round.
* Put the app in landscape orientation.
* Make it look pretty. :-)

![1-2](/assets/images/The_iOS_Apprentice/Getting_Started/1-2.png)

## Code
---

{% highlight swift %}

import UIKit

class ViewController: UIViewController {
  var currentValue: Int = 50
  
  override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view, typically from a nib.
  }

  @IBAction func showAlert() {
    let message = "The value of the slider is: \(currentValue)"
    
    let alert = UIAlertController(title: "Hello, World",
      message: message,
      preferredStyle: .Alert)
    
    let action = UIAlertAction(title: "OK", style: .Default,
      handler: nil)
    
    alert.addAction(action)
    
    presentViewController(alert, animated: true, completion: nil)
  }
  
  @IBAction func sliderMoved(slider: UISlider) {
    currentValue = lroundf(slider.value)
  }
}
{% endhighlight %}


## SnapShot
---

![2-1](/assets/images/The_iOS_Apprentice/Getting_Started/2-1.png)
