---
title: Creating a page to organize tags
categories:
  - Dev
tags:
  - Jekyll
---

I'm an organized person, so I want my blogs to be organized as well. I want to organize the posts according to their tags. Unfortunately, it's not that convenient to create that kind of a page. After searching the internet for a while, I found some solutions, here is the one I'm using now.

Add a `tag.mkd` file and put the following code inside.

```html
{% raw %}<div id='tag_cloud'>
{% for tag in site.tags %}
<a href="#{{ tag[0] }}" title="{{ tag[0] }}" rel="{{ tag[1].size }}">{{ tag[0] }}</a>
{% endfor %}
</div>
 
<ul id='tag_list'>
{% for tag in site.tags %}
  <li class='tag_item' id="{{ tag[0] }}">
    <span class='tag_name'>{{ tag[0] }}</span>
    <span>
      <ul>
      {% for post in tag[1] %}
        <li class='tag_post'><a href="{{ post.url }}" title="{{ post.title }}">{{ post.date|date_to_long_string }}&nbsp;&nbsp;&nbsp;&nbsp;{{ post.title }}</a></li>
      {% endfor %}
      </ul>
    </span>
  </li>
{% endfor %}
</ul>
 
<script src="/assets/js/jquery-2.0.3.min.js" type="text/javascript" charset="utf-8"></script> 
<script src="/assets/js/jquery.tagcloud.js" type="text/javascript" charset="utf-8"></script> 
<script language="javascript">
$.fn.tagcloud.defaults = {
    size: {start: 0.9, end: 2, unit: 'em'},
      color: {start: '#e77471', end: '#f62817'}
};
 
$(function () {
    $('#tag_cloud a').tagcloud();
});
</script>{% endraw %}
```

And this suffice to do the trick. However, I still have some bugs to fix and the fond and color of the text is not elegant at all. So still got a lot of things to do. You can fork this project in github and see the raw code, or if you have any suggestion and ideas, feel free to email me or @ me in weibo.
