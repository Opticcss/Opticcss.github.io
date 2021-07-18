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
## **1. Introduction to Prototype Network as FSL**

​	One of valid ways for handling the visual comprehension task is CNN, which achieve a breakthrough for semantic segmentation (always be treated as an intensive forecasting task), now exposes the following problems.

-    **Large-scale data sets** are required to support intensive forecasting tasks, which portends high overhead.
-    Difficulties of **model generalization**, especially in the prediction of new categories.

​	The design of few-shot segmentation (FSS, few-shot learning in semantic segmentation) is to **achieve the segmentation of categories in the new sample**, but **only based on a support set containing $K$** (small sampling of data) **training images**.

​	Up to present (2021. 07), an outright majority good performances are concentrated on the embedding function (which map small samples to $N$ vectors as prototypes, the key of **prototype network**, P-net[^1]) and its derivatives, the principle of which is mapping the samples (support set) and the detected image (query set) to vectors (one support prototype for each category), and identify the one with shortest mean point Euclidean distance (Bregman divergence) as the predicted category (query vector $\mapsto$ query prototype), some of them are summarized as following.

- co-FCN[^2], in which the robustness of few-shot segmentation for sparse labels is explained, it is also feasible to use sparse labels to guide the FSS.
- 

## **2. Methodological Approaches**

(HOW is your investigated problem addressed in prior works? We strongly encourage you to classify all methods you collected into several categories.)

-    Key Idea of the method
-    Justification of method
-    Reflections on the methodology (eg. effectiveness, strengths and limitations of the methodology)
-    (Optional) Your Idea to improve the prior works, if have

### **1.1. co-FCN**

​	An algorithm for semantic segmentation of small samples using sparse labels, which adopted the double branch network structure, one is the conditional branch for support set, the other is the segmentation branch for the query image.[^2]

​	To be more precise, co-FCN feed the concatenation of labelled information with the origin image into the conditioning branch, after embedding, the output is used to concatenate with the embedding (feature) of query image, this will give a pixel-level segmentation, as following shows.

![[OPTSxa6b3]_Co-FCN_Model](/assets/images/[OPTSxa6b3]_Co-FCN_Model.svg)

### **1.2. PANet**

​	Prototypes alignment regularization was proposed in PANet to make full use of information in support set, and derive a metric learning without parameters, which performs the segmentation by matching the embedded space by each pixel.

​	The formal procedure is to retrieve the depth feature from the support set and query image by the same backbone, and then adopt the masked average pooling to embed different categories into different prototypes. By the similar process of comparison as in co-FCN ($\mathrm{cosine}\to\mathrm{softmax}(\cdot)$), with the extra process of backward verification, which takes the predicted result (mask) and the query feature as support set, and the original support set as the query set, the final output as the lass will give further justification/optimization of the former segmentation.

### **1.3. CANet**

​	In CANet, a two-branch dense comparison module which adopt the multi-level feature from CNN effectively, with these features, it performs comparison between the support set and query image, and use an iterative optimization module which iteratively refines the predicted results. Its ability to refine the result to fine-grained iteratively can be generalized to imperceptible categories.

​	The attention mechanism is also used in CANet to effectively fuse information from multiple support examples under the setting of $K$-shot learning, which reaches an $\mathrm{MIoU}$ of $55.4\%$ for 1-shot segmentation and $57.1\%$ for 5-shot segmentation.

### **1.4. CRNet**





## **3. Findings**

Your comments on the development of your investigated topic/field

further step

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
  - model based, manipulate model parameters to establish the mapping between input and prediction, $P_\theta(y\|x,S)=f_\theta(x,S)$.
  - metric based, the application of the nearest-neighbour, on the samples from batch and support set, $P_\theta(y\|x,S)=\sum_{(x_i,y_i)\in{S}}k_\theta(x,x_i,S)y_i$.
  - optimization based, adjust the optimization method to get the job done, $P_\theta(y\|x,S)=f_{\theta(S)}(x)$.

> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSxa6b3]**

[^1]: Snell, Jake, Kevin Swersky, and Richard S. Zemel. "Prototypical networks for few-shot learning." arXiv preprint arXiv:1703.05175 (2017).
[^2]: Rakelly, Kate, et al. "Conditional networks for few-shot semantic segmentation." (2018).
[^3]: Wang, Kaixin, et al. "Panet: Few-shot image semantic segmentation with prototype alignment." *Proceedings of the IEEE/CVF International Conference on Computer Vision*. 2019.
[^4]: Zhang, Chi, et al. "Canet: Class-agnostic segmentation networks with iterative refinement and attentive few-shot learning." *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition*. 2019.

[^5]: Liu, Weide, et al. "Crnet: Cross-reference networks for few-shot segmentation." *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition*. 2020.
[^6]: Boudiaf, Malik, et al. "Few-Shot Segmentation Without Meta-Learning: A Good Transductive Inference Is All You Need?." *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition*. 2021.

