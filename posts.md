---
layout: page
title: Posts
---
{% for post in site.posts %}
<div>
  <span class="post-title"><a href="{{ post.url }}">{{ post.title }}</a></span>
  <br />
  <span style='color: #808080; font-size: 0.75em'><em>{{ post.date | date_to_long_string }}</em></span>
</div>
<div style='line-height: 1.5;'>&nbsp;</div>
{% endfor %}
