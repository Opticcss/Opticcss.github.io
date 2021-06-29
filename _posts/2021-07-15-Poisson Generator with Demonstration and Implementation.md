---
layout: optics_post
title:  "Poisson Generator with Demonstration and Implementation"
author: Li Jinzhao
categories: [stochastic]
image: ....jpg
tags: [stochastic analysis]
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

​	which could also be written in source code in `Julia`, or, if you like, the pseudocode as a function `poisson_generator(λ::Float64)::Float64` as shown below.

```julia
# the implementation of generator for random variables for Poission distribution
# with λ as the pre-defined parameter
# which takes a float as input and a float number as output
function poisson_generator(λ::Float64)::Int64
	L = exp(-λ); k = 0; p = 1; # initialization of parameters
	while(p > L)
		k = k + 1; p = p*rand(); # update until p > L
	end
	return k - 1
end
```









```julia
julia> using UnicodePlots; A = Array{Float64}(undef, 1000); A .= 20;
julia> histogram(poisson_generator.(A), nbins = 25, closed = :left)
                ┌                                        ┐
   [ 6.0,  8.0) ┤ 1
   [ 8.0, 10.0) ┤▇ 3
   [10.0, 12.0) ┤▇▇ 11
   [12.0, 14.0) ┤▇▇▇▇▇▇▇▇▇▇ 49
   [14.0, 16.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 93
   [16.0, 18.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 153
   [18.0, 20.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 158
   [20.0, 22.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 167
   [22.0, 24.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 145
   [24.0, 26.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 116
   [26.0, 28.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇ 58
   [28.0, 30.0) ┤▇▇▇▇ 19
   [30.0, 32.0) ┤▇▇▇▇ 17
   [32.0, 34.0) ┤▇▇ 8
   [34.0, 36.0) ┤ 0
   [36.0, 38.0) ┤ 2
                └                                        ┘
                                Frequency
```





## **2. Demonstration of Knuth's Algorithm**

​	Denote the uniform distribution in the generation algorithm as $X\sim\mathrm{Uniform}(0,1)$, and their multiplication as $\prod_{i=1}^n X_i$, to obtain the distribution of $\prod X$, first we notice the distribution of $\log X$ (as an exponential distribution), and then the distribution of $\log\prod X=\sum\log X$ (the Gamma distribution), and finally the distribution of its exponential, the $\prod X$. Firstly, there exists the relationship
$$
\begin{equation}
\begin{split}
\log\prod_{i=1}^n X_i=\sum_{i=1}^n\log X_i,
\end{split}
\end{equation}
$$

​	then the distribution of $\boxed{\log X}$ (denoted as $\boxed{f_{\log X}(\mathscr{X})}$) is an (with $f_X(x)=1$ for $0\leq x\leq1$ and $f_X(x)=0$ for others) exponential distribution as
$$
\begin{equation}
\begin{split}
f_{\log X}(\mathscr{X})&=\frac{\mathrm{d}}{\mathrm{d}\mathscr{X}}\int_{-\infty}^{e^{\mathscr{X}}}f_X(x)\mathrm{d}x\\
&=f_X(e^{\mathscr{X}})e^{\mathscr{X}},\\
&=\left\{\begin{split}
&e^{\mathscr{X}},\text{ }&-\infty<\mathscr{X}\leq0,\\
&0,\text{ }&\text{otherwise},
\end{split}\right.
\end{split}
\end{equation}
$$
​	the characteristic function (ch. f[^2]) of $f_{\log X}(\mathscr{X})$ is as
$$
\begin{equation}
\begin{split}
\hat{f}_{\log X}(\eta)&=\int_{-\infty}^\infty f_{\log X}(x)e^{\mathrm{i}\eta x}\mathrm{d}x=\frac1{\mathrm{i}\eta+1},
\end{split}
\end{equation}
$$
​	hence, the ch. f of $\sum_{i=1}^nf_{\log X}(\mathscr{X})$ is the multiplication of each $(\mathrm{i}\eta+1)^{-1}$, which is corresponding the the convolution for the distributions, as
$$
\begin{equation}
\begin{split}
\hat{f}_{\log\prod X}(\eta)=\frac1{(\mathrm{i}\eta+1)^n},
\end{split}
\end{equation}
$$
​	whose inverse Fourier transform is the distribution of variable $\boxed{\log\prod X}$ (denoted as $\boxed{f_{\log\prod X}(\mathscr{Y})}$), which can be evaluated by choosing the path of integral as shown below
$$
\begin{equation}
\begin{split}
{f}_{\log\prod X}(\mathscr{Y})=\frac1{2\pi}\int_{-\infty}^\infty\frac1{(\mathrm{i}\eta+1)^n}e^{\mathrm{i}\eta \mathscr{Y}}\mathrm{d}\eta=\left\{\begin{split}
&\frac{(-\mathscr{Y})^{n-1}}{(n-1)!}e^{\mathscr{Y}},\text{ }&-\infty<\mathscr{Y}\leq0,\\
&0,\text{ }&\text{otherwise},
\end{split}\right.
\end{split}
\end{equation}
$$
![[OPTSx4602]_Integral_Path](/assets/images/[OPTSx4602]_Integral_Path.svg)

​	from this $\Gamma$ distribution, the distribution function of $\boxed{\prod X}$ (denoted as ${f}_{\prod X}(\mathscr{Z})$) is as
$$
\begin{equation}
\begin{split}
{f}_{\prod X}(\mathscr{Z})=\mathscr{Z}^{-1}{f}_{\log\prod X}(\log\mathscr{Z})=\left\{\begin{split}
&\frac{(-\log\mathscr{Z})^{n-1}}{(n-1)!},\text{ }&0<\mathscr{Z}\leq1,\\
&0,\text{ }&\text{otherwise},
\end{split}\right.
\end{split}
\end{equation}
$$
​	hence, probability of $\boxed{\prod X<e^{-\lambda}}$ as the algorithm goes is the integration of its distribution function,
$$
\begin{equation}
\begin{split}
P_n(\prod X<e^{-\lambda})&=\int_0^{e^{-\lambda}}{f}_{\prod X}(\mathscr{Z})\mathrm{d}\mathscr{Z}\\
&=\frac1{(n-1)!}\int_\lambda^{\infty}e^{-t}t^{n-1}\mathrm{d}t,\\
&=\frac{\lambda^{n-1}}{(n-1)!}e^{-\lambda}+P_{n-1}(\prod X<e^{-\lambda}),\text{ }n>1\\
P_1(\prod X<e^{-\lambda})&=e^{-\lambda},
\end{split}
\end{equation}
$$
​	recurrence leads to the probability as
$$
\begin{equation}
\begin{split}
P_n(\prod X<e^{-\lambda})=\sum_{k=0}^{n-1}\frac{\lambda^k}{k!}e^{-\lambda},
\end{split}
\end{equation}
$$
​	hence, the distribution of when the first time multiplication of $X\sim\mathrm{Uniform}(0,1)$ is less than $e^{-\lambda}$ is as
$$
\begin{equation}
\begin{split}
P(N=n)&=P(\prod_{i=1}^{n} X_i<e^{-\lambda})-P(\prod_{i=1}^{n-1} X_i<e^{-\lambda})\\
&=\frac{\lambda^{n-1}}{(n-1)!}e^{-\lambda},
\end{split}
\end{equation}
$$
​	which is the Poisson distribution.


> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx4602]**

[^1]: https://en.wikipedia.org/wiki/Poisson_distribution
[^2]: Papoulis, Athanasios, and H. Saunders. "Probability, random variables and stochastic processes." (1989): 123-125.

