---
layout: default
---
<ul>
    {% for post in site.posts %}
        <li>
           <a href="{{ post.url }}"> <{{post.display_date}}>: {{ post.title }}</a> ({{post.tags}} 
        </li>
    {% endfor %}
</ul>
