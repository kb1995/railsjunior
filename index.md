---
layout: default
---

<div>
  <h1>Hello! 👋</h1>
  <h3>Blog posts are coming soon!</h3>
</div>

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>