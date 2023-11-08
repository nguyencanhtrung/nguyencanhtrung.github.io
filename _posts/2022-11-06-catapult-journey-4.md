---
layout: distill
title: Catapult Journey - Untimed C++ - lab 4
description: Working with memory
date: 2022-11-06
tags: catapult
categories: vi

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
  - name: Map C++ arrays to memories
  - name: Memory word-width
  - name: Interleave
  - name: Block size

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

## Map C++ arrays to memories

{% highlight c++ %}

#include "test.h"
#include <mc_scverify.h>

#pragma hls_design top
void CCS_BLOCK(test)(ac_channel<ac_int<10> >      &data_in, 
                     ac_int<7>                    coeffs[32][4], // array is mem interface not ac_channel
                     ac_channel<ac_int<5,false> > &coeff_addr, 
                     ac_channel<ac_int<19> >      &result)
{
  static ac_int<10> regs[4]; // shift register
  ac_int<19> acc = 0;
  ac_int<5,false> addr = coeff_addr.read();
#pragma unroll yes
  SHIFT:for (int i=3; i>=0; i--) {
    if (i==0) {
      regs[i] = data_in.read();
    } else {
      regs[i] = regs[i-1];
    }
  }

  MAC:for (int i=0; i<4; i++) {
    acc += regs[i] * coeffs[addr][i];
  }

  result.write(acc);
}

{% endhighlight %}

