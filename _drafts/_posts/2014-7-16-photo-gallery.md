---
title: Create a photo gallery for Jekyll-engined blog
categories:
  - Dev
tags:
  - Jekyll
---

There is still one piece left for my blog, and that's the photo gallery. Unfortunately, Jekyll doesn't support this feature automatically. Though there is a [plugin](https://github.com/ggreer/jekyll-gallery-generator), I don't think it's good enough and suits my need. In [this post](http://christianspecht.de/2014/03/08/generating-an-image-gallery-with-jekyll-and-lightbox2/), I found the solution I want, and the following is the details.

## Grid of Images

For simplicity, the image grids are built using only HTML and CSS, see [this post](http://alijafarian.com/responsive-image-grids-using-css/) as a reference.

### The HTML

The grid is formed using unordered list, inside each `li`, I add an image and descriptive texts.

```html
{% raw %}<li>
	<a href="{{ gallery.imagefolder }}/{{ image.name }}" data-lightbox="{{ gallery.id }}" title="{{ image.text }}">
	    <img src="{{ gallery.imagefolder }}/{{ image.name }}">
	</a><br>
	{{ image.text }}
</li>{% endraw %}
```

### The CSS

I'm no expert on CSS, so I'm just gonna list some key points from the refered [post](http://alijafarian.com/responsive-image-grids-using-css/).

#### Display or Float

Normally, "Image Grid" is achieved through floating, but here `display: inline-block` is used with `ul` set to `font-size: 0px` and `li` set to `font-size:16px` **AND** `font-size: 1rem`. The double font size declaration is for the IE (Internet Explorer doesn't recognize rem units). Here is what the CSS codes look like.

```css
ul.rig {
    list-style: none;
    font-size: 0px;
    margin-left: -2.5%; /* should match li left margin */
}
ul.rig li {
    display: inline-block;
    padding: 10px;
    margin: 0 0 2.5% 2.5%;the layout file knows that it 
    background: #fff;
    border: 1px solid #ddd;
    font-size: 16px;
    font-size: 1rem;
    font-family: "futura-pt-1","futura-pt-2",helvetica,arial,sans-serif;
    font-weight: bold;
    vertical-align: top;
    text-align: center;
    box-shadow: 0 0 5px #ddd;
    box-sizing: border-box;
    -moz-box-sizing: border-box;
    -webkit-box-sizing: border-box;
}
ul.rig li img {
    max-width: 100%;
    height: auto;
    margin: 0 0 10px;
}
```

#### Margin

The margin I chose is 2.5%, this translates into a left margin of 2.5% for each li and a negative left margin of 2.5% for the ul. Now the width for each grid is calculated as:\

> **2 Column Grid**: 100% / 2 - 2.5% = 47.5%
> 
> **3 Column Grid**: 100% / 3 - 2.5% = 30.83%
>
> **4 Column Grid**: 100% / 4 - 2.5% = 22.5%

```css
/* class for 2 columns */
ul.rig.columns-2 li {
    width: 47.5%; /* this value + 2.5 should = 50% */
}
/* class for 3 columns */
ul.rig.columns-3 li {
    width: 30.83%; /* this value + 2.5 should = 33% */
}
/* class for 4 columns */
ul.rig.columns-4 li {
    width: 22.5%; /* this value + 2.5 should = 25% */
}
```

#### Media Queries

```css
@media (max-width: 480px) {
    ul.grid-nav li {
        display: block;
        margin: 0 0 5px;
    }
    ul.grid-nav li a {
        display: block;
    }
    ul.rig {
        margin-left: 0;
    }
    ul.rig li {
        width: 100% !important; /* over-ride all li styles */
        margin: 0 0 20px;
    }
}
```

## "Image Displaying" script

The available scripts include [FancyBox](http://fancybox.net/) and [Lightbox2](http://lokeshdhakar.com/projects/lightbox2/) and I chose the latter.

### Step 1 - Load the js and CSS files

1. Look inside the js folder to find `jquery-1.11.0.min.js` and `lightbox.min.js` and load both of these files. Load jQuery first.

```js
{% raw %}<script src="js/jquery-1.11.0.min.js"></script>
<script src="js/lightbox.min.js"></script>{% endraw %}
```

2. Look inside the `css` folder to find `lightbox.css` and load it.

```css
<link href="css/lightbox.css" rel="stylesheet" />
```

3. Look inside the `img` folder to find `close.png`, `loading.gif`, `prev.png`, and `next.png`. These files are used in `lightbox.css`. By default, `lightbox.css` will look for these images in a folder called img.

### Step 2 - Turn it on

Add a `data-lightbox` attribute to any image link to activate Lightbox. For the value of the attribute, use a unique name for each image. For example:

```html
{% raw %}<a href="img/image-1.jpg" data-lightbox="image-1" data-title="My caption">Image #1</a>{% endraw %}
```

## `_data` directory and `galleries.yml` file

`galleries.yml` holds the information of the images of all galleries, including *gallery id*, *gallery description*, *image folder for each gallery*, *image names* and *image description*.

```yaml
- id: gallery1
  description: 毕业旅行之大连站 - 沿途
  imagefolder: /assets/galleries/2014-06-Dalian/travel
  images:
  - name: image 1.jpg
    text: gallery1, image 1
- id: gallery2
  description: 毕业旅行之大连站 - 发现王国
  imagefolder: /assets/galleries/2014-06-Dalian/discovery kingdom
  images:
  - name: image 1.jpg
    text: gallery2, image 1
```

## Gallery overview

This page serves as the entrance to each gallery, therefore related gallery information, such as description, thumb image, should be provided.

```html
{% raw %}<ul class = "rig columns-2">
	{% for gallery in site.data.galleries %}
	<li>
		<a href="/nav/galleries/{{ gallery.id }}" title="{{ gallery.id }}">
			<img src="{{ gallery.imagefolder }}/image 1.jpg"><br>
			<p>{{ gallery.description }}</p>
		</a>
	</li>
	{% endfor %}
</ul>{% endraw %}
```

This loops through the list of galleries, displays the related description and image, links to the subpages of each gallery using `gallery.id`. Here is the result

![gallery overview]({{ site.url }}{{ site.baseurl }}/images/gallery overview.png)

## Gallery layout

The subpage displaying the images of a certain gallery uses the same layout file.

```html
{% raw %}{% for gallery in site.data.galleries %}
  {% if gallery.id == page.galleryid %}
    <h2>{{ gallery.description }}</h2>
    <ul class = "rig columns-3">
    {% for image in gallery.images %}
      <li>
        <a href="{{ gallery.imagefolder }}/{{ image.name }}" data-lightbox="{{ gallery.id }}" title="{{ image.text }}">
            <img src="{{ gallery.imagefolder }}/{{ image.name }}">
        </a><br>
        <p>{{ image.text }}</p>
      </li>
    {% endfor %}
    </ul>
  {% endif %}
{% endfor %}{% endraw %}
```

So in order to build a new gallery page, all you have to do is create an Markdown file with only YAML front-matter:

```yaml
---
title: 毕业旅行之大连站 - 沿途
layout: gallery
galleryid: gallery1
---
```

> `layout: gallery` points to the layout file
>
> `galleryid: gallery1` refers to the `galleryid` in the `galleries.yml` file, so the layout file knows to load the images of `gallery1`.

## Add a new gallery

> Put the images into the directory `/assets/galleries/a-certain-gallery/sub-folder`, rename the image names to `image n`, `n` refers to the index of the image, then add the information of the gallery to file `/_data/galleries.yml`.  
>
> Create a new folder in the directory `nav/galleries`, the name of the folder is the `galleryid` of the new gallery. Then create an `index.md` file in the newly created folder with only YAML front-matter listed above, `galleryid: ...` is the gallery id of the new gallery.
