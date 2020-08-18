---
layout: page
title: Postss
---
<ul>
  {% for post in site.posts %}
    <li style='line-spacing: 1.5'>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
