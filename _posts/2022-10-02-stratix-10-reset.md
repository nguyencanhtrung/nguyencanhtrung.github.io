---
layout: distill
title: Stratix 10 - Reset release IP
description: Describe how to do proper reset for user logic in Stratix 10
date: 2022-10-01
tags: introduction stratix
categories: Intel

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
  - name: 1. Introduction
  - name: 2. Proposed architecture for safe reset
  - name: 3. Register initialization during Power-Up
  - name: 4. Reset release IP instantiation
  - name: 5. Note for Partial Reconfiguration
  - name: 6. Youtube video

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

## 1. Introduction

Intel Stratix 10 and Intel Agilex fpga are configured through <b>Secure Device Manager </b> (SDM). SDM is a microprocessor block that provides a robust, secure and fully authenticated configuration scheme. The SDM sends configuration data to each sector in the FPGA where there is a microprocessor for each sector that minimally helps with the configuration of its respective sector.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/1.post_stratix10_reset/1.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/1.post_stratix10_reset/2.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

The configuration of sectors is done in a <b>pseudo-serial fashion</b>. Each sector starts its configuration <b>asynchronously</b> to each other which results in different areas of the sector entering into user mode before the entire sector has been fully configured. 

This creates a condition where the intended intitial state of the design becomes more a transitory state since a portion of the sector is operational before the entire sector configured.

Lets examine the following examples:

We have a design which has following state machine:
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/1.post_stratix10_reset/3.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/1.post_stratix10_reset/4.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

On this state machine, design depends on registers entering an initial state. Without an adequate reset, the state machine begins operating when part of the device is active, while nearby logic included in the state machine remains frozen.

Register B is in active mode and changes its state after 1 clock cycle, while the other is still in frozen state. When whole fabric enters user mode, the system enters illegal or unknown state.

Hence, the Intel reset release IP will prevent entering illegal state by holding the fpga in reset state until entire fabric enters user mode.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/1.post_stratix10_reset/7.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/1.post_stratix10_reset/5.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

The Reset Release Intel® FPGA IP is available in the Intel® Quartus® Prime Software. This IP consists of a single output signal, `nINIT_DONE`. 

The `nINIT_DONE` signal is the core version of the `INIT_DONE` pin and has the same function in both FPGA First and HPS First configuration modes. 

Intel recommends that you hold your design in reset while the `nINIT_DONE` signal is <b>high</b> or while the `INIT_DONE` pin is <b>low</b>. When you instantiate the Reset Release IP in your design, the SDM drives the nINIT_DONE signal. 

Consequently, the IP does not consume any FPGA fabric resources, but does require routing resources.

## 2. Proposed architecture for safe reset

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/1.post_stratix10_reset/8.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

People normally use the `pll_lock` signal to hold the system in reset state until PLL circuit output stable frequency. There are posibilities that duration for PLL to lock shorter than time for fabric entering user mode. 

Hence, the best approach is to get PLL reset input with an `INIT_DONE` from the reset release IP as shown in the figure.

{% highlight vhdl %}

RST_REL: component reset_release 
	port map (
		ninit_done => ninit_done; 	-- reset = HIGH
	);

SYS_PLL : component pll
	port map (
		rst      => ninit_done,     
		refclk   => refclk,   
		locked   => locked,   
		outclk_0 => clk   	
	);

sys_rst 	<= rst_in OR (NOT(locked));

{% endhighlight %}


<b>Another alternative</b> if you're using the PLL lock in your reset sequence is to gate the PLL lock output with the an `NINIT_DONE` signal from the Reset Release IP.

{% highlight vhdl %}

RST_REL: component reset_release 
	port map (
		ninit_done => ninit_done; 	-- HIGH: not done fabric reset
	);

SYS_PLL : component pll
	port map (
		rst      => rst,      
		refclk   => refclk,  
		locked   => locked,   
		outclk_0 => clk   		
	);
	
sys_rst 	<= locked AND (NOT(ninit_done));

{% endhighlight %}

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/1.post_stratix10_reset/9.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

Note: Có thể thấy logic của `sys_rst <= locked AND (NOT(ninit_done))` không hoàn toàn đúng với bảng truth table mà nó phải là`sys_rst <= ninit_done OR (NOT(locked))`. Tuy nhiên, thực tế xảy ra 2 điều sau:
<ul>
	<li>Ở giai đoạn power-up, trạng thái `locked` của PLL luôn đạt được sớm hơn so với việc toàn bộ fabric hoàn thành việc chuyển sang trạng thái người dùng</li>
	<li>Khi ở trong trạng thái operating, lúc này sys_rst chỉ phụ thuộc vào `rst_in` để reset user logic => chỉ phụ thuộc vào trạng thái của `locked`</li>
</ul>

Từ đó có thể thấy trạng thái thứ 4 của truth table sẽ không xảy ra trong thực tế, nên có thể sử dụng `sys_rst <= locked AND (NOT(ninit_done))` để điều khiển `sys_rst`

## 3. Register initialization during Power-On

Initialization during power-up is described in VHDL

{% highlight vhdl %}

signal s1 	: std_logic_vector(3 downto 0) := b"0001";

{% endhighlight %}

Or in verilog

{% highlight C++ %}

reg q = 1'b1;

{% endhighlight %}

When design on Stratix 10 or Agilex device, it is <b>recommend not</b> to rely on initial conditions of the registers as in the snippet code above. We should <b> rely on the reset network to maintain desired initial condition</b>.

To ensure having a proper functioning reset, we must disable power-up initialization, so that the simulation can verify the reset network behaviour. Opening assignments menu in the intel quartus prime pro:

`Assignment > Device > Device and Pin Options > Configuration > Disable Register Power-up initialization`


## 4. Reset release IP instantiation

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/1.post_stratix10_reset/6.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

`ninit_done = '1'` Fabric is in reseting state. Must hold all user logic in the reset state.

`ninit_done = '0'` Fabric finishes its reseting. User logic can transit into operating state.

{% highlight vhdl %}

component reset_release is
		port (
			ninit_done : out std_logic
		);
end component reset_release;

....

RST_REL: component reset_release 
	port map (
		ninit_done => ninit_done
	);

{% endhighlight %}



## 5. Note for Partial Reconfiguration
<a href="https://www.intel.com/content/www/us/en/docs/programmable/683762/21-3/guidance-when-using-partial-reconfiguration.html">References </a>

## 6. Youtube video

You can look at the following video.

[![References](https://img.youtube.com/vi/qhGfZwX9jKw/0.jpg)](https://www.youtube.com/watch?v=qhGfZwX9jKw)


***
<b>Best resource</b>

<a href="https://www.intel.com/content/www/us/en/docs/programmable/683762/21-3/including-the-reset-release-in-your-design.html"> https://www.intel.com/content/www/us/en/docs/programmable/683762/21-3/including-the-reset-release-in-your-design.html</a>