---
layout: page
title: tutorials
permalink: /tutorials/
---

{% for tutorial in site.tutorials %}

{% if tutorial.redirect %}
<div class="project">
    <div class="thumbnail">
        <a href="{{ tutorial.redirect }}" target="_blank">
        {% if tutorial.img %}
        <img class="thumbnail" src="{{ tutorial.img | prepend: site.baseurl | prepend: site.url }}"/>
        {% else %}
        <div class="thumbnail blankbox"></div>
        {% endif %}    
        <span>
            <h1>{{ tutorial.title }}</h1>
            <br/>
            <p>{{ tutorial.description }}</p>
        </span>
        </a>
    </div>
</div>
{% else %}

<div class="project ">
    <div class="thumbnail">
        <a href="{{ tutorial.url | prepend: site.baseurl | prepend: site.url }}">
        {% if tutorial.img %}
        <img class="thumbnail" src="{{ tutorial.img | prepend: site.baseurl | prepend: site.url }}"/>
        {% else %}
        <div class="thumbnail blankbox"></div>
        {% endif %}    
        <span>
            <h1>{{ tutorial.title }}</h1>
            <br/>
            <p>{{ tutorial.description }}</p>
        </span>
        </a>
    </div>
</div>

{% endif %}

{% endfor %}
