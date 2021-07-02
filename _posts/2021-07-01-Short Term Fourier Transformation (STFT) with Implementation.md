---
layout: optics_post
title:  "Short Term Fourier Transformation (STFT) with Implementation"
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
## **1. Recall, Stationary Signal Analysis, Ramifications of Fourier Transform × 4**

​	Fourier transform cannot handle the case when both the time and frequency information of one signal is required, in other words, FT (same as DFT, FFT) only have a comparably good frequency resolution, but lose all its time resolution, because it only multiply the same $\sin(\cdot)$ and $\cos(\cdot)$ factors for all samples in any time and then sum them together.

​	In order to be more explicit, the commonly used equations for Fourier transform are listed below, they are operations for manipulation of stable signals (signals with constant frequency/frequency distribution), Fourier series representation (FS), the Fourier transform (FT), the discrete Fourier transform (DFT), and the discrete time Fourier transform (DTFT).

- **Fourier series** representation: most basic examples in the field of harmonic analysis

  - **continuous** time, **periodic**.
  - **discrete** frequency, **aperiodic**.

$$
\begin{equation}
\begin{split}
x(t)&=\sum_{k=-\infty}^{+\infty}a_ke^{\mathrm{j}k\omega_0t}
=\sum_{k=-\infty}^{+\infty}a_ke^{\mathrm{j}k(2\pi/T)t},\\
a_k&=\frac1T\int_Tx(t)e^{-\mathrm{j}k\omega_0t}{\rm d}t=\frac1T\int_Tx(t)e^{-\mathrm{j}k(2\pi/T)t}{\rm d}t,
\end{split}
\end{equation}
$$

​	Parseval's theorem for it yields $1/T\int_T|x(t)|^2{\rm d}t=\sum_{k=-\infty}^{+\infty}|a_k|^2$. Lead to an overshoot of $\sim9\%$ at jump discontinuity (the Gibbs phenomenon). Equivalently, there exists the **trigonometric Fourier series representation** as

$$
\begin{equation}
  \begin{split}
  x(t)&=_{[\omega_0=2\pi/T]}a_0+\sum_{n=1}^{+\infty}(a_n\cos{n\omega_0t}+b_n\sin{n\omega_0t})\\
  &=a_0+\sum_{n=1}^{+\infty}A_n\cos{(n\omega_0t+\varphi_n)},\\
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
x(t)&=\frac1{2\pi}\int_{-\infty}^{+\infty}X(\mathrm{j}\omega)e^{\mathrm{j}\omega t}{\rm d}\omega,\\
X(\mathrm{j}\omega)&=\mathscr{F}\{x(t)\}=\int_{-\infty}^{+\infty}x(t)e^{-\mathrm{j}\omega t}{\rm d}t,
\end{split}
\end{equation}
$$

- **discrete Fourier transform**: always used in computation (sampling in both the time and the frequency), implemented by FFT

  - **discrete** time, **periodic**.
  - **discrete** frequency, **periodic**.

$$
\begin{equation}
\begin{split}
x[n]&=\frac1N\sum_{k=0}^{N-1}a_k\cdot e^{\mathrm{j}k(2\pi /N)n},\\
a_k&=\mathcal{DFT}\{x[n]\}=\sum_{n=0}^{N-1}x[n]\cdot e^{-\mathrm{j}k(2\pi /N)n},
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
x[n]&=\frac1{2\pi}\int_{2\pi}X(e^{\mathrm{j}\omega})e^{\mathrm{j}\omega n}{\rm d}\omega,\\
X(e^{\mathrm{j}\omega})&=\sum_{n=-\infty}^{+\infty}x[n]e^{-\mathrm{j}\omega n},
\end{split}
\end{equation}
$$

​	Obviously, all these methods take in a bunch of time series and throw out a sequence of their global frequency property, act as a $\mathscr{X}(\cdot)$, which yields that the information in frequency domain is of no relation with their time domain counterparts. The so called "instantaneous frequency" does not exists here.

## **2. Weird Idea with Multiple Frequency Components (Noisy), Short Term Fourier Transform (STFT)**

​	From above, it is difficult for Fourier transform/Hilbert transform to describe the full properties of the signal when it has **multiple frequency components and time-varying (especially its frequency) property**. Our ears, however, are highly capable of processing sound and can perform precise frequency analysis on very short sounds.

​	Based on naïve consideration, it maybe useful **to directly apply the Fourier transform for a extremely short time signal to derive its instantaneous frequency** (it maybe seems that for an infinitesimal time, the real instantaneous frequency can be obtained, however, this is not correct), i.e., the short term Fourier transform (STFT).

​	To a certain extent, the short time Fourier transform, as kind of commonly used technology in audio and music processing, reflects the transient characteristics of the signal, especially the time-varying characteristics of its frequency. In this algorithm, a window $w(t)$ with appropriate width is introduced to realize the interception of signal fragments, hence, its analyze equation is as shown below (convolution in time domain is equivalent to sliding window operation)

$$
\begin{equation}
\begin{split}
X(t,\omega)&=\int_{-\infty}^\infty x(\tau)w(\tau-t)e^{-j\omega(\tau-t)}\mathrm{d}t,\\
&=\sum_{\tau=-\infty}^{+\infty}x(\tau)w(\tau-t)e^{-j\omega(\tau-t)},
\end{split}
\end{equation}
$$

​	This result is, of course, a 2D function that links the time $t$ and frequency $\omega$ domains of a signal, and the square of it is $S(t,\omega)=\|X(t,\omega)\|^2$ is often be of great usage as the spectrogram of an speech signal. Note that different lengths of window naturally correspond to different spectrograms, which are generally divided into narrow-band spectrograms and broadband spectrograms. The former one has high frequency resolution but extremely low time resolution, which can make each harmonic component of speech be identified more easily. On the contrary, broadband spectrogram has higher temporal resolution, and it is suitable for the analysis of speech signal.



a true time-frequency representation (TFR) of the signal.

### **2.1. **



### **2.2. Realization of N-Points Periodic Hanning Window**

​	Note that the Hanning window realized here can maintain the horizontal phase property of FFT results, so it is more suitable for the application scenarios of FFT. Of course, it is an asymmetric window, and the sequence length is taken as the denominator in the generation formula (hence it includes the first zero-weighted window sample).

​	Take the `varargin` as the input, firstly, the symmetric Hanning window is realized by the equation as below

$$
\begin{equation}
\begin{split}
w(\cdot)=\left\{
\begin{split}
\frac12\bigg[1-\cos\bigg(2\pi\frac{x}{N+1}\bigg)\bigg]\boxed{\circlearrowleft}&\text{, for }n\_\text{ is even,}\\
\frac12\bigg[1-\cos\bigg(2\pi\frac{x-1}{N+1}\bigg)\bigg]\boxed{\circlearrowleft}&\text{, for }n\_\text{ is odd,}\\
\end{split}
\right.
\end{split}
\end{equation}
$$

​	Hence, the calculation is realized by

```julia
# for calculating the hanning window samples
calc_hanning(m_, n_) = 0.5.*(1 .- cos.(2π*(1:m_)./(n_ + 1))) # returns the first m_ points of an n_ point Hanning window
# for calculating the symmetric hanning window
function sym_hanning(n_)
    if(rem(n_, 2) == 0) # for the cindition of the even number
        w_ = calc_hanning(n_/2, n_)
        w_ = [w_; w_[end:-1:1]]
        else # for the cindition of the odd length window
        w_ = calc_hanning((n_ + 1)/2, n_)
        w_ = [w_; w_[end - 1:-1:1]]
    end
    return w_
end
```



```julia
# for periodic hanning window samples generation
function prd_hanning(n_)
    w_ = [0; sym_hanning(n_ - 1)]
    return w_
end
```





> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx84a3]**

[^1]:https://ww2.mathworks.cn/help/signal/ref/hann.html
[^2]:
[^3]: