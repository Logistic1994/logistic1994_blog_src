title: Experience of data science
date: 2015-09-14 23:51:56
category: Machine Learning
tags: [Machine Learning]
---
> Original post is [关于数据科学，书上不曾提及的三点经验][1].

Algorithms are important as we all know. But in practice, we should never know how such an algorithm is coded, for they all have been packed already. Most of us even have never implemented such complex toys, such as SVM etc. So what really counts?

## Evaluation Methods
Our models could be invalid at any time! If simply getting half of dataset to be trainning set, while others to be test set, most of time we could get good models. But when data is sensitive to time, or other things, models can be very werid. For example, dresses are hot in June, cold in Feb.

At the other case, when there is high correlationship among the dataset, Stock as an example... we all know. 

So we should get evaluation methods as good as possible, just to avoid some worst cases.

## Feature Engineering
Models are simple even **ugly** tools for TB-level data. 
Features are arts. 
Features are arts.
Features are arts.
(Important things should be repeated for 3 times.)

## Model parameters should spend most of the time
**I should need more time to understand that.**

[1]:http://www.csdn.net/article/2015-09-10/2825668 "关于数据科学，书上不曾提及的三点经验"