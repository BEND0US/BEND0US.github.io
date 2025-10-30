---
layout: default
title: "Red Team"
permalink: /red-team/
---

# Red Team

Posts focused on offensive security, red teaming techniques and research.

<ul class="post-list">
{% for post in site.posts %}
  {% if post.categories contains "redteam" %}
    <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> <span class="post-meta">• {{ post.date | date: "%b %d, %Y" }}</span></li>
  {% endif %}
{% endfor %}
</ul>
