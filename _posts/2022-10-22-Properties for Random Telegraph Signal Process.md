---
layout: optics_post
title:  "Properties for Random Telegraph Signal Process"
author: Li Jinzhao
categories: [Signal Processing]
image: .jpg
tags: [Harmonic Analysis]
typora-root-url: .
---
> **Abstract**/**Snippet**. The random telegraph signal is sent by an automated system, and used to transfer telemetry data to ground stations, with properties like WSS and ergodicity that are of basis in classical random signal processing.

**Contents**

* toc
{:toc}
## **definition, RTS process and its pmf**

​	Random telegraph signal (RTS) process[^1] is a stochastic process $\{X(t)\}$, with $X(t)=X(0)(-1)^{N(t)}$, and satisfy following properties,

- $X(t)=\pm1$,
- number of zeros crossings in interval $(0,t)$ $N(t)$ is described by a Poisson process $\mathbf{Poi}_n[\lambda t]$ (sequence with elements that satisfied Poisson distribution, as a special case of Gamma distribution, $N(t)\sim\mathbf{Poi}_n[\lambda t]=\mathbf{Gam}_{\lambda t}[n,1]=(\lambda t)^{n}e^{-\lambda t}/\Gamma(n+1)$, for integer $n$, the expression becomes $(\lambda t)^{n}e^{-\lambda t}/n!,n\in\mathbb{N}$),
- $X(0)\sim\begin{pmatrix}-1&1\\1-p&p\end{pmatrix}$ is the initial state.

​	Obviously, for every increment of $N(t)$, $X(t)$ would change its polarity (from $0\to1$ or $1\to0$).

​	The (1 dimensional) **probability mass function** (pmf) for $X(t)$ is,
$$
\begin{equation}
\begin{split}
P(X(t)=-1)&=P(X(0)=-1)\sum_{k=0}^{\infty}P(N(t)=2k)\\
&+P(X(0)=1)\sum_{k=0}^{\infty}P(N(t)=2k+1)\\
&=(1-p)\sum_{k=0}^{\infty}\frac{(\lambda t)^{2k}}{(2k)!}e^{-\lambda t}\\
&+p\sum_{k=0}^{\infty}\frac{(\lambda t)^{2k+1}}{(2k+1)!}e^{-\lambda t}\\
&=-pe^{-2\lambda t}+\frac12e^{-2\lambda t}+\frac12,\\
P(X(t)=1)&=P(X(0)=-1)\sum_{k=0}^{\infty}P(N(t)=2k+1)\\
&+P(X(0)=1)\sum_{k=0}^{\infty}P(N(t)=2k)\\
&=pe^{-2\lambda t}-\frac12e^{-2\lambda t}+\frac12,
\end{split}
\end{equation}
$$
in which, notice that for the odd and even summation, there exists,
$$
\begin{equation}
\begin{split}
A:&\sum_{k=0}^{\infty}\frac{(\lambda t)^{2k+1}}{(2k+1)!}+\sum_{k=0}^{\infty}\frac{(\lambda t)^{2k}}{(2k)!}=e^{\lambda t},\\
B:&-\sum_{k=0}^{\infty}\frac{(\lambda t)^{2k+1}}{(2k+1)!}+\sum_{k=0}^{\infty}\frac{(\lambda t)^{2k}}{(2k)!}=e^{-\lambda t},\\
e^{-\lambda t}(A-B):&\sum_{k=0}^{\infty}P(N(t)=2k+1)=e^{-\lambda t}\sinh(\lambda t)=\frac{1-e^{-2\lambda t}}2,\\
e^{-\lambda t}(A+B):&\sum_{k=0}^{\infty}P(N(t)=2k)=e^{-\lambda t}\cosh(\lambda t)=\frac{1+e^{-2\lambda t}}2,
\end{split}
\end{equation}
$$
this further indicates that in a Poisson process, **probability of drawing even number is higher than its odd number counterpart**, which could be seen by following separation,
$$
\begin{equation}
\begin{split}
\sum_{k=0}^{\infty}P(N(t)=2k+1)&=\sum_{k=0}^{\infty}\frac{(\lambda t)^{2k+1}}{(2k+1)!}e^{-\lambda t}\\
&=\lambda t\sum_{k=0}^{\infty}\frac{(\lambda t)^{2k}}{(2k)!}\frac1{2k+1}e^{-\lambda t}\\
&=\sum_{k=0}^{\infty}\frac{\lambda t}{2k+1}\times P(N(t)=2k),
\end{split}
\end{equation}
$$

## **proposition 1: mean, variance, autocorrelation and autocovariance**

​	The mean of RTS process is given by
$$
\begin{equation}
\begin{split}
\mathbb{E}\{X(t)\}&=(-1)\times P(X(t)=-1)+(1)\times P(X(t)=1)\\
&=pe^{-2\lambda t}-\frac12e^{-2\lambda t}-\frac12+pe^{-2\lambda t}-\frac12e^{-2\lambda t}+\frac12\\
&=(2p-1)e^{-2\lambda t},
\end{split}
\end{equation}
$$
for $p=1\text{ or }0$, i.e., the semi-random telegraph signal process, $\mathbb{E}\{X(t)\}=\pm e^{-2\lambda t}$ is time-dependent, hence it is not stationary, and is called evolutionary process.

​	The evolutionary property for common RTS process could be seen by noticing the exponential changing expectation value in following simulation with average arrival rate $\lambda=0.2$, initial state $p=0.95$, trail number $1000$ and time from $0\sim200\mathrm{\ sec}$.

> **simulation RTS process and its expectation value in time domain**
>
> ![time_domain_stat_](./[OPTSx0235]_time_domain_stat_.svg)  

​	While for $p=1/2$, the RTS process yields a zero mean $\mathbb{E}\{X(t)\}=0$, hence it is a wide-sense stationary (WSS, with $R_X(t_1,t_2)=R_X(t_1+D,t_2+D),\forall D\in\R$ as following shows) process.

​	The autocorrelation of RTS is,
$$
\begin{equation}
\begin{split}
R_X(t_1,t_2)&=\mathbb{E}\{X(t_1)X(t_2)\}\\
&=(-1)\times(-1)\times P(X(t_1)=-1)\sum_{k=0}^{\infty}P(N|t_2-t_1|=2k)\\
&+(-1)\times(1)\times P(X(t_1)=-1)\sum_{k=0}^{\infty}P(N|t_2-t_1|=2k+1)\\
&+(1)\times(-1)\times P(X(t_1)=1)\sum_{k=0}^{\infty}P(N|t_2-t_1|=2k+1)\\
&+(-1)\times(-1)\times P(X(t_1)=1)\sum_{k=0}^{\infty}P(N|t_2-t_1|=2k)\\
&=\bigg(-pe^{-2\lambda t_1}+\frac12e^{-2\lambda t_1}+\frac12\bigg)\frac{1+e^{-2\lambda|t_2-t_1|}}2\\
&+\bigg(pe^{-2\lambda t_1}-\frac12e^{-2\lambda t_1}-\frac12\bigg)\frac{1-e^{-2\lambda|t_2-t_1|}}2\\
&+\bigg(-pe^{-2\lambda t_1}+\frac12e^{-2\lambda t_1}-\frac12\bigg)\frac{1-e^{-2\lambda|t_2-t_1|}}2\\
&+\bigg(pe^{-2\lambda t_1}-\frac12e^{-2\lambda t_1}+\frac12\bigg)\frac{1+e^{-2\lambda|t_2-t_1|}}2\\
&=e^{-2\lambda|t_2-t_1|},
\end{split}
\end{equation}
$$
​	The autocovariance of $X(t)$ is hence,
$$
\begin{equation}
\begin{split}
C_X(t_1,t_2)&=R_X(t_1,t_2)-\mathbb{E}\{X(t_1)\}\mathbb{E}\{X(t_2)\}\\
&=e^{-2\lambda|t_2-t_1|}-(2p-1)^2e^{-2\lambda(t_1+t_2)},
\end{split}
\end{equation}
$$
based on which, the variance of RTS process is $\sigma^2=C_X(t,t)=1-(2p-1)^2e^{-4\lambda t}$.

​	A visualization results for these statistics is provided by simulation with $p=0.95$, trail number $1000$ and time from $0\sim200\mathrm{\ sec}$ as following shows, denote that these values becomes explicit for near-diagonal elements, i.e., with smaller $|t_2-t_1|$, the same as theoretical results.

> **simulation second-order statistics for RTS process in 2 dimensional time domain**
>
> ![auto_corr_covar_](./[OPTSx0235]_auto_corr_covar_.svg)  

## **proposition 2: Formal RTS process with $p=1/2$ is time & autocorrelation-ergodic**

​	On the case for $p=1/2$, the expectation of RTS process becomes $\mathbb{E}\{X(t)\}=0$, hence one can denote $\tau=t_2-t_1$, and demonstrate its ergodicity by calculating the following limit,
$$
\begin{equation}
\begin{split}
\lim_{T\to\infty}\frac1{2T}\int_{-2T}^{2T}\bigg(1-\frac{|\tau|}{2T}\bigg)R_X(\tau)\mathrm{d}\tau&=\lim_{T\to\infty}\frac1{2T}\int_{-2T}^{2T}\bigg(1-\frac{|\tau|}{2T}\bigg)e^{-2\lambda|\tau|}\mathrm{d}\tau\\
&=\lim_{T\to\infty}\frac1{2\lambda T}\bigg(1-\frac{1-e^{4\lambda T}}{4\lambda T}\bigg)\\
&=0,
\end{split}
\end{equation}
$$
​	It could be directly seen that for $p=1/2$ 's simulation result the mean value and the autocovariance matrix are near-constant with varying $t$.

> **simulation ergodic RTS process on the condition of $p=1/2$**
>
> ![auto_corr_covar_](./[OPTSx0235]_time_domain_ergodic_.svg)  

## **proposition 3: the power spectral density for RTS process**

​	Based on Wiener-Khinchin theorem, the power spectral density for a RTS process can be derived by applying the Fourier transform of $R_X(\tau)=e^{-2\lambda|\tau|}$,
$$
\begin{equation}
\begin{split}
S_X(f)&=\int^0_{-\infty}e^{2\lambda\tau}e^{-\mathrm{j}2\pi f\tau}\mathrm{d}\tau+\int_0^{+\infty}e^{-2\lambda\tau}e^{-\mathrm{j}2\pi f\tau}\mathrm{d}\tau\\
&=\frac1{2\lambda-\mathrm{j}2\pi f}+\frac1{2\lambda+\mathrm{j}2\pi f}\\
&=\frac{4\lambda}{4\lambda^2+4\pi^2f^2},
\end{split}
\end{equation}
$$
​	The frequency domain representation could be obtained by taking FFT for the autocorrelation sequence but in $\tau$ domain, a simulation result for this is as following.

> **simulation for the power spectral density based on RTS process with $\lambda=0.2$ and $p=1/2$**
>
> ![auto_corr_covar_](./[OPTSx0235]_freq_domain_stat_.svg)  


> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx0235]**

[^1]:Durrett R. Probability: theory and examples[M]. Cambridge university press, 2019.
