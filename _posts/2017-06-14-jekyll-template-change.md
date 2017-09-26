---
title: Change to the al-folio theme
categories:
  - Dev
tags:
  - Jekyll
---

This [Jekyll theme](https://github.com/alshedivat/al-folio) is developed by [Maruan Al-Shedivat](https://www.cs.cmu.edu/~mshediva/), which is modified from the original template [-folio](https://github.com/bogoli/-folio) developed by [Lia Bogoev](http://www.liabogoev.com). Thanks to those two for this clean, and simplistic design.

## code highlighter
Switch from python-based highlighter `highlighter` to ruby-based highlighter `rouge`.

### Install Rouge

```shell
gem install kramdown rouge
```
Make sure that the version of `kramdown` is at least **1.5.0**.

### Use Rouge
In the `_config.yml`, change from `highlighter: pygments` to `highlighter: rouge`,

```yaml
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

My Jekyll template use the `_syntax_highlighting.scss` located in `_scss` for highlighting. This file contains the default style sheet for syntax highlighter. If you website don't have this file, then find out which file is responsible for the syntax highlighting, and then copy the content of `syntex.css` into this file. Otherwise, you can do the following:

1. Select a Pygments theme, for instance `monokai.css`;
2. Copy the css file of theme `monokai.css` to `blog_root_dir/[assets]/css/` directory;
3. Open `blog_root_dir/[assets]/css/main.css` and add the selected theme:
```
@import url(monokai.css);
```
One more step: all the Pygments themes use `.codehilite` as the default class. Thus we need to replace `.codehilite` with `.highlight` in the theme css file.

There is a project called [base16](https://github.com/chriskempson/base16) for color schemes. On the [preview website](http://chriskempson.github.io/base16/) you can see how they look like. The themes as they are packaged cannot be directly consumed by rouge. Fortunately though, there is a git repo with [base16 styles for pygments](https://github.com/idleberg/base16-pygments/tree/master/css).

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

```plaintext
.DS_Store
.sass-cache/
```
If those have already been staged, then first execute these two commands

```shell
find . -name .DS_Store -print0 | xargs -0 git rm -f --ignore-unmatch # remove .DS_Store
git rm -r --cache .sass-cache/ # remove .sass-cache
```