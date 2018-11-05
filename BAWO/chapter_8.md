# Chapter 8
## Conclusion 
### Learning Life Lessons from Bandit Algorithms 

Three algorithms presented for solving the Multiarmed Bandit Problem:
* The epsilon-Greedy algorithm
* The Softmax Algorithm
* The UCB Algorithm

Important to have an intuition about which algorithm will work well in practice
* Use Monte Carlo simulation to help develop the intuition & judgement
* Use many other hypothetical worlds as well

General Life Lessons exemplified by bandit algorithms:
1. Tradeoffs

Always have to trade off between gathering data and acting on that data. Pure experimentation (exploration) is always a short-term loss, but pure profit-making(exploitation) is blind to the long-term benefits of curiosity and openmindedness.

2. God does play dice

Randomization is key to good life. Controlled experiments won't work without randomization. UCB algorithms are't truly randomized, but behave partially randomly in the user's point of view. 

3. Defaults matter a lot

Initialization to the algorithm is important, may have powerful effect on its long term success. Figure out whether biases are hurting you or helping you. 

4. Take a chance

Exploration, in the aspect that you know a little bit about the potential of every option, is important. Also, just one experience should be enough to convince you that same mistakes aren't worth repeating.

5. Everybody's gotta grow up sometime

Explore less over time. Don't spend your whole life trying out every crazy idea that comes into your head.

6. Leave your mistakes behind

Direct explorations to focus on the second/third-best options that are just a little further away from the best. Don't waste much (or any!) of your time on clearly losing options.

7. Don't be cocky

Keep track of how CONFIDENT you are about your evaluations of each of the options available to you. Don't be close minded when you don't have evidence to support your beliefs. (UCB)

8. Context matters

Use any and every piece of information you have available to you about the context of your experiments. Don't simplify the world too much and pretend you've got things figured out. 

### Taxonomy of Bandit ALgorithms

Six dimensions along which you can measure bandit algorithms:
1. Curiosity

Does the algorithm keep track of how much it knows about each arm? Does the algorithm try to gain knowledge explicitly, rather than incidentally? Is the algorithm curious?

2. Increased Exploitation over Time

Does the algorithm explicitly try to explore less over time? In other words, does the algorithm use annealing?

3. Strategic Exploration

What factors determine the algorithm’s decision at each time point? Does it maximize reward, knowledge, or a combination of the two?

4. Number of Tunable Parameters

How many parameters does the algorithm have?
Since you have to tune these parameters, it’s generally better to use algorithms that have fewer parameters.

5. Initialization Strategy

What assumptions does the algorithm make about the value of arms it has not yet explored?

6. Context-Aware

Is the algorithm able to use background context about the value of the arms?

### Learning more and other Topics

* Reinforcement Learning: An Introduction (book)
* The Nonstochastic Multiarmed Bandit Problem (paper) - Exp3, Exp4
* A knowledge-gradient policy for sequential information collection (paper) - Knowledge Gradient
* A modern Bayesian look at the multiarmed bandit (paper) - Randomized Probability Matching
* An Empirical Evaluation of Thompson Sampling (paper) - Thompson Sampling
* A Contextual-Bandit Approach to Personalized News Article Recommendation (paper) - LinUCB
* Parametric Bandits: The Generalized Linear Case (paper) - GLMUCB
* Sequential Decision Making in Non-stochastic Environments (paper) - bandit algorithm review papers
* Online Learning and Online Convex Optimization (paper) - bandit algorithm review papers
* Learning for Contextual Bandits (paper) - Yahoo Bandit
* Contextual-Bandit Approach to Personalized News Article Recommendation (paper) - Yahoo Bandit
