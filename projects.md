---
layout: page
title: Projetos
permalink: /projects
---

<ul>
{% for p in site.data.projects %}
  <li><a href="{{ p.url }}">{{ p.name }}</a> — {{ p.description }}</li>
{% endfor %}
</ul>