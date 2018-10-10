# Preface

- Every algorithm described is illustrated in Python
- Code available (in Python & Julia) [here](https://github.com/johnmyleswhite/BanditsBook)

## Jargons Used throughout this book

1. ***Reward***

**Quantitative** measure of success.
More Reward is always better than Less Reward.

2. ***Arm***

The options available to us.
(History behind this name to be explained)

3. ***Bandit***

Collection of *Arm*s.
When many options(arms) are available, the collection of options are called a *Multiarmed Bandit*.

4. ***Multiarmed Bandit***

Mathematical model used to reason about how to make decisions when:
* There are many actions that can be taken
* You have imperfect information about the rewards received after the actions are taken

The problem of choosing 'arms' depending on the situation is referred to as the Multiarmed Bandit Algorithm.

5. ***Play/Trial***

When dealing with a bandit, it's assumed that you get to pull on each arm multiple times.
Play/Trial = Each chance to pull n an arm

6. ***Horizon***

The number of trials(plays) left before the game is finished.
The strategy to be used may differ depending on the horizons left.

7. ***Exploitation***

An algorithm for solving the Multiarmed Bandit Problem *exploits* if it plays the arm with the **highest estimated value** based on the previous plays.

8. ***Exploration***

An algorithm for solving the Multiarmed Bandit Problem *explores* if it plays any other arm that does not have the highest estimated value based on the previous plays.
Thus, whenever exploitation does not occur, exploration occurs.

9. ***Explore/Exploit Dilemma***

The problem on the compromise between the impulse to explore and the impulse to exploit.

10. ***Annealing***

An algorithm for solving the Multiarmed Bandit Problem anneals if it **explores less over time**.

11. ***Temperature***

A **parameter** that can be adjusted to increase the amount of exploration (in the Softmax algorithm) for solving the Multiarmed Bandit Problem.
If the temperature is decreased over time, the algorithm **anneals**.

12. ***Streaming Algorithms***

An algorithm that can process data one piece at a time.
Opposite of batch processing algorithms (require access to all of the data before operating on the data).

13. ***Online Learning***

Algorithm that cannot only process data one piece at a time (not streaming), but can also provide provisional results of its analysis after each piece of data is seen.

14. ***Active Learning***

Algorithm that can decide which piece of data it wants to see next in order to learn mot effectively.

15. ***Bernoulli***

A Bernoulli system outputs a 1 with probability p and a 0 with probability (1 - p).
