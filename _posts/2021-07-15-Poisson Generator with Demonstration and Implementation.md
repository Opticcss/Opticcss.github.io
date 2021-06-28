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



```julia
# the implementation of generator for random variables for Poission distribution
# with λ as the pre-defined parameter
# which takes a float as input and a float number as output
function poisson_generator(λ::Float64)::Float64
	L = exp(-λ); k = 0; p = 1; # initialization of parameters
	while(p > L)
		k = k + 1; p = p*rand(); # update until p > L
	end
	return k - 1
end
```









```julia
julia> using UnicodePlots; A = Array{Float64}(undef, 1000); A .= 20;
julia> histogram(poisson_generator.(A), nbins = 50, closed = :left)
                ┌                                        ┐
   [ 7.0,  8.0) ┤ 1
   [ 8.0,  9.0) ┤▇ 2
   [ 9.0, 10.0) ┤▇ 2
   [10.0, 11.0) ┤▇▇ 5
   [11.0, 12.0) ┤▇▇▇▇ 12
   [12.0, 13.0) ┤▇▇▇▇▇ 14
   [13.0, 14.0) ┤▇▇▇▇▇▇▇▇▇▇▇ 31
   [14.0, 15.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇ 38
   [15.0, 16.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 62
   [16.0, 17.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 54
   [17.0, 18.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 71
   [18.0, 19.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 95
   [19.0, 20.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 69
   [20.0, 21.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 103
   [21.0, 22.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 88
   [22.0, 23.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 74
   [23.0, 24.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 72
   [24.0, 25.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 49
   [25.0, 26.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇ 37
   [26.0, 27.0) ┤▇▇▇▇▇▇▇▇▇▇▇▇▇▇ 42
   [27.0, 28.0) ┤▇▇▇▇▇▇▇▇▇▇ 29
   [28.0, 29.0) ┤▇▇▇▇▇ 16
   [29.0, 30.0) ┤▇▇▇▇▇▇ 17
   [30.0, 31.0) ┤▇▇▇ 9
   [31.0, 32.0) ┤▇ 2
   [32.0, 33.0) ┤▇ 2
   [33.0, 34.0) ┤▇ 3
   [34.0, 35.0) ┤ 0
   [35.0, 36.0) ┤ 0
   [36.0, 37.0) ┤ 1
                └                                        ┘
                                Frequency
```





## **2. Demonstration of Knuth's Algorithm**

​	Denote the uniform distribution in the generation algorithm as

$$
\begin{equation}
\begin{split}
P=\prod_{i=1}^n X_i,X_i\sim\mathrm{Uniform}(0,1),
\end{split}
\end{equation}
$$

​	






> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx4602]**

[^1]: https://en.wikipedia.org/wiki/Poisson_distribution





