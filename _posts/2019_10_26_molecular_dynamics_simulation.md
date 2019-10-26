---
layout:     post
title:      molecular daynamics simulation
subtitle:   分子动力学模拟基础知识笔记
date:       2019-10-26
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - molecular_dynamics_simulation
    - 分子动力模拟
---

### simulation和minimization的区别
minimization（或者docking）是通过调整原子的位置来计算分子的最低势能，不计算其速度等，是一个线性计算。
而docking可以看作是两个分子的minimization。
而simulation则是基于现有的势能，计算各原子收到的力及加速度，然后进一步计算之后的位置坐标和受力，如此往返重复。
### 关于课题方向
三个选择 or more，多尝试
1. 分子动力模拟
2. 生物信息学
3. 深度学习方向
