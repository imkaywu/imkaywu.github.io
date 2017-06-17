---
title: Changes to the al-filio theme
categories:
  - Dev
tags:
  - Jekyll
---

## code highlighter
change from `highlighter` to `rouge`, add the following to `_config.yml`

```yml
highlighter: rough

# Markdown Processing
kramdown:
  input: GFM
  syntax_highlighter: rouge
```

## truncate post

See the [original post](http://briankhuu.com/blog/self/jekyll/2014/12/03/post-truncation-in-jekyll.html).

```html
{% raw %}{% if post.content contains '<!--more-->' %}
{{ post.content | split:'<!--more-->' | first }}
{% else %}
{{ post.excerpt }}
{% endif %}
<a href="{{ post.url }}">read more</a>{% endraw %}
```