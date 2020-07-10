---
title: HTeuMeuLeu.com — HTML Email and Web Developer — Rémi Parmentier
permalink: /
---
<div class="intro">
    Hi! My name is Rémi Parmentier, also known as HTeuMeuLeu. I'm an HTML email and Web developer. And this is my personal homepage.
</div>
<ul class="posts-list">
{% assign favorite-posts = site.posts | where: "favorite", "true" | sort:"date" | reverse  %}
{% for post in favorite-posts %}
<li class="posts-list-item posts-list-item--favorite"><a href="{{ post.url }}">{{ post.title | markdownify | remove: '<p>' | remove: '</p>' | strip }}</a><time datetime="{{ post.date | date_to_xmlschema}}">{{ post.date | date: '%B %d, %Y' }}</time></li>
{% endfor %}
</ul>