---
layout: page
title: How-to Guides
permalink: /guides/
---

Practical, step-by-step instructions designed to help you accomplish specific tasks with minimal cognitive burden.

<div class="collection-grid">
  {% for guide in site.guides %}
    <div class="collection-item">
      <h3><a href="{{ guide.url | relative_url }}">{{ guide.title }}</a></h3>
      {% if guide.cognitive_load %}
      <div class="item-meta">Cognitive Load: {{ guide.cognitive_load | capitalize }}</div>
      <div class="meter-bar">
        <div class="meter-fill {{ guide.cognitive_load }}"></div>
      </div>
      {% endif %}
      <p>{{ guide.excerpt | strip_html | truncate: 120 }}</p>
      <a href="{{ guide.url | relative_url }}" class="btn">Read More</a>
    </div>
  {% endfor %}
</div>
