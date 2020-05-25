---
permalink: /
---
<ul>
{% assign latest-posts = site.posts | sort:"date" | reverse  %}
{% for post in latest-posts %}
<li><a href="{{ post.url }}">{{ post.title | markdownify | remove: '<p>' | remove: '</p>' | strip }}</a>, {{ post.date | date: '%B %d, %Y' }}</li>
{% endfor %}
</ul>