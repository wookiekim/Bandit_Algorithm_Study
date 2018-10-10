# Chapter 3
## The epsilon-Greedy Algorithm 
### Introducing the epsilon-Greedy Algorithm
Greedy Algorithm
- always takes whatever action seems best at the present moment
- even when that decision might lead to bad long term consequences.

epsilon-Greedy Algorithm
- 'almost' Greedy
  * generally exploits the best available option
  * once in a while explores the other available options
  * BIG IDEA : flip a coin, and depending on the result either 
    * Explore for a whle
    * Exploit

Example of this Algorithm in the previous LOGO case: (Assume red vs green)
* New visitor comes to the site, the algorithm flips the coin that comes up tails with probability _epsilon_.
* If coin comes up heads (1 - _epsilon_), the algorithm is going to exploit. 
  * looks up the historical conversion rates for both green and red logos.
  * Shows new visitor the color that's been most successful historically (Best Arm)
* If coin comes up heads (_epsilon_), the algorithm explores. 
  * Exploration in this case: experimenting with the two colours being considered
  * Algorithm flips a second coin to choose between the two colour choices.
    * This coin has a 50% chance for both side unlike the first coin.
  * Show the new visitor a certain colour logo depending on the result of the coin flip.

Therefore, the mathematical definition of epsilon-Greedy Algorithm:
* With probability **1 – epsilon**, the epsilon-Greedy algorithm _exploits_ the best known option.
* With probability **epsilon / 2**, the epsilon-Greedy algorithm _explores_ the best known option.
* With probability **epsilon / 2**, the epsilon-Greedy algorithm _explores_ the worst known option.

### Describing our Logo-Choosing Problem Abstractly

Arms : Options (as described in the preface)
Arm 1, Arm 2 ... Arm N == Option 1, Option 2 ... Option N

```History

Bandit algorithms were originally invented to explain how a gambler would try to make as much money as possible in a hypothetical casino.
In the hypothetical casino, there is only one type of game : a slot machine
A slot machine is also called a one-armed bandit (arm probably referring to the stick) because of its propensity(tendency) to take your money.

The Multiarmed Bandit Problem is therefore referring to a giant slot machine that has many arms.
```

Reward : Measure of success (as described in the preface)

Therefore, **the abstract idea of a bandit problem**:
* We're facing a complicated slot machine, called a bandit, that has a set of N arms that we can pull on.
* When pulled, any given arm will output a reward. But these rewards aren't **reliable**, which is why we're gambling. Arm 1 might give us 1 unit of reward only 1 % of the time, while Arm 2 might give us 1 unit of reward only 3% of the time. ANy specific pull of any specific arm is risky.
* Not only each pull of an arm is risky, we don't start off knowing what the reward rates are for any of the arms. We have to figure out experimentally by actually pulling on the unknown arms.
  * Need to cope with the risk by figuring out which arm has the highest average reward.
    * ..which can be done by pulling on each arm a lot of times and computing the mean of the rewards you get back. 
    * But a real bandit problem is more complicated & realistic.
* We only find out about the reward that was given out by teh arm we actually pulled. Whichever arm we pull, we miss out on information about the other arms that we didn't pull. Just like in real life, you only learn about the path you took and not the paths you could have taken.
* Every time we experiment with an arm that **isn't the best arm**, we lost reward because we could (_in principle_) have pulled on a better arm.

The above 5 features define the **full Multiarmed Bandit Problem**.

Any algorithm that offers a proposed solution to the Multiarmed Bandit Problem must give a rule for selecting arms in some sequence.
- This rule has to balance the desire to:
  1. Learn about new arms
  2. Earn as much reward as possible by pulling on arms you already know are good choices.





### Implementing the epsilon-Greedy Algorithm

### Thinking Critically about the epsilon-Greedy Algorithm
