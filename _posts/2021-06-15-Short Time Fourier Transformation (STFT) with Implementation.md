---
layout: optics_post
title:  "..."
author: Li Jinzhao
categories: [Jekyll]
image: ....jpg
tags: [sticky]
typora-root-url: ..
---
> **Abstract**/**Snippet**.


**Contents**

* toc
{:toc}

## **1...**



## **Realization of N-Points Periodic Hanning Window**

​	Note that the Hanning window realized here can maintain the horizontal phase property of FFT results, so it is more suitable for the application scenarios of FFT. Of course, it is an asymmetric window, and the sequence length is taken as the denominator in the generation formula (hence it includes the first zero-weighted window sample).

​	Take the `varargin` as the input, firstly, the symmetric Hanning window is realized by the equation as below
$$
\begin{equation}
\begin{split}
\left\{
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