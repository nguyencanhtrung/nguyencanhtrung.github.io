---
layout: post
title: Sample - A post with diagrams
date: 2022-09-09
description: an example of a blog post with diagrams
categories: sample-posts
---

This theme supports generating various diagrams from a text description using [jekyll-diagrams](https://github.com/zhustec/jekyll-diagrams){:target="\_blank"} plugin.
Below, we generate a few examples of such diagrams using languages such as [mermaid](https://mermaid-js.github.io/mermaid/){:target="\_blank"}, [plantuml](https://plantuml.com/){:target="\_blank"}, [vega-lite](https://vega.github.io/vega-lite/){:target="\_blank"}, etc.

**Note:** different diagram-generation packages require external dependencies to be installed on your machine.
Also, be mindful of that because of diagram generation the fist time you build your Jekyll website after adding new diagrams will be SLOW.
For any other details, please refer to [jekyll-diagrams](https://github.com/zhustec/jekyll-diagrams){:target="\_blank"} README.


## Mermaid

Install mermaid using `node.js` package manager `npm` by running the following command:

First, changing `npm` default behavior to install global packages in the home directory. This will prevent issues of execution permissions later.

```bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH
source ~/.profile
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.profile
source ~/.profile
```
<a href="https://stackoverflow.com/questions/59088731/error-failed-to-download-chromium-r686378-set-puppeteer-skip-chromium-downloa"> Source: Stackoverflow </a>

Then, install `mermaid.cli`


```bash
npm install -g mermaid.cli
```

The diagram below was generated by the following code:

{% raw %}
```
{% mermaid %}
sequenceDiagram
    participant John
    participant Alice
    Alice->>John: Hello John, how are you?
    John-->>Alice: Great!
{% endmermaid %}
```
{% endraw %}

{% mermaid %}
sequenceDiagram
    participant John
    participant Alice
    Alice->>John: Hello John, how are you?
    John-->>Alice: Great!
{% endmermaid %}