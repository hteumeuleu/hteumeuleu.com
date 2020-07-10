---
title: Email and Web Development Blog
permalink: /blog/
---
<p class="intro">Here are all the articles in english I wrote from 2016 to today. Those marked with a star are my personal favorite. I hope you will enjoy them too.</p>
{% assign latest-posts = site.posts | sort:"date" | reverse  %}
{% assign current-year = -1  %}
{% for post in latest-posts %}
    {% assign this-post-year = post.date | date: '%Y' %}
    {% if current-year != this-post-year %}
        {% if current-year != -1 %}
</ul>
        {% endif %}
        {% assign current-year = this-post-year  %}
<h2>{{ this-post-year }}</h2>
<ul class="posts-list">
    {% endif %}
<li class="posts-list-item{% if post.favorite == true %} posts-list-item--favorite{% endif %}"><a href="{{ post.url }}">{{ post.title | markdownify | remove: '<p>' | remove: '</p>' | strip }}</a><time datetime="{{ post.date | date_to_xmlschema}}">{{ post.date | date: '%B %d, %Y' }}</time></li>
{% endfor %}
</ul>