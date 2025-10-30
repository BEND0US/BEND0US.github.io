---
layout: default
title: "Vulnerabilities"
permalink: /vulnerabilities/
---

# Vulnerabilities

<ul class="post-list">
{% for post in site.posts %}
  {% if post.categories contains "vulnerabilities" %}
    <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> <span class="post-meta">• {{ post.date | date: "%b %d, %Y" }}</span></li>
  {% endif %}
{% endfor %}
</ul>
