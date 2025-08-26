---
layout: page
title: Artigos
permalink: /articles
---

<ul>
{% for a in site.data.articles %}
  <li><a href="{{ a.url }}">{{ a.title }}</a> ({{ a.date }})</li>
{% endfor %}
</ul>