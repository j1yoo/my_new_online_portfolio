---
layout: post
title: "Hand-Rolling OLS Using R"
categories: Research
tag: Research
---

To illustrate how to calculate ordinary least squares (OLS) by hand, I am going to use the dataset provided by Nerlove (1963) "Returns to Scale in Electricity Supply" which is publically available at EconPapers (<https://econpapers.repec.org/paper/bocbocins/nerlove63.htm>).

Let's first import the data into R using [the 'foreign'](https://cran.r-project.org/web/packages/foreign/foreign.pdf) package and take a glance.

```r
nerlove63 <- foreign::read.dta("http://fmwww.bc.edu/ec-p/data/hayashi/nerlove63.dta")
tail(nerlove63, 5) # print the last five observations 
```

```
##     totcost output plabor pfuel pkap
## 141  44.894   9956   1.68  28.8  203
## 142  67.120  11477   2.24  26.5  151
## 143  73.050  11796   2.12  28.6  148
## 144 139.422  14359   2.31  33.5  212
## 145 119.939  16719   2.30  23.6  162
```

The dataset contains five variables: cost (i.e., total cost in million `$`), output (i.e., quantity (Q) in billion kilowatt hours), pLabor (i.e., unit price of labor or wage rate in `$` per hour), pCapital (i.e., unit price of capital), and pFuel (i.e., unit price of fuels in cent per million British thermal unit). Note that it is a firm-level cross-sectional data that comprises of observations from 145 American electric utility companies in 1955.

**Nerlove (1963) posits the following production function (this follows [the Cobb-Douglas](https://en.wikipedia.org/wiki/Cobb%E2%80%93Douglas_production_function) specification):**

\\[Q = A L^{\\alpha_1} K^{\\alpha_2} F^{\\alpha_3}\\]

where Q is the output, L is labor, K is capital, F is fuel. Taking logs and re-arranging the terms, we get:

\\[\\log Q = \\log A + \\alpha_1 \\log L + \\alpha_2 \\log K + \\alpha_3 \\log F + \\epsilon\\]

Since we do not observe A, L, K, and F but only observe output, labor and capital costs, total cost, and the prices of labor, capital, and fuel, we need to rearrange the cost minimization problem. Given perfect competition, cost minimization combined with the production function, the firm's cost function would take the form:

\\[\\log C = \\log A' + \\gamma_1 \\log Q + \\gamma_2 \\log p_L + \\gamma_3 \\log p_K + \\gamma_4 \\log p_F + \\epsilon\\]

where \\(\\gamma_1 = 1/(\\alpha_1 + \\alpha_2 + \\alpha_3)\\), \\(\\gamma_2 = \\alpha_1/(\\alpha_1 + \\alpha_2 + \\alpha_3)\\), \\(\\gamma_3 = \\alpha_2/(\\alpha_1 + \\alpha_2 + \\alpha_3)\\), and \\(\\gamma_4 = \\alpha_3/(\\alpha_1 + \\alpha_2 + \\alpha_3)\\).

If \\(\\alpha_1 + \\alpha_2 + \\alpha_3 = 1\\) (constant returns to scale), then \\(\\gamma_1 = 1\\). On the other hand, if \\(\\alpha_1 + \\alpha_2 + \\alpha_3 > 1\\) (increasing returns to scale), then \\(\\gamma_1 < 1\\). If \\(\\alpha_1 + \\alpha_2 + \\alpha_3 < 1\\) (decreasing returns to scale), then \\(\\gamma_1 > 1\\).

Let me now implement the log-linearized cost function estimation using the previously loaded data by hand. I am going to use the following log-linearized cost function:

\\[\\log(totcost) = \\beta_0 + \\beta_1 \\log(output) + \\beta_2 \\log(plabor) + \\beta_3 \\log(pkap) + \\beta_4 \\log(pfuel) + \\epsilon\\]

## Hand-Rolling OLS

Let's first take logs and create the design matrix X and the outcome vector y.

```r
library(magrittr)

# Let's take logs
logData <- log(nerlove63) %>% data.frame()
# rename variables for convenience
names(logData) <- c("logC", "logQ", "logPL", "logPF", "logPK")

# Let's create the design matrix (X)
X <- cbind(intercept = rep(1, nrow(logData)), logData[, -1])

# Let's create the outcome vector (y)
y <- logData[, 1] 

head(X, 5)
```

```
##   intercept     logQ    logPL    logPF    logPK
## 1         1 4.276666 1.131402 3.931826 4.406719
## 2         1 6.238325 1.070145 3.931826 4.369448
## 3         1 6.886532 1.098612 3.931826 4.394449
## 4         1 7.313220 1.609438 3.931826 4.394449
## 5         1 7.600902 1.504077 3.931826 4.394449
```

```r
head(y, 5)
```

```
## [1] 1.740466 2.564949 2.772589 3.091042 3.135494
```

Now let's manually perform the OLS estimation. Recall that the OLS estimator is defined as \\(\\hat{\\beta} = (X'X)^{-1}X'y\\).

```r
# Transpose of the design matrix: X'
Xt <- t(X)

# (X'X)^{-1}
XtX_inv <- solve(Xt %*% X)

# (X'X)^{-1}X'
XtX_inv_Xt <- XtX_inv %*% Xt

# beta_hat = (X'X)^{-1}X'y
beta_hat <- XtX_inv_Xt %*% y

print(beta_hat)
```

```
##                [,1]
## intercept -4.648257
## logQ       0.720820
## logPL      0.436240
## logPF      0.426359
## logPK      0.219626
```

Let's also calculate the fitted values and the residuals.

```r
# fitted values: y_hat = X * beta_hat
y_hat <- X %*% beta_hat 

# residuals: e = y - y_hat
e <- y - y_hat

head(y_hat, 10)
```

```
##           [,1]
##  [1,] 1.739022
##  [2,] 2.596848
##  [3,] 2.753745
##  [4,] 3.088636
##  [5,] 3.105847
##  [6,] 3.267154
##  [7,] 2.992733
##  [8,] 3.267154
##  [9,] 3.293624
## [10,] 3.403815
```

```r
head(e, 10)
```

```
##            [,1]
##  [1,]  0.001443537
##  [2,] -0.031899071
##  [3,]  0.018844133
##  [4,]  0.002406003
##  [5,]  0.029646913
##  [6,] -0.052242765
##  [7,] -0.055426806
##  [8,] -0.079002765
##  [9,] -0.063033008
## [10,] -0.095324421
```

Now let's calculate the variance-covariance matrix of the estimator. Recall that \\(Var(\\hat{\\beta}) = \\sigma^2 (X'X)^{-1}\\). We need to first calculate \\(\\hat{\\sigma}^2 = \\frac{e'e}{n-k}\\).

```r
# sigma_hat_squared = sum of squared residuals / degrees of freedom 
sigma_hat_squared <- sum(e^2) / (nrow(X) - ncol(X))

# variance-covariance matrix of the estimator
var_beta_hat <- as.numeric(sigma_hat_squared) * XtX_inv

print(var_beta_hat)
```

```
##              intercept        logQ       logPL       logPF       logPK
## intercept  0.171334065 -0.01994041 -0.02779829 -0.02779829 -0.02631336
## logQ      -0.019940413  0.00290417  0.00173978  0.00173978  0.00177936
## logPL     -0.027798290  0.00173978  0.01712086  0.00485714  0.00424429
## logPF     -0.027798290  0.00173978  0.00485714  0.01712086  0.00424429
## logPK     -0.026313364  0.00177936  0.00424429  0.00424429  0.01100980
```

From the above, we can calculate the standard errors of individual parameter estimates by taking the square root of the diagonal entries.

```r
# standard errors 
se_beta_hat <- sqrt(diag(var_beta_hat))

print(se_beta_hat)
```

```
##  intercept       logQ      logPL      logPF      logPK 
## 0.41391449 0.05389044 0.13086062 0.13086062 0.10492279
```

Let's calculate t-stat for testing \\(H_0: \\beta_j = 0\\) vs. \\(H_1: \\beta_j \\neq 0\\).

```r
# t-stat for testing H0: beta_j = 0 vs. H1: beta_j ≠ 0
t_stat <- beta_hat / se_beta_hat

print(t_stat)
```

```
##              [,1]
## intercept -11.235
## logQ       13.374
## logPL       3.334
## logPF       3.258
## logPK       2.093
```

## Let's verify our work using lm()

```r
fitted_model <- lm(logC ~ logQ + logPL + logPF + logPK, data = logData)
summary(fitted_model)
```

```
## 
## Call:
## lm(formula = logC ~ logQ + logPL + logPF + logPK, data = logData)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.47792 -0.07936 -0.00658  0.08699  0.34952 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -4.64826    0.41391 -11.235  < 2e-16 ***
## logQ         0.72082    0.05389  13.374  < 2e-16 ***
## logPL        0.43624    0.13086   3.334 0.001062 ** 
## logPF        0.42636    0.13086   3.258 0.001399 ** 
## logPK        0.21963    0.10492   2.093 0.038020 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.1456 on 140 degrees of freedom
## Multiple R-squared:  0.9255,	Adjusted R-squared:  0.9234 
## F-statistic: 434.5 on 4 and 140 DF,  p-value: < 2.2e-16
```

As expected, our hand-calculated estimates match the output of lm()! According to the result, \\(\\hat{\\gamma}_1 \\approx 0.72\\) which implies that American electrical utilities exhibited increasing returns to scale in 1955. In other words, \\(\\hat{\\gamma}_1 = 0.72 < 1\\) which indicates that \\(\\alpha_1 + \\alpha_2 + \\alpha_3 > 1\\).

##### Created on Feb. 13, 2019