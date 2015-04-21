---
layout: page
title: frankneff.ch
tagline: My blog about software development();
---
{% include JB/setup %}

<div class="col-md-12">
    <h2>Recent Posts</h2>
    {% for post in site.posts %}
    <div class="row">
        <div class="col-md-12">
            <h3>
                <a href="{{ BASE_PATH }}{{ post.url }}">
                    {{ post.title }}&nbsp;<small>{{ post.date | date_to_string }}</small>
                </a>
            </h3>
        </div>
    </div>
    {% endfor %}
</div>


