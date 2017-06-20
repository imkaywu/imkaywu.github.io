---
title: Change to the al-folio theme
categories:
  - Dev
tags:
  - Jekyll
---

## code highlighter
Switch from python-based highlighter `highlighter` to ruby-based highlighter `rouge`.

### Install Rouge

```
gem install kramdown rouge
```
Make sure that the version of `kramdown` is at least **1.5.0**.

### Use Rouge
In the `_config.yml`, change from `highlighter: pygments` to `highlighter: rouge`,

```yml
markdown: kramdown
highlighter: rough

# Markdown Processing
kramdown:
  input: GFM
  syntax_highlighter: rouge
```
This tells Jekyll to use kramdown when parsing markdown files and to pass the two settings to kramdown whenever it's running.

### Themes
Rouge is compatible with the Pygments syntax highlighter, which means that we can use [stylesheets created for Pygments](http://richleland.github.io/pygments-css/).

My Jekyll website use the `_syntax_highlighting.scss` located in `_scss` for highlighting. This file contains the default style sheet for syntax highlighter. If you website don't have this file, then find out which file is responsible for the syntax highlighting, **or** do the following.

1. Select a Pygments theme, for instance `monokai.css`;
2. Copy the css file of theme `monokai.css` to `blog_root_dir/[assets]/css/` directory;
3. Open `blog_root_dir/[assets]/css/main.css` and add the selected theme:
```
@import url(monokai.css);
```
One more step: all the Pygments themes use `.codehilite` as the default class. Thus we need to replace `.codehilite` with `.highlight` in the theme css file.

---

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

---

## .DS_Store and .sass-cache dir
Add a `.gitignore` file with the following entries
```
.DS_Store
.sass-cache/
```
If those have already been staged, then first execute these two commands
```
find . -name .DS_Store -print0 | xargs -0 git rm -f --ignore-unmatch # remove .DS_Store
git rm -r --cache .sass-cache/ # remove .sass-cache
```