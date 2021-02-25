---
title: kokodayo
tags: math
categories: Practice
abbrlink: 850214840
date: 2020-05-06 00:00:00
updated: 2020-05-06 00:00:00
---
第7题

求方程$x^2-56x+1=0$的两个根，使它至少具有4位有效数字（$\sqrt{783}\approx27.982$）.
$$
x_1=\frac{-(-56)+\sqrt{(-56)^2-4\times1\times1}}{2\times1}=28+\sqrt{783}
$$
$$
x_2=\frac{-(-56)-\sqrt{(-56)^2-4\times1\times1}}{2\times1}=28-\sqrt{783}
$$

$$
\Downarrow
$$

$$
x_1\approx28+27.982=55.982
$$
$$
x_2\approx28-27.982=0.018
$$

**但是我们看题目还有其他要求**

> 使它至少具有4位有效数字

过于相近的两个数相减会丢失有效数字的位数，我们只能考虑其他的办法。例如，

根据韦达定理，有$x_1+x_2=-56$和$x_1x_2=1$.$\Rightarrow$
$$
x_2=\frac{1}{x_1}=\frac{1}{28+\sqrt{783}}\\
$$
$$
x_2\approx\frac{1}{55.982}\approx0.0017863
$$
