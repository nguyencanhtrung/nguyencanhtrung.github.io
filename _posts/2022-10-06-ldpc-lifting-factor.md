---
layout: post
title: 5G LDPC - Lifting factor Zc
date: 2022-10-06
description: Describing 5G LDPC lifting factor and how to calculate it
tags: ldpc
categories: algorithm
---

## Tổng quan


***
## Base matrix (B) and parity check matrix (H)

Ma trận cơ sở B là một ma trận có kích thước như sau:
* Base graph 1: 46 hàng x 68 cột
* Base graph 2: 42 hàng x 52 cột

Ma trận H được tạo thành từ việc thay thế các phần tử của ma trận cơ sở B, bằng các ma trận có kích thước Zc x Zc. Cụ thể, các phần tử của ma trận cơ sở sẽ có một trong các giá trị sau và tương ứng là ma trận ZcxZc


| Giá trị       | 	   |   Ý nghĩa 						|
|:--------------|------|-------------:					|
| -1         	|      | Ma trận 0						|
| 0      		|      | Ma trận I (Identity matrix)	|
| a > 0  		|      | Ma trận I được shift phải x lần|

<br />
Chú ý: Giá trị của x sẽ được tính bằng 

$$ 
x = a \bmod Zc 
$$

<br />

Hình dưới đây là một ví dụ. Với expansion factor = 5 hay Zc = 5 (Một số tài liệu họ gọi là lifting factor hay có thể ký hiệu là Z). Chi tiết về Zc có thể xem tại <a href="">đây</a>.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/01.blogs/221006_ldpc_basematrix/1.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>




***
## Base matrix structure


***
## How to construct 5G LDPC base matrix
Ma trận cơ sở B


***
## Matlab code

***
## References
<a href="https://panel.castle.cloud/view_spec/38212-f11/">3GPP - 38.212 - section 5.3.2</a>







This theme supports rendering beautiful math in inline and display modes using [MathJax 3](https://www.mathjax.org/) engine. You just need to surround your math expression with `$$`, like `$$ E = mc^2 $$`. If you leave it inside a paragraph, it will produce an inline expression, just like $$ E = mc^2 $$.

To use display mode, again surround your expression with `$$` and place it as a separate paragraph. Here is an example:

$$
\sum_{k=1}^\infty |\langle x, e_k \rangle|^2 \leq \|x\|^2
$$

You can also use `\begin{equation}...\end{equation}` instead of `$$` for display mode math.
MathJax will automatically number equations:

\begin{equation}
\label{eq:cauchy-schwarz}
\left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)
\end{equation}

and by adding `\label{...}` inside the equation environment, we can now refer to the equation using `\eqref`.

Note that MathJax 3 is [a major re-write of MathJax](https://docs.mathjax.org/en/latest/upgrading/whats-new-3.0.html) that brought a significant improvement to the loading and rendering speed, which is now [on par with KaTeX](http://www.intmath.com/cg5/katex-mathjax-comparison.php).
