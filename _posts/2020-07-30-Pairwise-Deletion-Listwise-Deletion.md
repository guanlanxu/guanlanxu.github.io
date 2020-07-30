---
title: "Pairwise Deletion v.s. Listwise Deletion"
date: 2020-07-30
permalink: /posts/2020/07/Pairwise-Deletion-Listwise-Deletion/
tags:
  - missing data
  - pairwise deletion
  - listwise deletion
  - reliability
  - coefficient alpha
---



This summer I am very fortunate to have the chance to validate a newly developed statistical software. One thing I learned is the differences between pairwise deletion and listwise deletion. When both of these two methods are common practices in taking care of missing values, results can be different and choosing which method to use requires more consideration. 

Listwise deletion means to remove the observation entirely from the data if it includes missing values. After listwise deletion, the data will contain no missing values, therefore, subsequent analyses can be applied on this smaller yet complete data. An obvious drawback of this practice is that it can lead to a great loss of data. If many observations contain missing values, listwise deletion will remove those observations entirely out and result in a fairly small dataset. Inadequate dataset can make subsequent analyses inappropriate or unreliable.   

Different form listwise deletion, pairwise deletion still includes the observations with missing values into analysis. When the analysis involves pairs of values (e.g., correlation or covariance), the pairs that have missing values will be removed. The first impression of pairwise deletion is that it can serve as a direct remedy for listwise deletion's data-loss issue by taking into account as much data as possible. However, for some analysis, this practice can be problematic. 

An example here is reliability analysis: the coefficient alpha. Coefficient alpha is calculated by

$\frac{m}{m-1} *(\frac{\sum_{i \neq j}^m cov(x_i,x_j)}{var(x_{r})})$ 

where $m$ is number of items,$x_i$ is responses to $i^{th}$ item, $x_r$ is sum score from $x_1$ to $x_m$. This formula involves both covariances and variances in computation. Pairwise deletion will remove some pairs in computing covariances, however, used all available data in computing variances, which makes the nominator and denominator be inconsistent with each other, as a result, lowered the precision of the result. Paul D. Allison (2002) also noted that pairwise deletion may make correlation matrix not positive definite.

Pairwise deletion should be used in caution. Many software, such as SAS or SPSS, has make listwise deletion as the default. However, one should note that this is not the ultimate solution of handling missing values. Except for data-loss issue, listwise deletion may also bring bias if missing is not completely at random. Unfortunately, this missing mechanism most commonly happens in real life. This is where various imputation methods come in.  




## Reference 

Allison Paul, D. (2002). Missing Data: Quantitative Application in Social Sciences. British Journal of Mathematical and Statistical Psychology, 55(1), 193-96.


