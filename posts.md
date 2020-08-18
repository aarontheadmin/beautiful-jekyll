---
layout: page
title: Posts
---
<div class="post" style='line-height: 1.5;'>
<ul style='line-height: 1.5;'>
{% for post in site.posts %}
<li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>
</div>
