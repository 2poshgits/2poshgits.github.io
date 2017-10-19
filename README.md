---
title: "Welcome to 2 PoSH gits!"
---

# Hello

## Here are some posts

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url | remove: ".html" }}">{{ post.title }} ({{post.date | date: '%Y-%m-%d' }}) - ({{post.content | number_of_words}} words)</a>
    </li>
  {% endfor %}
</ul>
