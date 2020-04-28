---
layout: post
title: lr算法模型学习
date: 2020-04-28
tags: 机器学习
---

# 简介

逻辑回归（LR,Logistic Regression）是传统机器学习中的一种分类模型，由于LR算法具有简单、高效、易于并行且在线学习（动态扩展）的特点，
在工业界具有非常广泛的应用。

> 在线学习算法：LR属于一种在线学习算法，可以利用新的数据对各个特征的权重进行更新，而不需要重新利用历史数据训练。

LR适用于各项广义上的分类任务，例如：评论信息正负情感分析（二分类）、用户点击率（二分类）、用户违约信息预测（二分类）、用户等级分类（多分类 ）等场景。

实际开发中，一般针对该类任务首先都会构建一个基于LR的模型作为Baseline Model，实现快速上线，然后在此基础上结合后续业务与数据的演进，不断的优化改进！

In statistics, the logistic model (or logit model) is used to model the probability of a certain class or event existing 
such as pass/fail, win/lose, alive/dead or healthy/sick. This can be extended to model several classes of events 
such as determining whether an image contains a cat, dog, lion, etc. 
Each object being detected in the image would be assigned a probability between 0 and 1 and the sum adding to one.

Logistic regression is a statistical model that in its basic form uses a logistic function to model 
a binary dependent variable, although many more complex extensions exist. 
In regression analysis, logistic regression[1] (or logit regression) is estimating the parameters of a logistic model 
(a form of binary regression). Mathematically, a binary logistic model has a dependent variable with two possible values, 
such as pass/fail which is represented by an indicator variable, where the two values are labeled "0" and "1". 
In the logistic model, the log-odds (the logarithm of the odds) for the value labeled "1" is a linear combination of 
one or more independent variables ("predictors"); the independent variables can each be a binary variable 
(two classes, coded by an indicator variable) or a continuous variable (any real value). The corresponding probability 
of the value labeled "1" can vary between 0 (certainly the value "0") and 1 (certainly the value "1"), hence the 
labeling; the function that converts log-odds to probability is the logistic function, hence the name. 
The unit of measurement for the log-odds scale is called a logit, from logistic unit, hence the alternative names. 
Analogous models with a different sigmoid function instead of the logistic function can also be used, 
such as the probit model; the defining characteristic of the logistic model is that increasing one of the 
independent variables multiplicatively scales the odds of the given outcome at a constant rate, with each independent 
variable having its own parameter; for a binary dependent variable this generalizes the odds ratio.

In a binary logistic regression model, the dependent variable has two levels (categorical). Outputs with more than 
two values are modeled by **multinomial logistic regression** and, if the multiple categories are ordered, 
by ordinal logistic regression (for example the proportional odds ordinal logistic model[2]). The logistic regression model 
itself simply models probability of output in terms of input and does not perform statistical classification 
(it is not a classifier), though it can be used to make a classifier, for instance by choosing a cutoff value and 
classifying inputs with probability greater than the cutoff as one class, below the cutoff as the other; this is a common 
way to make a binary classifier. The coefficients are generally not computed by a closed-form expression, unlike linear 
least squares; see § Model fitting. The logistic regression as a general statistical model was originally developed and 
popularized primarily by Joseph Berkson,[3] beginning in Berkson (1944), where he coined "logit"; see § History.

# 公式推导

![](/images/posts/0429_lr/img1.jpeg)
![](/images/posts/0429_lr/img2.jpeg)

# 面试中LR相关高频问题

- LR与SVM的联系与区别

联系

(1) LR和SVM都可以处理分类问题，且一般都用于**处理线性二分类问题（在改进的情况下可以处理多分类问题）**

(2) 两个方法都可以增加不同的正则化项，**如l1、l2等等**。所以在很多实验中，两种算法的结果是很接近的。

区别：

(1) **LR是参数模型[逻辑回归是假设y服从Bernoulli分布]，SVM是非参数模型**，LR对异常值更敏感。

(2) 从目标函数来看，区别在于逻辑回归采用的是logistical loss，SVM采用的是hinge loss，
这两个损失函数的目的都是**增加对分类影响较大的数据点的权重，减少与分类关系较小的数据点的权重。**

(3) SVM的处理方法是只考虑support vectors，也就是和**分类最相关的少数点，去学习分类器。**
而逻辑回归通过**非线性映射，大大减小了离分类平面较远的点的权重，相对提升了与分类最相关的数据点的权重。**

(4) 逻辑回归相对来说**模型更简单，好理解，特别是大规模线性分类时比较方便。** 而SVM的理解和优化相对来说复杂一些，
SVM转化为对偶问题后,分类只需要计算与少数几个支持向量的距离,这个在进行复杂核函数计算时优势很明显,能够大大简化模型和计算。

(5) **logic 能做的 svm能做，但可能在准确率上有问题，svm能做的logic有的做不了。**

- 面对特定的问题如何选择使用LR还是SVM？

（1）如果Feature的数量很大，**跟样本数量差不多，这时候选用LR或者是Linear Kernel的SVM**

（2）如果Feature的数量比较小，样本数量一般，不算大也不算小，选用SVM+Gaussian Kernel

（3）如果Feature的数量比较小，而样本数量很多，需要手工添加一些feature变成第一种情况。

（4）数据量：**数据量大就用LR，数据量小且特征少就用SVM非线性核。**

- 什么是参数模型（LR）与非参数模型（SVM）？

在统计学中，参数模型通常假设总体（随机变量）服从某一个分布，**该分布由一些参数确定（比如正太分布由均值和方差确定），**在此基础上构建的模型称为参数模型；

非参数模型对于总体的分布不做任何假设，**只是知道总体是一个随机变量，其分布是存在的（分布中也可能存在参数），但是无法知道其分布的形式，**
更不知道分布的相关参数，只有在给定一些样本的条件下，能够依据非参数统计的方法进行推断。

# 参考文献
1. [逻辑回归（LR）个人学习总结篇 https://www.jianshu.com/p/dce9f1af7bc9](https://www.jianshu.com/p/dce9f1af7bc9)