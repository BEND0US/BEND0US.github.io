---
layout: default
title: "Web Security"
permalink: /web-security/
---

# Web Security

Articles about web vulnerabilities, attack techniques and mitigations.

<ul class="post-list">
{% for post in site.posts %}
  {% if post.categories contains "websecurity" %}
    <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> <span class="post-meta">• {{ post.date | date: "%b %d, %Y" }}</span></li>
  {% endif %}
{% endfor %}
</ul>
