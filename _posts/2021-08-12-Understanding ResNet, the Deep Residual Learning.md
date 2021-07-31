---
layout: optics_post
title:  "Understanding ResNet, the Deep Residual Learning"
author: Li Jinzhao
categories: [Computer Vision]
image: ....jpg
tags: [Deep Learning]
typora-root-url: ..
---
> **Abstract**/**Snippet**. The main part of the article is [Sect 2.](#2-Structure-of-Residual-Network), which contains many kinds of conjectures about ResNet structure and the explanation of its mechanism. These ideas are enlightening and important, and are sometimes even more important than ResNet itself, although in this article, some basic knowledge is still recorded.


**Contents**

* toc
{:toc}
## **1. Degradation Problem when Stacking More Layers**

​	One of the possible way to learn better in ML tasks is to stack more layers, while simply increase the depth of network will lead to the **degradation**[^1]. As the results tested on CIFAR-10 with 2--layer and 56-layer plain network shown below (which is reproduced from [^1]), deeper network has higher training error and thus test error (note that this degradation is not caused by overfitting).

![[OPTSxa6b4]_Degradation_Problem](/assets/images/[OPTSxa6b4]_Degradation_Problem.svg)

​	To further address this problem, an suitable analysis is needed, the reason why deeper network perform worse than the shallow one is **there exists some optimization difficulties in training the deeper network using the SGD, back propagation or other solvers, this difficulties leads to the network with insufficient training, which can not only improve but even reduce the holistic performance.**

​	[Batch normalization]() is a method designed to mitigate the degradation problem, while the degree of mitigation fails to meet the demand.

​	The **ResNet** is mainly for this, which introduce the **identity shortcut** into each building block of the network, **hence help each block to be easier to go to the identity if the identity is optimal, if the optimal mapping is closer to identity, ResNet is also easier to find small the fluctuations**. In a word, its central idea is to "going deeper", but it is also worth noting that this is only one dimension to optimize the network, others are possibly going wider, .

​	To be more explicit, this residual mapping $F(\mathbf{x})$ with addition of identity shortcut $\mathbf{x}$ is shown as below (a building block), the any desired mapping is as final result and denoted as $H(\mathbf{x})=F(\mathbf{x})+\mathbf{x}$. It is more easier to learn the residual $F(\mathbf{x})=H(\mathbf{x})-\mathbf{x}$ compared with the original mapping $H(\mathbf{x})$ especially when the optimal mapping is nearly the identity.

![[OPTSxa6b4]_Residual_Block](/assets/images/[OPTSxa6b4]_Residual_Block.svg)

## **2. Structure of Residual Network**



残差结构可以放大输入中微小的扰动，因此更加灵敏

### **2.1. Ensembles of Relatively Shallow Networks**



### **2.2. Self-Adaptive Deep Neural Network**

当网络需要这个模块是恒等时，它比较容易变成恒等。而传统的conv模块是很难通过学习变成恒等的，因为大家学过信号与系统都知道，恒等的话filter的冲激响应要为一个冲激函数，而神经网络本质是学概率分布 局部一层不太容易变成恒等，而resnet加入了这种模块给了神经网络学习恒等映射的能力

网络可以自己调节层数的深浅，不需要太深时，中间恒等映射就多，需要时恒等映射就少。当然了，实际的神经网络并不会有这么强的局部特性，它的训练结果基本是一个整体，并不一定会出现我说的某些block就是恒等的情况

### **2.3. **



## **3. Implementation/Tricks of Residual Network**



### **3.1. **

### **3.2. Small Tricks to Design Your Network**

- Each time you reduce the spatial size by downsampling, you also need to increase the number of filters by two: $\mathrm{spatial}\  \mathrm{size}/2\to$ # $\mathrm{filters}\times2$ (this is from VGG)
- 



> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSxa6b4]**

[^1]: He, Kaiming, et al. "Deep residual learning for image recognition." *Proceedings of the IEEE conference on computer vision and pattern recognition*. 2016.
[^2]: Balduzzi, David, et al. "The shattered gradients problem: If resnets are the answer, then what is the question?." *International Conference on Machine Learning*. PMLR, 2017.
[^3]: https://github.com/pytorch/vision/blob/master/torchvision/models/resnet.py

[^4]:

