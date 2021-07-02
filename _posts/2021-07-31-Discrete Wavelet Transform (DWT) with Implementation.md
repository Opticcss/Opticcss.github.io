---
layout: optics_post
title:  "Discrete Wavelet Transform (DWT) with Implementation"
author: Li Jinzhao
categories: [signal processing]
image: ....jpg
tags: [harmonic analysis, Julia]
typora-root-url: ..
---
> **Abstract**/**Snippet**.


**Contents**

* toc
{:toc}
## **1. Semi-Adaptive within Gabor Limit, Continuous Wavelet Transform (CWT)**

​	The short term Fourier transform can also be denoted as form of basis function using $\phi(t)=e^{\mathrm{j}\omega t}w(t)$ (which yields $\phi(\tau-t)=e^{\mathrm{j}\omega(\tau-t)}w(\tau-t)$), this is also the formal implementation of the so-called "transform". (it is also worth noting here that the only thing STFT is different form FT is that it uses a different basis function, hence change the property of its action)

$$
\begin{equation}
\begin{split}
X(t,\omega)&=\int_{-\infty}^\infty x(\tau)\phi(\tau-t)\mathrm{d}t,
\end{split}
\end{equation}
$$

​	STFT has a compactly supported basis function, which provide a good performance compared with FT, while this is not useful for its adaptability, i.e., the width/type of the window $w(t)$ is constant for variant frequency of an unstable signal, and need the user to have a great understanding for the signal to adapt it by parameters (window width, ...)

​	The process of controlling the parameters to gain suitable time-frequency resolution is not always easy, and is limited by the so-called **Gabor limit** (the signal processing edition of the Heisenberg one...) of $\Delta t\Delta f>\mathrm{const}$, the uncertainty here are the time resolution and the frequency resolution respectively.

​	A solution with self-adaptive resolution is the continuous wavelet transform (CWT), kind of technique to perform signal analysing by using an alternative approach called the multiresolution analysis (MRA), which introduce suitable basis functions $\psi(t)$ with following properties.

- Compactly supported condition, i.e.,

$$
\begin{equation}
\begin{split}
\psi(t)=0,\exists a>0,\forall|t|>a,
\end{split}
\end{equation}
$$

​		to provide sliding window in convolution operation as shown below in the wavelet analysis equation, which is a measure of similarity between the basis functions (wavelets) and the signal itself. Here the similarity is in the sense of similar frequency content. The calculated CWT coefficients refer to the closeness of the signal to the wavelet at the current scale.

$$
\begin{equation}
\begin{split}
\Psi_{x\psi}(\tau,s)=\big\langle x(t),\psi_{\tau,s}(t)\big\rangle=\int x(t)\psi_{\tau,s}^*(t)\mathrm{d}t,
\end{split}
\end{equation}
$$

- Admissibility condition, which also yields $\displaystyle{\int}_{-\infty}^\infty\psi(t)\mathrm{d}t=0$, and limit the wavelet to be a "wave".

$$
\begin{equation}
\begin{split}
c_\psi=2\pi\int_{-\infty}^\infty\frac{|\mathscr{F}\{\psi(\xi)\}^*|^2}{|\xi|}\mathrm{d}\xi<+\infty,
\end{split}
\end{equation}
$$

- Orthogonality/Orthonormal, to ensure the existence of its inverse transform, which is in the form

$$
\begin{equation}
\begin{split}
x(t)=\frac1{c^2}\int_s\int_\tau\Psi_{x\psi}(\tau,s)\frac1{s^2}\psi\bigg(\frac{t-\tau}{s}\bigg)\mathrm{d}\tau\mathrm{d}s,
\end{split}
\end{equation}
$$

​	There exists multiple types of wavelet basis, with the common form as shown below, they have a range of frequencies, which is different from Fourier basis (of basis function in STFT), with two degree of freedom, $s$ as the scaling factor and $\tau$ as the shifting factor ($1/\sqrt{s}$ is a factor for energy conservation)

$$
\begin{equation}
\begin{split}
\psi_{\tau,s}=\frac1{\sqrt{s}}\psi\bigg(\frac{t-\tau}{s}\bigg),
\end{split}
\end{equation}
$$


































任意能量有限信号 $f(t)$ 的连续小波变换 (CWT)

$$
\begin{equation}
\begin{split}
W_f(a,b)=\frac1{\sqrt{a}}\int_{-\infty}^{+\infty}f(t)\psi^*\bigg(\frac{t-b}a\bigg){\rm d}t,
\end{split}
\end{equation}
$$

$\psi(t)$ 称作母小波或基本小波，满足 $\psi(\pm\infty)=0$, $\psi(0)=0$, $\int_{-\infty}^{+\infty}\psi(t){\rm d}t=0$，也即 $\psi(t)$ 在时域为有限长的函数，最后一个条件则表明 $\psi(t)$ 必须时正时负地波动，否则它的积分结果不会为零，因此它在频域上也是有限的。小波变换的基函数是一个经过衰减处理的有限长小波，小波基函数在时域和频域上都是局部化的

小波变换的基函数族为 $\psi(t)$ 伸缩平移后的结果 $\psi_{a,b}(t)$，称为分析小波，$a$ 为伸缩参数，$1/\sqrt{a}$ 是为了保持伸缩之后能量不变，$b$ 为平移参数

可见在低频区域的变换结果具有较高的频率分辨率(频率轴是对数轴，在低频区域跨度较小)，在高频区域具有较高的时间分辨率。

小波变换在不同时间和频率上具有不同尺寸的时频窗，可以在低频区域实现较高的频率分辨率，然而其仍然受到Heisenberg不确定原理的限制，时间分辨率和频率分辨率不能两全其美。同时小波变换的时频窗并非完全是自适应的，它还需要人为地选择基函数。











## **2. Discrete Wavelet Transform (DWT)**





> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx84a4]**

[^1]:
[^2]:
[^3]: