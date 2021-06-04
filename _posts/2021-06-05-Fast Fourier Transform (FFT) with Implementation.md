---
layout: optics_post
title:  "Fast Fourier Transform (FFT) with Implementation"
author: Li Jinzhao
categories: [signal processing]
image: ....jpg
tags: [time-frequency analysis]
typora-root-url: ..
---

> **Abstract**/**Snippet**. As the commonly use technic in video/audio compression, the fast Fourier transform (FFT) is a time-frequency analysis method of extreme important (also an implementation of the discrete Fourier transform, note that it is DFT instead of DTFT, which convert a periodic discrete time-domain signal into its periodic discrete frequency-domain representation). Principle and `Julia` realization of Radix-2 FFT are explained/made here. Furthermore, its fair property of making the $\Theta(N^2)$ complexity into a $\Theta(N\lg N)$ scheme for $N$ polynomial multiplication is verified.

**Contents**

* toc
{:toc}
## **1. Prerequisite: the Twiddle Factor**

> The **twiddle factor** is defined as $\omega_N^{km}=e^{-\mathrm{j}\frac{2\pi}Nkm}$[^1], and used throughout this article

​	Its following properties are essential to the acceleration of FFT

1. periodicity
   $$
   \begin{equation}
   \begin{split}
   \omega_N^{m+lN}=\omega_N^m,\text{ where }\omega_N^{-m}=\omega_N^{N-m},
   \end{split}
   \end{equation}
   $$

2. symmetry
   $$
   \begin{equation}
   \begin{split}
   \omega_N^{m+N/2}=-\omega_N^m,
   \end{split}
   \end{equation}
   $$
this indicates that "dividing by an extra $2$ over a complex exponential has the same effect as squaring an extra complex exponential,which can be represented in the complex plane by symmetry of equipartition".
   
3. reducibility
   $$
   \begin{equation}
   \begin{split}
   \omega_{N/m}^k=\omega_N^{mk},
   \end{split}
   \end{equation}
   $$

4. special twiddle factor as $\omega_N^0=1$.

> These are caused by the the multiplicative group formed by Fourier basis $\{e^{i2\pi mt}\}_{m\in\mathbb{Z}}=\{(e^{-i2\pi/N})^{1},(e^{-i2\pi/N})^{2},\cdots (e^{-i2\pi/N})^{N-1})\}$ on $L^2[\cdots]$ is **isomorphic to modular $n$ additive group** $(\mathbb{Z}_n,+)$ which leads to $(e^{-i2\pi /N})^j(e^{-i2\pi /N})^k=(e^{-i2\pi /N})^{(j+k)\text{ mod }N}$, in addition, there exists the relation of
>
> $$
> \begin{equation}
> \begin{split}
> (e^{-i2\pi /dN})^{dk}&=(e^{-i2\pi /N})^{k},\\
> [(e^{-i2\pi /N})^{k+N/2}]^{2}&=[(e^{-i2\pi /N})^{k}]^{2},
> \end{split}
> \end{equation}
> $$

​	Rewrite discrete Fourier transform (DFT) as below, the direct calculation will lead to the time complexity of $\Theta(N^2)$.
$$
\begin{equation}
\begin{split}
x[n]&=\frac1N\sum_{k=0}^{N-1}X_k\cdot e^{\mathrm{j}k2\pi /Nn},\\
X_k&=\sum_{n=0}^{N-1}x[n]\cdot e^{-\mathrm{j}k2\pi /Nn},
\end{split}
\end{equation}
$$



> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx84a2]**

[^1]:Cormen T H, Leiserson C E, Rivest R L, et al. Introduction to algorithms[M]. MIT press, 2009.
[^2]:
[^3]:





