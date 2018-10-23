---
layout: post
title:  "Welcome to xyanyue Blog!"
date:   2018-10-18 17:28:23 +0800
<!-- categories: jekyll update -->
---
<ul>
    {% for post in site.posts %}
    <li>
        <a href="{{post.url}}">{{post.title}}</a>
    </li>
    {% endfor %}
</ul>