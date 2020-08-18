---
layout: page
title: Postss
---
{% for post in site.posts %}
<div style='line-height: 2.0;'>
  <a href="{{ post.url }}">{{ post.title }}</a><br/>
  {{ post.date }}<br/>
  {% if page.tags.size > 0 %}
        <div>
          Tags:
          {% if site.link-tags %}
          {% for tag in page.tags %}
            <a href="{{ '/tags' | relative_url }}#{{- tag -}}">{{- tag -}}</a>
          {% endfor %}
          {% else %}
            {{ page.tags | join: ", " }}
          {% endif %}
        </div>
      {% endif %}
</div>
{% endfor %}
