---
title: "[Interview]蘑菇街题目汇总"
layout: post
date: 2018-05-25 17:01
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Interview
- Record
category: blog
author: ethan
description: 面试中遇到的问题及试题整理

---

# 蘑菇街

## 基础编程题

1. 两个单链表求并集
2. LeetCode 135 分糖果问题
3. LeetCode 547 朋友圈问题 DFS 维护一个seen的set
4. 字符串最优分割  [https://blog.csdn.net/qq_28203045/article/details/80113668](https://blog.csdn.net/qq_28203045/article/details/80113668)
5. 链表的相同元素的长度
6. Leetcode 24 两两交换相邻的节点


## 机器学习编程题

1. 正负样本比对LR，GBDT的影响以及原因。
2. ROC曲线与AUC的值。ROC曲线以分类的数作为阈值，横坐标为假阳性，纵坐标为真阳性。来评价模型的好坏。ROC越往上，曲线下面积越大，分类器效果越好。AUC属于正类样本的置信度。
3. LR和GBDT的区别。**ans**:LR是线性模型，具有很好的可解释性，可分布式计算。GBDT是树形模型，可用于线性和非线性问题。在数据特征维度很大的时候，LR可以利用正则化解决稀疏问题，GBDT在高维矩阵运算时效率偏低。LR的特征选择依赖人工，GBDT可以选择特征。
4. 异常值如何处理。**ans**:1).3倍标准差准则，[均值+3*标准差,均值-3\*标准差] 2) 箱体图，1.5QR 等
5. 损失函数有哪些？**ans**:1)log对数损失-LR 2）平方损失-OLS最小二乘法 3）指数损失函数-AdaBoost 4）Hinge损失函数-SVM 4）交叉熵loss-softmax函数
6. GBDT参数 **ans**:1)弱学习器最大迭代次数 n_estimators 2）弱学习器衰减系数-步长*learning_rate* 3）子采样比例*subsample* 4）树深度*max_depth* 5）内部节点划分的最小样本数*min_samples_split* 6）叶子节点最小权重*min_weight_fraction_leaf* 等等
