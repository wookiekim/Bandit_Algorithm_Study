# Chapter 7
## Bandits in the Real World: Complexity and Complications

This chapter gives tips about ways in which the real world is likely to be more complex than the simplifed examples we've used to test the algorithms we've implemented.

Questions to ask when deploying bandit algorithms in the wild:
* How sure are you that you won’t subtly corrupt your deployment code?
* How many different tests are you planning to run simultaneously? Will these tests interfere with each other? Will starting a new test while another one is already running corrupt its results?
* How long do you plan to run your tests?
* How many users are you willing to expose to non-preferred versions of your site?
* How well-chosen is your metric of success?
* How are the arms you’re measuring related to one another?
* What additional information about context do you have when choosing arms? Do you have demographics based on browser information? Does your site have access to external information about people’s tastes in products you might advertise to them?
* How much traffic does your site receive? Is the system you’re building going to scale up? How much traffic can your algorithm handle before it starts to slow your site down?
* How much will you have to distort the setup we’ve introduced when you admit that visitors to real websites are concurrent and aren’t arriving sequentially as in our simulations?

### A/A Testing 

Microsoft Research paper: "Trustworthy Online Controlled Experiments: Five Puzzling Outcomes Explained"
* does a good job of describing some problems you are likely to run into if you deploy a bandit algorithm on your site.

A/A testing: define two different ARMs that are actually the same user experience
* If difference are found between these two arms: need to reconsider claims about differences between other arms
* The difference may indicate a bug in your code, or a mistake in the way you're analyzing your data.
* Or the test may be running in a context that is subtly different from the assumptions we've been implicitly making when setting up our algorithms and simulations
* Even if no issues are found, provides a useful way to estimate the actual variability in data before trying to decide whether the differences found by bandit algorithms are real.

### Running Concurrent Experiments
Many real-world websites will end up running many different experiments simultaneously.
* may end up overlapping
  * ex) comparing two different logo colours and two different fonts at the same time
  * even if no overlap, may add a lot of uncertainty into your results.
  * can be solved by defining your arms in terms of the **combinations** of all the factors you want to test.
* may fail because people get sparks of inspiration that make them change course over time
* ...or number of arms may blow up if you start combining different factors into separate pairs

Best solution: try your best to keep track of all of the experiments each user is a part of
* and include this information in your analyses of any single experiment.

### Continuous Experimentation vs. Periodic Testing
Short term: A/B testing is not too bad
* Not so important to avoid testing inferior strategies because the consequences aren't so bad

Long term: bandit algorithms can be much better
* automatically start to filter out inferior designs over time without requiring you to make a judgement call.

Wise to think about HOW MANY USERS will be put to test
* small number of users: end up with too little data about the arms that the algorithm decided were inferior
  * thus unable to make strong conclusions about them.

### Bad Metrics of Success
Short-term optimizations may harm long-term business

Optimizing the wrong metric of success is ubiquitous
* many companies may have lost a greal deal because of poorly chosen metrics

No algorithmic solution to this problem.
* Exercise human judgement
* Keep an eye on what happens to your system
* Best hope is to monitor MANY different metrics (that are considered important) simultaneously

### Scaling Problems with Good Metrics of Success
Important to rescale the metrics into the 0-1 space 
* some algorithms may be numerically unstable for other spaces
* absolutely required for UCB1 algorithm

### Intelligent Initialization of Values
Smart initialization: use information you have before ever deploying a bandit algorithm
1. Use historical metrics for the control arm in bandit algorithm
  * can be given an initial value based on data from before you let the bandit algorithm loose.
  * Can initalize all unfamiliar arms using this same approach
2. Use amount of historical data you have to calibrate how much the algorithm thinks you know about the historical options.
  * For algorithms like UCB1, will encourage the algorithm to explore new options until the algorithm has some confidence about their worth relative to tradition.
  * Good, but needs to be done with caution.

### Running Better Simulations
Build appropriate simulations using historical data as well.
* Not just history-based arms
* Real world click-through rates are typically lower than this

### Moving Worlds
The value of different arms in a bandit problem can easily change over time (in the real world)
* ex) Orange-black logo may be perfect for Halloween, terrible for Christmas.
* you want your estimates to be able to do this well.
* The presented algorithms will not handle most sorts of change in the underlying values of arms as well. 

Initial `update` code included this snippet:
```python
new_value = ((n - 1) / float(n)) * value + (1 / float(n)) * reward
self.values[chosen_arm] = new_value
```

`1/float(n)` goes to 0 as n gets large.
* When dealing with millions or billions of plays, recent rewards will have almost zero effect on your estimates of the value of different arms.

A simple trick: overweight recent events by using a slightly different update rule.
```python
new_value = (1 - alpha) * value + (alpha) * reward
self.values[chosen_arm] = new_value
```
The new `alpha` variable allows estimate to shift much more with recent experiences.
* but have to be tuned for specific businesses.
* if `alpha = 1.0`, you can expect to unleash a nightmare for yourself.

### Correlated Bandits
If you can generalize experiences with some arms to other arms
* the arms are correlated
* Expected rewards of different arms are correlated
* Need to find ways to RELATE arms and UPDATE estimates based on this information.

This book doesn't cover the details
* encouraged to look into classical smoothing techniques in statistics
  * to get a sense of how you might deal with correlated arms.

### Contextual Bandits

May have background information about the context in which we're trying different options.
* ex) certain fonts are more appealing to male users than to female users. 
* `LinUCB` and `GLMUCB` are examples of algorithms to work with contextual information
  1. A Contextual-Bandit Approach to Personalized News Article Recommendation
  2. Parametric Bandits: The Generalized Linear Case
  * LinUCB updates a linear regression model for the arms' values after each play
  * GLMUCB updates a General Linear Model for the arms1 values after each play

### Implementing Bandit Algorithms at Scale
Even in simplest real-world settings:
* You often may not know what happened on your N-th play in the real world UNTIL a while after you've been forced to served a new page for (and select a new arm for) many other users
  * i.e. seen logo-> new users comes and sees other logo before the first user clicks/registers
* Destroys the clean sequential structure assumed throughout the book
  * Especially for websites that serve hundreds of thousands of hits in a second

Proposed Solution: Lso the solution chosen by Google Analytics based on information in their `help documents`
* Embraces this failure in assumption and develop a system that is easier to scale up
* Proposed in two separate parts:
  1. Blocked Assignments

Assign incoming users to new arms in advance and draw this information from a fast cache when users actually arrive. Store their responses for batch processing later in another fast cache.

  2. Blocked updates

Updates estimates of arm values in batches on a regular interval and regenerate your blocked assignments. Because you work in batches, easier to perform the kind of complex calculations you'll need to deal with correlated arms or contextual information.

Other interesting questions about scaling up bandit algorithms:
* What sort of database should you store information in? MySQL? Memcached? Wise to use the lowest latency data storage tool available to you.
* Where in your production code should you be running the equivalent of `select_arm` and `update` functions?
  * In the previous blocked assignments model, these snippets are far removed from the tools that directly generate served pages.
  * In previous examples (obvious strategies), happens in the page generation mechanism itself.
