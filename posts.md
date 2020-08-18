---
layout: page
title: Posts
---
{% for post in site.posts %}
<div style='line-height: 2.0;'>
  <a href="{{ post.url }}">{{ post.title }}</a><br/>
  <span style='color: #808080'; font-size: 0.5em'>{{ post.date | date_to_long_string }}</span>
</div>
{% endfor %}
