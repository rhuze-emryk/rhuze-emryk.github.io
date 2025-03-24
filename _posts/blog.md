---
layout: page
title: Blog
permalink: /blog/
---

Thoughts, announcements, and updates from NeuroFlow Labs.

{% for post in site.posts %}
  <article class="entry">
    <h2 class="entry-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
    <div class="entry-meta">
      <time class="entry-time" datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %-d, %Y" }}</time>
    </div>
    <div class="entry-excerpt">
      {{ post.excerpt }}
      <a href="{{ post.url | relative_url }}" class="more-link">Read More</a>
    </div>
  </article>
{% endfor %}
