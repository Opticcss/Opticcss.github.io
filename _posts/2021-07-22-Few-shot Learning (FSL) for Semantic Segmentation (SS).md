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
## **1. Introduction to Prototype Network for FSL**

​	One of valid ways for handling the visual comprehension task is CNN, which achieve a breakthrough for semantic segmentation (always be treated as an intensive forecasting task), now exposes the following problems.

-    **Large-scale data sets** are required to support intensive forecasting tasks, which portends high overhead (expensive, also time consuming).
-    Difficulties of **model generalization**, especially in the prediction of new categories.

​	The design of few-shot segmentation (FSS, few-shot learning in semantic segmentation) is to **achieve the segmentation of categories in the new sample**, but **only based on a support set containing $K$** (small sampling of data) **training images**.

​	Up to present (2021. 07), an outright majority good performances are concentrated on the embedding function (which map small samples to $N$ vectors as prototypes, the key of **prototype network**, P-net[^1]) and its derivatives, the principle of which is mapping the samples (support set) and the detected image (query set) to vectors (one support prototype for each category), and identify the one with shortest mean point Euclidean distance (Bregman divergence) as the predicted category (query vector $\mapsto$ query prototype), some of them are summarized as following.

- co-FCN[^2], in which the robustness of few-shot segmentation for **sparse labels** is explained, hence demonstrate that it is feasible to use sparse labels and comparably small samples to guide the FSS.
- PANet[^3], in which the prototype is optimized based on nonparametric metric learning, and the prototype alignment regularization between the support set and the query set is introduced, which makes full use of the knowledge from the support set, and provides **better generalization performance** in FSS.
- CANet[^4], introduced a pixelwise segmentation network independent of categories for FSL, which is based on iterative optimization modules and its support set as **bounding box annotations**.
- CRNet[^5], in which the model **concurrently make predictions** (i.e., the co-occurrent objects) for both the support set and query set based on the **cross-reference structure**. It also develop a **mask refinement** module to **recurrently refine the prediction of the foreground regions**.

## **2. Methodological Approaches**

​	Corresponding algorithms/method to handling the semantic segmentation task are noted below in detail.

### **2.1. co-FCN**

​	An algorithm for semantic segmentation of small samples using sparse labels, which adopted the **dual branch network** structure, one is the **conditional branch** (composed of the convolutional layer of VGG-16) for support set, the other is the **segmentation branch** (also composed of the convolutional layer of VGG-16) for the query image[^2].

​	To be more precise, co-FCN feed the concatenation of labelled information with the origin image into the conditioning branch, after embedding, the output is used to concatenate with the embedding (feature) of query image, this will give a pixel-level segmentation, as following shows.

![[OPTSxa6b3]_Co-FCN_Model](/assets/images/[OPTSxa6b3]_Co-FCN_Model.svg)

​	Note that co-FCN does not use logical OR operation in the case of $K$-Shot, but takes the **average operation** of the features as the output of conditional branch. Based on which, the combination with the segmentation branch will gives the final prediction. Moreover, the cross entropy is used as the loss function.

​	co-FCN performs better in the case of sparse labels, the segmentation task can even be achieved under the extreme conditions of only one positive pixel and one negative pixel.

​	It finally achieve an <u>$\mathrm{IoU}$ metric</u> (as for its definition, see [**Addendum 1st**](# jump01)) <u>of $60.1$ and $58.4$ for $1$-Shot and $5$-Shot sparse support annotations compared with the $52.6$ and $52.9$ by Global Parameter Prediction</u>, but needs the support from finetuning from ILSVRC pretraining to improve its stability and data efficiency.

​	One of the obvious LIMITATION of co-FCN is its **performance in dense support annotations**.

### **2.2. PANet**

​	**Prototypes alignment regularization**[^3] was proposed in the training process PANet to make full use of information in support set (hence it dose not increase the computational cost of inference), and adopt **metric learning without any extra parameters**, which performs the segmentation by matching the embedded space by each pixel.

​	The formal procedure is to retrieve the depth feature from the support set and query image by the **same backbone** (convolutional layer from VGG-16, the maximum pooling step in $4\mathrm{th}$ layer is changed into $1$, and the $5\mathrm{th}$ layer is changed into a dilated convolution layer with an expansion of $2$ to enlarge the receptive field), and then adopt the **masked average pooling** to embed different categories into different prototypes. By the similar process of comparison as in the prototypical network ($\mathrm{cosine}\to\mathrm{softmax}(\cdot)$, to obtain the probability as the metric for inference), with the extra process of backward verification, which takes the predicted result (mask) and the query feature as support set, and the original support set as the query set, the final output as the lass will give further justification/optimization of the former segmentation.

![[OPTSxa6b3]_PANet_Model](/assets/images/[OPTSxa6b3]_PANet_Model.svg)

​	The explicit process of the feature retrieving in PANet is to adopt the late fusion mask method (extract the feature image first, then mask the feature image, to maintain the consistency), the mask for the $k\mathrm{th}$ image ($F_{c,k}$) in category $c$ is denoted as $M_{c,k}$. For $K$ samples in each category, the prototype for category $c$ is obtained by the average pooling process

$$
\begin{equation}
\begin{split}
p_c=\frac1K\sum_k\frac{\sum_{x,y}F_{c,k}^{(x,y)}\mathbb{I}[M_{c,k}^{(x,y)}=c]}{\sum_{x,y}\mathbb{I}[M_{c,k}^{(x,y)}=c]},
\end{split}
\end{equation}
$$

​	The so-called prototype alignment regularization (PAR) is based on the maximum probability of query set $F_q$ in category $j$ and metric function (which is defined as  $\cos(\cdot)$ for stability), as shown below.

$$
\begin{equation}
\begin{split}
\hat{M}_q^{(x,y)}&=\mathop{\rm argmax}\limits_{j}\tilde{M}_{q,j}^{(x,y)}\\
&=\mathop{\rm argmax}\limits_{j}\frac{\exp\big(-\alpha\mathrm{d}(F_q^{(x,y)},p_j)\big)}{\sum_{p_j\in\mathcal{P}}\exp\big(-\alpha\mathrm{d}(F_q^{(x,y)},p_j)\big)},
\end{split}
\end{equation}
$$

​	After this, PAR exchange the positions of query set and the support set, and the prediction is made again. The prediction result is compared with the foregoing result, and loss function is calculated as $\mathcal{L}_{\mathrm{PAR}}$ shown below, so as to obtain more information from support set (only used in training process but not the prediction).

$$
\begin{equation}
\begin{split}
\mathcal{L}&=\mathcal{L}_{\mathrm{SEG}}+\lambda\mathcal{L}_{\mathrm{PAR}},\\
\mathcal{L}_{\mathrm{SEG}}&=-\frac1N\sum_{x,y}\sum_{p_j\in\mathcal{P}}\mathbb{I}[M_{q}^{(x,y)}=j]\log\tilde{M}_{q,j}^{(x,y)},\\
\mathcal{L}_{\mathrm{PAR}}&=-\frac1{CKN}\sum_{c,k,x,y}\sum_{p_j\in\mathcal{P}}\mathbb{I}[M_{q}^{(x,y)}=j]\log\tilde{M}_{q,j}^{(x,y)},
\end{split}
\end{equation}
$$

​	It achieve the <u>$\mathrm{MIoU}$ of $48.1$ and $55.7$ for $1$-Shot and $5$-Shot on PASCAL-5i dataset compared with the $41.1$ and $41.4$ by co-FCN</u>, note that these are the 1-way segmentation results, the 2-way results are $45.1$ and $53.1$ for $1$-Shot and $5$-Shot, which are far greater than other methods.

​	The possible LIMITATION of PANet is about the **computation complexity**, which is large when the prediction for each pixel is made (time complexity as $O(n^\gamma)+$).

### **2.3. CANet**

​	In CANet, a two-branch dense comparison module which adopt the multi-level feature from CNN effectively[^4], with this feature, it performs comparison between the support set and query image, and use an iterative optimization module which iteratively refines the predicted results. Its ability to refine the result to fine-grained iteratively can be generalized to imperceptible categories.

​	The following scheme shows the network structure composed of the **dense comparison module** (DCM, adopt the Res-50 as its backbone, first adopt global average pooling to obtain the global image features, and apply the concatenated feature maps to another convolutional block with $256$ $3\times3$ convolutional filters for comparison) and an **iterative optimization module** (IOM), explicitly. The **attention mechanism** is also used in CANet to effectively fuse information from multiple support examples under the setting of $K$-Shot learning, which is consists of $2$ convolutional blocks (and a $\mathrm{softmax}(\cdot)$ operation), one with $256$ $3\times3$ filters and $3\times3$ max pooling, the other has a $3\times3$ convolution and a global average pooling.

![[OPTSxa6b3]_CANet_Model](/assets/images/[OPTSxa6b3]_CANet_Model.svg)

​	The whole network can handle both the $K$-Shot problem and the $1$-Shot problem, the attention mechanism is only used in $K$-Shot problem to obtain the weights of each sample.

​	The iterative optimization module is designed to handle the problem of segment accuracy when there exist variances in appearance within the same category. The ensemble process is in a residual form, as following shows, which takes the feature map $F(x,y_{t-1})$ (concatenation of feature $x$ and the prediction mask $y_{t-1}$) as input, and the output $M_t$ is used to capture the multi-scale information by ASPP ($4$ parallel branches that include three $3\times3$ convolutions with dilation rates of $6$, $12$, and $18$ respectively and a $1\times1$ convolution, see [**Addendum 2nd**](# jump02)). Finally , a $1\times1$ convolution is used to generate the final foreground and background masks.

$$
\begin{equation}
\begin{split}
M_t=x+F(x,y_{t-1}),
\end{split}
\end{equation}
$$

​	Its performance measured by <u>$\mathrm{MIoU}$ is $52.0\%$ on PASCAL VOC 2012 dataset and SDS, it is worth noting that this performance is based on **bounding box** annotation support set, and is comparable to the result with expensive pixel-level annotated support set result as $54.0\%$. Compared with SOTA (ex. PL), by metric of $\mathrm{FB-IoU}$, is $66.2\%$ (CANet, $1$-Shot) to $61.2\%$ (PL, $1$-Shot) on PASCAL-5i dataset</u>.

​	The possible LIMITATION of CANet is  

### **2.4. CRNet**

​	CRNet can perform prediction in both the query set and the support set in the segmentation task[^5] (using the Siamese encoder, the cross-reference module, the condition module), and add a mask refinement module after the CR module and the condition module, to realize the finetuning for the $K$-Shot learning.

![[OPTSxa6b3]_CRNet_Model](/assets/images/[OPTSxa6b3]_CRNet_Model.svg)

​	The crucial part of the network is **cross-reference module** (CRM, average pooling the output from Siamese encoder, and add them to pair of two FC layers, then apply the Sigmoid activation function. Finally fused then together by elementwise multiplication), which is to mine **co-occurrent features** in two images and give out updated representations.

​	In the condition module (aimed at embedding, to fuse the features into representations), first bilinearly up-sampling the vector to the same spatial size of the feature maps and concatenating them to achieve a category relevant embedding, then a residual convolution to process the concatenated features.

​	The mask refinement module includes the down-sample block (feature maps samples to factor of $2$), global convolution block (composed of $2$ groups of $1\times7$ and $7\times1$ convolutional kernels) and the combine block (generate refined feature representations).

​	Its performance measured by <u>$\mathrm{IoU}$ is $71.5$ for $5$-Shot on COCO 2014 dataset, which is larger than SOTA method (the CANet), which gives $69.6$, for the performance in $\mathrm{MIoU}$, it have $58.8\%$ compared with $57.1\%$ in CANet</u>.

​	To conclude, above four ways of solving the segmentation tasks with support with small samples/sparse labels reveal following conclusions.

> - The existence of effective solution to the dense semantic segmentation problem by sparse samples, pixelwise classification, encoder-decoder structure and double branch prototype network.
> - Deep learning is significantly better than (in performance) non-learning method in segmenting problem.
> - The iterative optimization which includes segmentation prediction results is similar to self-supervised, maybe the introduction of ResNet into this form will get a comparably good result.
> - Both of the query set and support set have the same status in few-shot learning, thus can improve the segmentation accuracy through crossover/swapping of the two.
> - Network fine-tuning can help achieve higher performance, especially in $K$-Shot condition.

(Optional) Your Idea to improve the prior works, if have

## **3. Some Possible New Directions/Further Steps**

Your comments on the development of your investigated topic/field



#### **<span id="jump01">Addendum 1st </span>—— Terminology for FSL and SS**

​	Several terminologies used above are presented as following.

- support set $S$: a set includes few samples tagged for learning, the so-called "few-shot", to be more explicit, it composed of the images $x_s^i\in R^{H_i\times W_i\times3}$ and their segmentation masks $y_s^i\in R^{H_i\times W_i}$.
- query set $Q$: the test sample, the unmarked sample, composed of the query image $x_q$ and GT mask $y_q(c)$ for category $c$, the final results are presented as the prediction mask $\hat{y}_q(c)$.
- semantic segmentation: to label each point into elements in category set $C$ according to "semantic", so that different kinds of things can be visualized differently, this task can be digitalized as an operation

$$
\begin{equation}
\begin{split}
\mathbf{In}_{H\times W\times3}\to\mathbf{Pd}_{H\times W\times\mathrm{class}},
\end{split}
\end{equation}
$$

​	several metrics commonly used as reflections on the methodology for semantic segmentation are the intersection over union $\mathrm{IoU}$, mean intersection over union $\mathrm{MIoU}$ (can help to see how effective the model is in different categories), pixel accuracy $\mathrm{PA}$, category pixel accuracy $\mathrm{CPA}$, and the mean pixel accuracy $\mathrm{MPA}$.

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

#### **<span id="jump02">Addendum 2nd </span>—— Atrous Spatial Pyramid Pooling module**

​	The Atrous Spatial Pyramid Pooling module is used to capture the multi-dimensional information in the feature map, in the CANet, the ASPP module has an extra $1\times1$ convolution operation, which is as shown below.

```python
global_feature = F.avg_pool2d(out, kernel_size = feature_size) # the global feature [1, 256, 1, 1]
global_feature = self.layer6_0(global_feature) # the full convolution layers
global_feature = global_feature.expand(-1, -1, feature_size[0], feature_size[1]) #expansion by a [1, 256, 41, 41]
out = torch.cat([global_feature, self.layer6_1(out), self.layer6_2(out), self.layer6_3(out),self.layer6_4(out)], dim = 1) #convolution with multiple dialation, then concatenate with [1, 1280, 41, 41]
```

​	Its main operation is very simple, in which, the dilated convolution with different dilation rates is used to process the top feature map, and all the results obtained are concatenated together to expand the number of channels. Finally, through a $1\times1$ convolution layer, the number of channels is reduced to the desired one (since which the original operation do not include the former $1\times1$ convolution).

```python
class ASPP(nn.Module):
    def __init__(self, in_channel=512, depth=256):
        super(ASPP,self).__init__()
        # global average pooling : init nn.AdaptiveAvgPool2d ;also forward torch.mean(,,keep_dim=True)
        self.mean = nn.AdaptiveAvgPool2d((1, 1))
        self.conv = nn.Conv2d(in_channel, depth, 1, 1)
        # k=1 s=1 no pad
        self.atrous_block1 = nn.Conv2d(in_channel, depth, 1, 1)
        self.atrous_block6 = nn.Conv2d(in_channel, depth, 3, 1, padding=6, dilation=6)
        self.atrous_block12 = nn.Conv2d(in_channel, depth, 3, 1, padding=12, dilation=12)
        self.atrous_block18 = nn.Conv2d(in_channel, depth, 3, 1, padding=18, dilation=18)

            self.conv_1x1_output = nn.Conv2d(depth * 5, depth, 1, 1)

    def forward(self, x):
        size = x.shape[2:]

        image_features = self.mean(x)
        image_features = self.conv(image_features)
        image_features = F.upsample(image_features, size=size, mode='bilinear')

        atrous_block1 = self.atrous_block1(x)

        atrous_block6 = self.atrous_block6(x)

        atrous_block12 = self.atrous_block12(x)

        atrous_block18 = self.atrous_block18(x)

        net = self.conv_1x1_output(torch.cat([image_features, atrous_block1, atrous_block6,
                                              atrous_block12, atrous_block18], dim=1))
        return net
```

> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSxa6b3]**

[^1]: Snell, Jake, Kevin Swersky, and Richard S. Zemel. "Prototypical networks for few-shot learning." arXiv preprint arXiv:1703.05175 (2017).
[^2]: Rakelly, Kate, et al. "Conditional networks for few-shot semantic segmentation." (2018).
[^3]: Wang, Kaixin, et al. "Panet: Few-shot image semantic segmentation with prototype alignment." *Proceedings of the IEEE/CVF International Conference on Computer Vision*. 2019.
[^4]: Zhang, Chi, et al. "Canet: Class-agnostic segmentation networks with iterative refinement and attentive few-shot learning." *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition*. 2019.

[^5]: Liu, Weide, et al. "Crnet: Cross-reference networks for few-shot segmentation." *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition*. 2020.
[^6]: Boudiaf, Malik, et al. "Few-Shot Segmentation Without Meta-Learning: A Good Transductive Inference Is All You Need?." *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition*. 2021.

[^7]: Min, Juhong, Dahyun Kang, and Minsu Cho. "Hypercorrelation squeeze for few-shot segmentation." *arXiv preprint arXiv:2104.01538* (2021).

