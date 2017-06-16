---
title: Cross Validation (An implementation to find the optimal parameters for LIBSVM)
tags: [SVM, Machine Learning]
categories: blog
---

##Brief Introduction
Cross-validation, sometimes called rotation estimation, is a model validation technique for assessing how the results of a statistical analysis will generalize to an independent data set. It is mainly used in settings where the goal is prediction, and one wants to estimate how accurately a predictive model will perform in practice. It is worth highlighting that in a prediction problem, a model is usually given a dataset of known data on which training is run (training dataset), and a dataset of unknown data (or first seen data) against which the model is tested (testing dataset). The goal of cross validation is to define a dataset to "test" the model in the training phase (i.e., the validation dataset), in order to limit problems like overfitting, give an insight on how the model will generalize to an independent data set (i.e., an unknown dataset, for instance from a real problem), etc.

One round of cross-validation involves partitioning a sample of data into complementary subsets, performing the analysis on one subset (called the training set), and validating the analysis on the other subset (called the validation set or testing set). To reduce variability, multiple rounds of cross-validation are performed using different partitions, and the validation results are averaged over the rounds.

##Common types of cross-validation

### K-fold cross-validation
In k-fold cross-validation, the original sample is randomly partitioned into k equal size subsamples. Of the k subsamples, a single subsample is retained as the validation data for testing the model, and the remaining `k − 1` subsamples are used as training data. The cross-validation process is then repeated k times (the folds), with each of the k subsamples used exactly once as the validation data. The k results from the folds can then be averaged (or otherwise combined) to produce a single estimation. The advantage of this method over repeated random sub-sampling is that all observations are used for both training and validation, and each observation is used for validation exactly once. 10-fold cross-validation is commonly used, but in general k remains an unfixed parameter.

### 2-fold cross-validation
This is the simplest variation of k-fold cross-validation. Also, called holdout method. For each fold, we randomly assign data points to two sets s0 and s1, so that both sets are equal size (this is usually implemented by shuffling the data array and then splitting it in two). We then train on s0 and test on s1, followed by training on s1 and testing on s0.

This has the advantage that our training and test sets are both large, and each data point is used for both training and validation on each fold.

### Repeated random sub-sampling validation
This method randomly splits the dataset into training and validation data. For each such split, the model is fit to the training data, and predictive accuracy is assessed using the validation data. The results are then averaged over the splits. The advantage of this method (over k-fold cross validation) is that the proportion of the training/validation split is not dependent on the number of iterations (folds). The disadvantage of this method is that some observations may never be selected in the validation subsample, whereas others may be selected more than once. In other words, validation subsets may overlap. This method also exhibits Monte Carlo variation, meaning that the results will vary if the analysis is repeated with different random splits.

### Leave-one-out cross-validation
Leave-one-out cross-validation (LOOCV) involves using a single observation from the original sample as the validation data, and the remaining observations as the training data. This is repeated such that each observation in the sample is used once as the validation data. This is the same as a K-fold cross-validation with K being equal to the number of observations in the original sampling.

## An example: 10-fold cross-validation
将数据集分成十分，轮流将其中9份作为训练数据，1份作为测试数据，进行试验。每次试验都会得出相应的正确率（或差错率）。10次的结果的正确率（或差错率）的平均值作为对算法精度的估计，一般还需要进行多次10-fold cross-validation（例如10次10-fold cross-validation），再求其均值，作为对算法准确性的估计。

之所以选择将数据集分为10份，是因为通过利用大量数据集、使用不同学习技术进行的大量试验，表明10折是获得最好误差估计的恰当选择，而且也有一些理论根据可以证明这一点。但这并非最终诊断，争议仍然存在。而且似乎5折或者20折与10折所得出的结果也相差无几。

## An implementation of cross-validation to find the optimal parameters for LIBSVM.

The artical and source code are in [another post](http://imkaywu.com/2014/01/15/find-the-optimal-parameter.html), go check it out.