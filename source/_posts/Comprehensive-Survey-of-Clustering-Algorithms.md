title: Comprehensive Survey of Clustering Algorithms
date: 2015-09-22 21:01:23
category: Machine Learning
tags: [Machine Learning, paper]
---
<!-- more -->

This paper is [A Comprehensive Survey of Clustering Algorithms][1].

## Classic definition of clusering
1. Instances, in the same cluster, must be similar as much as possible
2. Instances, in the different clusters, must be different as much as possitble
3. Measurement for similarity and dissimilarity must be clear and have the practical meaning

## Standard process of clustering
1. Feature extraction and selection: extract and select the most **representative** features
2. Clustering algorithm design
3. Result evaluation
4. Result explanation

## Internal evaluation indicators
1. Davies-Bouldin
$$
DB=\frac{1}{k}\sum\_{i=1}^k{max\_{i\neq j}\left(\frac{\sigma\_i + \sigma\_j}{d(c\_i + c\_j)}\right)}
$$
2. Dunn indicator
There must be some errors.


**To-do**
1. understand Mahalanobis distance, $\sqrt{(x\_i - x\_j)^T S^{-1} (x\_i - x\_j)}$, where S is the covariance matrix inside the cluster
2. one of the similarity functions, for data of mixed type, map the feature into (0, 1), transform the fature into dichotomous one $S\_{ij}=\frac{1}{d} \sum\_{l=1}^d{S\_{ijl}}$
3. 

[1]:http://link.springer.com/article/10.1007/s40745-0150-040-1 "A Comprehensive Survey of Clustering Algorithms"
