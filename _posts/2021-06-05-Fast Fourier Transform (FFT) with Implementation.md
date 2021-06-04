---
layout: optics_post
title:  "Fast Fourier Transform (FFT) with Implementation"
author: Li Jinzhao
categories: [signal processing]
image: ....jpg
tags: [time-frequency analysis]
typora-root-url: ..
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>

> **Abstract**/**Snippet**. As the commonly use technic in video/audio compression, the fast Fourier transform (FFT) is a time-frequency analysis method of extreme important (also an implementation of the discrete Fourier transform, note that it is DFT instead of DTFT, which convert a periodic discrete time-domain signal into its periodic discrete frequency-domain representation). Principle and `Julia` realization of Radix-2 FFT are explained/made here. Furthermore, its fair property of making the $\Theta(N^2)$ complexity into a $\Theta(N\lg N)$ scheme for $N$ polynomial multiplication is verified.

**Contents**

* toc
{:toc}
## **1. Prerequisite: the Twiddle Factor**

> The **twiddle factor** is defined as $\omega_N^{km}=e^{-\mathrm{j}\frac{2\pi}Nkm}$[^1], and used throughout this article

​	Its following properties are essential to the acceleration of FFT

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





