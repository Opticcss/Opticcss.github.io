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

​	The **Nyquist-Whittaker-Shannon-Kotelnikov sampling theorem**, which point out the condition to avoid aliasing in the effective reconstruction of one signal, implies that the sampling frequency should be the twice of the frequency of the maximum frequency component. To guarantee this, hence to reconstruct the original signal effectively, the system used is always designed by a unreasonable structure, which limited the signal in $1/2$​ sampling frequency, and then apply the reconstruction. To be more precise, this make the signal a limited bandwidth signal, i.e., in $F(\xi)=0,\|\xi\|>f_s/2$​.

- input signal $\xrightarrow{\text{LPF (cut off frequency }f_s/2)}$ low pass filtered signal $\xrightarrow{\text{ADC}}$ sampled signal $\xrightarrow{\text{DAC}}$ output signal.

​	But the thing happened if the signal frequency (or the bandwidth, denoted as $B$) surpass the $f_s/2$ is still unclear enough. This phenomenon, called **aliasing** in general, **means that the high frequency components are mixed with the low frequency components, hence the reconstruction signal is different with the original one**.

​	The detail of this phenomena is worth studying, and it implies a **symmetry mixing** in real application/condition.

​	Most recently, the author ([@Opticcss](https://opticcss.github.io/)) was lucky to experience an experiment to perform the signal reconstruction using FM4 board, based on WM8731 codec (which is composed of an A/D convertor and a D/A convertor). As the result shows, when input a sinusoidal wave $7000{\rm\ Hz}$ into the WM8731 codec, the reconstruction signal is a sinusoidal wave of $999.9869{\rm\ Hz}$. On the other side, if give the sinusoidal wave of $4500{\rm\ Hz}$, the system will output a $3500.097{\rm\ Hz}$ wave. By the way, the sampling frequency is $8000{\rm\ Hz}$, these analysed results are as shown below, and displayed a symmetrical with respect to $\sim4000{\rm\ Hz}$.

![[OPTSx0922]_Phenomenon_for_Aliasing](/assets/images/[OPTSx0922]_Phenomenon_for_Aliasing.svg)

​	Summarization from the phenomenology is as following shows, which are some general patterns,

- No aliasing if $B\leq f_s/2$.
- Turnover the surpass components w.r.t. $f_s/2$ line, into the region of $[0,f_s/2]$, and superposition with the original components, if $f_s>B>f_s/2$.
- ... ... ... ... ... ...
- Turnover the surpass components w.r.t. $f_s(n-1)/2$ line, which make the components of $f_sn/2-B$ to be the frequencies of the reconstructed signal, if $f_sn/2>B>f_s(n-1)/2$.

	Of course these are just bits of assumptions/derivations based on the observations. There must be some theoretical thing underlaying them.

## **2. Basic Theory About Shah Function Sampling/Impulse Train Sampling, the Theory of Reconstruction**

​	The main character of this part, the **Shah function**/**Dirac comb** $\mathrm{Ш}_T[t]$, is defined as following.

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

​	The second equivalence is deduced by the **Poisson (summation) formula**, for more details, see [Addendum 1st](#jump01).

​	This demonstrate a splendid property of the Dirac comb that the Fourier transform of a comb is still a Dirac comb, with its frequency changed into the inverse of original frequency divided by $2\pi$, i.e.,  $\mathscr{F}\{\mathrm{Ш}_T(t)\}={2\pi}\mathrm{Ш}_{2\pi/T}(\omega)/T$.

​	Hence, the formal process in rigorous implementation is as

- Sampling, in which the multiplication is taken for the signal and Dirac comb in the time domain, which is equivalent to the convolution of impulses and original signal.

$$
\begin{equation}
\begin{split}
f[n]&=f(t)\cdot\mathrm{Ш}_T(t),\\
\mathscr{F}\{f[n]\}&=\frac1{2\pi}\hat{f}(\omega)\star\frac{2\pi}T\mathrm{Ш}_{2\pi/T}(\omega),
\end{split}
\end{equation}
$$

- Reconstruction, the window function $\Pi(\omega)$ in frequency domain (or the $\text{sinc}(t)$ in time domain, the lowpass filter) is used to perform convolution in time domain as following (which is called the Whittaker-Shannon interpolation formula, and have extreme importance in reconstruction, note that  $M=1/2B=1/W$, and the bandwidth satisfy  $B>f_s/2$).

$$
\begin{equation}
\begin{split}
f(t)&=f[n]\star\mathrm{sinc}\bigg(\frac{t}{M}\bigg)\\
&=\sum_{n=-\infty}^\infty f[n]{\rm sinc}\bigg(\frac{t-nM}{M}\bigg)\\
&=W\sum_{n=-\infty}^\infty f[n]{\rm sinc}(W({t-nM}),\\
\hat{f}(\omega)&=\mathscr{F}\{f[n]\}\cdot\Pi(\omega),
\end{split}
\end{equation}
$$

​	From which, the condition of effective reconstruction is $2B<f_s$. Otherwise, the lowpass filtering will include some frequency components, or even cannot include the holistic frequency components which are useful. The whole process or digitalization (sampling) $\to$ reconstruction can also be presented as following flow chart shows.

![[OPTSx0922]_Reconstruction_Process_of_a_Signal](/assets/images/[OPTSx0922]_Reconstruction_Process_of_a_Signal.svg)

​	It can be seen that for the original signal $f(t)$​, in this chart,

- The sampling use the $f(t)\times\mathrm{Ш}_T(t)$ in time domain, which is just the convolution operation $\hat{f}(\omega)\star2\pi\mathrm{Ш}_{2\pi/T}(t)$​ in frequency domain, which is then equivalent to **construct multiple copies for the frequency spectrum of original signal**.
- Then the lowpass filter is used to **cut/filtering the copied frequency spectrum**, which is simply the $\hat{f}_d(\omega)\times\hat{\phi}_s(\omega)$ operation in frequency domain, and $f_d(t)\star h(t)$ in the time domain.

## **3. Things Happened at Aliasing**

​	After the basic theory of sampling and reconstruction, it is easy to conclude that aliasing is a phenomena of the mixing of high frequency information/components into the low frequency information when perform interpolation under $f_s>2B$​.

​	

#### **<span id="jump01">Addendum 1st </span>——Strict Demonstration of Poisson (summation) Formula**

​	Here, distributions of the following two expressions are treated as equivalent (of course, for  $\forall\phi\in\C_0^\infty$, the weak continuous property should also be satisfied).
$$
\begin{equation}
\begin{split}
\int_{-\infty}^\infty d_1(t)\phi(t){\mathrm{d}}t=\int_{-\infty}^\infty d_2(t)\phi(t){\mathrm{d}}t,
\end{split}
\end{equation}
$$
​	In this sense, a test function $\hat\theta(\omega)$ is adopted to demonstrate $\mathscr{F}\{\mathrm{Ш}_T(t)\}={2\pi}\mathrm{Ш}_{2\pi/T}(\omega)/T$ (which can then deduce the conclusion of equivalence distribution). Firstly, act the $\mathscr{F}\{\mathrm{Ш}_T(t)\}$ on the test function $\hat\theta(\omega)$ by the inner product $\langle\cdot,\cdot\rangle$.

$$
\begin{equation}
\begin{split}
\langle\mathscr{F}\{\mathrm{Ш}_T(t)\},\hat\theta\rangle=\lim_{N\to+\infty}\int_{-\infty}^\infty\sum_{n=-N}^{N}e^{inT\omega}\hat\theta(\omega){\mathrm{d}}\omega=\frac{2\pi}T\hat\theta(0),
\end{split}
\end{equation}
$$

​	Then evaluate its summation by the geometry series as,

$$
\begin{equation}
\begin{split}
\sum_{n=-N}^{N}e^{inT\omega}=\frac{\sin[(N+1/2)T\omega]}{\sin[T\omega/2]},
\end{split}
\end{equation}
$$

​	followed by the substitution and denote a Fourier pair as $\psi(t)$ and $\hat\psi(\omega)$, generalize the integration into the whole field,

$$
\begin{equation}
\begin{split}
&\begin{split}
\langle\mathscr{F}\{\mathrm{Ш}_T(t)\},\hat\theta\rangle&=\lim_{N\to+\infty}\frac{2\pi}T\int_{-\pi/T}^{\pi/T}\frac{\sin[(N+1/2)T\omega]}{\pi\omega}\frac{T\omega/2}{\sin[T\omega/2]}\hat\theta(\omega){\mathrm{d}}\omega\\
&=\lim_{N\to+\infty}\frac{2\pi}T\int_{-\infty}^\infty\frac{\sin[(N+1/2)T\omega]}{\pi\omega}\hat\psi(\omega){\mathrm{d}}\omega,
\end{split}\\
&\text{where }\hat\psi(\omega)=\left\{
\begin{split}
&\hat\theta(\omega)\frac{T\omega/2}{\sin[T\omega/2]}\ \ \ \ \ &\text{if }|\omega|\leq\pi/T,\\
&0&\text{if }|\omega|>\pi/T,
\end{split}
\right.
\end{split}
\end{equation}
$$

​	Based on this, the Parseval formula is then used to convert the frequency equation into the time domain, notice that the Fourier transform of ${\sin[(N+1/2)T\omega]}/{\pi\omega}$ is just $\boldsymbol{1}_{[-(N+1/2),(N+1/2)]}(t)$, have that
$$
\begin{equation}
\begin{split}
\langle\mathscr{F}\{\mathrm{Ш}_T(t)\},\hat\theta\rangle
=\lim_{N\to+\infty}\frac{2\pi}T\int_{-(N+1/2)T}^{(N+1/2)T}\psi(\omega){\mathrm{d}}t,
\end{split}
\end{equation}
$$

​	Hence the conclusion of Poisson (Summation) formula is proved, it can also be seen that when $N\to+\infty$, the integration will converge to $\hat\psi(0)=\hat\theta(0)$. This conclusion also has its application in other field in the form as following shows,

$$
\begin{equation}
\begin{split}
\sum_{n=-\infty}^\infty f(t+nT)=\sum_{k=-\infty}^\infty\frac1T\mathscr{F}\{f(t)\}\bigg(\frac kT\bigg)e^{{\mathrm{j}}2\pi kt/T}.
\end{split}
\end{equation}
$$

> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx0922]**

[^1]: Proakis, John G., and Dimitris G. Manolakis. "Digital signal processing." *MPC, New York* (1992).
[^2]: Mallat, Stéphane. *A wavelet tour of signal processing*. Elsevier, 1999.
[^3]: https://en.wikipedia.org/w/index.php?title=Dirac_comb&oldid=1036647881 last edited on 1 August 2021, at 21:49 (UTC).

