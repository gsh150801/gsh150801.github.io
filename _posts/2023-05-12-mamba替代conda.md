---
layout:     post
title:      软件安装
subtitle:   mamba安装
date:       2023-05-12
author:     SH
header-img: img/post-bg-ios9-web.jpg
catalog: 	 true
tags:
    - mamba
---
conda好用，但有时候装包太慢太慢了。   
好在我们可以用mamba(python命令行版的conda)替代，实测安装软件的速度有大幅提升。  
micromamba(C++版conda)的安装教程请参考[这个](https://www.cnblogs.com/huanping/p/16861271.html)   
理论上来说，micromamba应该会更快一点。但我觉得mamba已经可以了，所以没有试过micromamba。  
感兴趣的请自己尝试下。  
>Tip:   
>在安装mamba之前，需要先卸载conda。可以把conda环境导出为yaml文件，然后再在mamba里导入yaml文件建立环境。  
>`conda env export xxx.yaml`  从当前环境导出yaml文件   
>`conda env create -f xxx.yaml`  根据导入的yaml文件建立环境   


```
#下载mamba
wget https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-pypy3-Linux-x86_64.sh
#安装mamba
bash Mambaforge-pypy3-Linux-x86_64.sh
```
### 安装完后先配置镜像源
由于肉身在广东，所以我配置的是南科大的镜像源。  
进入[南科大镜像网站conda页面](https://mirrors.sustech.edu.cn/help/anaconda.html)，直接复制`~/.condarc`里的内容到自己的`~/.condarc`文件里。  
当然，你也可以选择运行下列命令（二选一哈）：
```
conda config --add channels https://mirrors.sustech.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.sustech.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```
