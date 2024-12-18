# al-folio

[![demo](https://img.shields.io/badge/theme-demo-brightgreen.svg)](https://alshedivat.github.io/al-folio/)
[![license](https://img.shields.io/github/license/mashape/apistatus.svg?maxAge=2592000)](https://github.com/alshedivat/al-folio/blob/master/LICENSE)

A simple and clean [Jekyll](https://jekyllrb.com/) theme for academics.

<!-- [![Screenshot](assets/img/full-screenshot.png)](https://alshedivat.github.io/al-folio/) -->

This theme is adapted from **[al-folio](https://github.com/alshedivat/al-folio)**. Originally, **al-folio** was based on the [\*folio theme](https://github.com/bogoli/-folio) (published by [Lia Bogoev](http://liabogoev.com) and under the MIT license). Since then, it got a full re-write of the styles and many additional cool features. The emphasis is on whitespace, transparency, and academic usage: [theme demo](https://alshedivat.github.io/al-folio/).


## Getting started

For more about how to use Jekyll, check out [this tutorial](https://www.taniarascia.com/make-a-static-website-with-jekyll/).
Why Jekyll? Read this [blog post](https://karpathy.github.io/2014/07/01/switching-to-jekyll/)!


## Features

#### Collections
This Jekyll theme implements collections to let you break up your work into categories.
The example is divided into news and projects, but easily revamp this into apps, short stories, courses, or whatever your creative work is.

> To do this, edit the collections in the `_config.yml` file, create a corresponding folder, and create a landing page for your collection, similar to `_pages/projects.md`.

Two different layouts are included: the blog layout, for a list of detailed descriptive list of entries, and the projects layout.
The projects layout overlays a descriptive hoverover on a background image.
If no image is provided, the square is auto-filled with the chosen theme color.
Thumbnail sizing is not necessary, as the grid crops images perfectly.

#### Theming
Six beautiful theme colors have been selected to choose from.
The default is purple, but quickly change it by editing `$theme-color` variable in the `_sass/variables.scss` file (line 72).
Other color variables are listed there, as well.

#### Photos
Photo formatting is made simple using rows of a 2-column system (1/2 width), 3-column system (1/3, 2/3, or full width), or 4-column system (1/4 width). Easily create beautiful grids within your blog posts and projects pages:

#### Code Highlighting
This theme implements Jekyll's built in code syntax highlighting with Rouge.
Just use the tags `{% raw %}``{% endraw %}` to delineate your code:


## Contributing

Feel free to contribute new features and theme improvements by sending a pull request. Style improvements and bug fixes are especially welcome.


## License

MIT
