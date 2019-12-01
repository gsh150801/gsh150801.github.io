---
layout:     post
title:      单细胞分析流程练习
subtitle:   一个教程
date:       2019-12-01
author:     SH
header-img: img/post-bg-ios9-web.jpg
catalog: 	 true
tags:
    - 单细胞
    - 分析流程
---
该项目GitHub主页：[theislab/single-cell-tutorial](https://github.com/theislab/single-cell-tutorial)
对应的文章是："Current best practices in single-cell RNA-seq analysis: a tutorial" 
练习的具体过程分为几步：
1. 把整个github项目文件夹下载到本地电脑；
2. 设置conda的环境（安装接下来要用到的软件）
3. 下载数据
4. 案例学习


## 把整个github项目文件夹下载到本地电脑
* 这里可以用浏览器打开项目GitHub主页，点击绿色的'Clone or download'
* 也可以在终端用命令行
```
cd ~/Desktop
git clone https://github.com/theislab/single-cell-tutorial
# 下载到当前目录
cd single-cell-tutorial-master
```

## 配置conda环境
首先你需要先安装好conda，没有的话，请先安装conda: [conda的安装与使用](https://www.jianshu.com/p/edaa744ea47d)

* 从文件导入环境配置：
#
```
cd single-cell-tutorial-master
# 可以先把sc_tutorial_environment.yml中的"r-essentials"改为"r-essentials=3.4.1"
conda env create -f sc_tutorial_environment.yml
```
接下来还需要安装几个R包，在终端打开R后：
```
install.packages(c('devtools', 'gam', 'RColorBrewer', 'BiocManager'))
update.packages(ask=F)
BiocManager::install(c("scran","MAST","monocle","ComplexHeatmap","slingshot"), version = "3.8")
library(devtools)
library(gam)
library(RColorBrewer)
library(BiocManager)
library(scran)
library(MAST)
library(monocle)
library(ComplexHeatmap)
library(slingshot)
```
* 如果安装失败了，你也可以手动安装，只要把yml文件里的软件装齐了就好



## 下载数据
下载GSE92332数据：
```
cd single-cell-tutorial-master
mkdir -p data/Haber-et-al_mouse-intestinal-epithelium/
cd data/Haber-et-al_mouse-intestinal-epithelium/
wget ftp://ftp.ncbi.nlm.nih.gov/geo/series/GSE92nnn/GSE92332/suppl/GSE92332_RAW.tar
mkdir GSE92332_RAW
tar -C GSE92332_RAW -xvf GSE92332_RAW.tar
gunzip GSE92332_RAW/*_Regional_*
```
下载注释好的数据集
```
cd data/Haber-et-al_mouse-intestinal-epithelium/
wget ftp://ftp.ncbi.nlm.nih.gov/geo/series/GSE92nnn/GSE92332/suppl/GSE92332_Regional_UMIcounts.txt.gz
gunzip GSE92332_Regional_UMIcounts.txt.gz
```

## 案例学习
打开latest_notebook下的Case-study_Mouse-intestinal-epithelium_1906.ipynb文件

配合上面的文章食用，效果更好。

注：前面有一次，上面这个文件的代码执行出错了。然后我重新下了一次数据，问题消失。

所以，也许只是数据不全。
1. 读入数据
2. 预处理和可视化
- 2.1 质控
- 2.2 标准化
- 2.3 批量（效应）校正
- 2.4 高度变异基因
- 2.5 可视化
- 2.6 细胞循环分数
3. 下游分析
- 3.1 聚类
- 3.2 标记基因和（聚类的）簇注释
- 3.3 子聚类
- 3.4 成分分析
- 3.5 轨迹推理
 - 3.5.1 弹弓
 - 3.5.2 弹弓（w/o 批量效应校正）
 - 3.5.3 单眼绷带
 - 3.5.4 伪时间扩散
* 3.6 基因表达动力学
* 3.7 亚稳态
* 3.8 基于图论的分类
* 3.9 基因水平分析
* * 3.9.1 差异表达
* * 3.9.2 基因集分析
* 3.10 写入文件
4. 总结
* 4.1 与发表结果的比较















