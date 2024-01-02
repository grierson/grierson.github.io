---
layout: default
---

<ul>
{% for category in site.categories %}
  <li>
    <h2>{{ category | first | capitalize }}</h2>
    <ul>
    {% for post in category.last %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
    </ul>
  </li>
{% endfor %}
</ul>
