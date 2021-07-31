---
layout: optics_post
title:  "Understanding ResNet, the Deep Residual Learning"
author: Li Jinzhao
categories: [Computer Vision]
image: ....jpg
tags: [Deep Learning]
typora-root-url: ..
---
> **Abstract**/**Snippet**.


**Contents**

* toc
{:toc}
## **1. Degradation Problem when Stacking More Layers**

​	One of the possible way to learn better in ML tasks is to stack more layers, while simply increase the depth of network will lead to the degradation. Note that this degradation is not caused by overfitting, and adding more layers to a suitably deep model will lead to higher training error[^1].

![[OPTSxa6b4]_Degradation_Problem](/assets/images/[OPTSxa6b4]_Degradation_Problem.svg)



> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSxa6b4]**

[^1]: He, Kaiming, et al. "Deep residual learning for image recognition." *Proceedings of the IEEE conference on computer vision and pattern recognition*. 2016.
[^2]: Balduzzi, David, et al. "The shattered gradients problem: If resnets are the answer, then what is the question?." *International Conference on Machine Learning*. PMLR, 2017.
[^3]: https://github.com/pytorch/vision/blob/master/torchvision/models/resnet.py

[^4]:

