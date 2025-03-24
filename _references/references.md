---
layout: page
title: References
permalink: /references/
---

Helpful reference information for understanding technology concepts.

<div class="collection-grid">
  {% for reference in site.references %}
    <div class="collection-item">
      <h3><a href="{{ reference.url | relative_url }}">{{ reference.title }}</a></h3>
      <p>{{ reference.excerpt | strip_html | truncate: 120 }}</p>
      <a href="{{ reference.url | relative_url }}" class="btn">Read More</a>
    </div>
  {% endfor %}
</div>
