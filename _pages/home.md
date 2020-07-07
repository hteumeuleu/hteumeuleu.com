---
title: HTeuMeuLeu.com — HTML Email and Web Developer — Rémi Parmentier
permalink: /
---
<ul class="posts-list">
{% assign latest-posts = site.posts | sort:"date" | reverse  %}
{% assign current-year = -1  %}
{% for post in latest-posts %}
    {% assign this-post-year = post.date | date: '%Y' %}
    {% if current-year != this-post-year %}
        {% assign current-year = this-post-year  %}
</ul>
<h2>{{ this-post-year }}</h2>
<ul class="posts-list">
    {% endif %}
<li class="posts-list-item"><a href="{{ post.url }}">{{ post.title | markdownify | remove: '<p>' | remove: '</p>' | strip }}</a><time datetime="{{ post.date | date_to_xmlschema}}">{{ post.date | date: '%B %d, %Y' }}</time></li>
{% endfor %}
</ul>