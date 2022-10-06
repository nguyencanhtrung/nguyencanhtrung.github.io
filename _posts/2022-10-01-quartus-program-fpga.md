---
layout: distill
title: Quartus - Program FPGA
description: Describe how to program FPGA with Quartus
date: 2022-10-01
tags: introduction
categories: intel

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
  - name: Architecture
  - name: GUI approach
  - name: Script approach

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

## Architecture



## GUI approach


***

## Catapult flow (GUI flow)

### 1. Adding source code
### 2. Platform setting
### 3. Libraries
### 4. Clock and reset setting
### 5. Architectural setting

Just wrap the text you would like to show up in a footnote in a `<d-footnote>` tag.
The number of the footnote will be automatically generated.<d-footnote>This will become a hoverable footnote.</d-footnote>



## Hot reset

Resets in PCI express are a bit complex. There are two main types of resets - conventional reset, and function-level reset. There are also two types of conventional resets, fundamental resets and non-fundamental resets. See the PCI express specification for all of the details.

A 'cold reset' is a fundamental reset that takes place after power is applied to a PCIe device. There appears to be no standard way of triggering a cold reset, save for turning the system off and back on again. On my machines, the `/sys/bus/pci/slots` directory is empty.

A 'warm reset' is a fundamental reset that is triggered without disconnecting power from the device. There appears to be no standard way of triggering a warm reset.

A 'hot reset' is a conventional reset that is triggered across a PCI express link. A hot reset is triggered either when a link is forced into electrical idle or by sending TS1 and TS2 ordered sets with the hot reset bit set. Software can initiate a hot reset by setting and then clearing the secondary bus reset bit in the bridge control register in the PCI configuration space of the bridge port upstream of the device.

A 'function-level reset' (FLR) is a reset that affects only a single function of a PCI express device. It must not reset the entire PCIe device. Implementing function-level resets is not required by the PCIe specification. A function-level reset is initiated by setting the initiate function-level reset bit in the function's device control register in the PCI express capability structure in the PCI configuration space.

Linux exposes the function-level reset functionality in the form of `/sys/bus/pci/devices/$dev/reset`. Writing a 1 to this file will initiate a function-level reset on the corresponding function. Note that this only affects that specific function of the device, not the whole device, and devices are not required to implement function-level resets as per the PCIe specification.

I am not aware of any 'nice' method for triggering a hot reset (there is no sysfs entry for that). However, it is possible to use setpci to do so:

<d-code block language="bash">
  #!/bin/bash

  dev=$1

  if [ -z "$dev" ]; then
      echo "Error: no device specified"
      exit 1
  fi

  if [ ! -e "/sys/bus/pci/devices/$dev" ]; then
      dev="0000:$dev"
  fi

  if [ ! -e "/sys/bus/pci/devices/$dev" ]; then
      echo "Error: device $dev not found"
      exit 1
  fi

  port=$(basename $(dirname $(readlink "/sys/bus/pci/devices/$dev")))

  if [ ! -e "/sys/bus/pci/devices/$port" ]; then
      echo "Error: device $port not found"
      exit 1
  fi

  echo "Removing $dev..."

  echo 1 > "/sys/bus/pci/devices/$dev/remove"

  echo "Performing hot reset of port $port..."

  bc=$(setpci -s $port BRIDGE_CONTROL)

  echo "Bridge control:" $bc

  setpci -s $port BRIDGE_CONTROL=$(printf "%04x" $(("0x$bc" | 0x40)))
  sleep 0.01
  setpci -s $port BRIDGE_CONTROL=$bc
  sleep 0.5

  echo "Rescanning bus..."

  echo 1 > "/sys/bus/pci/devices/$port/rescan"
</d-code>

Ensure that all attached drivers are unloaded before running this script. This script will attempt to remove the PCIe device, then command the upstream switch port to issue a hot reset, then attempt to rescan the PCIe bus. This script has also only been tested on devices with a single function, so it may need some reworking for devices with multiple functions.

References <d-footnote> <a href="https://unix.stackexchange.com/questions/73908/how-to-reset-cycle-power-to-a-pcie-device/474378#474378"> Link </a> </d-footnote>

***

## Conclusion

The main text column is referred to as the body.
It is the assumed layout of any direct descendants of the `d-article` element.

<div class="fake-img l-body">
  <p>.l-body</p>
</div>

For images you want to display a little larger, try `.l-page`:

<div class="fake-img l-page">
  <p>.l-page</p>
</div>

All of these have an outset variant if you want to poke out from the body text a little bit.
For instance:

<div class="fake-img l-body-outset">
  <p>.l-body-outset</p>
</div>

<div class="fake-img l-page-outset">
  <p>.l-page-outset</p>
</div>

Occasionally you’ll want to use the full browser width.
For this, use `.l-screen`.
You can also inset the element a little from the edge of the browser by using the inset variant.

<div class="fake-img l-screen">
  <p>.l-screen</p>
</div>
<div class="fake-img l-screen-inset">
  <p>.l-screen-inset</p>
</div>

The final layout is for marginalia, asides, and footnotes.
It does not interrupt the normal flow of `.l-body` sized text except on mobile screen sizes.

<div class="fake-img l-gutter">
  <p>.l-gutter</p>
</div>

***

## Other Typography?

Emphasis, aka italics, with *asterisks* (`*asterisks*`) or _underscores_ (`_underscores_`).

Strong emphasis, aka bold, with **asterisks** or __underscores__.

Combined emphasis with **asterisks and _underscores_**.

Strikethrough uses two tildes. ~~Scratch this.~~

1. First ordered list item
2. Another item
⋅⋅* Unordered sub-list. 
1. Actual numbers don't matter, just that it's a number
⋅⋅1. Ordered sub-list
4. And another item.

⋅⋅⋅You can have properly indented paragraphs within list items. Notice the blank line above, and the leading spaces (at least one, but we'll use three here to also align the raw Markdown).

⋅⋅⋅To have a line break without a paragraph, you will need to use two trailing spaces.⋅⋅
⋅⋅⋅Note that this line is separate, but within the same paragraph.⋅⋅
⋅⋅⋅(This is contrary to the typical GFM line break behaviour, where trailing spaces are not required.)

* Unordered list can use asterisks
- Or minuses
+ Or pluses

[I'm an inline-style link](https://www.google.com)

[I'm an inline-style link with title](https://www.google.com "Google's Homepage")

[I'm a reference-style link][Arbitrary case-insensitive reference text]

[I'm a relative reference to a repository file](../blob/master/LICENSE)

[You can use numbers for reference-style link definitions][1]

Or leave it empty and use the [link text itself].

URLs and URLs in angle brackets will automatically get turned into links. 
http://www.example.com or <http://www.example.com> and sometimes 
example.com (but not on Github, for example).

Some text to show that the reference links can follow later.

[arbitrary case-insensitive reference text]: https://www.mozilla.org
[1]: http://slashdot.org
[link text itself]: http://www.reddit.com

Here's our logo (hover to see the title text):

Inline-style: 
![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1")

Reference-style: 
![alt text][logo]

[logo]: https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 2"

Inline `code` has `back-ticks around` it.

```javascript
var s = "JavaScript syntax highlighting";
alert(s);
```
 
```python
s = "Python syntax highlighting"
print s
```
 
```
No language indicated, so no syntax highlighting. 
But let's throw in a <b>tag</b>.
```

Colons can be used to align columns.

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

There must be at least 3 dashes separating each header cell.
The outer pipes (|) are optional, and you don't need to make the 
raw Markdown line up prettily. You can also use inline Markdown.

Markdown | Less | Pretty
--- | --- | ---
*Still* | `renders` | **nicely**
1 | 2 | 3

> Blockquotes are very handy in email to emulate reply text.
> This line is part of the same quote.

Quote break.

> This is a very long line that will still be quoted properly when it wraps. Oh boy let's keep writing to make sure this is long enough to actually wrap for everyone. Oh, you can *put* **Markdown** into a blockquote. 


Here's a line for us to start with.

This line is separated from the one above by two newlines, so it will be a *separate paragraph*.

This line is also a separate paragraph, but...
This line is only separated by a single newline, so it's a separate line in the *same paragraph*.
