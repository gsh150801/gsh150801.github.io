---
layout:     post
title:      组学数据分析
subtitle:   通路活性分析
date:       2023-03-08
author:     SH
header-img: img/post-bg-ios9-web.jpg
catalog: 	 true
tags:
    - 通路活性
---


## 论文名称与链接
***In silico Pathway Activation Network Decomposition Analysis (iPANDA) as a method for biomarker development.***  
[论文链接](https://www.nature.com/articles/s41467-022-34692-w "文章")  
[代码链接](https://github.com/varnivey/ipanda "代码")  
### 论文摘要
2016年，insilicon公司发布了一种新的算法，In silico Pathway Activation Network Decomposition Analysis，即iPANDA方法。  
iPANDA方法将预先计算的基因共表达数据与基于基因差异表达程度和通路拓扑分解的基因重要性因子相结合，获得通路激活评分。  
  
![简介图](https://media.springernature.com/lw685/springer-static/image/art%3A10.1038%2Fncomms13427/MediaObjects/41467_2016_Article_BFncomms13427_Fig1_HTML.jpg?as=webp "流程")  
  
从图上可以看到，iPANDA算法需要两种转录组数据，一组疾病组样本/处理组样本，一组健康组样本/控制组样本。  
对于每个基因，分别计算其logFC值。然后分别是估计统计权重、基因的共表达模块分组、估计拓扑权重的估计和计算iPANDA通路激活分数的计算。  

下面是一些公式：
1. 对于基因i和模块j，通路p对应的的激活分数公式为：  
![通路p的激活分数](https://media.springernature.com/lw333/springer-static/image/art%3A10.1038%2Fncomms13427/MediaObjects/41467_2016_Article_BFncomms13427_Equ1_HTML.gif "")  
也就是说，这里同时考虑了单独的基因和共现的基因块对通路的影响  
接下来是具体的基因分数和模块分数的计算公式：  
![基因i对通路p的贡献分数](https://media.springernature.com/lw323/springer-static/image/art%3A10.1038%2Fncomms13427/MediaObjects/41467_2016_Article_BFncomms13427_Equ2_HTML.gif "")  
![模块j对通路p的贡献分数](https://media.springernature.com/lw377/springer-static/image/art%3A10.1038%2Fncomms13427/MediaObjects/41467_2016_Article_BFncomms13427_Equ3_HTML.gif)  
在计算公式（2）中,   
$fc_i$ 代表基因i在两组间的倍数变化（FC值）；  
激活符号 $A_{ip}$ 是一个离散系数，表示基因i对通路p的影响，当是正向影响时，该值为1，负向影响的话该值为-1。  
$w_i^S$ 和 $w_{ip}^T$分别表示统计和拓扑系数，其值介于0-1之间。
因为 $log(fc_i)$ 和 $A_{ip}$ 都可以取正负值，所以最终通路p的iPANDA值也有正负值的可能。  
当iPANDA为正时，表示通路p被激活，反之，当该值为负时，表示通路p被抑制。

接下来看看拓扑系数 $w_{ip}^T$ 的计算公式
$$w_{ip}^T = N_{ip} / max(N_{jp})$$
$N_{ip}$ 表示在通路p内，包括基因i在内的路径数，路径从入度为0的节点（基因）开始，终止于出度为0的节点。

> 统计系数取决于P值，P值是通过每个基因的病例和正常样本集的t检验计算出来的。  
被称为P值阈值的方法通常被用来过滤伪基因，这些伪基因在集合之间没有显著差异。  
然而，使用锐阈值函数的一个主要问题是，它可能在过滤的基因集中引入不稳定性，从而导致数据集之间的通路激活评分。  
此外，通路激活值对任意选择的截断值变得敏感。为了解决这个问题，我们建议使用平滑阈值函数。  
  
其次是统计系数 $w_i^S$ 的计算公式

