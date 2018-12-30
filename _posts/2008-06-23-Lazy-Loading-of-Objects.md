---
layout: post
title: "Lazy Loading of Objects"
date: 2008-06-23
comments: true
disqus_identifier: 3
categories: [.NET]
redirect_from: "/Post/3/Lazy-Loading-of-Objects.aspx/"
---
Lazy loading of objects essentially means that the data is loaded only
when requested and not when the object instance is created.

For Eg. suppose we have a Blog class which has a blogText property. Now
this property can be encapsulated like so.
<!--more-->
{% highlight csharp %}
public string BlogText
{
    get
    {
        if (blogText == null)
            blogText = SiteProvider.Blogs.GetBlogBody(Id);
        return blogText;

    }
    set { blogText = value; }
}
{% endhighlight %}

Here the BlogText property implements the lazy load pattern which means
that the blogText field is retrieved only when it's being accessed by
another class. Hence if this property is not accessed from anywhere then
the data will not be read from the database. Once the data is retrieved
it will be held in memory for subsequent requests, hence i am checking
if the value is null, if not then only hit the database and get the
value.


