---
layout: optics_post
title:  "Theoretical Simulation of Digital Micromirror Device"
author: Li Jinzhao
categories: [(Integrated) Optics]
image: ....jpg
tags: [sticky]
typora-root-url: ..
---
> **Abstract**/**Snippet**. Digital micromirror device (DMD) is kind of microelectromechanical product, which employs numerous micromirrors as actuating components to switch small portions of light on and off[^1]. As device that has been widely used in digital light processing (DLP) and spatial light modulation technology, its statistical analysis with related theory for the wavefront shaping are presented and simulated in this art.

**Contents**

* toc
{:toc}
## **1. Basic Theoretical Treatments**

​	DMD has millions of switchable mirrors, each can be switched by $-12\deg$ (off state) or $+12\deg$ (on state) with respect to their main diagonal. These micromirrors structures (with metal coating, hence highly reflective with ) are located on a complementary metal-oxide-semiconductor (CMOS) substrate, with multiple layers providing the mechanical actuation for switching and he electric signal manipulation, as shown below[^2].

![[OPTSx0052]_DMD_Micromirror_Structure](/assets/images/[OPTSx0052]_DMD_Micromirror_Structure.svg)

​	Note that, a single DMD pixel here is composed of a mirror layer, a yoke, a hinge layer and a CMOS layer, these pixels can be addressed individually through the electric signal. Cooperated with modern fast acquisition and data allocation techniques (ex. FPGA), millions of DMD pixels (as the DMD-based wavefront shaping system) provide real-time control with numerous freedoms for wavefront tailoring.

### **1.1. Single DMD Pixel,  the Geometric Tracing**

​	The on-off-state of single DMD pixel is a topic which deserved serious consideration. There is of course no contribution from the off-state micromirror, and a reflected function from the on-state micromirror. To describe the reflected function in a formal way, model of a DMD pixel for the transmittance analysis of on-state micromirror is built[^3] as below.

![[OPTSx0052]_Schematic_Diagrams_of_Transmittance_Analysis_of_On-State](/assets/images/[OPTSx0052]_Schematic_Diagrams_of_Transmittance_Analysis_of_On-State.svg)

​	Denote $\varphi'$ as the angle of tilt for the micromirror, $a$ as the side length of an individual mirror, $b=\sqrt2a$, then the transmittance of a micromirror is the superposition of two rectangular functions, in addition of a phase shift, as figure $(1,2)$ shows
$$
\begin{equation}
\begin{split}
\mathfrak{t}_0(x',y')&\approx\mathfrak{f}_1(x',y')\mathfrak{f}_2(x',y')\exp\bigg[\frac{\mathrm{i}2\pi y'}{\lambda}\bigg(\frac{\sin\beta}{\cos\varphi}-\tan\varphi\bigg)\bigg],\\
\mathfrak{f}_1(x',y')&=\mathrm{rect}\bigg(\frac{x'}{b}-\frac{y'}{b\cos\varphi}\bigg),\\
\mathfrak{f}_2(x',y')&=\mathrm{rect}\bigg(\frac{x'}{b}+\frac{y'}{b\cos\varphi}\bigg),
\end{split}
\end{equation}
$$




> <span id="jump0">**[0.0]**</span> Noodle Security Number - **[OPTSx0052]**

[^1]: Ren, Yu‐Xuan, Rong‐De Lu, and Lei Gong. "Tailoring light with a digital micromirror device." *Annalen der physik* 527.7-8 (2015): 447-470.
[^2]: Hornbeck, Larry J. "Digital light processing for high-brightness high-resolution applications." *Projection Displays III*. Vol. 3013. International Society for Optics and Photonics, 1997.
[^3]: Ding, Xiang-Yu, et al. "Microscopic lithography with pixelate diffraction of a digital micro-mirror device for micro-lens fabrication." *Applied optics* 53.24 (2014): 5307-5311.



