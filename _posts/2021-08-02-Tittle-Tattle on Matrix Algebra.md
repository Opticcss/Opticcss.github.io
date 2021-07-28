---
layout: optics_post
title:  "Tittle-Tattle on Matrix Algebra"
author: Li Jinzhao
categories: [Algebra]
image: ....jpg
tags: [Algebra, Optimization, Sherman-Morrison Formula]
typora-root-url: ..
---
> **Abstract**/**Snippet**. This is a compilation of some frequently used formulas, theorems and related topics on the basis of matrix algebra. It can also be treated as mathematics notes that can be consulted, which contains some necessary proof ideas and application places of conclusions.

**Contents**

* toc
{:toc}
## **1. Identities for Determinants**

​	This section is mainly for identities related to determinants. There is no doubt that the content will inevitably increase as my experience increases, so each section of this section (generally representing a specific topic) is arranged in chronological order, and search tools `Ctrl+F` can be used to realize random browsing.

### **1.1. Product Rules for Determinants**

​	The **product rules for determinants**, which is given by Issai Schur as following, note that ${\color{crimson}{\mathbf{A}}\color{black}{}}$ and $\mathbf{D}$ real square matrices, and ${\color{olivedrab}{\mathbf{B}}\color{black}{}}$ and ${\color{darkcyan}{\mathbf{C}}\color{black}{}}$ are real matrix,

$$
\begin{equation}
\left\{
\begin{split}
&\left\|\begin{matrix}
{\color{crimson}{\mathbf{A}}\color{black}{}}&{\color{olivedrab}{\mathbf{B}}\color{black}{}}\\{\color{darkcyan}{\mathbf{C}}\color{black}{}}&\mathbf{D}
\end{matrix}\right\|=\|{\color{crimson}{\mathbf{A}}\color{black}{}}\|\|\mathbf{D}-\mathbf{CA}^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}}\|,\\
&\left\|\begin{matrix}
{\color{crimson}{\mathbf{A}}\color{black}{}}&{\color{olivedrab}{\mathbf{B}}\color{black}{}}\\{\color{darkcyan}{\mathbf{C}}\color{black}{}}&\mathbf{D}
\end{matrix}\right\|=\|\mathbf{D}\|\|{\color{crimson}{\mathbf{A}}\color{black}{}}-\mathbf{BD}^{-1}{\color{darkcyan}{\mathbf{C}}\color{black}{}}\|,
\end{split}
\right.
\end{equation}
$$

## **2. Matrix Identities**

​	This part is mainly for identities related to matrices. There is no doubt that the content will inevitably increase as my experience increases, so each section of this section (generally representing a specific topic) is arranged in chronological order, and search tools `Ctrl+F` can be used to realize random browsing.

### **2.1. Sherman-Morrison Formula**

​	Also called the **matrix inversion lemma**, Woodbury's identity and the modified matrices formula[^2], it is commonly used in control, estimation theory and signal processing, for the invertible real square matrices ${\color{crimson}{\mathbf{A}}\color{black}{}}$ and $\mathbf{D}$, and analogy real matrix ${\color{olivedrab}{\mathbf{B}}\color{black}{}}$ and ${\color{darkcyan}{\mathbf{C}}\color{black}{}}$, have that (its origination form and some of the equivalent expressions)

$$
\begin{equation}
\begin{split}
&({\color{crimson}{\mathbf{A}}\color{black}{}}-\mathbf{BD}^{-1}{\color{darkcyan}{\mathbf{C}}\color{black}{}})^{-1}={\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}+{\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}}(\mathbf{D}-\mathbf{CA}^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}})^{-1}{\color{darkcyan}{\mathbf{C}}\color{black}{}}{\color{crimson}{\mathbf{A}}\color{black}{}}^{-1},\\
&({\color{crimson}{\mathbf{A}}\color{black}{}}+\mathbf{BD}^{-1}{\color{darkcyan}{\mathbf{C}}\color{black}{}})^{-1}={\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}-{\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}}(\mathbf{D}+\mathbf{CA}^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}})^{-1}{\color{darkcyan}{\mathbf{C}}\color{black}{}}{\color{crimson}{\mathbf{A}}\color{black}{}}^{-1},
\end{split}
\end{equation}
$$

> to demonstrate this, define matrices $\mathbf{E}$ and $\mathbf{F}$ as $\mathbf{E}=\mathbf{D}-\mathbf{CA}^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}}$, $\mathbf{F}={\color{crimson}{\mathbf{A}}\color{black}{}}-\mathbf{BD}^{-1}{\color{darkcyan}{\mathbf{C}}\color{black}{}}$, the inverse of partitioned matrix $\begin{bmatrix}{\color{crimson}{\mathbf{A}}\color{black}{}}&{\color{olivedrab}{\mathbf{B}}\color{black}{}};\text{ }{\color{darkcyan}{\mathbf{C}}\color{black}{}}&\mathbf{D}\end{bmatrix}$leads to the equality $\mathbf{F}^{-1}={\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}+{\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}\mathbf{BE}^{-1}\mathbf{CA}^{-1}$, which yields the Sherman-Morrison formula.

​	This formula is always used to reduce the computation complexity for the calculation of the update inversion of matrix, it can be noted that, from left to right, the inversion is changed into $(\mathbf{D}+\mathbf{CA}^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}})^{-1}$ but not ${\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}$ (if ${\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}$ is already known).

### **2.2. Partial Derivation in Matrix Algebra**

​	Some basic law for matrix derivation are as following shows[^1], the determination of a matrix is defined by the Laplace expansion $\|{\color{crimson}{\mathbf{A}}\color{black}{}}\|=\det[{\color{crimson}{\mathbf{A}}\color{black}{}}]=\sum_{i=1}^n(-1)^{i+j}\mathrm{A}_{ij}\|{\color{crimson}{\mathbf{A}}\color{black}{}}^{(i,j)}\|,i\in[1,n]$.

$$
\begin{equation}
\begin{split}
\left\{
\begin{split}
&\frac{\partial}{\partial t}({\color{crimson}{\mathbf{A}}\color{black}{}}^{-1})=-{\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}(\frac{\partial}{\partial t}{\color{crimson}{\mathbf{A}}\color{black}{}}){\color{crimson}{\mathbf{A}}\color{black}{}}^{-1},\\
&\frac{\partial}{\partial t}{\color{crimson}{\mathbf{A}}\color{black}{}}^*=\bigg(\frac{\partial}{\partial t}{\color{crimson}{\mathbf{A}}\color{black}{}}\bigg)^*,\\
&\frac{\partial}{\partial t}|{\color{crimson}{\mathbf{A}}\color{black}{}}|=|{\color{crimson}{\mathbf{A}}\color{black}{}}|\mathrm{tr}\bigg({\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}\frac{\partial}{\partial t}{\color{crimson}{\mathbf{A}}\color{black}{}}\bigg),\\
&\frac{\partial}{\partial t}\log|{\color{crimson}{\mathbf{A}}\color{black}{}}|=\mathrm{tr}\bigg({\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}\frac{\partial}{\partial t}{\color{crimson}{\mathbf{A}}\color{black}{}}\bigg),\\
&\frac{\partial}{\partial t}({\color{crimson}{\mathbf{A}}\color{black}{}}\otimes {\color{olivedrab}{\mathbf{B}}\color{black}{}})=\bigg(\frac{\partial}{\partial t}{\color{crimson}{\mathbf{A}}\color{black}{}}\bigg)\otimes {\color{olivedrab}{\mathbf{B}}\color{black}{}}+{\color{crimson}{\mathbf{A}}\color{black}{}}\otimes\bigg(\frac{\partial}{\partial t}{\color{olivedrab}{\mathbf{B}}\color{black}{}}\bigg),\\
&\frac{\partial}{\partial t}({\color{crimson}{\mathbf{A}}\color{black}{}}\circ {\color{olivedrab}{\mathbf{B}}\color{black}{}})=\bigg(\frac{\partial}{\partial t}{\color{crimson}{\mathbf{A}}\color{black}{}}\bigg)\circ {\color{olivedrab}{\mathbf{B}}\color{black}{}}+{\color{crimson}{\mathbf{A}}\color{black}{}}\circ\bigg(\frac{\partial}{\partial t}{\color{olivedrab}{\mathbf{B}}\color{black}{}}\bigg),
\end{split}
\right.
\end{split}
\end{equation}
$$

> ​	The Kronecker product $\otimes(\cdot,\cdot)$ satisfies $({\color{crimson}{\mathbf{A}}\color{black}{}}\otimes {\color{olivedrab}{\mathbf{B}}\color{black}{}})^{-1}={\color{crimson}{\mathbf{A}}\color{black}{}}^{-1}\otimes {\color{olivedrab}{\mathbf{B}}\color{black}{}}^{-1}$, for ${\color{crimson}{\mathbf{A}}\color{black}{}}$ and ${\color{olivedrab}{\mathbf{B}}\color{black}{}}$ are square, there also exists that (the first one is for the the eigenvectors and eigenvalues of ${\color{crimson}{\mathbf{A}}\color{black}{}}\otimes {\color{olivedrab}{\mathbf{B}}\color{black}{}}$), it is also worth noting that the trace of a matrix is $\mathrm{tr}({\color{crimson}{\mathbf{A}}\color{black}{}})=\sum_i\mathrm{A}_{ii}$.

$$
\begin{equation}
\left\{
\begin{split}
&\begin{split}
{\color{crimson}{\mathbf{A}}\color{black}{}}\otimes {\color{olivedrab}{\mathbf{B}}\color{black}{}}&=V_{\color{crimson}{\mathbf{A}}\color{black}{}}\Lambda_{\color{crimson}{\mathbf{A}}\color{black}{}}V^{-1}_{\color{crimson}{\mathbf{A}}\color{black}{}}\otimes V_{\color{olivedrab}{\mathbf{B}}\color{black}{}}\Lambda_{\color{olivedrab}{\mathbf{B}}\color{black}{}}V^{-1}_{\color{olivedrab}{\mathbf{B}}\color{black}{}},\\
&=(V_{\color{crimson}{\mathbf{A}}\color{black}{}}\otimes V_{\color{olivedrab}{\mathbf{B}}\color{black}{}})(\Lambda_{\color{crimson}{\mathbf{A}}\color{black}{}}\otimes \Lambda_{\color{olivedrab}{\mathbf{B}}\color{black}{}})(V_{\color{crimson}{\mathbf{A}}\color{black}{}}\otimes V_{\color{olivedrab}{\mathbf{B}}\color{black}{}})^{-1},
\end{split}\\
&\mathrm{rank}({\color{crimson}{\mathbf{A}}\color{black}{}}\otimes {\color{olivedrab}{\mathbf{B}}\color{black}{}})=\mathrm{rank}({\color{crimson}{\mathbf{A}}\color{black}{}})\mathrm{rank}({\color{olivedrab}{\mathbf{B}}\color{black}{}}),\\
&\mathrm{tr}({\color{crimson}{\mathbf{A}}\color{black}{}}\otimes {\color{olivedrab}{\mathbf{B}}\color{black}{}})=\mathrm{tr}({\color{crimson}{\mathbf{A}}\color{black}{}})\mathrm{tr}({\color{olivedrab}{\mathbf{B}}\color{black}{}})=\mathrm{tr}(\Lambda_{\color{crimson}{\mathbf{A}}\color{black}{}}\otimes \Lambda_{\color{olivedrab}{\mathbf{B}}\color{black}{}}),\\
&|{\color{crimson}{\mathbf{A}}\color{black}{}}\otimes {\color{olivedrab}{\mathbf{B}}\color{black}{}}|=|{\color{crimson}{\mathbf{A}}\color{black}{}}|^{\mathrm{rank}({\color{olivedrab}{\mathbf{B}}\color{black}{}})}|{\color{olivedrab}{\mathbf{B}}\color{black}{}}|^{\mathrm{rank}({\color{crimson}{\mathbf{A}}\color{black}{}})},
\end{split}
\right.
\end{equation}
$$

There are totally six canonical forms of the matrix differential as shown below.

- Scalar with respect to scalar. ${\mathrm{d}y(x)}=a\mathrm{d}x$
- Vector with respect to scalar. ${\mathrm{d}\mathbf{y}(x)}=\mathbf{a}\mathrm{d}x$
- Matrix with respect to scalar. ${\mathrm{d}\mathbf{Y}(x)}={\color{crimson}{\mathbf{A}}\color{black}{}}\mathrm{d}x$
- Scalar with respect to vector. ${\mathrm{d}y(\mathbf{x})}=\mathbf{a}\mathrm{d}\mathbf{x}$
- Vector with respect to vector. ${\mathrm{d}\mathbf{y}(\mathbf{x})}={\color{crimson}{\mathbf{A}}\color{black}{}}\mathrm{d}\mathbf{x}$
- Scalar with respect to matrix. ${\mathrm{d}y(\mathbf{X})}=\mathrm{tr}(\mathbf{J}_{\mathbf{X}\to y}\mathrm{d}\mathbf{X})$, this can be determined by the partial derivation, and denoted as the Jacobian

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

​	Massage matrix to canonical form will further lead to the following expressions (by the common relationship of $\mathrm{tr}({\color{crimson}{\mathbf{A}}\color{black}{}}{\color{olivedrab}{\mathbf{B}}\color{black}{}})=\mathrm{tr}({\color{olivedrab}{\mathbf{B}}\color{black}{}}{\color{crimson}{\mathbf{A}}\color{black}{}})$, and the symmetric matrix $\mathbf{\Sigma}$).

$$
\begin{equation}
\left\{
\begin{split}
&\frac{\partial}{\partial\mathbf{x}}\mathrm{const}=\mathbf{0}^\mathrm{T},\\
&\frac{\partial}{\partial\mathbf{x}}\mathrm{sum}(\mathbf{x})=\mathbf{1}^\mathrm{T},\\
&\frac{\partial}{\partial\mathbf{x}}\|\mathbf{}x\|^2=2\mathbf{x}^\mathrm{T},\\
&\frac{\partial}{\partial\mathbf{x}}(\mathbf{x}^\mathrm{T}{\color{crimson}{\mathbf{A}}\color{black}{}}\mathbf{x})=\mathbf{x}^\mathrm{T}{\color{crimson}{\mathbf{A}}\color{black}{}}+\mathbf{x}^\mathrm{T}{\color{crimson}{\mathbf{A}}\color{black}{}}^\mathrm{T},\\
&\frac{\partial}{\partial\mathbf{X}}(\mathbf{X}^\mathrm{T}{\color{crimson}{\mathbf{A}}\color{black}{}}\mathbf{X})=\mathbf{X}^\mathrm{T}{\color{crimson}{\mathbf{A}}\color{black}{}}+\mathbf{X}^\mathrm{T}{\color{crimson}{\mathbf{A}}\color{black}{}}^\mathrm{T},\\
&\frac{\partial}{\partial\mathbf{X}}\mathrm{tr}({\color{crimson}{\mathbf{A}}\color{black}{}}\mathbf{X}{\color{olivedrab}{\mathbf{B}}\color{black}{}})={\color{olivedrab}{\mathbf{B}}\color{black}{}}{\color{crimson}{\mathbf{A}}\color{black}{}},\\
&\frac{\partial}{\partial\mathbf{X}}\mathrm{tr}({\color{crimson}{\mathbf{A}}\color{black}{}}\mathbf{X}^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}})=-\mathbf{X}^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}}{\color{crimson}{\mathbf{A}}\color{black}{}}\mathbf{X}^{-1},\\
&\frac{\partial}{\partial\mathbf{X}}\mathrm{tr}({\color{crimson}{\mathbf{A}}\color{black}{}}\mathbf{X}^\mathrm{T}{\color{olivedrab}{\mathbf{B}}\color{black}{}}\mathbf{X}{\color{darkcyan}{\mathbf{C}}\color{black}{}})={\color{darkcyan}{\mathbf{C}}\color{black}{}}{\color{crimson}{\mathbf{A}}\color{black}{}}\mathbf{X}^\mathrm{T}{\color{olivedrab}{\mathbf{B}}\color{black}{}}+{\color{crimson}{\mathbf{A}}\color{black}{}}^\mathrm{T}{\color{darkcyan}{\mathbf{C}}\color{black}{}}^\mathrm{T}\mathbf{X}^\mathrm{T}{\color{olivedrab}{\mathbf{B}}\color{black}{}}^\mathrm{T},\\
&\frac{\partial}{\partial\mathbf{X}}\mathrm{tr}({\color{crimson}{\mathbf{A}}\color{black}{}}(\mathbf{X}\mathbf{\Sigma} \mathbf{X}^\mathrm{T})^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}})=-\mathbf{\Sigma} \mathbf{X}^\mathrm{T}(\mathbf{X}\mathbf{\Sigma} \mathbf{X}^\mathrm{T})^{-1} ({\color{olivedrab}{\mathbf{B}}\color{black}{}}{\color{crimson}{\mathbf{A}}\color{black}{}}-{\color{crimson}{\mathbf{A}}\color{black}{}}^\mathrm{T}{\color{olivedrab}{\mathbf{B}}\color{black}{}}^\mathrm{T})(\mathbf{X}\mathbf{\Sigma} \mathbf{X}^\mathrm{T})^{-1},\\
&\frac{\partial}{\partial\mathbf{X}}\mathrm{tr}({\color{crimson}{\mathbf{A}}\color{black}{}}\mathbf{X}^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}})=-\mathbf{X}^{-1}{\color{olivedrab}{\mathbf{B}}\color{black}{}}{\color{crimson}{\mathbf{A}}\color{black}{}}\mathbf{X}^{-1},\\
&\frac{\partial}{\partial\mathbf{X}}|\mathbf{X}|=|\mathbf{X}|\mathbf{X}^{-1},\\
&\frac{\partial}{\partial\mathbf{X}}|\mathbf{X}^\mathrm{T}\mathbf{X}|=2|\mathbf{X}^\mathrm{T}\mathbf{X}|(\mathbf{X}^\mathrm{T}\mathbf{X})^{-1}\mathbf{X}^\mathrm{T},\\
&\frac{\partial}{\partial\mathbf{X}}f(\mathbf{X}{\color{olivedrab}{\mathbf{z}}\color{black}{}})={\color{olivedrab}{\mathbf{z}}\color{black}{}}\frac{\partial}{\partial\mathbf{X}}f(\mathbf{x})|_{\mathbf{x}=\mathbf{X}{\color{olivedrab}{\mathbf{z}}\color{black}{}}},\\
&\frac{\partial}{\partial\mathbf{X}}\mathrm{d}y(\mathbf{X})=(\mathbf{J}_{\mathbf{X}\to y}+\mathbf{J}_{\mathbf{X}\to y}^\mathrm{T})-(\mathbf{J}_{\mathbf{X}\to y}\circ\mathbf{I}),
\end{split}
\right.
\end{equation}
$$

## **Topics on Gaussian (Normal)**

​	For the random variable $\mathbf{X}$ which obeys Gaussian (normal) distribution, with its autocorrelation of ${\color{darkcyan}{\mathbf{C}}\color{black}{}}_\mathbf{X}$ and mean of $\bar{\mathbf{X}}$.

$$
\begin{equation}
\begin{split}
f_\mathbf{X}(\mathbf{X})&=\frac{1}{(2\pi)^{n/2}|{\color{darkcyan}{\mathbf{C}}\color{black}{}}_\mathbf{X}|^{1/2}}\exp\bigg[-\frac12(\mathbf{X}-\bar{\mathbf{X}})^\mathrm{T}{\color{darkcyan}{\mathbf{C}}\color{black}{}}_\mathbf{X}^{-1}(\mathbf{X}-\bar{\mathbf{X}})\bigg],\\
&=\mathscr{N}(\bar{\mathbf{X}},{\color{darkcyan}{\mathbf{C}}\color{black}{}}_\mathbf{X}),
\end{split}
\end{equation}
$$

​	If the invertible constant $n\times n$ matrix ${\color{crimson}{\mathbf{A}}\color{black}{}}$ and a constant $n$-element vector ${\color{olivedrab}{\mathbf{b}}\color{black}{}}$ are acted on $\mathbf{X}$, as $\mathbf{Y}={\color{crimson}{\mathbf{A}}\color{black}{}}\mathbf{X}+{\color{olivedrab}{\mathbf{b}}\color{black}{}}$, have that (which shows that the normality is preserved in linear transformations of random vectors)

$$
\begin{equation}
\begin{split}
f_\mathbf{Y}(\mathbf{Y})=\mathscr{N}({\color{crimson}{\mathbf{A}}\color{black}{}}\bar{\mathbf{X}}+{\color{olivedrab}{\mathbf{b}}\color{black}{}},{\color{crimson}{\mathbf{A}}\color{black}{}}{\color{darkcyan}{\mathbf{C}}\color{black}{}}_\mathbf{X}{\color{crimson}{\mathbf{A}}\color{black}{}}^\mathrm{T}),
\end{split}
\end{equation}
$$

​	

> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx0a2b]**

[^1]: Minka, Thomas P. "Old and new matrix algebra useful for statistics." *See www. stat. cmu. edu/minka/papers/matrix. html* 4 (2000).

[^2]: Simon, Dan. *Optimal state estimation: Kalman, H infinity, and nonlinear approaches*. John Wiley & Sons, 2006.

[^3]:

