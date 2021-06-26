---
layout: optics_post
title:  "Poisson Generator"
author: Li Jinzhao
categories: [signal processing]
image: ....jpg
tags: [time-frequency analysis]
typora-root-url: ..
---
> **Abstract**/**Snippet**. 

**Contents**

* toc
{:toc}
​	The commonly known Poisson distribution with $\lambda$ is
$$
\begin{equation}
\begin{split}
P(k)=\frac{\lambda^k}{k!}e^{-\lambda},
\end{split}
\end{equation}
$$
​	from a stopping time procedure, the Poisson variable could be generated/simulated by numerical method.

## **1. Algorithm to Generate Poisson Variable**

​	Formal method to do this can be described as three steps (Knuth) [^1]

- Generate a bunch of data with uniform distribution $\mathrm{Uniform}(0,1)$, given the parameter $\lambda>0$.
- Using the stopping time, find the number of multiplier (random variables of $\mathrm{Uniform}(0,1)$) when their multiplication less than/equals $e^{-\lambda}$.
-  stop the for-loop and the number of random numbers participating in the product <u>minus one</u> obeys the Poisson distribution.

## **2. Proof for Knuth's Algorithm**

​	Denote the uniform distribution in the generation algorithm as
$$
\begin{equation}
\begin{split}
P=\prod_{i=1}^n X_i,X_i\sim\mathrm{Uniform}(0,1),
\end{split}
\end{equation}
$$



> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx4602]**

[^1]: https://en.wikipedia.org/wiki/Poisson_distribution





