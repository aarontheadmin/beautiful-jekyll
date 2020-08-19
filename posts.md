---
layout: page
title: Posts
---
{% for post in site.posts %}
<div>
  <a href="{{ post.url }}">{{ post.title }}</a>
  <br /><span class="post-meta" style="color: #7f7f7f"><em>{{ post.date | date: "%B %-d, %Y" }}</em></span>
</div>
<div style='line-height: 1.5;'>&nbsp;</div>
{% endfor %}
