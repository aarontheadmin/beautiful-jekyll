---
layout: page
title: Postss
---
<div class="post" style='line-height: 200%;'>
    <ul style='line-height: 200%;'>
        {% for post in site.posts %}
            {% unless post.next %}
                <h3>{{ post.date | date: '%Y %b' }}</h3>
	    {% else %}
	      {% capture year %}{{ post.date | date: '%Y %b' }}{% endcapture %}
              {% capture nyear %}{{ post.next.date | date: '%Y %b' }}{% endcapture %}
              {% if year != nyear %}
              <h3>{{ post.date | date: '%Y %b' }}</h3>
            {% endif %}
       {% endunless %}
       <li><a href="{{ post.url }}">{{ post.title }}</a></li>
       {% endfor %}
    </ul>
</div>
