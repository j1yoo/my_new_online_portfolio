---
layout: post
title: "Multi-Armed Bandit (MAB) as an Alternative to A/B Testing: a Simulation using R"
categories: Research
tag: Research
---

Imagine an e-commerce retailer that wants to optimize its checkout process or an advertising agency that is trying to make an optimal advertising decision. This is often done in a *test-and-learn* framework via what is popularly known as A/B testing. In 2019, nearly half of the firms in the US (\\(\\approx 44\\%\\)) were using A/B testing softwares ([Saleh 2020](https://www.invespcro.com/blog/the-state-of-ab-testing/)). In an A/B test, firms equally / randomly divide their customer base into two or more groups each of which are assigned to different versions of what is being tested, e.g., layouts in a landing page or ad versions (explore phase). After finishing the test, firms then use the best performing version of ad / layout (exploit phase). What this means is a large number of customers will be served a suboptimal variant during the test phase which inevitably generates an opportunity cost. Firms can overcome this apparent limitation and do better by *earning-while-learning* using MAB ([Schwartz et al. 2017](10.1287/mksc.2016.1023)).

MAB is a classic adoptive experimentation & a dynamic optimization problem (Robin 1952; Thompson 1933). In a business context, MAB can be used to answer questions like "how should advertising agencies decide what percentage of impressions to allocate to each digital ad version on an ongoing basis to maximize earning while learning?" At its core, these types of questions involve a trade-off between reward later (i.e., exploration) and reward now (i.e., exploitation). MAB alghrithms (e.g., \\(\\varepsilon\\)-greedy, UCB, Thompson sampling) attempt to strike a balance between the two in a real-time basis to maximize the cumulative reward (as denoted below) while continuing to search for the options that may perform better in the future.

## The Setup

Let's first set the length of an individual simulation, \\(t = {1,\\dots, T}\\) (i.e., horizon), and how many times we are going to repeat each simulation over \\(t = {1, \\dots, T}\\) (i.e., simulations), which will be used in the simulator() function in ['contextual'](https://cran.r-project.org/web/packages/contextual/contextual.pdf). In this simulation, I will consider five alternative ad types each of which have conversion probabilities of 5%, 20%, 25%, 10%, and 15%.

```r
horizon <- 1000L
simulations <- 1000L
conversionProb <- c(0.05, 0.20, 0.25, 0.10, 0.15)
nTestSample <- 0.5 * horizon
clickProb <- rep(NA, simulations)
adDistMatrix <- matrix(NA, nrow = simulations, ncol = length(conversionProb))
adDistMatrixAB <- matrix(NA, nrow = simulations, ncol = length(conversionProb))
```

## Simulating A/B Testing

```r
for(i in 1:simulations){  
  testSample <- sapply(conversionProb,                      
                       function(x) sample(0:1, nTestSample, replace = TRUE, prob = c(1 - x, x)))      
  testColumns <- (1:length(conversionProb))[-which.max(colSums(testSample))]      
  p.values <- sapply(testColumns, function(x) prop.test(x = colSums(testSample[, c(x, which.max(colSums(testSample)))]),
                                                        n = rep(nTestSample, 2))$p.value)      
  adsAfterABTest <- (1:length(conversionProb))[- testColumns[which(p.values < 0.05)]]      
 
  # now just with the best performing ad(s)  
  ABSample <- sapply(conversionProb[adsAfterABTest],                        
                     function(x) sample(0:1, round((horizon - nTestSample) * length(conversionProb) /                                                  length(adsAfterABTest), 0), replace = TRUE, prob = c(1 - x, x)))      
  clickProbTest <- sum(as.vector(testSample)) / length(unlist(testSample))  
  clickProbAB <- sum(as.vector(ABSample)) / length(unlist(ABSample))      
  clickProb[i] <- clickProbTest * (nTestSample / horizon) + clickProbAB * (1 - nTestSample / horizon)      
 
  # distribution of which ads were seen over the course of all trials  
  adDistMatrix[i,] <- rep(1 / length(conversionProb), length(conversionProb))  
  adDistributionAB <- rep(0, length(conversionProb))  
  adDistributionAB[adsAfterABTest] <- rep(1 / length(adsAfterABTest), length(adsAfterABTest))  
  adDistMatrixAB[i,] <- adDistributionAB    
}
```

## Calculating the Total Payoff

```r
# Total Payoff from A/B Testing
ABPayoff <- (nTestSample * clickProbTest) + (nTestSample * clickProbAB)
print(ABPayoff)
```

```
## [1] 194.6
```

## Simulating MAB using \\(\\varepsilon\\)-Greedy algorithm

```r
library(contextual)
bandit <- BasicBernoulliBandit$new(weights = conversionProb)
policy <- EpsilonGreedyPolicy$new(epsilon = 0.10)
agent <- Agent$new(policy, bandit)

#historyEG <- Simulator$new(agent, horizon, simulations)$run()
# plot(historyEG, type = "arms",
#      legend_labels = c('Ad Type 1', 'Ad Type 2', 'Ad Type 3', 'Ad Type 4', 'Ad Type 5'),
#      legend_title = 'Epsilon Greedy',
#      legend_position = "topright",
#      smooth = TRUE)
#summary(historyEG)

print(bandit)
```

```
## <environment: 0x7fa5535b2b00>
```

```r
print(policy)
```

```
## <environment: 0x7fa5537523b8>
```

```r
print(agent)
```

```
## <environment: 0x7fa55500c828>
```

##### Created on Nov. 20, 2021