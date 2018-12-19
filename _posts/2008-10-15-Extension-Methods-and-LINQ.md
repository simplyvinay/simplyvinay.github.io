---
layout: post
title: "Extension Methods and LINQ"
date: 2008-10-15
comments: true
disqus_identifier: 10
categories: [C#]
redirect_from: "/Post/10/Extension-Methods-and-LINQ.aspx/"
---
This is the final post about the new features in C# 3.0. You can read
the first two posts
[here](/2008/07/01/Automatic-Properties-and-Object-Initializers/)
and
[here](/2008/10/01/Type-Inference,-Anonymous-types-and-Lambda-Expressions/).
In these posts I have just described the new features of C# 3.0 without
going much into details. If time permits I will elaborate on each of
these.

**Extension Methods**

Prior to C# 3.0, the only way to update member information of a
compiled type, was to recode and recompile the code. But now with
extension methods we can allow compiled type to obtain new
functionality. This is immensely helpful  when you want to add
functionality to a third party type where you are not allowed to change
the code. When you create an extension method, it actually behave as if
it was part of the original type. Lets see an example.

{% highlight csharp %}
class Extensions
{
    public void DoSometing()
    {
        string originalString = "EXTENSIONS";
        string reversedString = originalString.Reverse();
        Console.WriteLine(originalString); //EXTENSIONS
        Console.WriteLine(reversedString); //SNOISNETXE
    }
}

public static class ExtensionClass
{
    public static string Reverse(
        this string str)
    {
        char[] chr = str.ToCharArray();
        Array.Reverse(chr);
        return new string(chr);
    }
}
{% endhighlight %}

You create an extension method by creating a static class with a static
method. As you can see in the *ExtensionClass* the method Reverse is
taking a string parameter with *this* keyword which indicates the the
method is accessible to type of string. We are using the reverse method
in the DoSomething method of the Extensions class. As seen we are not
creating an object the *ExtensionClass* , instead we are calling the
Reverse method on the string itself.

**LINQ**

Language INtegrated Query(LINQ) enables you to write queries against
objects. You can query against any object that implements the
*IEnumerable<T>* interface. All queries are converted into method
calls to the *Enumerable* class. This class contains extension methods
that can be applied to any object that implements *IEnumerable*. Lets
see an example.

{% highlight csharp %}
class LINQ
{
    public void SelectFromList()
    {
        var list = new List<string> {"a", "b", "c", "d", "e"};
        var str = from s in list 8 where s.Contains("a") 9 select s;
        var str1 = list.Where(s => s.Contains("a")).Select(s => s);
    }
}
{% endhighlight %}

Here the first LINQ statement is using a query syntax and the second is
using a method syntax. The method syntax uses a lambda expression with
*Where* and *Select* extension methods. These two LINQ statements are
the same and gives out the same result.

