title: AMA_Geoffrey_Hinton
date: 2014-11-16 15:07:34
categories: Machine Learning
tags: [famous]
---
### Are we any closer to understanding biological models of computions?
...We are probably a long way from understanding how the brain does this...I think that the way we currently use an unstructured layer of artificial neurons to model a cortical area is utterly crazy.

### What is your most controversial opinion in machine learning?
...The pooling operation used in convolutional neural networks is a big mistake and the fact that it works so well is a disaster.

### Are there diminishing returns for data at Google scale?
It depends how your learning methods scale.For example, if you do phrase-based translation that relies on having seen particular phrases before, you need hugely more data to make a small improvement.If you use recurrent neural nets, however, the marginal effect of extra data is much greater.

### What are the reeatest obstacles RBM/DBMs face and can we expect to overcome them in the near future?
...One big question for RBM's was how to stack them in such a way that you get a deep Boltzmann Machine rather than a Deep Belief Net...I think the biggest current obstacle is that almost everyone is doing supervised learning by predicting the next frame in a sequence for recurrent nets or by using big labelled datasets for feed-forward nets.This is working so well that most people have lost interest in generative models...

### What are your thought on DeepMind's Neural Turing Machines?Is this a promising approach?
...It is very impressive that they can get an RNN to invent a sorting algorithm...it will be combined with reinforcement learning.

### How do you see mathematics, and how do you think it fits in machine learning?
...I use  mathematics to justify a conclusion after I have figured out what is going on by using physical intuition.