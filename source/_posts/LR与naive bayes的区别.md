title: LR与naive bayes的区别
date: 2015-09-11 14:20:36'
category: Machine Learning
tags: [LR, naive bayes, summary]
---
## 相同点
1. 都是对特征的线性表达
$$\sum\_j \theta\_j x\_j$$
区别在与两者所fit的parameters不同
2. 都是通过条件概率建模
$$P(y=k|x)$$

## 不同点
1. LR对于特征间的相关性不会像naive bayes那么敏感，所以LR在特征工程方面的工作量会少一点
2. naive bayes没有优化参数这一步，什么样的模型就出来什么样的结果
3. LR是判别模型，而naive bayes是生成模型，生成模型在有prior数据的情况下效果会更好
