---
layout: default
title: "Home"
---

# Latest Posts

<ul class="post-list">
{% for post in site.posts limit:12 %}
  <li>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <span class="post-meta">• {{ post.date | date: "%b %d, %Y" }}</span>
    <div class="excerpt">{{ post.excerpt | strip_html | truncate: 140 }}</div>
  </li>
{% endfor %}
</ul>
