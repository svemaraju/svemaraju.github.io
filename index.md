---
layout: default
---
<ul>
    {% for post in site.posts %}
        <li>
          <span style="color:red">[{{post.categories}}] </span> <a href="{{ post.url }}"> {{ post.title }}</a> 
        </li>
    {% endfor %}
</ul>