---
layout: optics_post
title:  "Dig in Aliasing in Classical Signal Reconstruction"
author: Li Jinzhao
categories: [Signal Processing]
image: ....jpg
tags: [Harmonic Analysis]
typora-root-url: ..
---
> **Abstract**/**Snippet**.


**Contents**

* toc
{:toc}
## **1. Special Symmetry in Aliasing**

​	The **Nyquist-Whittaker-Shannon-Kotelnikov sampling theorem**, which point out the condition to avoid aliasing in the effective reconstruction of one signal, implies that the sampling frequency should be the twice of the frequency of the maximum frequency component. To guarantee this, hence to reconstruct the original signal effectively, the system used is always designed by a unreasonable structure, which limited the signal in $1/2$ sampling frequency, and then apply the reconstruction. To be more precise, this make the signal a limited bandwidth signal, i.e., in $F(\xi)=0,\ |\xi|>f_s/2$.

- input signal $\xrightarrow{\text{LPF (cut off frequency }f_s/2)}$ low pass filtered signal $\xrightarrow{\text{ADC}}$ sampled signal $\xrightarrow{\text{DAC}}$ output signal.

​	But the thing happened if the signal frequency (or the bandwidth, denoted as $B$) surpass the $f_s/2$ is still unclear enough. This phenomenon, called **aliasing** in general, **means that the high frequency components are mixed with the low frequency components, hence the reconstruction signal is different with the original one**.

​	The detail of this phenomena is worth studying, and it implies a symmetry mixing in real application/condition.

​	Most recently, the author ([@Opticcss](https://opticcss.github.io/)) was lucky to experience an experiment to perform the signal reconstruction using FM4 board, based on WM8731 codec (which is composed of an A/D convertor and a D/A convertor). As the result shows, when input a sinusoidal wave $7000{\rm\ Hz}$ into the WM8731 codec, the reconstruction signal is a sinusoidal wave of $999.9869{\rm\ Hz}$. On the other side, if give the sinusoidal wave of $4500{\rm\ Hz}$, the system will output a $3500.097{\rm\ Hz}$ wave. By the way, the sampling frequency is $8000{\rm\ Hz}$, these analysed results are as shown below, and displayed a symmetrical with respect to $\sim4000{\rm\ Hz}$.

![[OPTSx0922]_Phenomenon_for_Aliasing](..\assets\images\[OPTSx0922]_Phenomenon_for_Aliasing.svg)

​	Summarization from the phenomenology is as following shows, they are some patterns included,

- No aliasing if $B\leq f_s/2$.
- Turnover the surpass components w.r.t. $f_s/2$ line, into the region of $[0,f_s/2]$, and superposition with the original components, if $f_s>B>f_s/2$.
- ... ... ... ... ... ...
- Turnover the surpass components w.r.t. $f_s(n-1)/2$ line, which make the components of $f_sn/2-B$ to be the frequencies of the reconstructed signal, if $f_sn/2>B>f_s(n-1)/2$.

	Of course these are just bits of assumptions/derivations based on the observations. There must be some theoretical thing underlaying them.

## **2. Basic Theory About Shah Function Sampling/Impulse Train Sampling, the Theory of Reconstruction**

​	The main character of this part, the Shah function/Dirac comb $\mathrm{Ш}_T[t]$, is defined as following.

> ​	The impulse train (or namely Dirac comb) is a bunch of translated Dirac(s), as $\mathrm{Ш}(t)=\sum_{n=-\infty}^\infty\delta(t-nT)$，with its Fourier transform,

$$
\begin{equation}
\begin{split}
\mathscr{F}\{\mathrm{Ш}_T(t)\}&=\sum_{n=-\infty}^{+\infty}e^{inT\omega}\\
&=\frac{2\pi}T\sum_{k=-\infty}^{+\infty}\delta\bigg(\omega-\frac{2\pi k}T\bigg)\\
&=\frac{2\pi}T\mathrm{Ш}_{2\pi/T}(\omega),
\end{split}
\end{equation}
$$

​	The second equivalence is deduced by the Poisson (summation) formula, for more details, see [Addendum 1st](#jump01).

​	This demonstrate a splendid property of the Dirac comb that the Fourier transform of a comb is still a Dirac comb, with its frequency changed into the inverse of original frequency divided by $2\pi$, i.e.,  $\mathscr{F}\{\mathrm{Ш}_T(t)\}={2\pi}\mathrm{Ш}_{2\pi/T}(\omega)/T$.

​	Hence, the formal process in rigorous implementation is as

- Sampling, in which the multiplication is taken for the signal and Dirac comb in the time domain, which is equivalent to the convolution of impulses and original signal.
- Reconstruction, the window function $\Pi(\omega)$ in frequency domain (or the $\text{sinc}(t)$ in time domain, the lowpass filter) is used to perform convolution in time domain as following (which is called the Whittaker-Shannon interpolation formula, and have extreme importance in reconstruction, note that  $M=1/2B=1/W$, and the bandwidth satisfy  $B>f_s/2$).

$$
\begin{equation}
\begin{split}
f(t)&=\sum_{n=-\infty}^\infty f[n]{\rm sinc}\bigg(\frac{t-nM}{M}\bigg)\\
&=W\sum_{n=-\infty}^\infty f[n]{\rm sinc}(W({t-nM}),
\end{split}
\end{equation}
$$

​	From which, the condition of effective reconstruction is $2B<f_s$. Otherwise, the lowpass filtering will include some frequency components, or even cannot include the holistic frequency components which are useful. The whole process or digitalization (sampling) $\to$ reconstruction can also be presented as following flow chart shows.

![[OPTSx0922]_Reconstruction_Process_of_a_Signal](..\assets\images\[OPTSx0922]_Reconstruction_Process_of_a_Signal.svg)

​	It can be seen that for the original signal $f(t)$​, in this chart,

- The sampling use the $f(t)\times\mathrm{Ш}_T(t)$ in time domain, which is just the convolution operation $\hat{f}(\omega)\star2\pi\mathrm{Ш}_{2\pi/T}(t)$​ in frequency domain, which is then equivalent to **construct multiple copies for the frequency spectrum of original signal**.
- Then the lowpass filter is used to **cut/filtering the copied frequency spectrum**, which is simply the $\hat{f}_d(\omega)\times\hat{\phi}_s(\omega)$ operation in frequency domain, and $f_d(t)\star h(t)$ in the time domain.

## **3. Things Happened at Aliasing**

​	After the basic theory of sampling and reconstruction, it is easy to conclude that 了解了采样和重建的基本知识后，自然就理解了为什么要求采样频率 $f_s$ 大于 $2B$，对一个不满足采样定理条件的函数进行插值就会发生混叠，那么什么叫混叠 (高频分量的信息混叠到低频分量中) 呢?

​	可能先看一张图才会更容易理解，

#### **<span id="jump01">Addendum 1st </span>——Strict Demonstration of Poisson (summation) Formula**

> ​	这里认为两个满足下述式子的分布是相等的 (对于 $\forall\phi\in\C_0^\infty$，当然，必须同时具备弱连续的性质)
> $$
> \begin{equation}
> \begin{split}
> \int_{-\infty}^\infty d_1(t)\phi(t){\mathrm{d}}t=\int_{-\infty}^\infty d_2(t)\phi(t){\mathrm{d}}t,
> \end{split}
> \end{equation}
> $$
> ​	在这层意义上，采用一个测试函数 $\hat\theta(\omega)$ 就能使 $\mathscr{F}\{\text{comb}_T(t)\}={2\pi}\text{comb}_{2\pi/T}(\omega)/T$ 得到证明 (证明上式成立就可以得到分布相等的结论)，首先将 $\mathscr{F}\{\text{comb}_T(t)\}$ 以内积形式作用于 $\hat\theta(\omega)$，有
> $$
> \begin{equation}
> \begin{split}
> \langle\mathscr{F}\{\text{comb}_T(t)\},\hat\theta\rangle=\lim_{N\to+\infty}\int_{-\infty}^\infty\sum_{n=-N}^{N}e^{inT\omega}\hat\theta(\omega){\mathrm{d}}\omega=\frac{2\pi}T\hat\theta(0),
> \end{split}
> \end{equation}
> $$
> ​	先求得里面几何级数的求和式，为
> $$
> \begin{equation}
> \begin{split}
> \sum_{n=-N}^{N}e^{inT\omega}=\frac{\sin[(N+1/2)T\omega]}{\sin[T\omega/2]},
> \end{split}
> \end{equation}
> $$
> ​	代入，并且设一个 Fourier 变换对 $\psi(t)$ 和 $\hat\psi(\omega)$，就能将积分扩展到全域了
> $$
> \begin{equation}
> \begin{split}
> &\begin{split}
> \langle\mathscr{F}\{\text{comb}_T(t)\},\hat\theta\rangle&=\lim_{N\to+\infty}\frac{2\pi}T\int_{-\pi/T}^{\pi/T}\frac{\sin[(N+1/2)T\omega]}{\pi\omega}\frac{T\omega/2}{\sin[T\omega/2]}\hat\theta(\omega){\mathrm{d}}\omega\\
> &=\lim_{N\to+\infty}\frac{2\pi}T\int_{-\infty}^\infty\frac{\sin[(N+1/2)T\omega]}{\pi\omega}\hat\psi(\omega){\mathrm{d}}\omega,
> \end{split}\\
> &\text{where }\hat\psi(\omega)=\left\{
> \begin{split}
> &\hat\theta(\omega)\frac{T\omega/2}{\sin[T\omega/2]}\ \ \ \ \ &\text{if }|\omega|\leq\pi/T,\\
> &0&\text{if }|\omega|>\pi/T,
> \end{split}
> \right.
> \end{split}
> \end{equation}
> $$
> ​	接下来就直接利用 Parseval 公式将频域转到时域，并且注意到 ${\sin[(N+1/2)T\omega]}/{\pi\omega}$ 的 Fourier 变换就是 $\boldsymbol{1}_{[-(N+1/2),(N+1/2)]}(t)$
> $$
> \begin{equation}
> \begin{split}
> \langle\mathscr{F}\{\text{comb}_T(t)\},\hat\theta\rangle
> =\lim_{N\to+\infty}\frac{2\pi}T\int_{-(N+1/2)T}^{(N+1/2)T}\psi(\omega){\mathrm{d}}t,
> \end{split}
> \end{equation}
> $$
> ​	证毕，可以看到当 $N\to+\infty$ 时，积分收敛到 $\hat\psi(0)=\hat\theta(0)$
>	
> ​	这里顺带再强调一下这个 Poisson (Summation) formula，它在信号处理理论中有很多应用，其含义为一个信号可以和它的 Fourier 变换联系起来，表述为
> $$
> \begin{equation}
> \begin{split}
> \sum_{n=-\infty}^\infty f(t+nT)=\sum_{k=-\infty}^\infty\frac1T\mathscr{F}\{f(t)\}\bigg(\frac kT\bigg)e^{{\mathrm{j}}2\pi kt/T},
> \end{split}
> \end{equation}
> $$

> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx0922]**

[^1]: Proakis, John G., and Dimitris G. Manolakis. "Digital signal processing." *MPC, New York* (1992).

[^2]: Mallat, Stéphane. *A wavelet tour of signal processing*. Elsevier, 1999.

