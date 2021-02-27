---
layout: default
---
<ul>
    {% for post in site.posts %}
        <li>
           <a href="{{ post.url }}"> <{{post.display_date}}>: {{ post.title }}</a>
        </li>
    {% endfor %}
</ul>
