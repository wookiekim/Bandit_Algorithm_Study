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
Easy to implement in python.

Create a class:
```python
class EpsilonGreedy():
  def __init__(self, epsilon, counts, values):
    self.epsilon = epsilon
    self.counts = counts
    self.values = values
    return
```
epsilon: floating point number representing the frequency of exploration

counts: vector of integers of length N that tells us how many times we've played each of the N arms available to us

values: vector of floating point numbers that defines the average amount of reward we've gotten when playing each of the N arms available to us

The behaviour of epsilon-Greedy algorithm is strongly controlled by the settings of _counts_ and _valuess_, so explicit initialization methods that allow you to reset these variables to their proper blank states are provided:
```python
def initialize(self, n_arms):
  self.counts = [0 for col in range(n_arms)]
  self.values = [0.0 for col in range(n_arms)]
  return
```

Next are two types of behaviours **any** algorithm for solving the Multiarmed Bandit Problem should provide:

1. ***select_arm***

Called without any arguments, returns the index of the next arm to pull.

```python
def ind_max(x):
  m = max(x)
  return x.index(m)

def select_arm(self):
  if random.random() > self.epsilon:
  #random.random() returns random float in the half-open interval [0.0, 1.0)
    return ind_max(self.values)
  else:
    return random.randrange(len(self.values))
```
The above implementation can be explained in two parts.
  1. A coin is flipped (random.random()) to see if the best arm will be chosen (exploit)
  2. If coin comes up tails (random() <= epsilon) then an arm is selected completely at random.

2. ***update***

After an arm is pulled, a reward signal is received from the system. Takes `an algorithm object`, `numeric index of the most recently chosen arm`, `reward received from choosing that arm` as arguments, and makes relevant changes to the algorithm's evaluation of all of the arms.

```python
def update(self, chosen_arm, reward):
  self.counts[chosen_arm] = self.counts[chosen_arm] + 1
  n = self.counts[chosen_arm]
 
  value = self.values[chosen_arm]
  new_value = ((n - 1) / float(n)) * value + (1 / float(n)) * reward
  self.values[chosen_arm] = new_value
  return
```
First increments the corresponding counts field, then updates the values corresponding to the reward received. When updating an already existing (i.e. an arm that has been played in the past) value, then a weight average is used.

```
Why weighted average?

So that single observations mean less and less to the algorithm when we already have a lot experience with any specific option.
```

### Thinking Critically about the epsilon-Greedy algorithm

The epsilon-Greedy algorithm can be configured to behave like what Cynthia suggested (traditional A/B testing):

```python
algo = EpsilonGreedy(1.0, [])
initialize(algo, 2)
```
The epsilon is set to one, so the algorithm **always explores**.

It can also be configured to behave like Bob's rule for profit-maximiation:
```python
algo.epsilon = 0.0
```
The epsilon is set to zero, so the algorithm **always exploits**.

The above examples are the two most extreme ways epsilon-Greedy algorithms can be configured.

When epsilon == 1.0 (Cynthia) :
* Algorithm chooses among different arms at complete random
  * Lot of data about all the arms in equal amounts
* But in a business, can lose a lot of money
  * Needless to accumulate a lot of data about bad options.
  * (Logo example) Worst logo shown as much as best logo, given there are two options.

When epsilon == 0.0 (Bob) :
* Stop wasting time on bad options
  * And never learn about new options again
  * May be left behind (in the business world)

The value of epsilon can be set somewhere in between (say, 0.1) and the algorithm will be much better than either of the extreme cases.

But not seamless:
* As you get more certain which arm is the best arm, the tendency to explore the worse option (5% of the time given epsilon = 0.1) will be wasteful.
  * Over-exploration
* In the beginning, new options are only tried 10% of the time, which is far too rare compared to exploiting.

There are many ways the epsilon-Greedy algorithm could be better.
- Other algorithms that cna improve on this algorithm to be described in the rest of this book

**Next up** :A 'testbed' to try out the algorithms
