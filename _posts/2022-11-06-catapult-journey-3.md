---
layout: distill
title: Catapult Journey - Untimed C++ - lab 3
description: Loop handling (unrolling and pipelining)
date: 2022-11-06
tags: catapult hls
categories: catapult

authors:
  - name: Nguyen Canh Trung
    url: "https://bobibo.one"
    affiliations:
      name: bobibo.one

bibliography: 2018-12-22-distill.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: Main
  - name: Loop
  - name: Dynamic index
  - name: Catapult Gantt Chart
  - name: Catapult Desing Analyzer 
  - name: RTL co-simulation

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

## Main

Khác với cách tiếp cận ở một số tool HLS khác, Catapult coi hàm `main` là một unbounded loop (infinite loop). Do đó ta hoàn toàn có thể cấu hình để tool tổng hợp `main` ra kiến trúc pipeline. Tuy nhiên, Catapult sẽ không tự động UNROLL các LOOP trong hàm `main` như Vitis HLS, việc này được thực hiện hoàn toàn dựa vào người thiết kế.


## LOOP

Chỉ có 2 kỹ thuật được thực hiện trên LOOP bao gồm

* LOOP UNROLLING
  * Fully unroll
  * Partial unroll
* LOOP PIPELINING

Pragmas UNROLL

```bash
#pragma unroll yes

```

## Dynamic index


{% highlight c++ %}

void test_orig(int din[40], uint6 offset, int dout[40])
{
  static int regs[40];

  // Loop is fully unrolled
#pragma unroll yes
  for (int i=0; i<40; i++) {
    if (i+offset < 40) {
      regs[i + offset] = din[i];
    }
  }

  // Loop is fully unrolled
#pragma unroll yes
  for (int i=0; i<40; i++) {
    dout[i] = regs[i];
  }
}

{% endhighlight %}


Dòng code `regs[i+offset] = din[i]` sẽ được tool báo `Warning`

 ```bash
 # Warning: test_orig.cpp(11): Writing to register bank 'test_orig:regs' with 17 registers using a dynamic index can cause excessive runtime and undesired hardware.  Please inspect coding style. (MEM-74)
 ```
Lý do: `offset` là biến tham số được đưa vào từ input. `i` tuy là biến counter nhưng khi thực hiện unroll, nó sẽ trở thành `constant`

Catapult được thiết kế sẽ chia tách mảng mà được truy cập bằng dynamic index thành nhiều biến nhỏ. Do đó, với dòng code trên mảng `regs` (mapped ở dạng registers) sẽ được chia nhỏ thành nhiều biến tương ứng với các giá trị của `offset`. Bên cạnh đó, dòng code kia lai ở bên trong một unrolled loop => Mảng `regs` sẽ được chia tách rất nhiều lần tương ứng với số vòng lặp của LOOP. Điều này dẫn tới thời gian runtime sẽ bị kéo dài, area sẽ lớn.


<strong>Phương án giải quyết</strong>

{% highlight c++ %}

void test(int din[40], uint6 offset, int dout[40])
{
  static int regs[40];

#pragma unroll yes
  for (int i=0; i<40; i++) {
#pragma unroll yes
    for (int j=0; j<40; j++) {
      if ((j==offset) & (j+i<40)) {
        regs[j+i] = din[i];
      }
    }
  }

  // Loop is fully unrolled
#pragma unroll yes
  for (int i=0; i<40; i++) {
    dout[i] = regs[i];
  }
}

{% endhighlight %}

`i` và `j` đều là constant khi cả hai loop thực hiện fully unroll. Do đó, indexing của `regs` lúc này là `constant`. Giá trị của `j` sẽ được guard bởi câu lệnh điều khiện.