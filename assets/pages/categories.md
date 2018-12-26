---
title: Categories
permalink: /categories/
include_nav: true
---


<div id="archives">
{% assign sortedCategories = site.categories | sort %}
{% for category in sortedCategories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %} 
    <div id="#{{ category_name | slugize }}"></div>
    <p></p>
    
    <h4 class="category-head">{{ category_name }}</h4>
    <a name="{{ category_name | slugize }}"></a>
    
      {% for post in site.categories[category_name] %}
      <div class="archives">
          <h3 class="title"><a href="{{ post.url }}">{{post.title}}</a></h3>
          <div class="meta">
              {% if site.comments.enabled and site.comments.show_count == true %}
              {% include post/comments_link.html %}
              {% endif %}
          </div>
      </div>
      {% endfor %}
  </div>
{% endfor %}
</div>