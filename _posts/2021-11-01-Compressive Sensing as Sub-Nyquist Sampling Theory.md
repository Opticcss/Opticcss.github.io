---
layout: optics_post
title:  "Compressive Sensing as Sub-Nyquist Sampling Theory"
author: Li Jinzhao
categories: [Signal Processing]
image: ....jpg
tags: [Harmonic Analysis, Julia, Machine Learning]
typora-root-url: ..
---
> **Abstract**/**Snippet**.

**Contents**

* toc
{:toc}
**Compressive sensing** (CS), by D. Donoho, E. Candes, and T. Tao, as an emerging **sampling theory** with support of the **sparse signal** and **random sampling** method, performs a **non-linear reconstruction algorithm** for perfect recovery of signal in a $f_s$​ **far less than Nyquist sampling rate**.

​	For short, compressive sensing change the sampling strategy to an **unequally spaced, random sub-sampling** to realize the "compressive sampling", hence compress the data when sampling.

​	This can dramatically reduce sampling points of imaging, thereby improving imaging speed, while also ensuring the quality of the original image (i.e., the reconstruction quality).

## **1. Sparse Problem to Solve**

​	The basic problem solved by compressive sensing is the way to sample the signal and how to reconstruct it. First of all, the input signal $\boldsymbol{x}$​​​ should satisfy the following two properties.

### **1.1. Prerequisite 1st, Sparsity**

​	**Sparsity** of one signal $\boldsymbol{x}$ can be measured by the $\ell_0$ norm $\|\boldsymbol{x}\|_0$, for $N$ as the length of vector $\boldsymbol{x}$, the signal can be called $K$-sparse,  if it satisfy

$$
\begin{equation}
\begin{split}
K=\|\boldsymbol{x}\|_{\ell_0}\ll N,
\end{split}
\end{equation}
$$

​	This property of signal in frequency domain can make it easier to reconstruct the signal in frequency domain. In the opposite, the iterative algorithm cannot solve the non-sparse problem in a tolerable time period.

​	In CS, the signal should always be sparse at least in one domain, which is hence called the **sparseland**, and the signal itself is called **compressible signal**.

### **1.2. Prerequisite 2nd, Incoherence**

​	**Incoherence** of the problem is measured by the **restricted isometry property** (**RIP**, which is stated by the uncertainty prociple) of the measurement matrix $\boldsymbol{\Phi}$​.

​	This condition (RIP) yields for all vector $\boldsymbol{c}$​ sharing same $K$​ nonzero entris as $\boldsymbol{x}$​, and let $\boldsymbol{\Phi}_T, T\subset\set{1,\cdots,N}$​, be the $M\times|T|$​ submatrix obtained by extracting the columns of $\boldsymbol{\Phi}$​ corresponding to the indices in $T$​, then for the S-restricted isometry constant $\delta_S$​ of $\boldsymbol{\Phi}$​ which is the smallest quantity, satisfied,

$$
\begin{equation}
\begin{split}
(1-\delta_S)\|\boldsymbol{c}\|_{\ell_2}^2\leq\|\boldsymbol{\Phi}_T\boldsymbol{c}\|_{\ell_2}^2\leq(1+\delta_S)\|\boldsymbol{c}\|_{\ell_2}^2,
\end{split}
\end{equation}
$$

​	Another equivalent method of identifing the matrix obeys RIP is to **check whether the inner product of the measurement matrix and the sparse basis $\mu(\boldsymbol{\Phi},\boldsymbol{\Psi})$ is small enough** (the smaller $\mu$ indicates the more irrelavent of $\boldsymbol{\Phi}$ and $\boldsymbol{\Psi}$​),

$$
\begin{equation}
\begin{split}
&\mu(\boldsymbol{\Phi},\boldsymbol{\Psi})\in[1,\sqrt{n}],\\
&\text{where }
\mu(\boldsymbol{\Phi},\boldsymbol{\Psi})=\sqrt{n}\max_{1\leq k,j\leq n}|\langle\phi_k,\psi_j\rangle|,
\end{split}
\end{equation}
$$

​	One of the measurement matrix that universal used in compressive sensing without violence of RIP is the **Gaussian ramdom matrix**, and it represents the random sub-sampling mechanism, hence **frequency of leaking is evenly distributed throughout the frequency domain**, which is always easier to deal with when reconstruction.



关于**稀疏性**可以这样简单直观地理解：若信号在某个域中**只有少量非零值**，那么它在该域稀疏，该域也被称为信号的**稀疏域**，可以轻松地在稀疏域（频域）复原出原信号，称为可压缩信号

如果它在频域中不稀疏，我们可以做DWT、DCT等，找到它的稀疏变换

如图像压缩领域的JPEG格式，就是将图像变换到离散余弦域，得到近似稀疏矩阵，只保留较大的值，从而实现压缩



而如果采用**随机**亚采样（图b上方的红点），那么这时候频域就不再是以固定周期进行延拓了，而是会产生大量不相关（incoherent）的干扰值。如图c，最大的几个峰值还依稀可见，只是一定程度上被干扰值覆盖。这些干扰值看上去非常像随机噪声，但实际上是由于三个原始信号的非零值发生能量泄露导致的（不同颜色的干扰值表示它们分别是由于对应颜色的原始信号的非零值泄露导致的）



这可以理解成随机采样使得频谱不再是整齐地搬移，而是一小部分一小部分胡乱地搬移，频率泄露均匀地分布在整个频域，因而泄漏值都比较小，从而有了恢复的可能。



核心思想——以比奈奎斯特采样频率要求的采样密度更稀疏的密度对信号进行随机亚采样，由于频谱是均匀泄露的，而不是整体延拓的，因此可以通过特别的追踪方法将原信号恢复。

### **1.3. Reconstruction as an Ill-posed Linear Inverse Problem**

​	Assume a sparsity signal $\boldsymbol{x}$​ (transformed by sparse matrix $\boldsymbol{\Psi}$​ and hence gives the $K$​-sparse parameters $\boldsymbol{s}$​) with incoherence measurement matrix $\boldsymbol{\Phi}$​, compressive sensing solve the reconstruction problem of $\boldsymbol{x}$​ given $\boldsymbol{y}$​​ as shown below.

![[OPTSx0011]_Compressive_Sensing_Sensing_Matrix](C:\Users\a1020\Desktop\Opticcss.github.io\assets\images\[OPTSx0011]_Compressive_Sensing_Sensing_Matrix.svg)

$$
\begin{equation}
\begin{split}
&\boldsymbol{y}=\boldsymbol{\Phi}\boldsymbol{x},\\
&\text{where }\boldsymbol{x}=\boldsymbol{\Psi}\boldsymbol{s},
\end{split}
\end{equation}
$$

​	Obviously, this is an **ill-posed linear inverse problem** (also called, the **underdetermined system of linear equation**), which is a $\ell_0$ minimization problem (NP hard), to make it more explicitly, write $\boldsymbol{\Theta}:=\boldsymbol{\Phi}\boldsymbol{\Psi}$​​, which yields,

![[OPTSx0011]_Compressive_Sensing_Observation_Matrix](C:\Users\a1020\Desktop\Opticcss.github.io\assets\images\[OPTSx0011]_Compressive_Sensing_Observation_Matrix.svg)

$$
\begin{equation}
\begin{split}
\boldsymbol{y}=\boldsymbol{\Theta}\boldsymbol{s},
\end{split}
\end{equation}
$$

​	i.e., given $\boldsymbol{y}$, $\boldsymbol{\Phi}$, $\boldsymbol{\Psi}$, solve $\boldsymbol{x}$ with the minimum $\ell_0$ norm $\|\boldsymbol{x}\|_0$.

​	Way to solve this is to transform it into a $\ell_1$​ minimization problem.





最核心的概念在于试图**从原理上降低对一个信号进行测量的成本**。比如说，一个信号包含一千个数据，那么按照传统的信号处理理论，至少需要做一千次测量才能完整的复原这个信号。这就相当于是说，**需要有一千个方程才能精确地解出一千个未知数来**。但是压缩感知的想法是假定信号具有某种特点（比如在小波域上系数稀疏的特点），那么就可以只做三百次测量就完整地复原这个信号（这就相当于只**通过三百个方程解出一千个未知数**）











如果一个信号**在某个变换域是稀疏的**，那么就可以用一个**与变换基不相关的**观测矩阵将变换所得高维信号投影到一个低维空间上，然后通过求**解一个优化问题**就可以从这些少量的投影中以高概率重构出原信号



















> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx0011]**

[^1]: Elad M. Sparse and redundant representations: from theory to applications in signal and image processing[J]. 2010.