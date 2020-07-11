---
title: HTeuMeuLeu.com — HTML Email and Web Developer — Rémi Parmentier
permalink: /
---
<div class="post">

Hi! My name is <b>Rémi Parmentier</b>, also known as <b>HTeuMeuLeu</b>.<br /> I'm an email and web developer.

Here are the last three articles I wrote.

<ul class="posts-list">
{% assign latest-posts = site.posts | sort:"date" | reverse  %}
{% for post in latest-posts limit:3 %}
<li class="posts-list-item{% if post.favorite == true %} posts-list-item--favorite{% endif %}"><a href="{{ post.url }}">{{ post.title | markdownify | remove: '<p>' | remove: '</p>' | strip }}</a><time datetime="{{ post.date | date_to_xmlschema}}">{{ post.date | date: '%B %d, %Y' }}</time></li>
{% endfor %}
</ul>
<p>And here are some of my favorite articles I think you should read.</p>
<ul class="posts-list">
{% assign favorite-posts = site.posts | where: "favorite", "true" | sort:"date" | reverse | sample:3  %}
{% for post in favorite-posts %}
<li class="posts-list-item posts-list-item--favorite"><a href="{{ post.url }}">{{ post.title | markdownify | remove: '<p>' | remove: '</p>' | strip }}</a><time datetime="{{ post.date | date_to_xmlschema}}">{{ post.date | date: '%B %d, %Y' }}</time></li>
{% endfor %}
</ul>
</div>