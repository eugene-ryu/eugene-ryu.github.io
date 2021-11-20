---
title: "[NLP] Convolutional Neural Networks for Sentence Classification"
excerpt: "CNN for Sentence Classification 논문 리뷰"

categories: 
  - Blog
tags:
  - [machine learning, paper, review, NLP, CNN, convolution, natural language processing]

use_math: true
toc: true
toc_sticky: true

date: 2019-07-05
last_modified_at: 2021-11-20
---

# Convolutional Neural Networks for Sentence Classification 


## Novelty
1) very fast and strong with a single CNN layer<br>
(the results of former papers that used CNN was not quite good at NLP tasks)<br>
2) use pre-trained word vectors (it is a google negative300.bin)<br><br>


## A Summary of Abstract & Model Architecuture
```
We report on a series of experiments with CNN trained on top of pre-trained word vectors for sentence-level classification tasks. We show that a simple CNN with little hyperparameter tuning and static vectors achieves excellent results on multiple benchmarks.
```
<br>

![model_architecture](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FQJsve%2FbtqDO2F3iyf%2FnY0JZGSJKzy202pYxOjJt0%2Fimg.png)<br><br>

## A Matrix for Word Vectors
$\mathbf{x}$ : input vector<br>
$H$ : a filter size (h=[2,3,5])<br>
$N$ : a number of vocabulary<br><br><br>


### Preprocess
1) Concatenate a word vector to make a lookup table<br>
$\mathbf{x}_{i}$ :  a word vector<br><br>
![x_i_to_n](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FmMxbT%2FbtqDNmSM9JX%2Fnefwvk6MPwviiQq80v8RM0%2Fimg.png)<br>

   ⊕: concatention operator<br>
   $\mathbf{x}_{i+j}$ : the concatenation of words $\mathbf{x}_{i}, \mathbf{x}_{i+1}, ... ,\mathbf{x}_{i+j}$<br><br>
2) Conduct a convolution operation with filter w to make a feature $c_{i}$<br>
$\mathbf{w}$ : a filter for convolution operation<br><br>
![w](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbs1RhX%2FbtqTfCyQtX3%2FkrhwwrqVQvTX9jONnFOB11%2Fimg.png)<br><br><br>

   $h$: a window of h words (to produce a new feature)<br>
   $k$: the dimension of a word vector (in this paper, it is 300)<br><br>
![k](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FKfb0p%2FbtqThZN25IL%2FKQTOLoQ03mEzn7XsV2sZrk%2Fimg.png)<br><br>

   $\mathbf{c}_{i}$ : a feature (generated from a window of words $\mathbf{x}_{i:i+h-1}$ by the above equation)<br>
   $f$ : a non-linear function<br><br><br>


3) Create a feature map with a feature $\mathbf{c}_{i}$<br><br>
![c_(i)](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb7dydM%2FbtqTfBUeVae%2Fhi21FZKpBkMgFjltA9bok1%2Fimg.png)<br><br>
$\mathbf{c} \in \mathbf{R}^{n-h+1}$<br><br><br>

4) apply a max-over-time pooling over the feature map $\hat{c}$<br><br>
![c](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FkH6S1%2FbtqS9ZH7dtQ%2F7HZZ4aHDRwtivpSOY2fJ10%2Fimg.png)<br><br>

the maximum value $\hat{c}$ as the feature corresponding to this particular filter.
(The idea is to capture the most importance feature - one with the highest value - for each feature map.)<br><br>

These features form the penultimate layer and are passed to a fully connected softmax layer whose output is the probability distribution over labels. (by using dropout and L2-norms, the penultimate layer has created)<br><br>
![the equation of penultimate layer](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FtOQJ5%2FbtqTfClxdSp%2FjROYJdM1Nx7hkXOCCXApO1%2Fimg.png)
<br><br><br>


## Experimental Setup

![Datasets](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F9G61a%2FbtqTjDxs1eI%2FW6vIhszSrVxR7Ldof4p1ek%2Fimg.png)<br><br><br>


## Hyperparameters and Training
- mini batch size: 50 (shuffled, and randomly select 10% of training data as the dev set)<br>
- gradient check: SGD + adadelta<br>
- filter windows h = [3,4,5] with 100 feature maps each<br>
- activation function: ReLU<br>
- dropout rate: 0.5<br>
- L2 constraint: s = 3<br>
- pre-trained vectors : Google negative300.bin<br>
- out-of-vocabulary word: randomly and uniformly initialized vector<br>
- cv-fold: 10<br><br><br>

## Model Variants
1) CNN-rand : all word vectors has randomly initialized.
2) CNN-static: all word vectors are not changed during training<br>
3) CNN-non-static: all word vectors are changed while training<br>
4) CNN-multi-channel: a part of word vectors are not changed and others are changed during training<br><br><br>


## Multi-Channel  vs. Single Channel Models
Multi-channel model showed a good performance that compare to single channel models. However, the performance results were mixed of multi and single channel models.<br><br><br>


## Static vs. Non-static Representations
![static_vs_nonstatic](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb5qZ2p%2FbtqS4NhqsED%2FkQK2AtqjSDal5YW4NWgY91%2Fimg.png)