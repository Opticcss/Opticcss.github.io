---
layout: optics_post
title:  "Instantaneous Frequency"
author: Li Jinzhao
categories: [harmonic analysis]
image: ....jpg
tags: [Fourier, Julia, cpp, harmonic]
---

> **Abstract**/**Snippet**. 这里多扯几句小想法。在一片混杂的原信号里提取单一成分并求瞬时频率,是个很有工程意义的问题,在信号降噪、模态提取、颤振预测、故障诊断上都有应用。传统的STFT和WT的思路是用统一的方法对整个频率范围做分析(本质上都是用一组不同尺度的基与不同时间的原信号求内积,获得系数矩阵) ,自然会遇到测不准原理的限制,出现分辨率不足的问题。
>
> 所以成分提取的方法需要有几个特点: @一定的自适应性,允许每一成分有频率波动; ②较好的分辩能力,不至于分量间交叉后就糊成一片; ③一定的噪声鲁棒性。


**Contents**

* toc
{:toc}
## **1. Stationary Signal Analysis, Ramifications of <u>Fourier Transform</u> × 4 (Recall)**

​	Before discussions, there are totally 4 forms of Fourier operations for manipulation of stable signals (signals with constant frequency/frequency distribution), Fourier series representation (FS), the Fourier transform (FT), the discrete Fourier transform (DFT), and the discrete time Fourier transform (DTFT).

- **Fourier series** representation: most basic examples in the field of harmonic analysis
  
  - **continuous** time, **periodic**.
  - **discrete** frequency, **aperiodic**.
  
  $$
  \begin{equation}
  \begin{split}
  x(t)&=\sum_{k=-\infty}^{+\infty}a_ke^{{\rm j}k\omega_0t}
  =\sum_{k=-\infty}^{+\infty}a_ke^{{\rm j}k(2\pi/T)t},\\
a_k&=\frac1T\int_Tx(t)e^{-{\rm j}k\omega_0t}{\rm d}t=\frac1T\int_Tx(t)e^{-{\rm j}k(2\pi/T)t}{\rm d}t,
  \end{split}
  \end{equation}
  $$
  ​	Parseval's theorem for it yields $1/T\int_T|x(t)|^2{\rm d}t=\sum_{k=-\infty}^{+\infty}|a_k|^2$. Lead to an overshoot of $\sim9\%$ at jump discontinuity (the Gibbs phenomenon). Equivalently, there exists the **trigonometric Fourier series representation** as
  $$
  \begin{equation}
  \begin{split}
  x(t)&\xlongequal{\omega_0=2\pi/T}a_0+\sum_{n=1}^{+\infty}(a_n\cos{n\omega_0t}+b_n\sin{n\omega_0t})\\
  &\xlongequal{\omega_0=2\pi/T}a_0+\sum_{n=1}^{+\infty}A_n\cos{(n\omega_0t+\varphi_n)},\\
  a_0&=\frac1T\int_Tx(t){\rm d}t,\\
  a_n&=\frac2T\int_Tx(t)\cos{(n{\omega_0t})}{\rm d}t,\ \ b_n=\frac2T\int_Tx(t)\sin{(n{\omega_0t})}{\rm d}t,\\
  A_n&=\sqrt{a_n^2+b_n^2},\ \ \varphi_n=-\tan^{-1}\frac{b_n}{a_n},
  \end{split}
  \end{equation}
  $$
  
- **Fourier transform**: most common used, the idea of domain (linear space, basis, ...)

  - **continuous** time, **aperiodic**.
  - **continuous** frequency, **aperiodic**.

  $$
  \begin{equation}
  \begin{split}
  x(t)&=\frac1{2\pi}\int_{-\infty}^{+\infty}X({\rm j}\omega)e^{{\rm j}\omega t}{\rm d}\omega,\\
  X({\rm j}\omega)&=\mathscr{F}\{x(t)\}=\int_{-\infty}^{+\infty}x(t)e^{-{\rm j}\omega t}{\rm d}t,
  \end{split}
  \end{equation}
  $$

- **discrete Fourier transform**: always used in computation (sampling in both the time and the frequency), implemented by FFT

  - **discrete** time, **periodic**.
  - **discrete** frequency, **periodic**.

  $$
  \begin{equation}
  \begin{split}
  x[n]&=\frac1N\sum_{k=0}^{N-1}a_k\cdot e^{{\rm j}k(2\pi /N)n},\\
  a_k&=\mathcal{DFT}\{x[n]\}=\sum_{n=0}^{N-1}x[n]\cdot e^{-{\rm j}k(2\pi /N)n},
  \end{split}
  \end{equation}
  $$
​	in form of twiddle factor (in FFT) $W_N^k=e^{-i2\pi k/N}$, can also be written as
  $$
  \begin{equation}
  \begin{split}
  \mathcal{DFT}\{x[n]\}=\sum_{n=0}^{N-1}x[n]\cdot W_N^{kn},
  \end{split}
  \end{equation}
  $$
  ​	Equivalently, the discrete cosine transform, with higher energy compaction (most of the signal information is concentrated in the low-frequency component) is as
  $$
  \begin{equation}
  \begin{split}
  x[n]&=\sum_{k=0}^{N-1}a_k\cdot \cos\bigg[\frac{(2n+1)\pi k}{2N}\bigg]\cdot c_k,\\
  a_k&=\mathcal{DCT}\{x[n]\}=\sum_{n=0}^{N-1}x[n]\cdot \cos\bigg[\frac{(2n+1)\pi k}{2N}\bigg]\cdot c_k,\\
  c_0&=\sqrt{\frac{1}{N}},\ \ c_k=\sqrt{\frac{2}{N}}\ \ \text{for }k>0,
  \end{split}
  \end{equation}
  $$
  
- **discrete time Fourier transform**: the discrete sampling in time domain after FT

  - **discrete** time, **aperiodic**.
  - **continuous** frequency, **periodic**.

  $$
  \begin{equation}
  \begin{split}
  x[n]&=\frac1{2\pi}\int_{2\pi}X(e^{{\rm j}\omega})e^{{\rm j}\omega n}{\rm d}\omega,\\
  X(e^{{\rm j}\omega})&=\sum_{n=-\infty}^{+\infty}x[n]e^{-{\rm j}\omega n},
  \end{split}
  \end{equation}
  $$



​	注意上面这一系列方法中，都是由 $\mathscr{X}\{\cdot\}$ 吃进整个一列的时间序列信号得到 global 的频率结果，频域中的信息与时域没有对应关系，自然就没有了“某一时刻的瞬时频率”这样的概念...

对于时域信号，它可以有很高的时间分辨率，然而其频率分辨率为零

傅里叶变换是一种全局的变换，时域信号经过傅里叶变换后，就变成了频域信号，从频域是无法看到时域信息的，我们可以从上节中的傅里叶变换和反变换公式进行解释，进行正变换时，积分区间为整个时域，所以变换结果将不包含时域信息，反变换同理。

仅仅通过时域信号或者幅度谱，我们是很难分析这段非平稳信号的特征的。

两路扫频信号叠加后的信号和它的幅度谱





## **2. Single Frequency Non-Stationary Signals or Signal with Few Overtones, <u>Hilbert Transform</u> (HT)**

​	Corresponding to the **non-stationary signals** with single frequency or with few overtones, the information in time domain becomes significant (it is difficult to analyse the characteristics of this non-stationary signal only through the time domain signal or its power spectrum. On the other side, it can be seen that the frequency domain signal obtained by Fourier transform can achieve high frequency resolution, but **its time resolution is zero**).

> ### **Examples of Signal with Single Frequency**
>
> One example for this kind of signal and its frequency is the **chirp signal** (swept-frequency cosine signal), which takes the form
> $$
> \begin{equation}
> \begin{split}
> x(t)=\exp\bigg[{\rm j}2\pi\bigg(f_0t+\frac12u_0t^2\bigg)\bigg],
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
H(\omega)=-{\rm j}\cdot\text{sgn}(\omega)=
\left\{
\begin{split}
&e^{{-\rm j}\pi/2},\text{ }&\omega\geq0,\\
&e^{{\rm j}\pi/2},\text{ }&\omega<0,
\end{split}
\right.
\end{split}
\end{equation}
$$
​	The analytic signal is the combination of original signal and its Hilbert transform, in form of $z(t)=x(t)+{\rm j}H[x(t)]$, take the time derivation of this analytic signal as shown in the example, the final result of the frequency $\omega_i(t)$ is as
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



## **2. Weird Idea with Multiple Frequency Components (Noisy), <u>Short Time Fourier Transform</u> (STFT)**

- **复杂时变信号的瞬时幅度和瞬时频率都不是一个常数，它们总是随着时间变化。**
- **IF是一个正值函数，则信号本身有同样多的过零点和极值点。**
- **IF有负值，则信号在相继的过零点间有多个极值。**

​	通过引入一个窗函数 $w(t)$，得到将信号时域与频域联系起来的一个二维函数 $X(n,\omega)$，模的平方 $S(n,\omega)=|X(n,\omega)|^2$ 称为语音信号的语谱图 (spectrogram)
$$
\begin{equation}
\begin{split}
X(n,\omega)=\sum_{t=-\infty}^{+\infty}x(t)\omega(n-t)e^{-j\omega t},
\end{split}
\end{equation}
$$
由一个0~250Hz二次递增的扫频信号和一个250~0Hz二次递减的扫频信号的叠加

得出非平稳信号的时变特性



不同的窗长度自然对应着不同的语谱图， 大体分为窄带语谱图，宽带语谱图，长时窗（至少两个基音周期）常被用于计算窄带语谱图，短窗则用于计算宽带语谱图。**窄带语谱图具有较高的频率分辨率和较低的时间分辨率**，良好的频率分辨率可以让语音的每个谐波分量更容易被辨别，在语谱图上显示为水平条纹。相反**宽带语谱图具有较高的时间分辨率和较低的频率分辨率**，低频率分辨率只能得到谱包络，良好的时间分辨率适合用于分析和检验英语语音的发音





帧长固定的短时傅里叶变换，在全局范围内的时间分辨率和频率分辨率是固定的。如果我们想要在低频区域具有高频率分辨率，在高频区域具有高时间分辨率，显然STFT是不能满足要求的

对于短时傅里叶变换(STFT)，它在时域和频域都有一定的分辨率，并且在全局范围内STFT的时频分辨率都是一样的。但是由于Heisenberg不确定原理(也就是量子力学中的测不准原理)的制约，每一个时频窗的面积都是固定的，即时间分辨率和频率分辨率成反比，所以这两个分辨率不能同时很高

## **3. Semi-Adaptive within Gabor Limit, <u>Continuous Wavelet Transform</u> (CWT)**

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





上述的方法都会受到Heisenberg不确定原理的限制，而且并不是完全自适应的方法。接下来介绍





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





> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx8686]**

[^1]: Algorithms for estimating instantaneous frequency
[^2]:Broman, H. "The instantaneous frequency of a Gaussian signal: the one-dimensional density function." *IEEE Transactions on Acoustics, Speech, and Signal Processing* 29.1 (1981): 108-111.
[^3]: 

