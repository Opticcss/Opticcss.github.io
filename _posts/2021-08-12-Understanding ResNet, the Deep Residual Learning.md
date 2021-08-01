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

​	The **ResNet** is mainly for this, which introduce the **identity shortcut** into each building block of the network, **hence help each block to be easier to go to the identity if the identity is optimal, if the optimal mapping is closer to identity, ResNet is also easier to find small the fluctuations**. In a word, its central idea is to "going deeper", but it is also worth noting that this is only one dimension to optimize the network, others are possibly going wider[^2].

​	To be more explicit, this residual mapping $F(\mathbf{x})$ with addition of identity shortcut $\mathbf{x}$ is shown as below (a building block), the any desired mapping is as final result and denoted as $H(\mathbf{x})=F(\mathbf{x})+\mathbf{x}$ (which is, like a one dimensional difference equation). It is more easier to learn the residual $F(\mathbf{x})=H(\mathbf{x})-\mathbf{x}$ compared with the original mapping $H(\mathbf{x})$ especially when the optimal mapping is nearly the identity.

![[OPTSxa6b4]_Residual_Block](/assets/images/[OPTSxa6b4]_Residual_Block.svg)

​	Further analysis facing the degradation problem shows[^3], 

## **2. Structure of Residual Network**





- **改进一：改进downsample部分，减少信息流失**。前面说过了，每个stage的第一个conv都有下采样的步骤，我们看左边第一张图左侧的通路，input数据进入后在会经历一个stride=2的1*1卷积，将特征图尺寸减小为原先的一半，请注意1x1卷积和stride=2会导致输入特征图3/4的信息不被利用，因此ResNet-B的改进就是就是将下采样移到后面的3x3卷积里面去做，避免了信息的大量流失。ResNet-D则是在ResNet-B的基础上将identity部分的下采样交给avgpool去做，避免出现1x1卷积和stride同时出现造成信息流失。ResNet-C则是另一种思路，将ResNet输入部分的7x7大卷积核换成3个3x3卷积核，可以有效减小计算量，这种做法最早出现在Inception-v2中。其实这个ResNet-C 我比较疑惑，ResNet论文里说它借鉴了VGG的思想，使用大量的小卷积核，既然这样那为什么第一部分依旧要放一个7x7的大卷积核呢，不知道是出于怎样的考虑，但是现在的多数网络都把这部分改成3个3x3卷积核级联。

![img](https://pic3.zhimg.com/80/v2-ecfb7042a787a92265c0d48eaab350fe_1440w.jpg)



- **改进二：ResNet V2**。这是由ResNet原班人马打造的，主要是对ResNet部分组件的顺序进行了调整。各种魔改中常见的预激活ResNet就是出自这里。

![img](https://pic4.zhimg.com/80/v2-e7418072b4de7f80414c6c061033fda3_1440w.jpg)



原始的resnet是上图中的a的模式，我们可以看到相加后需要进入ReLU做一个非线性激活，这里一个改进就是砍掉了这个非线性激活，不难理解，**如果将ReLU放在原先的位置，那么残差块输出永远是非负的，这制约了模型的表达能力**，因此我们需要做一些调整，我们将这个ReLU移入了残差块内部，也就是图e的模式。这里的细节比较多，建议直接阅读原文：[Identity Mappings in Deep Residual Networks](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1603.05027) ，就先介绍这么多。





从梯度反向传播角度理解ResNet的有效性

残差结构使得梯度反向传播时，更不易出现梯度消失等问题，由于Skip Connection的存在，梯度能畅通无阻地通过各个Res blocks，下面我们来推导一下 **ResNet v2** 的反向传播过程。

![img](https://pic4.zhimg.com/80/v2-8a8947d3efe483ebccb93244de28b993_1440w.png)

原始的残差公式是这样子的，函数F表示一个残差函数，函数f表示激活函数，：

![img](https://pic1.zhimg.com/80/v2-c03789fd0ddd18e981b60f59873b41bc_1440w.jpg)

ResNet v2 使用恒等映射，且相加后不使用激活函数，因此可得到：

![img](https://pic1.zhimg.com/80/v2-ae4fe137a33033dcc47a47a8dd7a2dec_1440w.png)

递归得到第L层的表达式：

![img](https://pic1.zhimg.com/80/v2-1808122ceea2990defec1259980bc094_1440w.png)

反向传播求第l层梯度：

![img](https://pic1.zhimg.com/80/v2-db3a156b4b6863bc1a154772099f2288_1440w.png)

我们从这个表达式可以看出来：第l层的梯度里，包含了第L层的梯度，通俗的说就是第L层的梯度直接传递给了第l层。因为梯度消失问题主要是发生在浅层，这种将深层梯度直接传递给浅层的做法，有效缓解了深度神经网络梯度消失的问题。



### **2.1. Ensembles of Relatively Shallow Networks**

ResNet的有效性

ResNet 中其实是存在着很多路径的集合，整个ResNet类似于多个网络的集成学习，证据是删除部分ResNet的网络结点，不影响整个网络的性能，但是在VGG上做同样的事请网络立刻崩溃，由此可见相比其他网络ResNet对于部分路径的缺失不敏感。

### **2.2. Self-Adaptive Deep Neural Network**

当网络需要这个模块是恒等时，它比较容易变成恒等。而传统的conv模块是很难通过学习变成恒等的，因为大家学过信号与系统都知道，恒等的话filter的冲激响应要为一个冲激函数，而神经网络本质是学概率分布 局部一层不太容易变成恒等，而resnet加入了这种模块给了神经网络学习恒等映射的能力

网络可以自己调节层数的深浅，不需要太深时，中间恒等映射就多，需要时恒等映射就少。当然了，实际的神经网络并不会有这么强的局部特性，它的训练结果基本是一个整体，并不一定会出现我说的某些block就是恒等的情况

### **2.3. Differential Amplifier**

残差结构可以放大输入中微小的扰动，因此更加灵敏



## **3. Implementation/Tricks of Residual Network**

​	Some basic tricks and general implementation of the residual network (ResNet34 is taken as the example here, while for other structures, the tricks and the general ideas are also suitable to be use) are presented and explained as following.

### **3.1. Torch Implementation of ResNet34**

​	The following part is the implementation of ResNet34 as an example using `Pytorch` (`torch`).[^4] Before this, it is also worth noting that the ResNet (ResNet18, ResNet34, ResNet50, ResNet101, ResNet152) is composed of three common blocks, the input block, output block and the middle block as convolutional layers, different ResNet have different middle part, with different parameters and amounts, the structure of ResNet34 is as shown below.

![[OPTSxa6b4]_Residual_Net_Structure](/assets/images/[OPTSxa6b4]_Residual_Net_Structure.svg)

​	Based on the common design of the residual network, the holistic network structure of ResNet34 `resnet34(·)` is defined in method `forward(self, x: Tensor) -> Tensor` pf class `ResNet`, which is, simply the three blocks (the `foward(·)` function represent the information forwarding/flow in the network structure). All the operation used are also defined by default,[^4]

- The input block contains a $\mathrm{conv}(\cdot)$ kernel of `size=`$7\times7$, `stride=2`, `padding=3` and a max pooling kernel of `size=`$3\times3$, `stride=2`, `padding=1` by default. This map a input image from $224\times224$ to a feature map of $56\times56$.
- The convolutional block contains of four layers, for ResNet34,
  - layer 1, $\begin{bmatrix}3\times3,&64 \\ 3\times3,&64\end{bmatrix}\times3$.
  - layer 2, $\begin{bmatrix}3\times3,&128 \\ 3\times3,&128\end{bmatrix}\times4$.
  - layer 3, $\begin{bmatrix}3\times3,&256 \\ 3\times3,&256\end{bmatrix}\times6$.
  - layer 4, $\begin{bmatrix}3\times3,&512 \\ 3\times3,&512\end{bmatrix}\times3$.
- The output block contains of a adaptive average pooling, which stretch the feature map into $1\times1$, and then the full connected layer will give out the final result of the network.

```python
# the input block
x = self.conv1(x) # nn.Conv2d(3, self.inplanes, kernel_size=7, stride=2, padding=3, bias=False) by default
x = self.bn1(x) # nn.BatchNorm2d(self.inplanes) by default
x = self.relu(x) # nn.ReLU(inplace=True) by default
x = self.maxpool(x) # nn.MaxPool2d(kernel_size=3, stride=2, padding=1) by default

# the convolutional block
x = self.layer1(x) # self._make_layer(block, 64, layers[0]) by default
x = self.layer2(x) # self._make_layer(block, 128, layers[1], stride=2, dilate=False) by default
x = self.layer3(x) # self._make_layer(block, 256, layers[2], stride=2, dilate=False) by default
x = self.layer4(x) # self._make_layer(block, 512, layers[3], stride=2, dilate=False) by default

# the output block
x = self.avgpool(x) # nn.AdaptiveAvgPool2d((1, 1)) by default
x = torch.flatten(x, 1)
x = self.fc(x) # nn.Linear(512 * block.expansion, num_classes) by default
```

​	Then it is intuitive to direct define the `resnet34` model as shown below, note that the `BasicBlock` is used here, another block defined for choices is the `Bottleneck` block, both of them are explained and compared as following image shows.

```python
# the implementation of the ResNet34, with BasicBlock
def resnet34(pretrained: bool=False, progress: bool=True, **kwargs: Any) -> ResNet:
    model = ResNet(BasicBlock, [3, 4, 6, 3], **kwargs)
    if pretrained:
        state_dict = load_state_dict_from_url(model_urls['resnet34'], progress=progress)
        model.load_state_dict(state_dict)
    return model
```

​	To be more precise, `BasicBlock` is used in `resnet18`, `resnet34` in `torchvision`, while `Bottleneck` block is used in `resnet50`, `resnet101`, `resnet152`, `resnext50_32x4d`, `resnext101_32x8d`, `wide_resnet50_2` and `wide_resnet101_2`. In `Bottleneck` structure, the $1\times1$ $\mathrm{conv}(\cdot)$ is introduced to increase the computational efficiency. Few advantages are included with this $1\times1$ $\mathrm{conv}(\cdot)$ operation,

- $1\times1$ $\mathrm{conv}(\cdot)$ composite multiple feature map while maintaining their size, thus merge the information from multiple channels.
- Low complexity compared with convolutional kernel of other size. (compared with the `BasicBlock`, `Bottleneck` save the computational complexity by $5.9\%$, from $1179648$ to $69632$ parameters)
- $1\times1$ $\mathrm{conv}(\cdot)$ introduce more nonlinear property by include one more $\mathrm{relu}(\cdot)$ compared with $3\times3$ $\mathrm{conv}(\cdot)$.

![[OPTSxa6b4]_BasicBlock_Bottleneck](/assets/images/[OPTSxa6b4]_BasicBlock_Bottleneck.svg)

### **3.2. Small Tricks to Design Your Residual Network**

​	There are some tricks when building a ResNet, most of them are inspired by VGG.

- Each time you reduce the **spatial size** by downsampling, you also need to increase the number of **filters** by two, thus the time complexity is maintained: $\mathrm{spatial}\  \mathrm{size}/2\to$ # $\mathrm{filters}\times2$ (inspired by VGG)

- $\mathrm{conv}(\cdot)$ of size $3\times3$ is used (inspired by VGG)

- There exists a **global average pooling** (GAP) operation before the full connected layer in the output block, there are few advantages to adopt this strategy:
  - It is more native to the convolution structure by enforcing correspondences between feature maps and categories. Thus the feature maps can be easily interpreted as categories confidence maps.
  - There is no parameter to optimize in the global average pooling thus **overfitting is avoided** at this layer.
  - GAP sums out the spatial information, thus it is **more robust** to spatial translations of the input.
  
- The GAP layer and 1000-d full connected layer with $\mathrm{softmax}(\cdot)$ is usually replaced by an **adaptive global average pooling** in real application, i.e., for `yourmodel`,

  ```python
  yourmodel = resnet34(pretrained = True)
  yourmodel.avgpool = nn.AdaptiveAvgPool2d((1, 1))
  yourmodel.fc = nn.Sequential(
      nn.BatchNorm1d(512*1), # which is important to prevent big loss explosion
      nn.Linear(512*1, '''your class number'''),
  )
  ```

- The holistic network is constructed with pure **batch normalization**, without any dropout.

> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSxa6b4]**

[^1]: He, Kaiming, et al. "Deep residual learning for image recognition." *Proceedings of the IEEE conference on computer vision and pattern recognition*. 2016.
[^2]: [The Video of Live Speech on ResNet, CVPR2016](https://zhuanlan.zhihu.com/p/54072011?utm_source=com.tencent.tim&utm_medium=social&utm_oi=41268663025664)
[^3]: Balduzzi, David, et al. "The shattered gradients problem: If resnets are the answer, then what is the question?." *International Conference on Machine Learning*. PMLR, 2017.
[^4]: https://github.com/pytorch/vision/blob/master/torchvision/models/resnet.py
[^5]:

