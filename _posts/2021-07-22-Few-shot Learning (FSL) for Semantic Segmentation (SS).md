---
layout: optics_post
title:  "Few-shot Learning (FSL) for Semantic Segmentation (SS): Research Investigation Report (for USTC-IMCL Hiring only)"
author: Li Jinzhao
categories: [Machine Learning]
image: ....jpg
tags: [Few-shot Learning, Semantic Segmentation]
typora-root-url: ..
---

> **Summary**. Briefly summarize your investigation report here.

**Contents**

* toc
{:toc}
## **1. Introduction**

​	One of valid ways for handling the visual comprehension task is CNN, which achieve a breakthrough for semantic segmentation (always be treated as an intensive forecasting task), now exposes the following problems.

-    Large-scale data sets are required to support intensive forecasting tasks, which portends high overhead.
-    Difficulties of model generalization, especially in the prediction of new classes.

​	The design of few-shot segmentation (few-shot learning in semantic segmentation) is to achieve the segmentation of categories in the new sample, but only based on a support set containing $K$ training images.



-    Aims and Objectives (WHAT problem(s) is/are at the heart of your investigated topic?) 
-    Summary of the current research progress in this field.



​	Up to present (2021. 07), some of the methods with good performances are concentrated on the embedding method and its derivatives. 

文章的主要比较惊奇的一点是仅对原始图像做一些稀疏的标注（目标位置上点几个关键点，背景位置上点几个关键点）就却能够实现对目标的像素级的分割。网络结构和BMVC那篇paper设置类似，也是采用双分支结构，将标注信息与原始图像concate后输入conditioning branch得到输入图像的embedding。利用segmentation branch对qurey image进行特征提取，并将结果与conditioning branch得到的embedding进行concate，再进行像素级分割。

**3.5 PANet**

**创新点：**

利用了prototypes上的度量学习，无参数

提出prototypes对齐正则化，充分利用support的知识

对于带有弱注释的少样本直接使用

用同一个backbone来提取support和query的深度特征，然后使用masked average pooling从support的特征将不同的前景物体和背景嵌入不同的prototypes中，每个prototype表示对应的类别，这样query图像的每个的像素通过参考离它的嵌入表达最近的特定类的prototype来标记，得到query的预测mask后；训练的时候，得到mask后，再将刚才提取的query feature和mask作为新的“support set”，将之前的support set作为新的“query set”，再用“support set”对“query set”做一波预测，然后再算一个loss

prototype紧凑且鲁棒的对每个语义类别进行表达；mask标记那块就是无参度量学习，通过和嵌入空间的逐像素匹配来执行分割

执行一个prototype对齐正则化，用query和他的mask建立新的support，然后用这个来预测原始的support set的分割，实验证明能鼓励query的prototype对齐他们的support的prototype，只有训练的时候这么做（反向再推一次，看看是否真的相似）





**核心思想**：从Support set里提取特征，然后**利用support的分割标记，将不同类型（背景、前景）区域特征平均池化，然后作为这类的 prototype,** 给query image做分割时，对于**每一个像素**，计算其与ptototype的cosine距离，然后将这些距离输入softmax函数，得到其标签。

Prototype alignment regularization: 如果分割结果够好的话，那么从预测mask得到的prototype也可以拿来指导其它图像的分割。所以作者反过来根据query的预测结果，按同样的方式得到从query计算出的prototype，去指导support 图像的分割。



**3.6 CANet**

**主要贡献：**

开发了一种新颖的双分支密集比较模块，该模块有效地利用来自CNN的多级特征表示来进行密集的特征比较。

提出迭代优化模块，以迭代方式改进预测结果。迭代细化的能力可以推广到具有少量镜头学习的看不见的类，以生成细粒度图。

采用注意机制有效地融合来自k-shot设置中的多个支持示例的信息，其优于单次结果的不可学习的融合方法。

证明给定的支持集具有弱注释，即边界框，我们的模型仍然可以获得与昂贵的像素级注释支持集的结果相当的性能，这进一步减少了新类别对于少数镜头分割的标记工作量。

## **2. Methodological Approaches**

(HOW is your investigated problem addressed in prior works? We strongly encourage you to classify all methods you collected into several categories.)

-    Key Idea of the method.

-    Justification of method. 

-    Reflections on the methodology (eg. effectiveness, strengths and limitations of the methodology).  

-    (Optional) Your Idea to improve the prior works, if have.

## **3. Findings**

(What do you think about your investigated research topic? Note that this is an open question.)

-    Your comments on the development of your investigated topic/field.

-    Your ideas on striving a further step for this field.

#### **<span id="jump01">Addendum 1st </span>—— Terminology for FSL and SS**

​	Several terminologies used above are presented as following.

- support set: a set includes few samples tagged for learning, the so-called "few-shot".
- query set: the test sample, the unmarked sample.
- semantic segmentation: to label each point in the category according to "semantic", so that different kinds of things can be visualized differently, this task can be digitalized as an operation

$$
\begin{equation}
\begin{split}
\mathbf{In}_{H\times W\times3}\to\mathbf{Pd}_{H\times W\times\mathrm{class}},
\end{split}
\end{equation}
$$

​	several metrics for semantic segmentation are the intersection over union, mean intersection over union, pixel accuracy, category pixel accuracy, and the mean pixel accuracy.

$$
\begin{equation}
\begin{split}
\left\{
\begin{split}
&\mathrm{IoU}=\frac{\mathrm{TP}}{\mathrm{TP+FP+FN}},\\
&\mathrm{MIoU}=\frac1{k+1}\sum_{i=0}^k\frac{\mathrm{TP}}{\mathrm{TP+FP+FN}},\\
&\mathrm{PA}=\frac{\mathrm{TP+TN}}{\mathrm{TP+TN+FP+FN}},\\
&\mathrm{CPA}=\frac{\mathrm{TP}}{\mathrm{TP+FP}},\\
&\mathrm{MPA}=\frac1{k+1}\sum_{i=0}^k\frac{\mathrm{TP}}{\mathrm{TP+FP}},
\end{split}
\right.
\end{split}
\end{equation}
$$

- few-shot learning: supervised meta-learning, mainstream methods are
  - model based, manipulate model parameters to establish the mapping between input and prediction, $P_\theta(y|x,S)=f_\theta(x,S)$.
  - metric based, the application of the nearest-neighbour, on the samples from batch and support set, $P_\theta(y|x,S)=\sum_{(x_i,y_i)\in{S}}k_\theta(x,x_i,S)y_i$.
  - optimization based, adjust the optimization method to get the job done, $P_\theta(y|x,S)=f_{\theta(S)}(x)$.

> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSxa6b3]**

[^1]: Rakelly, Kate, et al. "Conditional networks for few-shot semantic segmentation." (2018).
[^2]: Wang, Kaixin, et al. "Panet: Few-shot image semantic segmentation with prototype alignment." *Proceedings of the IEEE/CVF International Conference on Computer Vision*. 2019.
[^3]: Zhang, Chi, et al. "Canet: Class-agnostic segmentation networks with iterative refinement and attentive few-shot learning." *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition*. 2019.
[^4]: Liu, Weide, et al. "Crnet: Cross-reference networks for few-shot segmentation." *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition*. 2020.
