---
layout: optics_post
title:  "Emperical Mode Decomposition with Implementation"
author: Li Jinzhao
categories: [signal processing]
image: ....jpg
tags: [Julia, harmonic analysis]
typora-root-url: ..
---

> **Abstract**/**Snippet**. 这里多扯几句小想法。在一片混杂的原信号里提取单一成分并求瞬时频率,是个很有工程意义的问题,在信号降噪、模态提取、颤振预测、故障诊断上都有应用。传统的STFT和WT的思路是用统一的方法对整个频率范围做分析(本质上都是用一组不同尺度的基与不同时间的原信号求内积,获得系数矩阵) ,自然会遇到测不准原理的限制,出现分辨率不足的问题。
>
> 所以成分提取的方法需要有几个特点: @一定的自适应性,允许每一成分有频率波动; ②较好的分辩能力,不至于分量间交叉后就糊成一片; ③一定的噪声鲁棒性。


**Contents**

* toc
{:toc}




## **2. Single Frequency Non-Stationary Signals or Signal with Few Overtones, <u>Hilbert Transform</u> (HT)**

​	Corresponding to the **non-stationary signals** with single frequency or with few overtones, the information in time domain becomes significant (it is difficult to analyse the characteristics of this non-stationary signal only through the time domain signal or its power spectrum. On the other side, it can be seen that the frequency domain signal obtained by Fourier transform can achieve high frequency resolution, but **its time resolution is zero**).

> ### **Examples of Signal with Single Frequency**
>
> One example for this kind of signal and its frequency is the **chirp signal** (swept-frequency cosine signal), which takes the form
> $$
> \begin{equation}
> \begin{split}
> x(t)=\exp\bigg[\mathrm{j}2\pi\bigg(f_0t+\frac12u_0t^2\bigg)\bigg],
> \end{split}
> \end{equation}
> $$
> Obviously, its phase is
> $$
> \begin{equation}
> \begin{split}
> \theta(t)&=\arctan\frac{\Re e[{x(t)}]}{\Im m[{x(t)}]}\\
> &=2\pi\bigg(f_0t+\frac12u_0t^2\bigg),
> \end{split}
> \end{equation}
> $$
> the subsequent idea to get its instantaneous frequency $\omega_i$ (with the time information, definition is from Gabor and Ville) is obtained by <u>taking time derivation of the phase $\theta(t)$</u>, i.e.,
> $$
> \begin{equation}
> \begin{split}
> \omega_i(t)=\frac{{\rm d}\theta(t)}{{\rm d}t}=2\pi\bigg(f_0+u_0t\bigg),
> \end{split}
> \end{equation}
> $$
> this yields the property of "frequency sweeping" (for a visualization, try the code below in `MATLAB`, note that `spectrogram(·)` utilize the method named STFT, see below).
>
> ```matlab
> freq_sample = 8192; % 1 secs @ 1kHz sample rate
> t = 0 : 1/freq_sample : 1; y = chirp(t, 262, 1, 1480, 'quadratic', 0, 'convex'); plot(t, y);
> spectrogram(y, 256, 250, 1024, 1000, 'yaxis'); % spectrogram(x,window,noverlap,nfft,fs);
> y = repmat([y, 1000*ones(1,length(t))], 1, 3); sound(y, freq_sample);
> ```
>
> Another example of this is the signal after angle modulation, which is similar, as
> $$
> \begin{equation}
> \begin{split}
> &x(t)=10\cos[300\pi t^2+500\pi t+200]\to\\
> &\omega_i(t)=\frac{{\rm d}}{{\rm d}t}[300\pi t^2+500\pi t+200]=600\pi t+500\pi{\rm\ rad/s}.
> \end{split}
> \end{equation}
> $$

​	Kind of formal solution to retrieve the instantaneous frequency of one signal with single frequency (with property of "narrow band, no or less noise, no discontinuity, smooth, no unexplained negative frequency") is called the **Hilbert transform** (the integral is taken as Cauchy principle value)
$$
\begin{equation}
\begin{split}
H[x(t)]=\pi^{-1}\int_{-\infty}^{+\infty}\frac{x(\tau)}{t-\tau}{\rm d}\tau=x(t)*(\pi t)^{-1},
\end{split}
\end{equation}
$$
​	which performs like kind of  linear filter, and delay phases of all frequency components of the signal by $\pi/2=90\deg$, which is obvious in its frequency domain representation (hence $H^2(\omega)$ lead to $\pi$, and $H^4(\omega)\equiv1(\text{id})$),
$$
\begin{equation}
\begin{split}
H(\omega)=-\mathrm{j}\cdot\text{sgn}(\omega)=
\left\{
\begin{split}
&e^{{-\rm j}\pi/2},\text{ }&\omega\geq0,\\
&e^{\mathrm{j}\pi/2},\text{ }&\omega<0,
\end{split}
\right.
\end{split}
\end{equation}
$$
​	The analytic signal is the combination of original signal and its Hilbert transform, in form of $z(t)=x(t)+\mathrm{j}H[x(t)]$, take the time derivation of this analytic signal as shown in the example, the final result of the frequency $\omega_i(t)$ is as
$$
\begin{equation}
\begin{split}
\omega_i(t)=\frac{{\rm d}\theta(t)}{{\rm d}t}=\frac{{\rm d}}{{\rm d}t}\Bigg[\arctan\frac{\Re e[{z(t)}]}{\Im m[{z(t)}]}\Bigg]=\frac{{\rm d}}{{\rm d}t}\Bigg[\arctan\frac{x(t)}{H[x(t)]}\Bigg],
\end{split}
\end{equation}
$$

> ### **Details of Narrow Band Condition**
>
> Special attention should be paid on the "narrow band" condition of Hilbert transform, this condition is quantized by "band that do not let transform results be negative".
>
> Based on ==sss==, Hilbert transform is used to estimate instantaneous frequency by the entire length of signal, for a random narrowband signal, probability of the occurrence of negative frequency is
> $$
> \begin{equation}
> \begin{split}
> &p[\omega_i<0]=\frac12\bigg[1-\frac{\omega_{0}}{\pi\lambda_0}\bigg],\\
> &\text{where }
> \left\{
> \begin{split}
> &\lambda_0=\frac1\pi\Bigg\{\frac{\int_{-\infty}^{+\infty}\omega^2W(\omega){\rm d}\omega}{\int_{-\infty}^{+\infty}W(\omega){\rm d}\omega}\Bigg\}^{1/2},\\
> &\omega_{0}=\frac{\int_{-\infty}^{+\infty}|\omega|W(\omega){\rm d}\omega}{\int_{-\infty}^{+\infty}W(\omega){\rm d}\omega},
> \end{split}
> \right.
> \end{split}
> \end{equation}
> $$
> note that  $\omega_{0}$ here is the mean frequency of the signal, $W(\omega)$ is the power spectrum of signal, $\pi\lambda_0$ is the upper bound to $\omega_0$, only pure sinusoidal signal can reach this bound.
>
> Hence, the signal which can satisfy $p[\omega_i<0]\approx0$ (i.e., the probability of negative instantaneous frequency is approximately zero) is suitable to the Hilbert transform.

​	



![image-20210601001840833](C:\Users\a1020\AppData\Roaming\Typora\typora-user-images\image-20210601001840833.png)







envelop



不同的窗长度自然对应着不同的语谱图， 大体分为窄带语谱图，宽带语谱图，长时窗（至少两个基音周期）常被用于计算窄带语谱图，短窗则用于计算宽带语谱图。**窄带语谱图具有较高的频率分辨率和较低的时间分辨率**，良好的频率分辨率可以让语音的每个谐波分量更容易被辨别，在语谱图上显示为水平条纹。相反**宽带语谱图具有较高的时间分辨率和较低的频率分辨率**，低频率分辨率只能得到谱包络，良好的时间分辨率适合用于分析和检验英语语音的发音





帧长固定的短时傅里叶变换，在全局范围内的时间分辨率和频率分辨率是固定的。如果我们想要在低频区域具有高频率分辨率，在高频区域具有高时间分辨率，显然STFT是不能满足要求的

对于短时傅里叶变换(STFT)，它在时域和频域都有一定的分辨率，并且在全局范围内STFT的时频分辨率都是一样的。但是由于Heisenberg不确定原理(也就是量子力学中的测不准原理)的制约，每一个时频窗的面积都是固定的，即时间分辨率和频率分辨率成反比，所以这两个分辨率不能同时很高



## **4. Adaptive Time-frequency Analysis, <u>Hilbert-Huang Transform</u> (HHT)**



当时间跨度无限短，平均值就可以表示瞬时值。但对信号频率来说，有个区别：频谱分析有一个理论上的分辨率极限。通俗说就是，无法同时保证时间跨度足够短，频率足够精准（对，就是海森伯测不准原理的信号分析版本）。 Gabor limit





一种不受Heisenberg不确定原理限制、同时还有更好的自适应性的时频分析方法——希尔伯特黄变换

​	经过EMD分解出的IMF分量再经过Hilbert变换，最终得到信号瞬时频率和瞬时幅值”的方法

HHT的结果反映的是信号的时频特征，即信号的频域特征随时间变化的规律。相对于傅里叶变化得到的是信号的频率组成，HHT还可以获取频率成分随时间的“变化”。比如我们要分析的信号代表的是一个性能迅速退化的发动机（假设信号表征是某些IMF分量的频率逐渐升高），使用HHT就可以对该现象进行很好的捕捉。

HHT可以对局部特征进行反映，这点主要得益于EMD的作用。EMD可以自适应地进行时频局部化分析，有效提取原信号的特征信息

“分解”往往可以对应着“重构”，从HHT结果中选择出满足要求的特征分量并重组信号，有利于将关注的特征从复杂的混合信号中分离出来

Hilbert变换只能用于窄带信号
EMD分解有模态混叠和端点效应





时域里提取方案的代表有大名鼎鼎的EMD (经验模态分解) [1],它的思路比较直观:求原信号的上下包络线(并保证一定的平滑性) ,再求上下包络线的中值,作为一个分量从原信号中减去,并不断重复整个过程。相当于用一个很模糊的标准将不同频域尺度的分量分离开来,故算法名中有"经验"二字。其所得的结果是一个个分量的时域信号,因为它们成分足够单一,可以直接用希尔伯特变换求瞬时频率和瞬时幅值了,所以这种方案也叫HHT (希尔伯特-黄变换)可想而知,如果原信号的分量在频域上是较为稀疏的(比如机械振动) ,这个方法会很好用。但也有很多问题,比如端点延拓(信号较短,生成包络线的时候会在端点处产生巨大的失真) ,比如模态混叠(因为分离标准过于模糊,所以一些时高频信号会出现在低频分量里)。另外,很多人还会嫌弃EMD没有很好的数学根基。





> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx84a5]**

[^1]: Algorithms for estimating instantaneous frequency
[^2]:Broman, H. "The instantaneous frequency of a Gaussian signal: the one-dimensional density function." *IEEE Transactions on Acoustics, Speech, and Signal Processing* 29.1 (1981): 108-111.
[^3]: 

