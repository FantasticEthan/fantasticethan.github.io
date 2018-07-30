---
title: "[Algorithm]XGBoost算法解析"
layout: post
date: 2018-05-28 10:08
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Algorithm
- Machine Learning
category: blog
author: ethan
description: 对于XGBoost进行知识回顾和相关资料整理

---

## XGBoost

使用XGBoost有两年了,现在因为性能和速度原因。使用Lightgbm更多一些。XGBoost由陈天奇大神设计的基于GBDT的机器学习工具，作为当时一出道就霸占各种Kaggle、天池竞赛等大数据比赛，模型能力不用多说。把知识点总结下，以后用的少了，作为知识回顾使用。完整的概念推导建议直接看论文和PPT。

- XGBoost模型诞生
- 目标函数与其最优估计
- 贪心求解与泰勒二阶展开
- 模型参数	

---

### XGBoost模型诞生

我认为一个模型诞生的思想很重要，如果要创新新的模型，在论文中找新的trick,对于模型的追溯是有必要的。XGBoost的以决策树为基模型，利用ensemble中的boost思想，从弱模型转换为强模型，每轮预测利用上一轮预测与真实值的残差，在正则项上加入叶子数量和L2正则。最后，利用泰勒二阶展开进行优化，完善了优化中的步长信息。

### 目标函数与最优估计

这部分直接看大神PPT和论文吧，网上的模型介绍千差万别，每个人都有自己的看法，只有看原作者的公式推导才能进行理论的理解加深。

- [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/pdf/1603.02754v1.pdf)
- [Introduction to Boosted Trees(PPT)](https://homes.cs.washington.edu/~tqchen/pdf/BoostedTree.pdf)
- [Github XGBoost](https://github.com/dmlc/xgboost)

### 贪心求解与泰勒二阶展开

解释一下这两个问题，主要是为什么这么做和作者是如何做的。

首先，决策树在进行搜索算法的时候，有可能有无限个可能的树结构。所以假设存在一个游标，先将分支下的样本按照一个属性进行排序，再滑动游标按照这个属性从小到大遍历，分别将样本分为两部分，将两部分的样本带入上一节求得的目标函数最小值公式，再相加，与不分裂的样本的目标函数做差，即为分裂后的“收益”。贪心的策略就是找出收益最大的分裂“游标”的位置。

**作者在分裂收益上加入了两个部分。**这两部分一个是在分母加上$\lambda$，来降低分支收益的灵敏度，另外一个部分$\gamma$利用分支数量来控制模型。

![Split Part](https://ws4.sinaimg.cn/large/006tKfTcgy1frqv7x47tlj30jc05lwfa.jpg)

对于泰勒二阶展开，由于gbdt只用到了一阶信息，如果按照推导，相当于loss只进行了一阶泰勒展开。在没有复杂度项的情况下，无法确定步长，所以只能用常数步长根据一阶梯度方向去逼近。这就是牛顿下降法和梯度下降法的区别。由于二阶展开用二次函数去逼近函数，所以可以利用二阶信息确定更新步长，比只利用一阶信息的gdbt用更少的迭代获得更好的效果。感兴趣可以自己证明牛顿梯度法的二阶收敛性。

### 模型参数

- [Complete Guide to Parameter Tuning in XGBoost](https://www.analyticsvidhya.com/blog/2016/03/complete-guide-parameter-tuning-xgboost-with-codes-python/)
- [翻译版](https://blog.csdn.net/wzmsltw/article/details/50994481)
- [xgboost api](http://xgboost.readthedocs.io/en/latest/python/python_api.html)


完整的网格搜索和自定义Loss在[Project Xgboost relevant documents]


