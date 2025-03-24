---
layout: page
title: Reviews
permalink: /reviews/
---

Technology reviews from a cognitive accessibility perspective.

<div class="collection-grid">
  {% for review in site.reviews %}
    <div class="collection-item">
      <h3><a href="{{ review.url | relative_url }}">{{ review.title }}</a></h3>
      {% if review.cognitive_impact %}
      <div class="item-meta">Cognitive Impact: {{ review.cognitive_impact | capitalize }}</div>
      <div class="meter-bar">
        <div class="meter-fill {{ review.cognitive_impact }}"></div>
      </div>
      {% endif %}
      <p>{{ review.excerpt | strip_html | truncate: 120 }}</p>
      <a href="{{ review.url | relative_url }}" class="btn">Read More</a>
    </div>
  {% endfor %}
</div>
