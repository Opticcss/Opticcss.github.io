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

​	STFT has a compactly supported basis function, which provide a good performance compared with FT, while this is not useful for its adaptability, i.e., the width/type of the window $w(t)$ is constant for variant frequency of an unstable signal, and take a lot of effort into adjusting the parameters (window width, ...)

​	The process of controlling the parameters to gain suitable time-frequency resolution is not always easy, and is limited by the so-called **Gabor limit** (the signal processing edition of the Heisenberg one...) of $\Delta t\Delta f>\mathrm{const}$, the uncertainty here are the time resolution and the frequency resolution respectively.

​	A solution with self-adaptive resolution within the Gabor limit is the **continuous wavelet transform** (CWT), kind of technique to perform signal analysing by using an alternative approach called the **multiresolution analysis** (MRA, also called the "mathematical microscope", the wavelets used have different resolutions in different frequency bands), which introduce suitable mother functions $\psi(t)$ with several properties.

​	There exists multiple types of wavelet basis, but all of them applies in the wavelet transform in the same form as shown below, namely the basis function $\psi_{\tau, s}$, which have a range of frequencies, and is different from Fourier basis (of basis function in STFT), with two degree of freedom, $s$ as the scaling factor and $\tau$ as the shifting factor ($1/\sqrt{s}$ is a factor for energy conservation)

$$
\begin{equation}
\begin{split}
\psi_{\tau,s}=\frac1{\sqrt{s}}\psi\bigg(\frac{t-\tau}{s}\bigg),
\end{split}
\end{equation}
$$

​	As for mother functions $\psi(t)$, they should satisfies following properties, and **act like a band-pass filter,** only signals with frequencies close to the wavelet center frequency (after scaling) are allowed to pass through.

- Compactly supported condition, i.e.,

$$
\begin{equation}
\begin{split}
\psi(t)=0,\exists a>0,\forall|t|>a,
\end{split}
\end{equation}
$$

​	to provide sliding window in convolution operation as shown below in the wavelet analysis equation, which is **a measure of similarity between the basis functions (wavelets) and the signal itself**. Here **the similarity is in the sense of similar frequency content**. The **calculated CWT coefficients refer to the closeness of the signal to the wavelet at the current scale**.

$$
\begin{equation}
\begin{split}
\Psi_{x\psi}(\tau,s)=\big\langle x(t),\psi_{\tau,s}(t)\big\rangle=\int x(t)\psi_{\tau,s}^*(t)\mathrm{d}t,
\end{split}
\end{equation}
$$

- Admissibility condition, which yields $\displaystyle{\int}_{-\infty}^\infty\psi(t)\mathrm{d}t=0$, and limit the wavelet to be a "wave".

$$
\begin{equation}
\begin{split}
c_\psi=2\pi\int_{-\infty}^\infty\frac{|\mathscr{F}\{\psi(\xi)\}^*|^2}{|\xi|}\mathrm{d}\xi<+\infty,
\end{split}
\end{equation}
$$

​	note that the coefficient depends on wavelet used here, $c_\psi$, is used in the inverse wavelet transform, to perform reconstruction task of the analysis signal.

- Orthogonality/Orthonormal, i.e.,

$$
\begin{equation}
\begin{split}
&\langle\psi_k(t),\psi_\ell(t)\rangle=\int\psi_k(t)\psi_\ell^*(t)\mathrm{d}t=\delta_{k\ell},
\end{split}
\end{equation}
$$

​	to ensure the existence of its inverse transform, which is in the form

$$
\begin{equation}
\begin{split}
x(t)=\frac1{c^2}\int_s\int_\tau\Psi_{x\psi}(\tau,s)\frac1{s^2}\psi\bigg(\frac{t-\tau}{s}\bigg)\mathrm{d}\tau\mathrm{d}s,
\end{split}
\end{equation}
$$

- Vanishing moment $N$, applied to wavelet to make as many wavelet coefficients zero as possible or to generate as few non-zero wavelet coefficients as possible, which is conducive to data compression and noise elimination. A larger vanishing moment will make more wavelet coefficients zero and increase the supporting length.

$$
\begin{equation}
\begin{split}
\int t^p\psi(t)\mathrm{d}t=0,\text{ where }0<p<N,
\end{split}
\end{equation}
$$

​	It can be seen that in $\Psi_{x\psi}(\tau,s)=\big\langle x(t),\psi_{\tau,s}(t)\big\rangle$, the basis function has comparably **wide window width (bad time resolution but high frequency resolution, large $s$) for low frequency, and narrow window width (high time resolution but bad frequency resolution, small $s$)** for high frequency, which is the so-called self-adaptive. (of course, the wavelet function cannot be chosen by itself, so it is kind of semi-adaptive method)

### **1.1. A Little Wavelet Zoo**

​	There are several families of wavelet functions, and this article is trying to list someone commonly used here.

- **Haar wavelet** (1^st^ order Daubechies wavelet) and corresponding frequency representation as following, which has the simplest structure. It orthogonal to itself and its own integer displacement, with vanishing moment of $1$.

$$
\begin{equation}
\begin{split}
&\psi(t)=\phi(2t)-\phi(2t-1)=\left\{\begin{split}
&1,\text{ }&0\leq t\leq1/2,\\
&-1,\text{ }&1/2< t\leq1,\\
&0,\text{ }&\text{others},
\end{split}\right.\\
&\mathscr{F}\{\psi\}(\Omega)=\mathrm{j}\frac4\Omega\sin^2\bigg(\frac\Omega a\bigg)e^{\mathrm{j}\Omega/2},
\end{split}
\end{equation}
$$

​	where the Haar scaling function/father function is as
$$
\begin{equation}
\begin{split}
&\phi(t)=\left\{\begin{split}
&1,\text{ }&0< t\leq1,\\
&0,\text{ }&\text{others},
\end{split}\right.
\end{split}
\end{equation}
$$

- **Daubechies wavelet** (by Inrid Daubechies, and denoted as db$N$, where $N$ is the order of wavelet), with vanishing moment of $N$, supporting interval of $2N-1$, and asymmetry. Note that this wavelet does not have explicit form of mathematical expression.
- **Mexican Hat wavelet** (mexh) and corresponding frequency representation as following, which is the second derivative of the Gaussian. It has good localization in both time-frequency domain.

$$
\begin{equation}
\begin{split}
&\psi(t)=(1-t^2)e^{\frac{t^2}2}\bigg\{=\frac1{\sqrt{2\pi}\sigma^3}\bigg[e^{\frac{-t^2}{2\sigma^2}}\bigg(\frac{t^2}{\sigma^2}-1\bigg)\bigg]\bigg\},\\
&\mathscr{F}\{\psi\}(\Omega)=\sqrt{2\pi}\Omega^2e^{\frac{\Omega^2}2},
\end{split}
\end{equation}
$$

- The **Morlet wavelet** and corresponding basis function, where $\omega_0$ is called the center frequency.

$$
\begin{equation}
\begin{split}
&\psi(t)=\exp(\mathrm{i}\omega_0t)\exp\bigg(-\frac{t^2}2\bigg),\\
&\psi_{\tau,s}(t)=\exp\bigg(\mathrm{i}\omega_0\frac{t-\tau}s\bigg)\exp\bigg[-\frac{(t-\tau)^2}{2s^2}\bigg],
\end{split}
\end{equation}
$$

- **Meyer wavelet**, which does not satisfy the tight support property but converges fast and is infinitely differentiable.

## **2. The Discrete Wavelet Transform for Computing**

​	Discrete signal cannot be transformed by CWT, which is the only thing the computer can deal with, hence the **discrete wavelet transform (DWT)** are designed to replace the infinite number of operations in CWT, one of them is called the **Mallet algorithm**.

​	Mallet algorithm is 













> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx84a4]**

[^1]: The Wavelet Tutorial, http://feihu.eng.ua.edu/NSF_TUES/w7_2.pdf
[^2]: Boggess A, Narcowich F J. A first course in wavelets with Fourier analysis[M]. John Wiley & Sons, 2015.