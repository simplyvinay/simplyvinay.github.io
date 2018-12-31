---
title: Categories
permalink: /categories/
include_nav: true
---

<!-- https://github.com/mmistakes -->
{% assign categories_max = 0 %}
{% for category in site.categories %}
  {% if category[1].size > categories_max %}
    {% assign categories_max = category[1].size %}
  {% endif %}
{% endfor %}

<a id="page-title"></a>
<ul class="taxonomy__index">
  {% for i in (1..categories_max) reversed %}
    {% for category in site.categories %}
      {% if category[1].size == i %}
        <li>
            <a href="#{{category[0]|slugize}}">
          
            <strong>{{ category[0] }}</strong> <span class="taxonomy__count">{{ i }}</span>
          </a>
        </li>
      {% endif %}
    {% endfor %}
  {% endfor %}
</ul>

<div id="archives">
{% assign sortedCategories = site.categories | sort %}
{% for category in sortedCategories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %} 
    <div id="#{{ category_name | slugize }}"></div>
    <p></p>
    
    <a name="{{ category_name | slugize }}"></a>
    <h4 class="category-head">{{ category_name }}</h4>
    <ul>
      {% for post in site.categories[category_name] %}
      <li>
        <div class="archives">
            <h3 class="title"><a href="{{ post.url }}">{{post.title}}</a></h3>
            <div class="meta">
                {% if site.comments.enabled and site.comments.show_count == true %}
                {% include post/comments_link.html %}
                {% endif %}
            </div>
        </div>
      </li>
      {% endfor %}
    </ul>
    <div style="border-bottom: 1px solid;">
        <a href="#page-title" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
    </div>
  </div>
{% endfor %}
</div>