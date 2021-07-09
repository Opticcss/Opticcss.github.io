---
layout: optics_post
title:  "Fast Convolution (FC, FastConv) by Fast Fourier Transform"
author: Li Jinzhao
categories: [signal processing]
image: ....jpg
tags: [harmonic analysis, Julia]
typora-root-url: ..
---
> **Abstract**/**Snippet**. FFT convolution, as kind of realization for fast convolution, using the multiplication of frequency spectra, with FFT, and inverse FFT, provide a faster speed and same results compared with the standard one for filter with kernels longer than about 64 points.[^1] This article is mainly about its implementation in `Julia`. 

**Contents**

* toc
{:toc}
## **1. Commonly Used Fast Convolution Algorithm**

​	Fast convolution as FFT convolution uses the overlap-add method together with the fast Fourier transform, allowing signals to be convolved by multiplying their frequency spectra, which can be summarized as following[^2]
$$
\begin{equation}
\begin{split}
\mathrm{E}*\mathrm{k}=\mathscr{F}^{-1}\{\mathscr{F}\{\mathrm{E}\}\times\mathscr{F}\{\mathrm{k}\}\},
\end{split}
\end{equation}
$$
​	Take the a 2-D input, a image, as an example, the image is converted into the frequency domain (using FFT), multiply, and convert back to the time domain (using the IFFT).
​	Complexity analysis of this algorithm reveals that it
exhibits $O(n\log n)$ asymptotic behaviour, which out-perform the direct summation method as following (which takes $O(n^2)$), note that here, both `E` and `k` have $n$ samples.
$$
\begin{equation}
\begin{split}
(\mathrm{E}*\mathrm{k})(x,y)=\sum_{i=0}^{m-1}\sum_{j=0}^{n-1}\mathrm{E}(x+i,y+j)\mathrm{k}(i,j),
\end{split}
\end{equation}
$$
​	Note that the $O(n\log n)$ is the easiest to implement, and 



multiple dispatch, efficient
memory allocations, and macros to generate multi-
dimensional code



By allocating the memory in
a helper function, the algorithm also improves effi-
ciency by reusing memory within the actual convolu-
tion function. Additionally, we use @inbounds to
eliminate array bounds checking, further increasing
performance speedups.
Our implementation covers a wide variety of
signal types (real, complex, boolean, irrational, un-
signed integers, etc) through native Julia multiple
dispatch. Furthermore, we utilize the Cartesian pack-
age to create auto generative code that can compute
convolutions in any dimension. The process by the
compiler is as follows:
1) The dimensionality of the inputs is identified
(let’s say both inputs are of dimension k)
2) The k-dimensional convolution code is gener-
ated (if it has never been before)
3) The inputs are processed by the auto-generated
code.



#### **<span id="jump01">Addendum 1st </span>——**

​	write something...

> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx84a6]**

[^1]: Smith, Steven. *Digital signal processing: a practical guide for engineers and scientists*. Elsevier, 2013.
[^2]: https://ccrma.stanford.edu/~jos/ReviewFourier/Example_FFT_Convolution.html
[^3]: Amini, Alexander, Berthold Horn, and Alan Edelman. "Accelerated convolutions for efficient multi-scale time to contact computation in Julia." *arXiv preprint arXiv:1612.08825* (2016).
[^4]: Amini, Alexander, et al. “FastConv.jl: Accelerated Convolutions for the Julia language.” Github.

