---
layout: page
title: Postss
---
<div class="post" style='line-height: 1.5;'>
    <ul style='line-height: 1.5;'>
        {% for post in site.posts %}
              <h3>{{ post.date | date: '%Y %b' }}</h3>
       <li><a href="{{ post.url }}">{{ post.title }}</a></li>
       {% endfor %}
    </ul>
</div>
