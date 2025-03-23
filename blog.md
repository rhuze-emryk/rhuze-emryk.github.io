---
layout: page
title: Blog
permalink: /blog/
---

<div class="home">
  <h1 class="page-heading">Latest Posts</h1>
  <ul class="post-list">
    {% for post in site.posts %}
      <li>
        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
        <h2>
          <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
        </h2>
        <p>
          {{ post.excerpt }}
        </p>
      </li>
    {% endfor %}
  </ul>
</div>
