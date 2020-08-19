---
layout: page
title: Posts
---
{% for post in site.posts %}
<div>
  <a href="{{ post.url }}">{{ post.title }}</a>
  <span class="post-meta"><em>{{ post.date | date_to_long_string }}</em></span>
</div>
<div style='line-height: 1.5;'>&nbsp;</div>
{% endfor %}
