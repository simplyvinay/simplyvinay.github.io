---
layout: post
title: "Automatic Properties and Object Initializers"
date: 2008-07-01
comments: true
disqus_identifier: 6
categories: [C#]
redirect_from: "/Post/6/Automatic-Properties-and-Object-Initializers.aspx/"
---
**Automatic Properties**

In this post we will look at some of the new features added in C# 3.0.
The first of these will be automatic properties. Automatic properties
gives us with a shorthand notation for defining a new property.
<!--more-->
{% highlight csharp %}
public class AutoProp
{
    public string AddedBy { get; set; }
    public DateTime AddedDate { get; set; }
    public int ViewCount { get; private set; }

    //Normal way of defining property
    private int id;
    public int Id
    {
        get { return id; }
        set { id = value; }

    }
}
{% endhighlight %}

Notice that the first 2 properties do not have Getters or Setters, what
is happing is that the C# compiler generates the getters and setters
automatically. Also notice that the fields are declared as public unlike
the private field id. The ViewCount field in the snippet is interesting,
because it has a private set. What this means is that you cant directly
assign a value to this field from an object of the class. Thing to note
in automatic properties are that you cant add any logic for an automatic
property and also you cant create read-only automatic properties.

**Object Initializers**

Object initializers allows us to initialize the properties of an object
while its being created. 

{% highlight csharp %}
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class ObjectInit
{
    public void SetProduct()
    {
        //Normal way
        Product prod = new Product();
        prod.Id = 1;
        prod.Name = "Product1";
        Product newProd = new Product() {Id = 2, Name = "Product2"};
    }
}
{% endhighlight %}

For eg. in the above snippet, the Product class has 2 properties Id and
Name. In the ObjectInit class, i am creating 2 objects of the Product
class viz. prod and newProd. The new Prod is initialized directly when
the object is created unlike the prod where first the object is created
and then the properties are initialized.

I will continue on other new features in my next post




