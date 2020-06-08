---
title: HTeuMeuLeu.com — HTML Email and Web Developer — Rémi Parmentier
permalink: /
---
<ul class="posts-list">
{% assign latest-posts = site.posts | sort:"date" | reverse  %}
{% for post in latest-posts %}
<li class="posts-list-item"><a href="{{ post.url }}">{{ post.title | markdownify | remove: '<p>' | remove: '</p>' | strip }}</a><time datetime="{{ post.date | date_to_xmlschema}}">{{ post.date | date: '%B %d, %Y' }}</time></li>
{% endfor %}
</ul>