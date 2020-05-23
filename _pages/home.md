---
permalink: /
---
<ul>
{% for post in site.posts %}
<li><a href="{{ post.url }}">{{ post.title | markdownify | remove: '<p>' | remove: '</p>' | strip }}</a>, {{ post.date | date: '%B %d, %Y' }}</li>
{% endfor %}
</ul>