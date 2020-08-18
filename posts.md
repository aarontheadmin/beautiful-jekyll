---
layout: page
title: Posts
---
{% for post in site.posts %}
<div style='line-height: 2.0;'>
  <a href="{{ post.url }}">{{ post.title }}</a><br/>
  {{ post.date }}<br/>
  {{ post.tags }}
</div>
{% endfor %}
