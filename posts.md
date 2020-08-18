---
layout: page
title: Posts
---
{% for post in site.posts %}
<div>
  <a href="{{ post.url }}"><span class="post-title">{{ post.title }}</span></a>
  <br />
  <span class="post-meta"><em>{{ post.date | date_to_long_string }}</em></span>
</div>
<div style='line-height: 1.5;'>&nbsp;</div>
{% endfor %}
