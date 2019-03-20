footer: @kylebshr
theme: Lyft

[.slidenumbers: false]
[.hide-footer]

# **How *(not)* to A/B Test**
## Lessons learned in 
## experimentation at Lyft
--
--
--
--
--
--
### @kylebshr • 27 March 2019

---

# What is an A/B test?

^ You have an idea for an improvement, but you want to validate that it’s better in some measurable way

^ Useful for testing a hypothesis 

---

# Hypothesis

![inline](img/sign-up.png)

^ As a naive example, you want to test whether a yellow sign up button increases conversion

^ You’re testing experience (B) against a control, experience (A), hence the name

^ Now, I think a lot of people think you need to be able to dynamically insert code. But Swift is a compiled, static language.

---

## _In viewDidLoad..._
--
--
--
```ruby
FeatureFlag.yellowSignUpButton.on {
    button.setStyle(background: .yellow, text: .black)
} .off {
    button.setStyle(background: .blue, text: .white)
}
```

^ A/B test implementation usually looks something like this. 

---

# Monitor Results

![inline](img/sample-metrics.png)

^ Then you monitor the metrics...

---

# Ship It!

![inline](img/sign-up-decision.png)

^ And ship the more successful version! 

^ All you have to do to clean it up is remove the flag & code in losing variant.

^ Almost two years ago, decided to do something ambitious.. a huge a/b test.

---

# Lyft.app

^ Decided to a/b test a rewrite of the whole app

---

# Project X

![inline](img/v4-vs-x.png)

^ Now this was a complete redesign, so we had a few goals

^ Wanted a completely fresh start, essentially a new app

^ also wanted to effortlessly clean up, but for an entire app

^ now you’re probably thinking, “no they didn’t...”

---

# Implementation

![inline](img/one-delegate.png)

^ well, yes we did. We had not one, 

---

# Implementation

![inline](img/two-delegates.png)

^ not two

---

# Implementation

![inline](img/three-delegates.png)

^ but three app delegates. 

---

# Implementation

![inline](img/messy-swift.png)

^ two of which led to entirely different apps

---

# Implementation

![inline](img/switching-flag.png)

^ and one that implemented every single delegate method, and forwarded the calls to the correct delegate based on our feature flag

^ now, this was actually a pretty neat idea

^ Could iterate on new stuff without worry about inter-op or breaking the old app

^ Still in the same project, even shared modules

^ And after a ton of incredible work, it was time to run the experiment

---

# Results

![inline](img/x-metrics.png)

^ And the results looked something like this

^ some metrics were up, but some were down quite a bit

^ and realized downsides...

---

# What’s broken?
# **And why?**

^ With so many changes in a single a/b test, it’s often difficult to tell _why_ a metric moved a certain way

^ is it less intuitive? Is a specific button placement hurting conversion? Maybe the fact that we changed the order you do things?

^ one result of the decline in certain metrics meant we were testing this for a long time - over a year

^ this led to the second downside

---

# It’s hard to maintain 
# two apps

^ have to either build for future or current users, or sometimes twice

^ Applies to any extremely large change, doesn’t have to be as extreme as the whole app

^ So, clearly an A/B test can be too big.

---

# Demand Graph Redesign

![inline](img/demand-graph.png)

^ Now, let’s switch gears and take a look at an experiment we recently ran in the driver app.

^ this one’s a little more similar to our log in button example.

^ Recently released new feature to show passenger demand.

^ Wanted to improve readability & design of graphs

---

# Implementation
--
--
```swift
protocol GraphDisplaying {...}

class BarGraphView: UIView, GraphDisplaying {...}           
``` 

^ protocols for graphs make a/b test nice

---

# Implementation
--
--
```swift
protocol GraphDisplaying {...}

class BarGraphView: UIView, GraphDisplaying {...}

class InteractiveBarGraphView: UIView, GraphDisplaying {...}

typealias GraphView = UIView & GraphDisplaying
``` 

---

# Implementation

```swift
final class DemandGraphView: UIView {
    private let chartView: GraphView

    init(frame: CGRect) {
        if FeatureFlag.demandGraphV2.getValue() {
            self.chartView = BarGraphView()
        } else {
            self.chartView = InteractiveBarGraphView()
        }
        ...
    }
}
``` 

---

# Results

![inline](img/graph-metrics.png)

^ Nice and self contained

^ Didn’t get any useful results

^ Flat metrics don’t mean the experiment was bad

^ but looking back, it’s clear that an a/b test can be too small or subtle

^ If there’s a clear improvement that’s not changing much, sometimes not worth the time to test

---

# What does a good 
# A/B test look like?

---

# Driver Home Redesign

![inline](img/home-tab.png)

^ Self contained, but also meaningful change

---

# Driver Home Redesign

![inline](img/home-tab-expand.png)

---

# Implementation

```swift
lazy var guidanceViewController = ...
lazy var newsFeedViewController = ...

override func viewDidLoad() {
    FeatureFlag.homeTabGuidance.on {
        self.setUpGuidancePanel()
    } .off {
        self.setUpNewsFeedPanel()
    }
    ...
}
```

^ lazy vars make this nice

---

# Results

![inline](img/home-metrics.png)

---

# Final thoughts

---

# Thanks for listening!
## _Questions?_

---

-