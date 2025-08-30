---
title: K-Fold Cross Validation
date: 2023-11-21 13:53:58
tags: Deep Learning
categories: Tech
---
The **K-fold cross validation** is to divide the training data into K parts, using K-1 of them for training and the remaining part for testing. Finally, take the average of the testing errors as the **generalization error**. This allows for better utilization of the training data.
However, I encountered some problems when I was trying two kinds of k-fold cross validation methods. Firstly, we need to understand the significance of data division. 
- The training set is used to train the model and obtain its parameters. 
- The validation set is used for tuning the model's hyperparameters. 
- The test set is used to evaluate the model's performance.
<!--more-->

###### Plan 1 - Not setting aside a test set in advance

- Split the dataset into k parts randomly
- Using K-1 of them for training, the remaining part for testing
- After K rounds of training, we obtain K different models

<p align="center">
<img src="/img/kfold/kfold2.png" width="45%" height="45%">
</p>

###### Plan 2 - Set aside a test set in advance

- Set aside a test set in advance
- Split the remaining dataset into k parts randomly
- Using K-1 of them for training, the remaining part for validation
- After K rounds of training, we obtain K different models
- Select the best hyperparameters from these models
- Use the model with the best hyperparameters and retrain it using all K parts of the data as the training set to obtain the final model

<p align="center">
<img src="/img/kfold/kfold1.png" width="45%" height="45%">
</p>

- In Plan 1: If you adjust hyperparameters using the test set, the goal is to find hyperparameters that slightly improve the performance of all the K-1 models. The aim is to find a relatively good set of hyperparameters.
- In Plan 2: Hyperparameters are adjusted using the validation set, and the seemingly best model out of the five is chosen. Then, the model is retrained using all data outside the training set, and finally, the test set is used to evaluate its effectiveness. This is to select a final usable model.

In this case, the intuition is that the plan 1 helps you evaluate your generalized model but not for choosing a set of model parameters. If your goal is to get a final model with suitable parameters, the plan 2 would be your better choice.

#### References ⭐
- [防止K折交叉踩坑](https://blog.51cto.com/Lolitann/5247375)
- [K-折交叉验证(记一个坑)](https://zhuanlan.zhihu.com/p/83841282)