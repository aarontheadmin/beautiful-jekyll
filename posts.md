---
layout: page
title: Posts
---
<ul style='line-height: 2.0;'>
{% for post in site.posts %}
<li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>
