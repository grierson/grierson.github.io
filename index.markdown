---
layout: default
---

Some of my software engineering notes

{% for category in site.categories %}

<h2>{{ category | first | capitalize }}</h2>
  <ul>
  {% assign sorted = category.last | reverse %}
  {% for post in sorted %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
  </ul>

{% endfor %}
