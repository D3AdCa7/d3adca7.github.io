---
layout: page
title: D3AdCa7
tagline: Necromorph
---
{% include JB/setup %}
    
## Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## Links

Inspired by [Slipper's Blog](http://slipper.tk)


