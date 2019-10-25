---
title: 'Power and Sample Size Calculation for Logistic Regression'
subtitle: "Part I: Analytical Method"
date: 2019-05-01
permalink: /posts/2019/05/power-samplesize-1/
tags:
  - power
  - sample size
  - logistic regression
---

## Introduction

There are two common ways to calculate sample size. One method is to run a precision analysis because sample size is closly related to the standard error and thus would affect the range of confidence interval. Researchers can calculate sample size based on a predefined precision level. Another method is to do a power analysis. In statistics, power is defined as the probability of rejecting a false null hypothesis. Previous simulation studies have shown that power has a positive association with sample size as well as effect size. If we assume the test statistic follows a standard normal distribution, sample size can then be calculated by the general formula (shown below), based on a predefine power size ($1-\beta$) and effect size ($ES$).  

$$ n = (\frac{Z_{1-\alpha/2} - Z_{1-\beta} }{ES})^2 $$

In the case of logistic regression, hypothesis can be tested by wald test, likelihood ratio test, or F test. Considering the simplisity and popularity of wald test, we will focus on the wald test here. 

Below is the setup of wald test.

* Hypotheses: $H_0: \beta_1 = 0$ ; $H_1: \beta_1 = \beta_A$

* Test Statistic: $\frac{\hat \beta_A - \beta_0}{se(\hat \beta_1)} \sim Z$

* Effect Size: $\frac{\beta_A - \beta_0}{sd(\beta_1)}$

Since the test statistic follows a $z$ distribution, we can use the general sample size formula and plug in the effect size:

$$n = (\frac{Z_{1-\alpha/2} - Z_{1-\beta} }{ES})^2 = \frac{(Z_{1-\alpha/2} - Z_{1-\beta})^2 * var(\beta_1)}{(\beta_A)^2 }$$


Clearly, the difficulty is to find out the variance of the slope ($\beta_1$). One way to deal with this problem is to use fisher information matrix. 

## Fisher Information Matrix Method

Assume the predictor variable $\mathbf{X}$ follows a normal distribution with mean equals to $\mu$ and variance equals to $\sigma^2$, if n is large, the maximum likelihood estimator of $\beta_i$ will follow a normal distribution, with mean equals to $\beta_i$ and variance equals to its inverse of fisher information matrix $\mathbf{I}^{-1}$. 

In logistic regression, we know: 

* $\theta = log(\frac{\pi}{1-\pi})$;
* $b(\theta) = log(1+e^\theta)$;
* $a(\phi) = \frac{1}{m}$.


Define: $\mathbf{W} = diag(\frac{1}{b''(\theta_i)(g'(\mu_i))^2})  =  diag(\pi_i(1-\pi_i)) = diag(\frac{e^{\beta_0 + \beta_1X_i}}{(1+e^{\beta_0 + \beta_1X_i})^2})$

Then fisher information matrix is:






$$\mathbf{I}= X^TWX = \left( \begin{array}{rr}
  E(\frac{e^{\beta_0+\beta_1X}}{(1+e^{\beta_0+\beta_1X})^2}) & E(\frac{Xe^{\beta_0+\beta_1X}}{(1+e^{\beta_0+\beta_1X})^2}) \\\\
  E(\frac{Xe^{\beta_0+\beta_1X}}{(1+e^{\beta_0+\beta_1X})^2}) & E(\frac{X^2 e^{\beta_0+\beta_1X}}{(1+e^{\beta_0+\beta_1X})^2})  
 \end{array} \right)$$
 
For simplification, we can denote each elements as $i_{00}$,$i_{01}$,$i_{10}$,$i_{11}$.  


$$
\left[ \begin{array}{rr}
    i_{00} & i_{01} \\\\ 
    i_{10} & i_{11}  
  \end{array} \right]
$$

If we take inverse of the matrix and the second diagnal element would be variance of slope. 

$$var(\hat \beta_1) = \frac{i_{00}}{i_{00}i_{11}-i_{10}^2}$$

Now we can plug it into the sample size formula.  

$$n = \frac{(i_{00}) *(Z_{1-\alpha/2} - Z_{1-\beta})^2 }{(i_{00}i_{11}-i_{10}^2)(\beta_A)^2 }$$

Still, from the formula, we can find that in order to solve sample size, we have to calculate each element in the fisher information matrix. Researchers have come up with two different ways. 

1. Hermite Polynomial Approximation

This is a method mentioned in Grieve, A. et al.(2016). Since $\mathbf{X}$ follows a normal distribution, they re-wrote each element in the fisher information matrix as the form of expectation of a function of $\mathbf{X}$, utilizing the normal density function and then applied Hermite polynomial approximation to solve it. 

$$\frac{1}{\sqrt(2\pi\sigma)}\int \frac{X^ke^{\beta_0+\beta_1X}}{(1+e^{\beta_0+\beta_1X})^2} e^{-\frac{1}{2 \sigma^2}(X-\mu)^2} dX (K = 0,1,2)$$

$$\sum_{i=1}^{N} \frac{W_i}{\sqrt \pi} \frac{(\sqrt{2}\sigma Z_i + \mu)^K e^{\beta_0+\beta_1(\sqrt{2}\sigma Z_i)+ \mu}}{(1+e^{\beta_0 + \beta_1(\sqrt{2}\sigma Z_i +\mu)})^2}$$


Here, $K$ is $0,1,2$, N is an integer selected by researcher. Bigger N generally gives more accurate result. Grieve, A. et al.(2016) used N = 20 in their paper because it is the maximum number provided by Abramowitz and Stegun(1984). $Z_i$ and $W_i$ are zeros and weights of Hermite polynomial approximation that can be found on published table. 


2. Whittemore’s Approximation

Whittemore(1981) developed a special case for low prevalence. If the prevalence is low, the probability of case ($P(Y=1)$) will be extremely small, and approach to 0. Therefore, the demoninator of logit (i.e., $P(Y=0)$) will be close to 1. As a result, the logistic model can be approximated by a log-linear model  


$$\pi (Y=1 | \beta_0, \beta_1, X) = \frac{e^{\beta_0+\beta_1X}}{1+e^{\beta_0+\beta_1X}} \approx e^{\beta_0+\beta_1X}$$

This method greatly facilitated computation of fisher information matrix. The sample size formula given by this method is:

$$N = \frac{(Z_\alpha + e^{-\frac{\beta_1^2}{4}} Z_\beta)^2 } {e^{\beta_0}\beta_1^2} * 
[1+2^{e ^ {\beta_0}} * \frac
{[1+(1+\beta_1^2)e^{\frac{5\beta_1^2}{4}}]}
{1+2e^{\beta_0}}]$$ 

## Two Sample T-test Method

1. Heieh et al.(1998)'s Approximation

Heieh et al.(1998) developed a sample size formula from a different angle. Instead of using fisher information matrix, they treated the test as a two sample T-test by assuming $P(X_1 \vert Y=1) \sim N(m_1,\sigma_1)$, $P(X_2 \vert Y=0) \sim N(m_2, \sigma_2)$, and $\sigma_1 = \sigma_2 = \sigma$. 

Define $\Delta$ is the difference between null hypothsised value and true value, then they obtained the sample size formula for equal sample size condition.
 
$$n = \frac{(Z_{1-\alpha/2}+ Z_{1-\beta})^2}{\frac{\Delta^2}{2(\sigma_1^2 + \sigma_2^2)}}$$ 

Considering that in logistic regression if $P(Y=1)$ and $P(Y=0)$ differs to each other, sample sizes of these two groups will be different, they multiplied a factor of $\frac{(K+1)^2}{4K}$ to the above formula for correction, where K is sample size ratio. In the case of logistic regression,$K = \frac{1-p}{p}$, and p is the probability evaluated at $x=0$ (i.e., $p = \frac{e^{\beta_0}}{1+e^{\beta_0}}$). The derivation is shown below.

$$n = \frac{(Z_{1-\alpha/2}+ Z_{1-\beta})^2}{\frac{\Delta^2}{2(\sigma_1^2 + \sigma_2^2)}} \frac{(K+1)^2}{4K}  = \frac{\sigma^2 (Z_{1-\alpha/2}+ Z_{1-\beta})^2 \frac{(K+1)^2}{K}}{\Delta^2}$$

$$\frac{(K+1)^2}{K} = \frac{(\frac{1-p}{p} + \frac{p}{p})^2} {\frac{1-p}{p}} = \frac{1}{p(1-p)}$$

Considering that the slope is the change in log odds for every 1 unit increase of X, Heieh et al.(1998) further defined slope $\beta^*$as $\frac{\Delta}{\sigma}$, and finalized the sample size formula below:

$$n = \frac{(Z_{1-\alpha/2}+ Z_{1-\beta})^2}{p(1-p){\beta^*}^2}$$


2. Novikov et al.(2010)'s Modification

Novikov et al.(2010) commented that Heieh et al.(1998)'s method requires four assumptions and none of them actually hold in practice. Those four assumptions are:

* Conditional distributions of $P(X_1 \vert Y=1)$ and $P(X_2 \vert Y=0)$ are normally distributed.
* Variances of two groups are equal, i.e., $\sigma_1 = \sigma_2$.
* Slope equals to standardized difference in the conditional means, i.e.,$\frac{\Delta}{\sigma} = \beta^*$.
* Conditional distribution have zero skewness. 

Thus, Novikov et al.(2010) used Schouten’s sample size formula as modification. They broke the sample size into two parts $n_1$ and $n_0$. The final sample size is approximated by summing those two parts up.

$$n_1 = (Z_{1-\alpha/2}+ Z_{1-\beta})^2 \frac{(\tau + \gamma)S_1^2}{\gamma (m_1 - m_2)^2} + \frac{(\tau^2 + \gamma^3)Z_{1-\alpha/2}}{2 \gamma (\tau + \gamma)^2}$$

$$n_0 = \gamma n_1$$

$$n = n_1 + n_0$$
Where: $\gamma = \frac{ sample\, size\, of group \,0}{sample\,size\,  of\, group\, 1}$; $\tau = \frac{sample \,variance \, of\, group\, 0}{sample \,variance \, of\, group \,1}$.


## Disadvantages of Analytical Approximation Methods 

The disadvantages are very obvious. 

1. As mentioned in title, these methods are all approximation methods. None of them can give accurate result. Even worse, those methods vary in their ability to determine accurately sample size. Simulation studies have shown that the hermite polynomial method tends to overpower by up to 16%. Whittemore(1981)'s method only works for pervalence that is no greater than 27%. Hsieh et al.(1998)'s method has 50% reduction in the targeted power. Novikov et al.(2010)'s method could underpower by 5% and may also overpower by 10%.

2. Those approximation formulas only takes one predictor varible so they can only deal with sample size calculation for simple logistic regression. Deriving formulas for multiple logistic regression will be a much more challenging job. 




### Reference

Grieve, A. P., & Sarker, S. J. (2016). Simulation‐based sample‐sizing and power calculations in logistic regression with partial prior information. Pharmaceutical statistics, 15(6), 507-516.

Novikov, I., Fund, N., & Freedman, L. S. (2010). A modified approach to estimating sample size for simple logistic regression with one continuous covariate. Statistics in medicine, 29(1), 97-107.

Demidenko, E. (2007). Sample size determination for logistic regression revisited. Statistics in medicine, 26(18), 3385-3397.

Abramowitz M, Stegun IA. (1984) Pocketbook of mathematical functions (abridged edition of handbook of mathematical functions). Harri Deutsch Verlag: Thun.

Whittemore, A. S. (1981). Sample size for logistic regression with small response probability. Journal of the American Statistical Association, 76(373), 27-32.

Hsieh, F. Y., Bloch, D. A., & Larsen, M. D. (1998). A simple method of sample size calculation for linear and logistic regression. Statistics in medicine, 17(14), 1623-1634.
