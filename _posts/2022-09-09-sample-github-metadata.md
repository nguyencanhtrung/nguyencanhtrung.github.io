---
layout: post
title: Sample - A post with github metadata
date: 2022-09-09
description: a quick run down on accessing github metadata.
categories: sample-posts
---

A sample blog page that demonstrates the accessing of github meta data.

## What does Github-MetaData do?
* Propagates the site.github namespace with repository metadata
* Setting site variables :
  * site.title
  * site.description
  * site.url
  * site.baseurl
* Accessing the metadata - duh.
* Generating edittable links.

## Installation

Adding it to your `Gemfile` like this

```bash
    gem 'jekyll-github-metadata'
```

At it to your `_config.yml`
```bash
plugins:
  - jekyll-github-metadata
```

Then, go ahead and run `bundle install`

## Additional Reading
* If you're recieving incorrect/missing data, you may need to perform a Github API<a href="https://github.com/jekyll/github-metadata/blob/master/docs/authentication.md"> authentication</a>.
* Go through this <a href="https://jekyll.github.io/github-metadata/">README</a> for more details on the topic.
* <a href= "https://github.com/jekyll/github-metadata/blob/master/docs/site.github.md">This page</a> highlights all the feilds you can access with github-metadata.
<br />

## Example MetaData
* Host Name : {{ site.github.hostname }}
* URL : {{ site.github.url }}
* Archived : {{ site.github.archived}}
* Contributors :
{% for contributor in site.github.contributors %}
  * {{ contributor.login }}
{% endfor %}
