---
layout: optics_post
title:  "Tittle-Tattle on Matrix Algebra"
author: Li Jinzhao
categories: [Algebra]
image: ....jpg
tags: [Algebra, Analysis]
typora-root-url: ..
---
> **Abstract**/**Snippet**.

**Contents**

* toc
{:toc}
## **1...**

\frac{\mathrm{d}}{\mathrm{d}t}

\frac{\mathrm{d}}{\mathrm{d}\mathbf{X}}

​	Some basic law for matrix derivation are as following shows, the determination of a matrix is defined by the Laplace expansion $|\mathbf{A}|=\det[\mathbf{A}]=\sum_{i=1}^n(-1)^{i+j}\mathrm{A}_{ij}|\mathbf{A}^{{(i,j)}}|,i\in[1,n]$.

$$
\begin{equation}
\begin{split}
\left\{
\begin{split}
&\frac{\partial}{\partial t}(\mathbf{A}^{-1})=-\mathbf{A}^{-1}(\frac{\partial}{\partial t}\mathbf{A})\mathbf{A}^{-1},\\
&\frac{\partial}{\partial t}\mathbf{A}^*=\bigg(\frac{\partial}{\partial t}\mathbf{A}\bigg)^*,\\
&\frac{\partial}{\partial t}|\mathbf{A}|=|\mathbf{A}|\mathrm{tr}\bigg(\mathbf{A}^{-1}\frac{\partial}{\partial t}\mathbf{A}\bigg),\\
&\frac{\partial}{\partial t}\log|\mathbf{A}|=\mathrm{tr}\bigg(\mathbf{A}^{-1}\frac{\partial}{\partial t}\mathbf{A}\bigg),\\
&\frac{\partial}{\partial t}(\mathbf{A}\otimes \mathbf{B})=\bigg(\frac{\partial}{\partial t}\mathbf{A}\bigg)\otimes \mathbf{B}+\mathbf{A}\otimes\bigg(\frac{\partial}{\partial t}\mathbf{B}\bigg),\\
&\frac{\partial}{\partial t}(\mathbf{A}\circ \mathbf{B})=\bigg(\frac{\partial}{\partial t}\mathbf{A}\bigg)\circ \mathbf{B}+\mathbf{A}\circ\bigg(\frac{\partial}{\partial t}\mathbf{B}\bigg),
\end{split}
\right.
\end{split}
\end{equation}
$$

> ​	The Kronecker product $\otimes(\cdot,\cdot)$ satisfies $(\mathbf{A}\otimes \mathbf{B})^{-1}=\mathbf{A}^{-1}\otimes \mathbf{B}^{-1}$, for $\mathbf{A}$ and $\mathbf{B}$ are square, there also exists that (the first one is for the the eigenvectors and eigenvalues of $\mathbf{A}\otimes \mathbf{B}$), it is also worth noting that the trace of a matrix is $\mathrm{tr}(\mathbf{A})=\sum_i\mathrm{A}_{ii}$.

$$
\begin{equation}
\left\{
\begin{split}
&\begin{split}
\mathbf{A}\otimes \mathbf{B}&=V_\mathbf{A}\Lambda_\mathbf{A}V^{-1}_\mathbf{A}\otimes V_\mathbf{B}\Lambda_\mathbf{B}V^{-1}_\mathbf{B},\\
&=(V_\mathbf{A}\otimes V_\mathbf{B})(\Lambda_\mathbf{A}\otimes \Lambda_\mathbf{B})(V_\mathbf{A}\otimes V_\mathbf{B})^{-1},
\end{split}\\
&\mathrm{rank}(\mathbf{A}\otimes \mathbf{B})=\mathrm{rank}(\mathbf{A})\mathrm{rank}(\mathbf{B}),\\
&\mathrm{tr}(\mathbf{A}\otimes \mathbf{B})=\mathrm{tr}(\mathbf{A})\mathrm{tr}(\mathbf{B})=\mathrm{tr}(\Lambda_\mathbf{A}\otimes \Lambda_\mathbf{B}),\\
&|\mathbf{A}\otimes \mathbf{B}|=|\mathbf{A}|^{\mathrm{rank}(\mathbf{B})}|\mathbf{B}|^{\mathrm{rank}(\mathbf{A})},
\end{split}
\right.
\end{equation}
$$

There are totally six canonical forms of the matrix differential as shown below.

- Scalar with respect to scalar. ${\mathrm{d}y(x)}=a\mathrm{d}x$
- Vector with respect to scalar. ${\mathrm{d}\mathbf{y}(x)}=\mathbf{a}\mathrm{d}x$
- Matrix with respect to scalar. ${\mathrm{d}\mathbf{Y}(x)}=\mathbf{A}\mathrm{d}x$
- Scalar with respect to vector. ${\mathrm{d}y(\mathbf{x})}=\mathbf{a}\mathrm{d}\mathbf{x}$
- Vector with respect to vector. ${\mathrm{d}\mathbf{y}(\mathbf{x})}=\mathbf{A}\mathrm{d}\mathbf{x}$
- Scalar with respect to matrix. ${\mathrm{d}y(\mathbf{X})}=\mathrm{tr}(\mathbf{J}_{\mathbf{X}\to y}\mathrm{d}\mathbf{X})=(\mathbf{J}_{\mathbf{X}\to y}+\mathbf{J}_{\mathbf{X}\to y}^\mathrm{T})-(\mathbf{J}_{\mathbf{X}\to y}\circ\mathbf{I})$, this can be determined by the partial derivation, and denoted as the Jacobian $\mathbf{J}_{\mathbf{X}\to y}$

$$
\begin{equation}
\begin{split}
\mathbf{J}_{\mathbf{X}\to y}=\frac{\partial y}{\partial\mathbf{X}}=
\begin{bmatrix}
{\partial y}/{\partial\mathrm{X}_{11}}&\cdots&{\partial y}/{\partial\mathrm{X}_{1n}}\\
\vdots&\ddots&\vdots\\
{\partial y}/{\partial\mathrm{X}_{m1}}&\cdots&{\partial y}/{\partial\mathrm{X}_{mn}}
\end{bmatrix},
\end{split}
\end{equation}
$$

​	Massage matrix to canonical form will further lead to the following expressions (by the common relationship of $\mathrm{tr}(\mathbf{A}\mathbf{B})=\mathrm{tr}(\mathbf{B}\mathbf{A})$, and the symmetric matrix $\mathbf{\Sigma}$).
$$
\begin{equation}
\left\{
\begin{split}
&\frac{\partial}{\partial\mathbf{x}}(\mathbf{x}^\mathrm{T}\mathbf{A}\mathbf{x})=\mathbf{x}^\mathrm{T}\mathbf{A}+\mathbf{x}^\mathrm{T}\mathbf{A}^\mathrm{T},\\
&\frac{\partial}{\partial\mathbf{X}}(\mathbf{X}^\mathrm{T}\mathbf{A}\mathbf{X})=\mathbf{X}^\mathrm{T}\mathbf{A}+\mathbf{X}^\mathrm{T}\mathbf{A}^\mathrm{T},\\
&\frac{\partial}{\partial\mathbf{X}}\mathrm{tr}(\mathbf{A}\mathbf{X}\mathbf{B})=\mathbf{B}\mathbf{A},\\
&\frac{\partial}{\partial\mathbf{X}}\mathrm{tr}(\mathbf{A}\mathbf{X}^{-1}\mathbf{B})=-\mathbf{X}^{-1}\mathbf{B}\mathbf{A}\mathbf{X}^{-1},\\
&\frac{\partial}{\partial\mathbf{X}}\mathrm{tr}(\mathbf{A}\mathbf{X}^\mathrm{T}\mathbf{B}\mathbf{X}\mathbf{C})=\mathbf{C}\mathbf{A}\mathbf{X}^\mathrm{T}\mathbf{B}+\mathbf{A}^\mathrm{T}\mathbf{C}^\mathrm{T}\mathbf{X}^\mathrm{T}\mathbf{B}^\mathrm{T},\\
&\frac{\partial}{\partial\mathbf{X}}\mathrm{tr}(\mathbf{A}(\mathbf{X}\mathbf{\Sigma} \mathbf{X}^\mathrm{T})^{-1}\mathbf{B})=-\mathbf{\Sigma} \mathbf{X}^\mathrm{T}(\mathbf{X}\mathbf{\Sigma} \mathbf{X}^\mathrm{T})^{-1} (\mathbf{B}\mathbf{A}-\mathbf{A}^\mathrm{T}\mathbf{B}^\mathrm{T})(\mathbf{X}\mathbf{\Sigma} \mathbf{X}^\mathrm{T})^{-1},\\
&\frac{\partial}{\partial\mathbf{X}}\mathrm{tr}(\mathbf{A}\mathbf{X}^{-1}\mathbf{B})=-\mathbf{X}^{-1}\mathbf{B}\mathbf{A}\mathbf{X}^{-1},\\
&\frac{\partial}{\partial\mathbf{X}}|\mathbf{X}|=|\mathbf{X}|\mathbf{X}^{-1},\\
&\frac{\partial}{\partial\mathbf{X}}|\mathbf{X}^\mathrm{T}\mathbf{X}|=2|\mathbf{X}^\mathrm{T}\mathbf{X}|(\mathbf{X}^\mathrm{T}\mathbf{X})^{-1}\mathbf{X}^\mathrm{T},\\
&\frac{\partial}{\partial\mathbf{X}}f(\mathbf{X}\mathbf{z})=\mathbf{z}\frac{\partial}{\partial\mathbf{X}}f(\mathbf{x})|_{\mathbf{x}=\mathbf{X}\mathbf{z}},
\end{split}
\right.
\end{equation}
$$






$\mathrm{vec}(\cdot)$ operator
$$
\mathrm{vec}\bigg(\begin{bmatrix}a_{11}&a_{12}\\a_{21}&a_{22}\end{bmatrix}\bigg)=\begin{bmatrix}a_{11}\\a_{12}\\a_{21}\\a_{22}\end{bmatrix}
$$











#### **<span id="jump01">Addendum 1st </span>——**

​	write something...

> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSxXXXX]**

[^1]: Old and New Matrix Algebra Useful for Statistics
[^2]:
[^3]: