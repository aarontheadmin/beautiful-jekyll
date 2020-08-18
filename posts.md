---
layout: page
title: Postss
---
{% for post in site.posts %}
<div style='line-height: 2.0;'>
  <a href="{{ post.url }}">{{ post.title }}</a><br/>
  {{ post.date | date_to_long_string }}
</div>
{% endfor %}
