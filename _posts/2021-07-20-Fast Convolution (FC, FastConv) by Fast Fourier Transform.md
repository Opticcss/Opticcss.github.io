---
layout: optics_post
title:  "Fast Convolution (FC, FastConv) by Fast Fourier Transform"
author: Li Jinzhao
categories: [signal processing]
image: ....jpg
tags: [harmonic analysis, Julia]
typora-root-url: ..
---
> **Abstract**/**Snippet**. FFT convolution, as kind of realization for fast convolution, using the multiplication of frequency spectra, with FFT, and inverse FFT, provide a faster speed and same results compared with the standard one for filter with kernels longer than about 64 points.[^1] This article is mainly about its implementation in `Julia`. 

**Contents**

* toc
{:toc}
## **1. Commonly Used Fast Convolution Algorithm**

​	Fast convolution as FFT convolution uses the overlap-add method together with the fast Fourier transform, allowing signals to be convolved by multiplying their frequency spectra, which can be summarized as following[^2]
$$
\begin{equation}
\begin{split}
\mathrm{E}*\mathrm{k}=\mathscr{F}^{-1}\{\mathscr{F}\{\mathrm{E}\}\times\mathscr{F}\{\mathrm{k}\}\},
\end{split}
\end{equation}
$$
​	Take the a 2-D input, a image, as an example, the image is converted into the frequency domain (using FFT), multiply, and convert back to the time domain (using the IFFT).
​	Complexity analysis of this algorithm reveals that it
exhibits $O(n\log n)$ asymptotic behaviour, which out-perform the direct summation method as following (which takes $O(n^2)$), note that here, both `E` and `k` have $n$ samples.
$$
\begin{equation}
\begin{split}
(\mathrm{E}*\mathrm{k})(x,y)=\sum_{i=0}^{m-1}\sum_{j=0}^{n-1}\mathrm{E}(x+i,y+j)\mathrm{k}(i,j),
\end{split}
\end{equation}
$$
​	Note that the $O(n\log n)$ is the easiest to implement, and can gain a comparably good performance for 1D array (which is noted as `Vector{Float64}` in `Julia`), as `fast_conv1` in the following source code.

```julia
# simplest implementation for the 1D fast convolution
function fast_conv1(E::Vector{Float64}, k::Vector{Float64})::Vector{Float64}
    N_E_ = length(E)
    N_k_ = length(k)
    nfft_ = nextpow(2, N_E_ + N_k_ - 1) # take the number of points for Fourier transform
    E_padding_ = ComplexF64.([E; zeros(nfft_ - N_E_)]) # padding the corresponding length of vector to the nfft_
    k_padding_ = ComplexF64.([k; zeros(nfft_ - N_k_)])
    E_fft = radix_2_fft(E_padding_)
    k_fft = radix_2_fft(k_padding_)

    FC_fft = E_fft .* k_fft # inner product in frequency domain
    FC = real(radix_2_ifft(FC_fft)) # take the real part of inverse FFT
    FC[1:N_E_ + N_k_ - 1] # get back to the formal length
end
```

​	To test its performance, notice that for the modulated signals, their convolution can be seen as another signal and indicates their correlation. The convolution will skyrocket in the region where they are similar, and of course, you have to reverse them, which is the opposite of the correlation coefficient. Based on this, design the following two signals, get their 1D convolution and sketch them, as shown below.

```julia
using BenchmarkTools, ProfileSVG, Gadfly
# @profview fast_conv([1 3 4 6], [2 4 7 9])
n1 = rand(250) .+ 5 .* sin.((0.01:0.2:50) .* cos.(0.01 .* (0.01:0.2:50)))
n2 = rand(250) .+ 5 .* sin.((0.01:0.2:50) .* 0.1)
fast_conv1(n1, n2)
set_default_plot_size(25cm, 20cm)
Gadfly.with_theme(:default) do
    w_o_p = n1
    ori_1 = plot(x=1:length(w_o_p), color=1:length(w_o_p), y=w_o_p, Geom.point, Geom.bar, # Geom.line,
        alpha = [0.5],
        Theme(bar_spacing=-0.2mm, key_position=:none),
        style(line_width=.5mm, line_style=[:solid]), size=[2.5pt],
        Guide.xlabel("time"), Guide.ylabel("original signal 1"),
        # Guide.title(""),
        # Scale.color_continuous(colormap = palettef, minvalue=1, maxvalue=length(w_o_p)),
        Guide.xticks(ticks=1:Int64(round(length(w_o_p)/6)):length(w_o_p), label=true, orientation=:horizontal), Guide.yticks(ticks=minimum(w_o_p):(maximum(w_o_p) - minimum(w_o_p))/6:maximum(w_o_p)),
        Coord.cartesian(xmin=1, xmax=length(w_o_p), ymin=minimum(w_o_p), ymax=maximum(w_o_p)));
    w_o_p = n2
    ori_2 = plot(x=1:length(w_o_p), color=1:length(w_o_p), y=w_o_p, Geom.point, Geom.bar, # Geom.line,
        alpha = [0.5],
        Theme(bar_spacing=-0.2mm, key_position=:none),
        style(line_width=.5mm, line_style=[:solid]), size=[2.5pt],
        Guide.xlabel("time"), Guide.ylabel("original signal 2"),
        # Guide.title(""),
        # Scale.color_continuous(colormap = palettef, minvalue=1, maxvalue=length(w_o_p)),
        Guide.xticks(ticks=1:Int64(round(length(w_o_p)/6)):length(w_o_p), label=true, orientation=:horizontal), Guide.yticks(ticks=minimum(w_o_p):(maximum(w_o_p) - minimum(w_o_p))/6:maximum(w_o_p)),
        Coord.cartesian(xmin=1, xmax=length(w_o_p), ymin=minimum(w_o_p), ymax=maximum(w_o_p)));
    w_o_p = fast_conv1(n1, n2)
    result = plot(x=1:length(w_o_p), color=1:length(w_o_p), y=w_o_p, Geom.point, Geom.bar, # Geom.line,
        alpha = [0.5],
        Theme(bar_spacing=-0.2mm, key_position=:none),
        style(line_width=.5mm, line_style=[:solid]), size=[2.5pt],
        Guide.xlabel("time"), Guide.ylabel("convolution signal"),
        # Guide.title(""),
        # Scale.color_continuous(colormap = palettef, minvalue=1, maxvalue=length(w_o_p)),
        Guide.xticks(ticks=1:Int64(round(length(w_o_p)/6)):length(w_o_p), label=true, orientation=:horizontal), Guide.yticks(ticks=minimum(w_o_p):(maximum(w_o_p) - minimum(w_o_p))/6:maximum(w_o_p)),
        Coord.cartesian(xmin=1, xmax=length(w_o_p), ymin=minimum(w_o_p), ymax=maximum(w_o_p)));
    Nia_draw = hstack(ori_1, ori_2)
    Nia_draw = vstack(Nia_draw, result)
    Gfspy = SVG("C:/Users/a1020/Desktop/Nia_img_plot.svg")
    Gadfly.draw(Gfspy, Nia_draw)
end
```

​	The generated signals are as shown below, it can be seen that convolution has a certain ability to eliminate noise. The final result is a smooth curve with the characteristics of both modulated signals.

![[OPTSx84a6]_1D_Fast_Convolution_Test](/assets/images/[OPTSx84a6]_1D_Fast_Convolution_Test.svg)

## **2. Special Treatment for Small Kernel in Image/Neural Network**



multiple dispatch, efficient
memory allocations, and macros to generate multi-
dimensional code



By allocating the memory in a helper function, the algorithm also improves efficiency by reusing memory within the actual convolution function. Additionally, we use @inbounds to eliminate array bounds checking, further increasing performance speedups.
Our implementation covers a wide variety of signal types (real, complex, Boolean, irrational, unsigned integers, etc) through native Julia multiple dispatch. Furthermore, we utilize the Cartesian package to create auto generative code that can compute convolutions in any dimension. The process by the compiler is as follows:
1) The dimensionality of the inputs is identified (let’s say both inputs are of dimension k)
2) The k-dimensional convolution code is generated (if it has never been before)
3) The inputs are processed by the auto-generated code.

`using Base.Cartesian`



```julia
# direct version (do not check if threshold is satisfied)
@generated function fast_conv_n(E::Array{T,N}, k::Array{T,N}) where {T,N}
    quote
        retsize = [size(E)...] + [size(k)...] .- 1
        retsize = tuple(retsize...)
        ret = zeros(T, retsize)
        conv_n!(ret,E,k)
        return ret
    end
end
```



```julia
# in place helper operation to speedup memory allocations
@generated function conv_n!(out::Array{T}, E::Array{T,N}, k::Array{T,N}) where {T,N}
    quote
        @inbounds begin
            @nloops $N x E begin
                @nloops $N i k begin
                    (@nref $N out d->(x_d + i_d - 1)) += (@nref $N E x) * (@nref $N k i)
                end
            end
        end
        return out
    end
end
```







```julia
# @profview fast_conv([1 3 4 6], [2 4 7 9])
@time a1 = fast_conv1(repeat(n1, 1000), repeat(n2, 1000)) # 0.405179 seconds (30 allocations: 79.816 MiB, 3.49% gc time)
@time an = fast_conv_n(repeat(n1, 1000), repeat(n2, 1000)) # 34.178557 seconds (12 allocations: 7.630 MiB)
@time fast_conv_n([1 3 4 6; 2 42 12 90], [2 4 7 9; 2 42 12 90]) # 0.000020 seconds (10 allocations: 1.094 KiB)
@time fast_conv_n(rand(1000, 1000), rand(10, 10)) # 0.126845 seconds (12 allocations: 15.398 MiB)
println(a1 ≈ an) # true
```





#### **<span id="jump01">Addendum 1st </span>——**

​	write something...

> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx84a6]**

[^1]: Smith, Steven. *Digital signal processing: a practical guide for engineers and scientists*. Elsevier, 2013.
[^2]: https://ccrma.stanford.edu/~jos/ReviewFourier/Example_FFT_Convolution.html
[^3]: Amini, Alexander, Berthold Horn, and Alan Edelman. "Accelerated convolutions for efficient multi-scale time to contact computation in Julia." *arXiv preprint arXiv:1612.08825* (2016).
[^4]: Amini, Alexander, et al. “FastConv.jl: Accelerated Convolutions for the Julia language.” Github.

