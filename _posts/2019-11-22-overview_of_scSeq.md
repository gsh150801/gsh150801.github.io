---
layout:     post
title:      单细胞测序了解
subtitle:   综述
date:       2019-11-22
author:     SH
header-img: img/post-bg-ios9-web.jpg
catalog: 	 true
tags:
    - scSeq
    - 单细胞
---
some parts come from: [回顾：单细胞入门-读一篇scRNA-seq综述](https://mp.weixin.qq.com/s/fGvatRdd2GOzi_GbHBlkEw)
# 单细胞测序了解整理
## 测序技术回顾
### 一代测序
* 源起于HGP，
* HGP一开始是有两个团队竞争，一个是政府支持的，一个是私企支持的，二者测序思路大相径庭。
1. 逐个克隆法
* 对连续克隆系中排定的BAC（细菌人工染色体）克隆逐个进行亚克隆测序并进行组装（公共领域测序计划）；
* 方法：Sanger法(末端终止法)

由于双脱氧核苷酸的存在，使得DNA链无法再延长；

同时双脱氧核苷酸上的荧光信号使得可以识别某个双脱氧核苷酸的碱基成为可能；

随机的DNA复制会形成许许多多个序列相差一个核苷酸的DNA分子。
* 优点：精准（一代测序仍然是测序的金标准，二代测序中无法测到的序列会用一代测序进行补测）
* 缺点：费时费力费钱；

2. 全基因组鸟枪法
* 把全基因组DNA片段打碎成小片段，然后对小片段进行测序，再利用超级计算机等结合组装算法将小片段组装成染色体等；

在第一次测定人类基因组序列上，鸟枪法败下阵了（我觉得可能因为组装比较难一点）
### 二代测序(NGS)
二代测序的策略都是鸟枪法。特点是，读长（reads）短，通量（througput）高，边合成边测序。

二代测序方法经典的有三种:454 FLX、Illumina/Solexa、SOLID
1. 454 FLX
* Pros: longer reads;
* Cons: higher expenditure;
2. Illumina/Solexa
* Pros: high throughput; lower expenditure;
* Cons: short reads; limited sequencing deepth;
3. SOLID
* Pros: higher accurancy in NGS
* Cons: higher expenditure;

### 三代测序
* pros: detect different genes expression of single cells
* cons: low accurancy(noise is still huge; information loss: some gene expression information will be droup)

***Single-cell RNA sequencing: Technical advancements and biological applications.*** 

#### workflow of common scRNA-seq:
1. dissociation
2. RNA RT to cDNA
3. amplify cDNA
3. prepartion of sequencing library
4. sequencing
5. single-cell expression profiles
6. cell types identification
#### data analysis of scRNA-seq
1. Reads QC
2. Alignment
3. Mapping QC
4. Cell QC
5. Normalization
6. Confounders
7. Differential expression; Clustering; Network
#### challenges of scRNA-Seq
* Bias of amplification
* Gene information dropouts
##### 单细胞分离方法：
1. 组织切片

2. 酶解法











