---
layout:     post
title:      机器学习python编程环境
subtitle:   Anaconda+vscode+pytorch/tensorflow
date:       2019-11-15
author:     SH
header-img: img/post-stastistics.png
catalog: 	 true
tags:
    - ml
    - vscode
---
参考的是知乎上的一篇文章：
[机器学习Python编程环境：VSCode+Anaconda](https://zhuanlan.zhihu.com/p/40378960)

## 好处：
一开始我先安装了vscode和miniconda，之前自己装的python，然后再建了个虚拟环境后，vscode的jupyter notebook功能用不了了。
搜到的教程都没能解决我的问题，刚好看到了这篇文章，觉得这样也不错，于是就动了下手，觉得还是不错的。
* 只安装anaconda，不需要再安装python。嗯，不知道之后会不会有问题。
* 可以在vscode的终端里用anaconda的命令。美滋滋，Windows的cmd，丑的一匹好嘛！

1. 先安装anaconda（注意，这里用的是anaconda，不是miniconda，但我觉得应该也行）。需要设置镜像
2. 安装完anaconda后，需要把anaconda安装目录下的Anaconda\Library、Anaconda3\condabin、Anaconda3\Scripts、Anaconda四个加入到PATH环境变量里（文件资源管理器，此电脑，右键选择属性，高级系统设置，高级，环境变量）
3. 再安装vscode，然后在文件-首选项-设置里，把python.pythonPath一栏设置为你的anaconda的python解释器位置。可以装上中文插件和python拓展插件。
4. 然后可以在vscode的终端里用conda命令安装所需的包

![path](img/191115Path.png)
* 设置清华镜像
```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
```
[conda的安装与使用（2019-6-28更新）](https://www.jianshu.com/p/edaa744ea47d)

* 安装机器学习包，这一步可以在cmd或vscode上进行，我是因为cmd卡卡的才用了vscode
```
& ...Anaconda3\condabin\conda install numpy mkl scipy matplotlib scikit-learn pandas gensim
# 安装预备包
& ...Anaconda3\condabin\conda install pytorch torchvision cpuonly -c pytorch
# 安装pytorch(windows system without cuda)
& ...Anaconda3\condabin\conda install -c conda-forge tensorflow
# 安装tensorflow
```
[pytorch的conda安装命令](https://pytorch.org/)





















