# Chapter 2
## Why Use Multiarmed Bandit Algorithms?

### What are we trying to do?

Focus on exploring and exploitation in the concept of **website optimization**.

Website Optimization: Step-by-step process in which a web developer makes a series of changes to a website, each of which is meant to increase the success of that site.
* ex) Search Engine Optimization (SEO) - increaseing a site's rank in search engine results.

By the term "success", it will describle measurable achievements like:

1. Traffic
2. Conversions
3. Sales
4. CTR's

and will be referred to as 'rewards'.

'Arms' will refer to the list of potential changes - in the previous example, change to the colour of the logo would be an arm.

Why use **bandit algorithms** when A/B testing is already there? 
- A/B testing is about assigning an incoming web user to one of two groups until the developer is convinced that either Option A is more successful than Option B (or vice versa).
  * Then the inferior version of the website is closed out.
- A/B testing : Pure Exploration -> Pure Exploitation
  * Pure exploration : assigning equal number of users to Groups A and B
  * Pure exploitation : send all the users to the more successful option.
  * Jumps discretely from exploration to exploitation, when you might be able to smoothly transit between the two.
  * During pure exploration, resources are wasted in exploring inferior options.
- Bandit algorithm provides answers to the above problems:
  * smoothly decrease the amount of exploring done over time (instead of discrete transition)
  * Focus resources during exploration on the better options instead of wasting them on inferior, over-explored options.
  * All good bandit algorithms eventually converge (gradually fixate on the best available option over time).
  * Therefore lets you operate in the much larger and interesting space between the two extreme states (exploration v exploitation)

Next up : **epsilon-Greedy algorithm**
