# Preface

- Every algorithm described is illustrated in Python
- Code available (in Python & Julia) [here](https://github.com/johnmyleswhite/BanditsBook)

## Jargons Used throughout this book

*Reward*
**Quantitative** measure of success.
More Reward is always better than Less Reward.

*Arm*
The options available to us.
(History behind this name to be explained)

*Bandit*
Collection of *Arm*s.
When many options(arms) are available, the collection of options are called a *Multiarmed Bandit*.

*Multiarmed Bandit*
Mathematical model used to reason about how to make decisions when:
* There are many actions that can be taken
* You have imperfect information aobut the rewards received after the actions are taken
The problem of choosing 'arms' depending on the situation is referred to as the Multiarmed Bandit Algorithm.

*Play/Trial*
When dealing with a bandit, it's assumed that you get to pull on each arm multiple times.
Play/Trial = Each chance to pull n an arm


