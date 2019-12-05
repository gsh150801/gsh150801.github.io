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
* 2.1 质控
* 2.2 标准化
* 2.3 批量（效应）校正
* 2.4 高度变异基因
* 2.5 可视化
* 2.6 细胞循环分数
3. 下游分析
* 3.1 聚类
* 3.2 标记基因和（聚类的）簇注释
* 3.3 子聚类
* 3.4 成分分析
* 3.5 轨迹推理
* * 3.5.1 弹弓
* * 3.5.2 弹弓（w/o 批量效应校正）
* * 3.5.3 单眼绷带
* * 3.5.4 伪时间扩散
* 3.6 基因表达动力学
* 3.7 亚稳态
* 3.8 基于图论的分类
* 3.9 基因水平分析
* * 3.9.1 差异表达
* * 3.9.2 基因集分析
* 3.10 写入文件
4. 总结
* 4.1 与发表结果的比较


----------------------------------------------------------------------------------

下面是案例学习具体过程：
### 0. 载入各种包
#### 0.1 载入python包
```
import scanpy as sc
import numpy as np
import scipy as sp
import pandas as pd
import matplotlib.pyplot as plt
from matplotlib import rcParams
from matplotlib import colors
import seaborn as sb
from gprofiler import GProfiler

import rpy2.rinterface_lib.callbacks
import logging

from rpy2.robjects import pandas2ri
import anndata2ri
```
#### 0.2 R设置
```
# Ignore R warning messages
#Note: this can be commented out to get more verbose R output
rpy2.rinterface_lib.callbacks.logger.setLevel(logging.ERROR)

# Automatically convert rpy2 outputs to pandas dataframes
pandas2ri.activate()
anndata2ri.activate()
%load_ext rpy2.ipython

plt.rcParams['figure.figsize']=(8,8) #rescale figures
sc.settings.verbosity = 3
#sc.set_figure_params(dpi=200, dpi_save=300)
sc.logging.print_versions()
```
#### 0.3 载入R包
```
%%R
# Load libraries from correct lib Paths for my environment - ignore this!
.libPaths(.libPaths()[c(3,2,1)])

# Load all the R libraries we will be using in the notebook
library(scran)
library(RColorBrewer)
library(slingshot)
library(monocle)
library(gam)
library(clusterExperiment)
library(ggplot2)
library(plyr)
library(MAST)
```
### 1. 读入数据
数据集可以通过GEO号GSE92332获取。原始单细胞表达数据集是从GSE92332_RAW.tar文件提取来的[链接](https://www.ncbi.nlm.nih.gov/geo/download/?acc=GSE92332&format=file)，通过scanpy的read函数载入。需要注意的是，单细胞数据格式多样，因此在载入数据前可能需要预处理。像Scanpy和Seurat之类的包，它们的read函数能通过对数个稀疏和密集数据格式的支持，来加速数据的载入过程。当你想要用教程的代码去跑你自己的数据集时，很可能需要改动预处理这块使得匹配你的数据格式。

这里，我们我们读进稀疏count矩阵，然后通过.toarray()函数立马转换成密集形式。我们产生密集矩阵因为我们批量效应校正方法输出一个密集矩阵， 而且R和python间的数据转换目前仅限于密集矩阵。当稀疏批次校正方法可用，且rpy2支持稀疏矩阵时，保持数据的稀疏格式会更加有效利用内存。

需要注意的是，R(Seurat, Scater)和python(Scanpy)排列单细胞数据的习惯也不同。Scanpy期望数据保存为cells x genes的格式， 而R则相反。因为GEO中的数据是以genes x cells的格式保存的，我们必须在使用数据前对其进行转置（用python进行读取时）。

Macosko_cell_cycle_genes.txt文件包含一个不同细胞周期时相的标记基因列表。我们将会在之后的细胞周期分数部分用到。

#### 文件名字符代码
```
# Set up data loading

#Data files
sample_strings = ['Duo_M1', 'Duo_M2', 'Jej_M1', 'Jej_M2', 'Il_M1', 'Il_M2']
sample_id_strings = ['3', '4', '5', '6', '7', '8']
file_base = '../data/Haber-et-al_mouse-intestinal-epithelium/GSE92332_RAW/GSM283657'
exp_string = '_Regional_'
data_file_end = '_matrix.mtx'
barcode_file_end = '_barcodes.tsv'
gene_file_end = '_genes.tsv'
cc_genes_file = '../Macosko_cell_cycle_genes.txt'
```

#### 加载第一个数据集（第一个样品）
```
# First data set load & annotation
#Parse Filenames
sample = sample_strings.pop(0)
sample_id = sample_id_strings.pop(0)
data_file = file_base+sample_id+exp_string+sample+data_file_end
barcode_file = file_base+sample_id+exp_string+sample+barcode_file_end
gene_file = file_base+sample_id+exp_string+sample+gene_file_end

#Load data
adata = sc.read(data_file, cache=True)
adata = adata.transpose()
adata.X = adata.X.toarray()

barcodes = pd.read_csv(barcode_file, header=None, sep='\t')
genes = pd.read_csv(gene_file, header=None, sep='\t')

#Annotate data
barcodes.rename(columns={0:'barcode'}, inplace=True)
barcodes.set_index('barcode', inplace=True)
adata.obs = barcodes
adata.obs['sample'] = [sample]*adata.n_obs
adata.obs['region'] = [sample.split("_")[0]]*adata.n_obs
adata.obs['donor'] = [sample.split("_")[1]]*adata.n_obs

genes.rename(columns={0:'gene_id', 1:'gene_symbol'}, inplace=True)
genes.set_index('gene_symbol', inplace=True)
adata.var = genes

```

#### 循环读取后续几个数据集
```
# Loop to load rest of data sets
for i in range(len(sample_strings)):
    #Parse Filenames
    sample = sample_strings[i]
    sample_id = sample_id_strings[i]
    data_file = file_base+sample_id+exp_string+sample+data_file_end
    barcode_file = file_base+sample_id+exp_string+sample+barcode_file_end
    gene_file = file_base+sample_id+exp_string+sample+gene_file_end
    
    #Load data
    adata_tmp = sc.read(data_file, cache=True)
    adata_tmp = adata_tmp.transpose()
    adata_tmp.X = adata_tmp.X.toarray()

    barcodes_tmp = pd.read_csv(barcode_file, header=None, sep='\t')
    genes_tmp = pd.read_csv(gene_file, header=None, sep='\t')
    
    #Annotate data
    barcodes_tmp.rename(columns={0:'barcode'}, inplace=True)
    barcodes_tmp.set_index('barcode', inplace=True)
    adata_tmp.obs = barcodes_tmp
    adata_tmp.obs['sample'] = [sample]*adata_tmp.n_obs
    adata_tmp.obs['region'] = [sample.split("_")[0]]*adata_tmp.n_obs
    adata_tmp.obs['donor'] = [sample.split("_")[1]]*adata_tmp.n_obs
    
    genes_tmp.rename(columns={0:'gene_id', 1:'gene_symbol'}, inplace=True)
    genes_tmp.set_index('gene_symbol', inplace=True)
    adata_tmp.var = genes_tmp
    adata_tmp.var_names_make_unique()

    # Concatenate to main adata object
    adata = adata.concatenate(adata_tmp, batch_key='sample_id')
    adata.var['gene_id'] = adata.var['gene_id-1']
    adata.var.drop(columns = ['gene_id-1', 'gene_id-0'], inplace=True)
    adata.obs.drop(columns=['sample_id'], inplace=True)
    adata.obs_names = [c.split("-")[0] for c in adata.obs_names]
    adata.obs_names_make_unique(join='_')
```

```
#Assign variable names and gene id columns
adata.var_names = [g.split("_")[1] for g in adata.var_names]
adata.var['gene_id'] = [g.split("_")[1] for g in adata.var['gene_id']]
```

#### 注释数据集
```
# Annotate the data sets
print(adata.obs['region'].value_counts())
print('')
print(adata.obs['donor'].value_counts())
print('')
print(adata.obs['sample'].value_counts())
```


```
# Checking the total size of the data set
adata.shape
```
### 2. 预处理和可视化
#### 2.1 质控
目的：确保数据可用性
数据质控可以分为细胞质控和基因质控。评价一个细胞质量的三个经典评测指标是：
1. 每个barcode的counts（UMIs）数目（count depth）；
2. 每个barcode的基因数目；
3. 每个barcode的线粒体基因counts的占比/分数。
较高的线粒体reads分数可能暗示着细胞应激，因为核mRNA在细胞内的占比较低。需要注意的是，较高的线粒体RNA占比也可能是指示着升高的呼吸过程的生物信号。

* 比如，count depth低，检测到的基因少，线粒体count占比高的barcode，可能是胞质mRNA通过破裂的细胞膜泄露的细胞，只有线粒体mRNA得以保留。
* 相反，count depth出乎意料的高，检测到的基因数量很多，可能是doublet（一个barcode里有不止一个细胞）。
* 因而，高count depth阈值常用于过滤可能的doublet。三个对应的doublet检测软件（DoubletDecon, Scrublet, Doublet Finder），提供了更优雅、也许更好的解决方法。

孤立地考虑这三个QC协变量会导致对细胞信号的误解、误判。
比如：
* 线粒体counts相对偏高可能是因为参与了呼吸过程；
* 低counts和/或基因少可能对应静息期细胞群；
* 高counts可能是体积较大的细胞。
```
# Quality control - calculate QC covariates
adata.obs['n_counts'] = adata.X.sum(1)
adata.obs['log_counts'] = np.log(adata.obs['n_counts'])
adata.obs['n_genes'] = (adata.X > 0).sum(1)

mt_gene_mask = [gene.startswith('mt-') for gene in adata.var_names]
adata.obs['mt_frac'] = adata.X[:, mt_gene_mask].sum(1)/adata.obs['n_counts']
```


```
# Quality control - plot QC metrics
#Sample quality plots
t1 = sc.pl.violin(adata, 'n_counts', groupby='sample', size=2, log=True, cut=0)
t2 = sc.pl.violin(adata, 'mt_frac', groupby='sample')
```

































