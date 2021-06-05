---
layout: optics_post
title:  "Fast Fourier Transform (FFT) with Implementation"
author: Li Jinzhao
categories: [signal processing]
image: ....jpg
tags: [time-frequency analysis]
typora-root-url: ..
---
> **Abstract**/**Snippet**. As the commonly use technic in video/audio compression, the fast Fourier transform (FFT) is a time-frequency analysis method of extreme important (also an implementation of the discrete Fourier transform, note that it is DFT instead of DTFT, which convert a periodic discrete time-domain signal into its periodic discrete frequency-domain representation). Principle and `Julia` realization of Radix-2 FFT are explained/made here. Furthermore, its fair property of making the $\Theta(N^2)$ complexity into a $\Theta(N\lg N)$ scheme for $N$ polynomial multiplication is verified.

**Contents**

* toc
{:toc}
## **1. Prerequisite: the Twiddle Factor**

> The **twiddle factor** is defined as $\omega_N^{km}=e^{-\mathrm{j}\frac{2\pi}Nkm}$[^1], and used throughout this article

​	Its following properties are essential to the acceleration of FFT

1. periodicity

   $$
   \begin{equation}
   \begin{split}
   \omega_N^{m+lN}=\omega_N^m,\text{ where }\omega_N^{-m}=\omega_N^{N-m},
   \end{split}
   \end{equation}
   $$

2. symmetry

   $$
   \begin{equation}
   \begin{split}
   \omega_N^{m+N/2}=-\omega_N^m,
   \end{split}
   \end{equation}
   $$

   this indicates that "dividing by an extra $2$ over a complex exponential has the same effect as squaring an extra complex exponential,which can be represented in the complex plane by symmetry of equipartition".

3. reducibility

   $$
   \begin{equation}
   \begin{split}
   \omega_{N/m}^k=\omega_N^{mk},
   \end{split}
   \end{equation}
   $$

4. special twiddle factor as $\omega_N^0=1$.

> These are caused by the the multiplicative group formed by Fourier basis of

$$
\begin{equation}
\begin{split}
\{e^{i2\pi mt}\}_{m\in\mathbb{Z}}=\{(e^{-i2\pi/N})^{1},(e^{-i2\pi/N})^{2},\cdots (e^{-i2\pi/N})^{N-1})\}
\end{split}
\end{equation}
$$

> which is on $L^2[\cdots]$, this is then **isomorphic to modular $n$ additive group** $(\mathbb{Z}_n,+)$, and will lead to

$$
\begin{equation}
\begin{split}
(e^{-i2\pi /N})^j(e^{-i2\pi /N})^k=(e^{-i2\pi /N})^{(j+k)\text{ mod }N}
\end{split}
\end{equation}
$$

> in addition, there exists the relation of

$$
\begin{equation}
\begin{split}
(e^{-i2\pi /dN})^{dk}&=(e^{-i2\pi /N})^{k},\\
[(e^{-i2\pi /N})^{k+N/2}]^{2}&=[(e^{-i2\pi /N})^{k}]^{2},
\end{split}
\end{equation}
$$

​	Rewrite discrete Fourier transform (DFT) as below, the direct calculation will lead to the time complexity of $\Theta(N^2)$.

$$
\begin{equation}
\begin{split}
x[n]&=\frac1N\sum_{k=0}^{N-1}X_k\cdot e^{\mathrm{j}k2\pi /Nn},\\
X_k&=\sum_{n=0}^{N-1}x[n]\cdot e^{-\mathrm{j}k2\pi /Nn},
\end{split}
\end{equation}
$$

> Original form is as (variable as subscript) below, which map discrete variable to discrete variable...

$$
\begin{equation}
\begin{split}
x_n&=\frac1N\sum_{k=0}^{N-1}X_k\cdot e^{i2\pi kn/N},\\
X_k&=\sum_{n=0}^{N-1}x_n\cdot e^{-i2\pi kn/N},
\end{split}
\end{equation}
$$

## **2. The Decimation in Time (DIT) - Radix-2 FFT Algorithm**

​	First of all, the radix-2 FFT is based on following hypothesis,

- the **sampling frequency is Nyquist frequency**.
- $N$ (the number of sample, the sequence length) is the **integer power of two.**
- the **data length is long enough** to satisfy equation as below (data resolution $\Delta f$, sequence length $N$, sampling period $T_s$, the reality length of data $N\times T_s$)

$$
\begin{equation}
\begin{split}
\Delta f=\frac1{N\times T_s},
\end{split}
\end{equation}
$$

### **2.1. Divide-and-Conquer by DIT**

​	The simplify operation provided by J. W. Cooley and J. W. Tukey (1965) is to divide the DFT equation into odd part and even part as below (this method is also called decimation in time DIT, and realized based on the **bit-reversal permutation**, see [2.2.](# 22-dit-and-bit-reversal-permutation)), it lead to two part of time sequence $\{0,2,4,...\}$ and $\{1,3,5,...\}$.

$$
\begin{equation}
\begin{split}
X_k&=\sum_{n=0}^{N-1}x_n\cdot \omega_N^{nk}\\
&=\sum_{n=0}^{N-1}x_n\cdot e^{-i2\pi kn/N}\\
&=\overbrace{\sum_{m=0}^{N/2-1}x_{2m}\cdot e^{-i2\pi km/(N/2)}}^{\text{EVEN PART}}+e^{-i2\pi k/N}
\overbrace{\sum_{m=0}^{N/2-1}x_{2m+1}\cdot e^{-i2\pi km/(N/2)}}^{\text{ODD PART}}\\
&=\sum_{m=0}^{N/2-1}x_{2m}\cdot \omega_{\frac N2}^{mk}+\omega_N^k
\sum_{m=0}^{N/2-1}x_{2m+1}\cdot \omega_{\frac N2}^{mk},
\end{split}
\end{equation}
$$

​	to get the reduction of complexity, using **the symmetry (and periodicity) of the twiddle factor,** i.e., $\omega_N^{m+N/2}=-\omega_N^m$.

$$
\begin{equation}
\begin{split}
X_k&=\sum_{m=0}^{N/2-1}x_{2m}\cdot \omega_{\frac N2}^{km}+\omega_N^{k}
\sum_{m=0}^{N/2-1}x_{2m+1}\cdot \omega_{\frac N2}^{km},\\
X_{k+\frac N2}&=\sum_{m=0}^{N/2-1}x_{2m}\cdot \omega_{\frac N2}^{km}-\omega_N^{k}
\sum_{m=0}^{N/2-1}x_{2m+1}\cdot \omega_{\frac N2}^{km},
\end{split}
\end{equation}
$$

​	the computation is hence, broken down into two subproblems of half size, odd and even (this corresponding to a change from $1, \omega_{N}^1, \omega_{N}^2, \cdots, \omega_{N}^{N-1}$ to $1, \omega_{N/2}^1, \omega_{N/2}^2, \cdots, \omega_{N/2}^{N/2-1}$), of course, $k$ satisfied that $0\leq k\leq N/2-1$.

​	Take the even part as $X^{[1]}_k$,  the odd part as $X^{[2]}_k$, hence have the operation named the **butterfly operation** (defined schematically in graph subsequently, for its realization, see [2.3.](# 23-the-iterative-implementation-of-fft))

$$
\begin{equation}
\begin{split}
&\left.
\begin{split}
&X_k=X^{[0]}_k+\omega_N^{k}X^{[1]}_k,\\
&X_{k+\frac N2}=X^{[0]}_k-\omega_N^{k}X^{[1]}_k,
\end{split}
\right\}
(0\leq k\leq N/2-1),
\end{split}
\end{equation}
$$

$$
\begin{equation}
\begin{split}
&\text{where }\left\{
\begin{split}
&X^{[0]}_k=\sum_{m=0}^{N/2-1}x_{2m}\cdot \omega_{\frac N2}^{mk},\\
&X^{[1]}_k=\sum_{m=0}^{N/2-1}x_{2m+1}\cdot \omega_{\frac N2}^{mk},
\end{split}
\right.
\end{split}
\end{equation}
$$

​	the formal operation (named butterfly, and has schematic definition) has two input value entered from left, the output are sum and difference, related to the twiddle factor $\omega_n^k$. This is simplified as dark (green) box on the right.

![[OPTSx84a2]_Butterfly_Operation](\assets\images\[OPTSx84a2]_Butterfly_Operation.svg)

​	Following the stage $\lg N$ decomposition, which **gives $2$ sequences subproblems with half size** ($N/2$), subsequently, based on this stage, there also exists the stage $\lg(N/2)=\lg N-1$ decomposition, yields (totally $4$ subproblems with $N/4$)

$$
\begin{equation}
\begin{split}
&\left.
\begin{split}
&X^{[0]}_k=X^{[2]}_k+\omega_{\frac N2}^{k}X^{[3]}_k,\\
&X^{[0]}_{k+\frac N4}=X^{[2]}_k-\omega_{\frac N2}^{k}X^{[3]}_k,\\
&X^{[1]}_k=X^{[4]}_k+\omega_{\frac N2}^{k}X^{[5]}_k,\\
&X^{[1]}_{k+\frac N4}=X^{[4]}_k-\omega_{\frac N2}^{k}X^{[5]}_k,
\end{split}
\right\}
(0\leq k\leq N/4-1),
\end{split}
\end{equation}
$$

$$
\begin{equation}
\begin{split}&\text{where }\left\{
\begin{split}
&X^{[2]}_k=\sum_{m=0}^{N/4-1}X^{[0]}_{2m}\cdot \omega_{\frac N4}^{mk},\\
&X^{[3]}_k=\sum_{m=0}^{N/4-1}X^{[0]}_{2m+1}\cdot \omega_{\frac N4}^{mk},\\
&X^{[4]}_k=\sum_{m=0}^{N/4-1}X^{[1]}_{2m}\cdot \omega_{\frac N4}^{mk},\\
&X^{[5]}_k=\sum_{m=0}^{N/4-1}X^{[1]}_{2m+1}\cdot \omega_{\frac N4}^{mk},
\end{split}
\right.
\end{split}
\end{equation}
$$

​	The final stage should have totally $2^{\lg N-1}=N/2$ subproblems with size of $N/(N/2)=2$ (the last component is $X^{[2^1+2^2+\cdots+2^{\lg N/2}-1]}_k=X^{[2(1-2^{\lg N/2})/(1-2)-1]}_k=X^{[N-3]}_k$, the first component is $X^{[N/2-2]}_k$). After this, there will lead to $N$ subproblems with $1$ size, which is undoubted the identity transform of $x[k]$ (which is $x_n$ of $x_n\to x[k]$, again, see [2.2.](# 22-dit-and-bit-reversal-permutation) for details of realize this indices transformation)

$$
\begin{equation}
\begin{split}
&\left.
\begin{split}
&X^{[N/2-2]}_k=X^{[N-2]}_k+\omega_{2}^{k}X^{[N-1]}_k,\\
&\quad\quad\quad\quad\quad\vdots\\
&X^{[N-3]}_k=X^{[2N-3]}_k+\omega_{2}^{k}X^{[2N-2]}_k,
\end{split}
\right\}
(0\leq k\leq N/2^{\lg N}-1\to k=0),
\end{split}
\end{equation}
$$

$$
\begin{equation}
\begin{split}&\text{where }\left\{
\begin{split}
&X^{[N-2]}_k=X^{[N-2]}_0=x[1],\\
&X^{[N-1]}_k=X^{[N-1]}_0=x[2],\\
&\quad\quad\quad\vdots\\
&X^{[2N-2]}_k=X^{[2N-2]}_0=x[N-1],\\
&X^{[2N-3]}_k=X^{[2N-3]}_0=x[N],
\end{split}
\right.
\end{split}
\end{equation}
$$

​	Hence, by implementing this method recursively/iteratively, the final result can be deduced, in $\Theta(n\lg n)$.

> Proof of the $\Theta(n\lg n)$ as time complexity is deduced by considering the divide-and-conquer process, which reduce a large problem continuously to a small problem of half size, the size of whole problem is $N$, after one iteration, it becomes $a=2$ subproblems with size of $N/b=N/2$, and the complexity of the decomposition/merging is denoted by $f(n)=\Theta(n)$, this yields

$$
\begin{equation}
\begin{split}
T(n)&=aT(n/b)+f(n)\\
&=2T(n/2)+\Theta(n)\\
&=2(2T(n/4)+\Theta(n))+\Theta(n)\\
&=4T(n/4)+2\Theta(n)\\
&=8T(n/4)+3\Theta(n)\\
&=2^xT(n/2^x)+x\Theta(n)\\
&\cdots\\
&=nT(1)+\lg n\times\Theta(n)\\
&=\Theta(n\lg n)\Big(=\sum_{\text{stage}=1}^{\lg n}\frac n{2^\text{stage}}\cdot2^{\text{stage-1}}
\Big),
\end{split}
\end{equation}
$$

> ​	Finally, have that $n/2^x=1$ ($n=2^x\to x=\lg n$), this subsequently yields the factor of $\lg n$.

​	To conclude, the complexity is largely reduced by the periodicity and symmetric properties of the twiddle factor $\omega_N^{km}$, and then **calculate the new results by adopting the calculated results** (i.e., $X^{[\cdots]}_k$s).

### **2.2. DIT and Bit-Reversal Permutation**

​	Actually, there are two kinds of decimation formats exists for the process of  $x_n\to x[k]$, called the decimation in time (DIT) and decimation in frequency (DIF), take $N=8$ as example, these results in different results, for DIT, have that

$$
\begin{equation}
\begin{split}
&\{&a_0,a_1,a_2,a_3,a_4,a_5,a_6,a_7&\}\\
\to&&a_0,a_4,a_2,a_6,a_1,a_5,a_3,a_7&,
\end{split}
\end{equation}
$$

![[OPTSx84a2]_DIT_Scheme](\assets\images\[OPTSx84a2]_DIT_Scheme.svg)

​	on the other side, the DIF scheme gives

$$
\begin{equation}
\begin{split}
&\{&a_0,a_1,a_2,a_3,a_4,a_5,a_6,a_7&\}\\
\to&&a_0,a_1,a_2,a_3,a_4,a_5,a_6,a_7&,
\end{split}
\end{equation}
$$

​	here, the **DIT scheme** is chosen, because in the divide-and-conquer process, the sequence is **divided by even and odd parts**. Furthermore, written the indices of sequence as binary code, which indicate that DIT format can be realized by the reversal process of original indices, i.e., $x[k]=x_{\text{rev}\{n\}}$.

$$
\begin{equation}
\begin{split}
&\{&000,001,010,011,100,101,110,111&\}\\
\to&&000,100,010,110,001,101,011,111&,
\end{split}
\end{equation}
$$

​	The bit reversal permutation process is needed here for DIT (and DIF, of course), which is implemented based on the rule shown above. For example, in $N=8$, $a_1$ permutates with $a_4$ (i.e., $1$ with $N/2$), and both $a_0$ and $a_7$ permutate with themselves (i.e., $0$ and $N-1$). Others indices should be reversed only when it is less than its reversal (the reversal is obtained by the **reverse carry operation in each cycle**, as the figure below). Finally, this bit-reversal permutation is as

```julia
# bit reverse algorithm based on reverse carry operation
function bit_reverse!(a₀::Vector{ComplexF64})
    N₀ = length(a₀) # length of vector a₀
    J₀ = Int64(N₀/2) # start from (1, N₀/2)
    for iter_index = 1:(N₀ - 2)
        if iter_index < J₀ # the swapping operation is only needed for half of N₀
            @swap(a₀[iter_index + 1], a₀[J₀ + 1]) # in Julia, index starts from 1
        end
        # determine all the reverse number 
        pow = Int64(N₀/2) # judge from the first bit, 2^lg(N₀ - 1)
        while true # the reverse carry operation in each cycle
            if J₀ < pow
                J₀ += pow # change the first corresponding bit from "0" to "1"
                break # escape the loop
            else
                J₀ -= pow # change the corresponding bit from "1" to "0"
                pow = Int64(pow/2) # move to the next bit
            end
        end
    end
end
```

![[OPTSx84a2]_Bit_Reversal_Permutation](\assets\images\[OPTSx84a2]_Bit_Reversal_Permutation.svg)

> note the macro `@swap` is correspond to the swapping operation implemented as below,
>
> ```julia
> # macro for swapping entries in ::Vector
> macro swap(x,y)
>    quote
>       local tmp = $(esc(x)) # temporary
>       $(esc(x)) = $(esc(y))
>       $(esc(y)) = tmp
>     end
> end
> ```

### **2.3. The Iterative Implementation of FFT**

​	Although the FFT top-down recursive method is simple in code level and more readable, it has high hardware implementation cost and low software efficiency, so the **bottom-up iterative method** is adopted here. Based on the bit-reversal permutation, the iterative FFT can be designed, below is a iterative process (**FFT circuit**) of $N=2^3=8$, $A_k,k=0,1,...,2^3$ is the `::Vector{ComplexF64}` going to be used in , $x_k$ and $X_k$ are the correspondingly original and resulting signals (data), $X_k^{[\cdots]}$ as intermedia variables are not appear in the code (as they have comparably complicated indices, which are useless).

![[OPTSx84a2]_Butterfly_Operation_for_N_8](\assets\images\[OPTSx84a2]_Butterfly_Operation_for_N_8.svg)

​	The total number of stages related to the iterative process is $s_\max=\lg N$, and there are $N$ operations in each stage, hence totally $N\lg N$ operations here. According to the induction hypothesis, there exists following rules

- the **indices of inputs (and hence the outputs) in stage $s$** are $(\text{any_pstn_in}, \text{any_pstn_in}+2^{s-1})$, the index distance between them is $\Delta i=2^{s-1}$.
- the **number of twiddle factors types (same as the number of types of butterfly operations) related in stage $s$** is $\Sigma=2^{s-1}$.
- the twiddle factors for stage $s$ are $\{\omega_{2^s}^{0},\omega_{2^s}^{1},...\}$, which can also be written as $\{\omega_{N}^{0},\omega_{N}^{2^{\lg N-s}},...\}$, hence the **increment of twiddle factor indices (same as number of same type of butterfly operations) in stage $s$** is noted as $\Delta\Omega=2^{\lg N-s}$.
- similarly, the **same twiddle factors occurred in stage $s$** are $(\text{any_pstn_}\omega,\text{any_pstn_}\omega+2\Delta i,...)$, the index distance between them is $2\Delta i$.

​	from which, the algorithm is designed by left to right (although the FFT circuit is constructed in direction of right to left). as the following three loop. (whole algorithm is realized as a function, which takes a vector of `ComplexF64`, and transform it into another vector of `ComplexF64`)

```julia
# radix-2 fft algorithm
function radix_2_fft(x_::Vector{ComplexF64})::Vector{ComplexF64}
    X_ = copy(x_);
    bit_reverse!(X_)
    N_ = length(X_)
    for s = 1:Int64(log2(N_)) # for each stage of butterfly operations
        Δi = Σ = 2^(s - 1) # index distance and types of operations
        ΔΩ = 2^(Int64(log2(N_)) - s) # number of same type of butterfly operation
        for any_pstn = 0:(Δi - 1) # for different butterfly operations in each stage
            index_ω = any_pstn * ΔΩ # the index of ω (twiddle factor)
            ω_ = exp(-1im * 2π * index_ω / N_) # calculate (the "same") twiddle factor
            for butterfly_index = 0:(ΔΩ - 1) # for same type of butterfly operation
                butterfly_operation!(X_, ω_, any_pstn + 2Δi*butterfly_index, Δi)
            end
        end
    end
    return X_
end
```

​	the butterfly operation is realized by a temporary variable and two operations (`plus` and `minus`), as below, note that the operation is based on the modification of corresponding indices.

```julia
# single butterfly operation
function butterfly_operation!(A₀::Vector{ComplexF64}, ω_::ComplexF64, any_pstn_in::Int64, Δi_::Int64)
    tmp = A₀[any_pstn_in + 1] # for the temporary varible
    A₀[any_pstn_in + 1] = A₀[any_pstn_in + 1] + A₀[any_pstn_in + Δi_ + 1] * ω_ # plus
    A₀[any_pstn_in + Δi_ + 1] = tmp - A₀[any_pstn_in + Δi_ + 1] * ω_ # minus
end
```

## **3. Radix-2 Inverse FFT Algorithm Based on Radix-2 FFT**

​	Based on radix-2 FFT algorithm, using the similarity between the discrete Fourier transform and the inverse discrete Fourier transform. From which, the inverse one is different from the FFT by a conjugate operation and a normalizing operation.

$$
\begin{equation}
\begin{split}
x_n=\frac1N\sum_{k=0}^{N-1}X_k\cdot (\omega_N^{nk})^*=\frac1N\bigg(\sum_{k=0}^{N-1}X_k\cdot \omega_N^{nk}\bigg)^*=\frac1N\bigg\{\mathcal{DFT}\{X_k^*\}\bigg\}^*
\end{split}
\end{equation}
$$

​	Based on the $(\omega_N^{nk})^*$ and $1/N$ (as the only two differences between FFT and IFFT), the radix-2 IFFT algorithm is implemented similarly as FFT (the way of calling, i.e., the signature, of course, is same with FFT)


```julia
# radix-2 ifft algorithm
function radix_2_ifft(x_::Vector{ComplexF64})::Vector{ComplexF64}
    X_ = copy(x_);
    bit_reverse!(X_)
    N_ = length(X_)
    for s = 1:Int64(log2(N_)) # for each stage of butterfly operations
        Δi = Σ = 2^(s - 1) # index distance and types of operations
        ΔΩ = 2^(Int64(log2(N_)) - s) # number of same type of butterfly operation
        for any_pstn = 0:(Δi - 1) # for different butterfly operations in each stage
            index_ω = any_pstn * ΔΩ # the index of ω (twiddle factor)
            ω_ = exp(1im * 2π * index_ω / N_) # calculate (the "same") conjugate twiddle factor
            for butterfly_index = 0:(ΔΩ - 1) # for same type of butterfly operation
                butterfly_operation!(X_, ω_, any_pstn + 2Δi*butterfly_index, Δi)
            end
        end
    end
    X_ /= N_
    return X_
end
```

## **4. Comparison Between FFTW Standard**

​	In `FFTW` standard, the `AbstractFFTs.fft(·)` function is used to perform the following one-dimensional discrete Fourier transform operation as defined by (note that it performs a multidimensional FFT by default. Furthermore, the FFT libraries in other languages such as `Python` and `Octave` will perform a one-dimensional FFT along the first non-singleton dimension of the array)

$$
\begin{equation}
\begin{split}
\mathcal{DFT}(A)[k] = \sum_{n=1}^{\operatorname{length}(A)} \exp\left(-{\rm i}\frac{2\pi (n-1)(k-1)}{\operatorname{length}(A)} \right) A[n].
\end{split}
\end{equation}
$$

​	Using these `fft` (and the `ifft`) functions in `FFTW` package of `Julia`, the final results of them are compared by the `≈` (approximate in `\approx`) operator in `Julia`, and the corresponding results are noted in the commands following the corresponding line as below.

```julia
using FFTW, BenchmarkTools, ProfileSVG, Gadfly
# execution time monitor: for 32 of data size
x_ori = sin.(2π.*(1:2^5)/25).*sin.(4π.*(1:2^5)/5) .+ rand(2^5)im
X_radix2fft = @btime radix_2_fft($x_ori) # 1.280 μs (1 allocation: 624 bytes)
X_fft = @btime fft($x_ori) # 5.217 μs (33 allocations: 3.03 KiB)
# results comparison: for 32 of data size
println("the complex results comparison yields (32, fft): ", X_radix2fft ≈ X_fft) # true
# execution time monitor: for 32 of data size
X_radix2ifft = @btime radix_2_ifft($X_fft) # 1.240 μs (2 allocations: 1.22 KiB)
X_ifft = @btime ifft($X_fft) # 5.720 μs (39 allocations: 3.41 KiB)
# results comparison: for 32 of data size
println("the complex results comparison yields (32, ifft): ", X_radix2ifft ≈ X_ifft) # true
# execution time monitor: for 1048576 of data size
# x_ori_1048576 = rand(2^20) .+ rand(2^20)im
# X_radix2fft_1048576 = @btime radix_2_fft($x_ori_1048576)
# X_fft_1048576 = @btime fft($x_ori_1048576)
# results comparison: for 1048576 of data size
# println("the complex results comparison yields (1048576): ", X_radix2fft_1048576 ≈ X_fft_1048576)
```

​	It is clear that in comparably large data size `1~2^10`, the self-defined functions `radix_2_ifft(·)` and `radix_2_ifft(·)` have a faster speed compared with the system function, but they are a little slower compared with the system `fft(·)` and `ifft(·)` when the size of signal is very large (as `~2^20`), the further results are compared and displayed as follows. The processing results of the original signal by the self-defined function and the system function are respectively listed in the form of line chart below. It can be seen that they will get the same result, and there is almost no difference between the two in terms of accuracy

```julia
set_default_plot_size(28cm, 7cm)
Gadfly.with_theme(:dark) do
    ori_ = plot(x=1:length(x_ori), y=abs.(x_ori), Geom.line, Geom.point,
        color=[colorant"aliceblue"], style(line_width=.5mm, line_style=[:solid]), size=[1pt],
        Guide.xlabel("time"), Guide.ylabel("original signal"));
    radix2fft_ = plot(x=1:length(x_ori), y=abs.(X_radix2fft), Geom.line, Geom.point,
        color=[colorant"darkorange"], style(line_width=.5mm, line_style=[:solid]), size=[1pt],
        Guide.xlabel("frequency"), Guide.ylabel("fft(radix-2) signal"));
    fft_ = plot(x=1:length(x_ori), y=abs.(X_fft), Geom.line, Geom.point,
        color=[colorant"orangered2"], style(line_width=.5mm, line_style=[:dash]), size=[1pt],
        Guide.xlabel("frequency"), Guide.ylabel("fft signal"));
    radix2ifft_ = plot(x=1:length(x_ori), y=abs.(X_radix2ifft), Geom.line, Geom.point,
        color=[colorant"olivedrab1"], style(line_width=.5mm, line_style=[:solid]), size=[1pt],
        Guide.xlabel("frequency"), Guide.ylabel("ifft(radix-2) signal"));
    ifft_ = plot(x=1:length(x_ori), y=abs.(X_ifft), Geom.line, Geom.point,
        color=[colorant"olivedrab"], style(line_width=.5mm, line_style=[:dash]), size=[1pt],
        Guide.xlabel("frequency"), Guide.ylabel("ifft signal"));
    hstack(ori_, radix2fft_, fft_, radix2ifft_, ifft_)
end
```

![[OPTSx84a2]_Comparison](\assets\images\[OPTSx84a2]_Comparison.svg)

​	The more performance (calling structure) is shown below in the form of volcano diagram/profile by calling for `@profview radix_2_fft(x_ori_1048576)`.

<img src="\assets\images\[OPTSx84a2]_ProfileSeen.svg" alt="[OPTSx84a2]_ProfileSeen" />

​	

![[OPTSx84a2]_Cos_Cos](\assets\images\[OPTSx84a2]_Cos_Cos.svg)


> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx84a2]**

[^1]:Cormen T H, Leiserson C E, Rivest R L, et al. Introduction to algorithms[M]. MIT press, 2009.
[^2]:https://github.com/JuliaMath/FFTW.jl, with docs in https://juliamath.github.io/FFTW.jl/stable
[^3]:http://fftw.org/fftw-3.3.9.tar.gz





