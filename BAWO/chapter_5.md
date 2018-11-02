# Chapter 5
## The Softmax Algorithm 
### Introducing the Softmax Algorithm 

Obvious problem with epsilon-Greedy algorithm:
* explores options completely at random without any concern about their merits.
  * independent of `how bad` the worse arm is
* If the reward rate difference between to arms is small, you'll need to explore a lot more often to correctly determine which option is actually better.
* If the difference is large, you need to explore a lot less to estimate the better of the two options. But since the value of epsilon is set, you will end up wasting time on infedior options anyway.

We need to make our bandit algorithm `care` about the `known differences` between the estimated values of the arms when our algorithm decides which arm to explore.
* **Structured exploration** rather than **haphazard exploration**.

Initial intuition for how the Softmax Algorithm handles this problem:
* Imagine each arm is chosen in proportion to its estimated value.
* Assume two rates of success, rA and rB for arm A and B.
* Most naive possible implementation of a Softmax-like algorithm:
  * choose Arm A with probability ` rA/ (rA + rB)`
  * choose Arm B with probability ` rB/ (rB + rA)`
* This proportional approach would look like:

```python
def categorical_draw(probs):
  z = random.random()
  cum_prob = 0.0
  for i in range(len(probs)):
    prob = probs[i]
    cum_prob += prob
    if cum_prob > z:
      return i
 
  return len(probs) - 1

def select_arm(self):
  z = sum(self.values)
  probs = [v / z for v in self.values]
  return categorical_draw(probs)
```

This naive algorithm should be reconstructed for people to actually use.
* Calculate a different scale for reward rates by exponentiating our estimates of rA and rB.
  * Choose arm A with probability `exp(rA) / (exp(rA) + exp(rB))`
  * doesn't behavely strangely if someone used negative numbers as rates of success.
  * Also ensures any negative value in the denominator won't cancel out a positive denominator.
  * Using exponentiation brings use close to the ful Softmax algorithm as well.
* Add a new type of scaling factor
  * typically called a temperature parameter based on analogy with physics
    * systems at high temperatures tend to behave randomly.
    * related to a concept called the Boltzmann distribution in physics.
  * a new temperature parameter `tau` is introduced:
    * choose arm A with probability `exp(rA/tau)/(exp(rA/tau) + exp(rB/tau))`
```
What does this `tau` do?

It’s easiest to
think of tau as letting us shift the behavior of the Softmax algorithm along a continuum
defined by two extreme ways to select arms. At one extreme, we set tau = 0.0. This
will give us a fully deterministic choice of the arm that has the highest estimated value.
At the other extreme, we set tau = Inf, which gives us purely random exploration like
we got out of the epsilon-Greedy algorithm. The reason this tau parameter is called a
temperature parameter is that its effect on the selection of arms is like the effect of
temperatures on atoms in traditional physics.

Low temperature: atoms behave orderly and produce solids
High temperature: atoms behave randomly and produce gases
```

Putting the above concepts together, an implementation for a full Softmax Algorithm:
```python
import math
import random

def categorical_draw(probs):
  z = random.random()
  cum_prob = 0.0
  for i in range(len(probs)):
    prob = probs[i]
    cum_prob += prob
    if cum_prob > z:
      return i
  return len(probs) - 1

class Softmax:
  def __init__(self, temperature, counts, values):
    self.temperature = temperature
    self.counts = counts
    self.values = values
    return

def initialize(self, n_arms):
  self.counts = [0 for col in range(n_arms)]
  self.values = [0.0 for col in range(n_arms)]
  return
 
def select_arm(self):
  z = sum([math.exp(v / self.temperature) for v in self.values])
  probs = [math.exp(v / self.temperature) / z for v in self.values]
  return categorical_draw(probs)
 
def update(self, chosen_arm, reward):
  self.counts[chosen_arm] = self.counts[chosen_arm] + 1
  n = self.counts[chosen_arm]
 
  value = self.values[chosen_arm]
  new_value = ((n - 1) / float(n)) * value + (1 / float(n)) * reward
  self.values[chosen_arm] = new_value
  return
```

Trying out the Softmax Algorithm in the same simulated deployment scenarios in which we tested the epsilon-Greedy algorithm:

```python
execfile("core.py")

import random

random.seed(1)
means = [0.1, 0.1, 0.1, 0.1, 0.9]

n_arms = len(means)
random.shuffle(means)
arms = map(lambda (mu): BernoulliArm(mu), means)
print("Best arm is " + str(ind_max(means)))

f = open("algorithms/softmax/standard_softmax_results.tsv", "w")

for temperature in [0.1, 0.2, 0.3, 0.4, 0.5]:
  algo = Softmax(temperature, [], [])
  algo.initialize(n_arms)
  results = test_algorithm(algo, arms, 5000, 250)
  for i in range(len(results[0])):
    f.write(str(temperature) + "\t")
    f.write("\t".join([str(results[j][i]) for j in range(len(results))]) + "\n")

f.close()
```

### Measuring the Performance of the Softmax Algorithm

Full comparisons between Softmax Algorithm and epsilon-Greedy algorithm in the next chapter.

Just a comparison of how Softmax Algorithm performs compared to the epsilon-Greedy algorithms using the Approaches used in the previous chapter.

Approach 1: Accuracy of the Softmax Algorithm (tendency to choose the best arm)
(((((Image)))))

It can be seen that it starts to select the right arm 100% of the time if waited long enough.
* epsilon-Greedy never got past the intrinsic errors caused by the purely random strategy it used.

Approach 2: Performance of the Softmax Algorithm (average reward per choice)
((((Image))))

Similar results to APproach 1, except that the rewards are capped at 0.9 instead of 1.0 because 0.9 is the expected rate of reward associated with the best arm in our test suite.

Approach 3: Cumulative Reward of the Softmax Algorithm
(((Image)))

Lowest temperature setting being used (0.1) gives the best results nearly right from the start
* Typical in settings in which there are clear differences between arms.
* Much less likely that the low level of exploration we're seeing will be ideal if the arms are closer together (in terms of rewards)

### Annealing Softmax Algorithm
Often a good idea to encourage an algorithm to explore less over time.
* In softmax algorithm, can be achieved by slowly decreasing the temperature (tau).

```
Annealing?

Annealing is a process in which the blacksmith slowly decreases the temperature
 at which he works with molten melt so that it becomes more solid and less
flexible. This is helpful for building stronger materials as the metal gets closer to its final,
desired shape.
```

Two equivalent definitions for the term annealing:
* process of decreasing the temperature in a Softmax algorithm over time
* process of modifying a bandit algorithm's behavior so that it will explore less over time.
  * The first definition is a special case of the second definition.

Full annealing strategy based on two simple lines of code:
```python
t = sum(self.counts) + 1
temperature = 1/ math.log(t + 0.0000001)
```

Initially temperature = 1/math.log(1.0000001), which is close to infinite.
* temperature is extremely high, and the system will explore (almost) completely randomly
* as t goes up, the temperature decreases.

**Logarithmically scaled versions aren't always the best approaches.**

The full implementation of annealing Softmax Algorithm:
```python
import math
import random

def categorical_draw(probs):
  z = random.random()
  cum_prob = 0.0
  for i in range(len(probs)):
    prob = probs[i]
    cum_prob += prob
    if cum_prob > z:
      return i
  return len(probs) - 1

class AnnealingSoftmax:
  def __init__(self, counts, values):
    self.counts = counts
    self.values = values
    return
 
  def initialize(self, n_arms):
    self.counts = [0 for col in range(n_arms)]
    self.values = [0.0 for col in range(n_arms)]
    return
 
  def select_arm(self):
    t = sum(self.counts) + 1
    temperature = 1 / math.log(t + 0.0000001)
 
    z = sum([math.exp(v / temperature) for v in self.values])
    probs = [math.exp(v / temperature) / z for v in self.values]
    return categorical_draw(probs)
 
def update(self, chosen_arm, reward):
  self.counts[chosen_arm] = self.counts[chosen_arm] + 1
  n = self.counts[chosen_arm]
 
  value = self.values[chosen_arm]
  new_value = ((n - 1) / float(n)) * value + (1 / float(n)) * reward
  self.values[chosen_arm] = new_value
  return
```

Results of the annealing Softmax Algorithm using the same approaches:

((image 1 ~ 3))

The system doesn't reach perfect performance as quickly as the best of our hard-coded temperature settings in the previous set of graphs.
* but reaches peak performance much faster than the worse of our settings
* And reaches a higher peak.
* NOT doing MUCH WORSE than the best case (in the previous hard coded case)
  * but only knew which temperature was best AFTER trying.
  * ..which is a nontrivial task for a system that needs to be deployed in the real world to be fully tested.
