---
layout: distill
title: 5G LDPC - Base Matrices
description: Describing 5G LDPC base matrix and how to construct it
date: 2022-10-06
tags: 5g
categories: algorithm vi

authors:
  - name: Nguyen Canh Trung
    url: "https://bobibo.one"
    affiliations:
      name: bobibo.one

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: 1. Overview
  - name: 2. Base matrix (B) and parity check matrix (H)
  - name: 3. Base matrix structure
  - name: 4. How to construct 5G LDPC base matrices
  - name: 5. Precomputed base matrices
  - name: 6. References

# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }

---

## 1. Overview

Ma trận cơ sở B là một trong những thông tin cần phải được hiểu rõ trong khi tìm hiểu giải thuật mã hóa và giải mã LDPC. Ma trận này khi được triển khai trên FPGA sẽ được lưu dưới dạng ROM, hay nói cách khác chúng sẽ được tính toán trước (pre-computed) thay vì tính toán on-fly. Phần dưới đây sẽ mô tả các đặc tính của một ma trận cơ sở cũng như cách chúng được xây dựng.

## 2. Base matrix (B) and parity check matrix (H)

<b>Ma trận cơ sở B</b> là một ma trận có kích thước như sau:
* Base graph 1: 46 hàng x 68 cột
* Base graph 2: 42 hàng x 52 cột

<b>Ma trận H</b> được tạo thành từ việc thay thế các phần tử của ma trận cơ sở B, bằng các ma trận có kích thước Zc x Zc. Cụ thể, các phần tử của ma trận cơ sở sẽ có một trong các giá trị sau và tương ứng là ma trận kích thước ZcxZc


| <b>Giá trị</b>|   <b>Ý nghĩa</b>				|
|:-------------:|:-------------:				|
| -1         	| Ignore <=> Ma trận 0			|
| 0      		| Ma trận I (Identity matrix)	|
| $$ Zc > a > 0 $$| Ma trận I được shift phải $$ a $$ lần|

<br />
Hình dưới đây là một ví dụ. Với expansion factor = 5 hay Zc = 5 (Một số tài liệu họ gọi là lifting factor hay có thể ký hiệu là Z). Chi tiết về Zc có thể xem tại <a href="">đây</a>.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/01.blogs/221006_ldpc_basematrix/1.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

## 3. Base matrix structure

Ma trận cơ sở có kích thước khác nhau ứng với mỗi loại base graph. Tuy nhiên, chúng đều có chung một cấu trúc như sau:

$$
 B =  
\left[\begin{array}{cc} 
A  & E  & O \\
C_1 & C_2 & I
\end{array}\right]
$$ 


Trong đó, kích thước của từng ma trận sẽ là: 


| <b>Ma trận</b>|<b>Base graph 1</b>|<b>Base graph 2</b>|
|:-------------:|:-----------------:|:-----------------:|
| A          	| 4 x 22 			| 4 x 10 			|
| E     		| 4 x 4 			| 4 x 4 			|
| $$ C_1 $$		| 4 x 42 			| 4 x 38 			|
| $$ C_2 $$		| 42 x 22 			| 38 x 10 			|
| O 			| 42 x 4 			| 38 x 10 			|
| I 			| 42 x 42 			| 38 x 38 			|

Ma trận $$A, E$$ được sử dụng để tính 4 parity bit đầu tiên (Ký hiệu: $$p_a$$).

Ma trận $$C_1, C_2$$ được sử dụng cùng với 4 parity bit đầu tiên {$$p_{a_1}, p_{a_2}, p_{a_3}, p_{a_4}$$} để tính các parity bit còn lại (Ký hiệu: $$p_c$$).

Ma trận $$ O, I $$ có thể bỏ qua trong quá trình tính toán.

Để dễ hiểu hơn, ta có thể nhìn vào hình sau:

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/01.blogs/221006_ldpc_basematrix/4.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

Ma trận $$E$$ là một ma trận đặc biệt có tên gọi "Ma trận đường chéo đôi" (Double diagonal matrix). Đặc tính của ma trận này, giúp việc tính toán 4 parity bits ($$p_a$$) trở lên đơn giản và hiệu quả hơn. Cụ thể, quá trình tính toán sẽ được mô tả ở bài viết [5G LDPC encoder](www.bobibo.one).

## 4. How to construct 5G LDPC base matrices
Ma trận cơ sở B sẽ được xây dựng dựa trên ba tham số { <b>`NBG`, $$ i_{LS} $$, $$ Z_{c} $$</b> }

Từ tham số `NBG` và $$ i_{LS} $$, ta sử dụng bảng 5.3.2-2 và 5.3.2-3 trong 3GPP-38.212 (Section 5.3.2) để xây dựng lên base matrix cho giá trị $$ Z_c $$ lớn nhất.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/01.blogs/221006_ldpc_basematrix/2.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

<b>Ví dụ</b> 

NBG = 2 (Base graph 2), $$ i_{LS} = 6 $$. Tra theo bảng 5.3.2-3

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/01.blogs/221006_ldpc_basematrix/3.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

Ta sẽ có base matrix của $$ Z_c = 208 $$ sẽ là:

$$
 B =  
\left[\begin{array}{cc} 
143 & 19 & 176 & 165 & -1 & -1  & 196 & -1 & -1 & 13  & 0  & 0 & -1 & -1 & -1 & ... \\
18  & -1 & -1  & 27  &  3 & 102 & 185 & 17 & 14 & 180 & -1 & 0 & 0  & -1 & -1 & ... \\
...
\end{array}\right]
$$ 

Với các trường hợp còn lại của $$ Z_c = 13, 26, 52, 104 $$ ứng với $$ i_{LS} = 6 $$. Giá trị của mỗi element sẽ được tính theo công thức:

$$ 
b = a \bmod Zc 
$$

Trong đó:
* a là giá trị của element trong ma trận B ứng với $$ Z_c $$ lớn nhất
* b là giá trị của element của ma trận B ứng với $$ Z_c $$ cần tính

Khi $$ Z_c = 13 $$ thì ma trận B sẽ trở thành:


$$
 B =  
\left[\begin{array}{cc} 
0  & 6 & 7 & 9 & -1 & -1  & 1 & -1 & -1 & 0  & 0  & 0 & -1 & -1 & -1 & ... \\
5 & -1 & -1  & 1  &  3 & 11 & 3 & 4 & 1 & 11 & -1 & 0 & 0  & -1 & -1 & ... \\
...
\end{array}\right]
$$ 


<b>Kết luận</b>
* Có 51 giá trị $$Z_c$$, tương ứng sẽ có 51 ma trận cơ sở B với mỗi Base Graph. Do đó, 5G LDPC sẽ có tổng cộng 102 ma trận cơ sở. Việc thiết kế FPGA sẽ cần phải tối ưu việc lưu trữ các ma trận này để đảm bảo tiết kiệm tài nguyên nhớ và không bị "nghẽn cổ chai" khi thực thi việc mã hóa và giải mã.

## 5. Precomputed base matrices

102 ma trận cơ sở của 5G LDPC có thể xem tại [đây](www.bobibo.one) 


## 6. References
<a href="https://panel.castle.cloud/view_spec/38212-f11/">3GPP - 38.212 - section 5.3.2</a>