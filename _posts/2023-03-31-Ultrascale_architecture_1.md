---
layout: distill
title: Ultrascale Architecture - part 1
description: CLB resources
date: 2023-03-31
tags: docker
categories: en

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
  - name: 1. Workflow với docker
  - name: 2. Các trạng thái của một container
  - name: 3. Docker commands
    subsections:
    - name: a. std logic vector
    - name: b. signed and unsigned 
    - name: c. What can and cannot be done with std_logic?

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
## CLB layout

### What are differences of Ultrascale CLB?

* Removed slice boundaries: In the 7 series architecture, 1 CLB includes 2 slices which are independent. But in the new architecture, this boundary is removed with a new mux as shown in the following picture. Then, it allows wider functions.
* Carry chain is expaned from 4 bits to 8 bits per CLB.
* All flip-flop outputs are always available in the Ultrascale architecture for enhanced packing and routing.
    * Have 8 FFs granularity 

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/01.blogs/230331_ultrascale/1.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


The comparison between 7 series and Ultrascale architecture is shown here:

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/01.blogs/230331_ultrascale/2.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

One CLB has 16 FFs why said 8 FFs granularity?
