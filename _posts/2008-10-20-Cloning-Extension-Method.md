---
layout: post
title: "Cloning Extension Method"
date: 2008-10-20
comments: true
disqus_identifier: 11
categories: [C#]
redirect_from: "/Post/11/Cloning-Extension-Method.aspx/"
---
As you know cloning creates a replica of the instance of a type. There
are basically 2 ways of cloning. *Shallow Copy*which means the original
object and the copied object reference the same memory location after
the process and *Deep Copy*which means the original object and the
copied object do not reference the same location after the process. You
can shallow copy the object by using the protected *MemberwiseClone*
method of the object. You can implement the *Clone* method of the
*ICloneable* interface for each class you have to clone for the deep
copy. I somehow did not like the idea of implementing this *Clone*
method in each and every class that I wanted to clone, so I created a
generic class  to get the deep cloning working. Here is the code.
<!--more-->
{% highlight csharp %}
public class GenericClone
{
    public T DeepClone<T>() where T : class
    {
        object cloned = new object();
        BinaryFormatter formatter = new BinaryFormatter();
        using (MemoryStream stream = new MemoryStream())
        {
            formatter.Serialize(
                stream,
                this);

            stream.Flush();
            stream.Position = 0;
            cloned = formatter.Deserialize(stream);
        }

        return cloned as T;
    }
}

class Test
{
    GenericClone getClone = new GenericClone();

    public void Clone()
    {
        Test deeptest = getClone.DeepClone<Test>();

    }
}
{% endhighlight %}

Thing to note here is that the class which needs to be cloned has to be
marked as *Serializable*for this method to work. To create a clone of a
class we just have to create an object of the GenericClone class and
call the DeepClone method as in the Test class. The problem here is that
we need to have an object of the GenericClone class before we can clone
the object. With C# 3.0, now we can write an extension method for the
cloning process and call it on the object itself. Here is an example.

{% highlight csharp %}
public static class ExtensionClone
{
    public static T DeepClone<T>(
        this object obj) where T : class
    {
        object cloned = new object();
        BinaryFormatter formatter = new BinaryFormatter();
        using (MemoryStream stream = new MemoryStream())
        {
            formatter.Serialize(
                stream,
                obj);
            stream.Flush();
            stream.Position = 0;
            cloned = formatter.Deserialize(stream);
        }

        return cloned as T;
    }
}

class Test
{
    public void Get()
    {
        Test cloned = this.DeepClone<Test>();
    }
}
{% endhighlight %}

As you can see this is a much cleaner way of cloning the object. I don't
have to create an object of the ExtensionClone class to get my cloned
object.

