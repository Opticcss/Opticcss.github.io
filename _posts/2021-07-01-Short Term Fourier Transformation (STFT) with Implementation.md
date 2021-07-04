---
layout: optics_post
title:  "Short Term Fourier Transformation (STFT) with Implementation"
author: Li Jinzhao
categories: [signal processing]
image: ....jpg
tags: [harmonic analysis, Julia]
typora-root-url: ..
---
> **Abstract**/**Snippet**. Short term Fourier transform as an effective method to obtain the time-frequency representation of signals, was proposed in the early stage and is still active in audio processing tasks today[^1]. The basic principle and implementation scheme based on `Julia` are introduced here, and an example result is presented.


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

​	Parseval's theorem for it yields $1/T\int_T\|x(t)\|^2{\rm d}t=\sum_{k=-\infty}^{+\infty}\|a_k\|^2$. Lead to an overshoot of $\sim9\%$ at jump discontinuity (the Gibbs phenomenon). Equivalently, there exists the **trigonometric Fourier series representation** as

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

​	This result is, of course, a 2D function that links the time $t$ and frequency $\omega$ domains of a signal, and the square of it is $S(t,\omega)=\|X(t,\omega)\|^2$ is often be of great usage as the spectrogram of an speech signal. Note that different lengths of window naturally correspond to different spectrograms, which are generally divided into narrow-band spectrograms and broadband spectrograms. The former one has **high frequency resolution but extremely low time resolution, which can make each harmonic component of speech be identified more easily**. On the contrary, **broadband spectrogram has higher temporal resolution**. Hence, STFT provide a true time-frequency representation (TFR) of the signal compared with DFT.

### **2.1. Implementation of the STFT**

​	The implementation provided here[^2] is based on `hanning` window, for others, users can define their own window type and treat them as one of the parameter to perform the multiple dispatch in `Julia`. There are three predefined parameters in total, as shown below.

- width of each window, denoted by the length of each section `nsection_`.
- number of overlapping points for each adjacent window `noverlap_`.
- FFT sampling points for each window `nfft_`, which is used in the Fourier transform (here, the self-defined FFT is used in the code, which is equivalent to the `FFTW` standard).
- sampling frequency `fs_`, which should always be predefined.

![[OPTSx84a3]_Sliding_Windows_STFT_Parameters](/assets/images/[OPTSx84a3]_Sliding_Windows_STFT_Parameters.svg)

​	After defining related parameters, using zero padding to let signal goes to $2^{(\cdot)}$, and calculate the number of window slides (number of slices the signal is divided), as

$$
\begin{equation}
\begin{split}
\text{col_}=\frac{\text{length}(x(\tau))-\text{nsection}}{\text{nsection}-\text{noverlap}},
\end{split}
\end{equation}
$$

​	This is hence the number of column in the final result, broadcast `*` to the corresponding elements in window function and the signal, the apply the FFT to these column elements, the spectrogram can be hence obtained.

​	Realize this procedure with `Julia`, the source code is shown as below. One for the self-defined parameters (`noverlap_`, `nsection_`) and the other multiple dispatch function for the automatic choice for these parameters, but the sampling frequency is always needed for the context. Additionally, it is also worth that all of them take a tuple as `Tuple{Matrix{Float64}, Vector{Any}, Vector{Any}}`, which contains a STFT matrix, a time ticks (as `Vector{StepRangeLen{Float64, Base.TwicePrecision{Float64}, Base.TwicePrecision{Float64}}}`, which is same for the frequency) and a frequency ticks, as their output.

```Julia
# multiple dispatch of the hanning STFT algorithm with undefined parameters
function hanning_stft(x_::Vector{ComplexF64}, fs_::Float64)::Tuple{Matrix{Float64}, Vector{Any}, Vector{Any}}
    nsection_ = floor(Int64, fs_/8) # nsection_ = Int64(floor((length(x_)/4.5))) # the width of hanning window
    noverlap_ = floor(Int64, 0.9*(floor(fs_/8))) # noverlap_ = Int64(floor(nsection_/3)) # the number of points for overlaping
    nfft_ = max(256, 2^(nextpow(2, nsection_))) # number of points of FFT
    Hanning_ = prd_hanning(nsection_) # hanning weight function (periodic)
    step_col_ = nsection_ - noverlap_ # number of step per column
    col_ = round(Int64, ((length(x_) - nsection_)/step_col_), RoundToZero) + 1 # number of column
    row_ = Int64(nfft_/2 + 1) # number of row
    X_ = ComplexF64.(zeros(row_, col_)) # pre-allocated memory for pre-processed signal
    x_n_index = 1
    for n_index = 1:col_
        temp_X = radix_2_fft([x_[x_n_index:x_n_index + nsection_ - 1] .* Hanning_; zeros(nfft_ - nsection_)]); # weighting the signal using the window and take its FFT
        X_[:,n_index] = temp_X[1:row_]; # take half of the temporary value
        x_n_index = x_n_index + (nsection_ - noverlap_); # sliding window...
    end
    X_ = abs.(X_./nfft_) # if want to convert the result into a real matrix
    f_axis = fs_.*[0:row_ - 1]./nfft_ # generate the frequency axis
    t_axis = (0.5*nsection_:step_col_:(step_col_*(col_ - 1) + 0.5*step_col_))./fs_ # generate the time axis
    X_, f_axis, t_axis # output as tuple
end
```

​	while the other multiple dispatch has the different signature as

```julia
# multiple dispatch of the hanning STFT algorithm with self-defined parameters
function hanning_stft(x_::Vector{ComplexF64}, nsection_::Int64, noverlap_::Int64, nfft_::Int64, fs_::Float64)::Tuple{Matrix{Float64}, Vector{Any}, Vector{Any}}
	# ...
end
```

​	where the `f_axis` are determined by following equation, and the `t_axis` is obtained by sliding window.

$$
\begin{equation}
\begin{split}
\text{f_aixs}&=i\times\frac{\text{fs_}}{\text{nff_}}, i\in(0,\text{nff_}),
\end{split}
\end{equation}
$$

​	As one of the test of this self-define STFT function, the time-frequency representation of the two-channel sweeping signal in the figure below (left) is shown in the image on the right, which indicates that STFT has certain time and frequency resolution ability (as if the parameters are properly defined), from which one can learn the information of signal frequency variation with time.

![[OPTSx84a3]_Chirp_STFT_Test](/assets/images/[OPTSx84a3]_Chirp_STFT_Test.svg)

​	the source code used to produce this test for STFT algorithm is as following shown, it is clear that two chirp signal are produced for completely inverse changes in their frequencies, by switching the $\varphi$ parameter to a minus one.

```julia
k_nor = 60
fs_ = 500.0
time_vec = 0:1/fs_:(0.002*(2^10-1)) # make f = kt, φ = 2π.*k.*t.^2
w_o_p = cos.(2π.*k_nor.*time_vec.^2) .+ (sin.(2π.*k_nor.*time_vec.^2))im
w_o_p += cos.(-2π.*k_nor.*(0.002*(2^10-1) .- time_vec).^2) .+ (sin.(-2π.*k_nor.*(0.002*(2^10-1) .- time_vec).^2))im
s_o_p, f_axis, t_axis = hanning_stft(w_o_p, fs_)
w_o_p = abs.(w_o_p)
# palettef = Scale.lab_gradient("#cb997e", "#ddbea9", "#ffe8d6", "#b7b7a4", "#a5a58d", "#6b705c")
# palettef = Scale.lab_gradient("#1d3557", "#457b9d", "#a8dadc", "#f1faee", "#e63946")
# palettef = Scale.lab_gradient("#277da1", "#577590", "#4d908e", "#43aa8b", "#90be6d", "#f9c74f", "#f9844a", "#f8961e", "#f3722c", "#f94144")
palettef = Scale.lab_gradient("#fffcf2", "#ccc5b9", "#403d39", "#252422", "#eb5e28", "darkred")
# palettef = Scale.lab_gradient("#ffbe0b", "#fb5607", "#ff006e", "#8338ec", "#3a86ff")
# palettef = Scale.lab_gradient("#264653", "#2a9d8f", "#e9c46a", "#f4a261", "#e76f51")
Gadfly.with_theme(:default) do
    spacer = Int64(round(max(size(s_o_p, 1), size(s_o_p, 2))/200))
    Nia_draw = Gadfly.spy(20s_o_p[1:spacer:end, 1:spacer:end], alpha = [0.9],
        Scale.color_continuous(colormap = palettef, minvalue=minimum(20s_o_p[1:spacer:end, 1:spacer:end]), maxvalue=maximum(20s_o_p[1:spacer:end, 1:spacer:end]))
    )
    ori_ = plot(x=1:length(w_o_p), y=w_o_p, Geom.line,
        style(line_width=.1mm, line_style=[:solid]),
        Coord.cartesian(xmin=1, xmax=length(w_o_p), ymin=minimum(w_o_p), ymax=maximum(w_o_p)))
    Gfspy = SVG("C:/Users/a1020/Desktop/Nia02.svg")
    Gadfly.draw(Gfspy, hstack(ori_, Nia_draw))
end
```

### **2.2. Realization of N-Points Periodic Hanning Window**

​	Note that the Hanning window realized here can maintain the horizontal phase property of FFT results, so it is more suitable for the application scenarios of FFT. Of course, it is an asymmetric window, and the sequence length is taken as the denominator in the generation formula (hence it includes the first zero-weighted window sample).

​	Take the `varargin` as the input, firstly, the symmetric Hanning window is realized by the equation as below[^3]

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

​	Hence, the calculation is realized by following functions, which includes the periodic and symmetric Hanning window, note that for the periodic one, the results should include a $0$ term as its head, as shown in the equation.

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
    w_
end
# for periodic hanning window samples generation
prd_hanning(n_) = [0; sym_hanning(n_ - 1)]
```

​	As one of the visualization, a Hanning window can be calculated and plotted using `Gadfly` as shown below.

![[OPTSx84a3]_Hanning_Window_Function](/assets/images/[OPTSx84a3]_Hanning_Window_Function.svg)

​	the source code used to produce this Hanning window is as following shown.

```julia
using Gadfly
w_o_p = prd_hanning(60)
set_default_plot_size(20cm, 8cm)
Gadfly.with_theme(:default) do
    ori_ = plot(x=1:length(w_o_p), color = 1:length(w_o_p), y=w_o_p, Geom.point, Geom.bar, # Geom.line,
        alpha = [0.5],
        Theme(bar_spacing=-0.2mm, key_position=:none),
        style(line_width=.5mm, line_style=[:solid]), size=[2.5pt],
        Guide.xlabel("number of indices"), Guide.ylabel("hanning window"),
        Guide.title("hanning window"),
        # Scale.color_continuous(colormap = palettef, minvalue=1, maxvalue=length(w_o_p)),
        Guide.xticks(ticks=1:15:length(w_o_p), label=true, orientation=:horizontal), Guide.yticks(ticks=minimum(w_o_p):0.1:maximum(w_o_p)),
        Coord.cartesian(xmin=1, xmax=length(w_o_p), ymin=minimum(w_o_p), ymax=maximum(w_o_p)));
    Gfspy = SVG("C:/Users/a1020/Desktop/Nia01.svg")
    Gadfly.draw(Gfspy, ori_)
end
```

> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx84a3]**

[^1]: Oppenheim, Alan V., Ronald W. Schafer, and John R. Buck. *Discrete-Time Signal Processing*. 2nd Ed. Upper Saddle River, NJ: Prentice Hall, 1999.
[^2]: Fulop, Sean A., and Kelly Fitz. “Algorithms for computing the time-corrected instantaneous frequency (reassigned) spectrogram, with applications.” *Journal of the Acoustical Society of America*. Vol. 119, January 2006, pp. 360–371.
[^3]: https://ww2.mathworks.cn/help/signal/ref/hann.html

