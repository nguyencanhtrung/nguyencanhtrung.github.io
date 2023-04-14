---
layout: distill
title: Python - Beginner - Day 1st Variables
description: 100 Days of code - The complete Python Pro Bootcamp
date: 2022-10-19
categories: python

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
  - name: Overview
  - name: Installation
  - name: My Hello World with cocotb
  - name: Code Blocks
  - name: Layouts
  - name: Other Typography?

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

## Overview
Learning

[Brand Name Generator](https://replit.com/@appbrewery/band-name-generator-end)

[Brand Name Generator](https://replit.com/@appbrewery/day-1-printing-start#main.py)

***

## Installation

The current stable version 1.7.1 of cocotb requires:
* Python 3.6+
* GNU Make 3+
* A Verilog or VHDL simulator, depending on your RTL source code


### Python installation

```bash
sudo apt-get install make python3 python3-pip
```

We can use the global python installation for `cocotb`. However, the better way is to create a separated environment for `cocotb` to prevent any corruption on main python installation.

Here, we create our own python env inside the `cocotb` working directory.

```bash
cd $PATH_TO_YOUR_COCOTB_WS
python3 -m venv venv
```

It creates the `venv` directory. Now, check the current environment

```bash
which python3
```

You will notice that it still uses the global environment `usr/bin/python3`

Lets activate the working environment

```bash
source venv/bin/active
```

Now, check the current environment

```bash
which python3
```

New environment for cocotb is activated.


```bash
cd $PATH_TO_YOUR_COCOTB_WS
python3 -m venv venv
sudo apt-get install make python3 python3-pip
```



```bash
pip install pytest cocotb cocotb-bus cocotb-coverage
```

You may need to add to `PATH` and add this line into your `.bashrc` file

```bash
export PATH=/home/tesla/.local/bin:$PATH
```

Checking whether it is successfull or not by typing `cocotb-config`


### Installation simulator Icarus

```bash
sudo apt install iverilog
```


***

## My Hello World with cocotb



***

## Code Blocks



***

## Layouts



