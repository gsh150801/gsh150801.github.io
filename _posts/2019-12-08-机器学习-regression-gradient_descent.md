---
layout:     post
title:      机器学习1
subtitle:   Regression，Gradient Descent
date:       2019-12-08
author:     SH
header-img: img/post-bg-ios9-web.jpg
catalog: 	 true
tags:
    - ML
    - 回归
---
搬运自课程：[李宏毅深度学习19](speech.ee.ntu.edu.tw/~tlkagk/courses_ML19.html)

机器学习，一般是需要traning data和testing data，

前者用于训练模型，后者用于检验模型分类和回归的好坏

比如对于一堆二维平面的点，(xn,yn)，模型会基于xn得到ynhap，

这是个预测值，和实际的yn会有出入，这个差值就是所谓的loss，

loss function就是基于这些差值的一个函数，用来评价模型预测值和实际值之间的差距（即模型的准确性）

[机器学习中常见的几种损失函数](https://www.cnblogs.com/hejunlin1992/p/8158933.html)

需要注意的是，对于两个数据集：traning data 和testing data都有自己的loss function

（参数一致，但是具体数值不一定一样）

当训练集的loss远小于测试集的loss时，我们称之为过拟合overfitting

亦即模型过于吻合数据集（），而使得其预测效果反而差了。

提示我们不能要求模型对于训练集的完美拟合，也可以增大训练数据量来避免


梯度下降gradient descent

随机找一个起始点x0，求dy0/dx0，

如果导数为负，则图像是下降的，应该往右边移动来寻找最低点，反之则应往左移。

然后从x0移动(-ηdy0/dx0)，再求导，一直到导数为0。

问题：可能会陷入局部最优local optimal而非全局最优global optimal。但线性系统不存在该问题。

回归见：[机器学习中的回归算法详解](https://blog.csdn.net/program_developer/article/details/79113765)





