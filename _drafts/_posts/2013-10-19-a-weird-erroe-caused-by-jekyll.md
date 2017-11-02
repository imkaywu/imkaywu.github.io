---
title: A weird error caused by jekyll
categories:
  - Dev
tags:
  - Jekyll
---

I haven't got the chance to update my blog for a while. When I did today and tried to run it locally in my machine, I ran into an error.

![jekyll_error]({{ site.url }}{{ site.baseurl }}/images/jekyll error.png)

I googled and found the solution. Because the default format of YAML is `parameter+: +white space`, so if you happen to forget the white space, you gonna run into the same error as I did. So simple solution is adding the white space.
