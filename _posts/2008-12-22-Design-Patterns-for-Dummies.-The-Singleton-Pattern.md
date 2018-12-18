---
layout: post
title: "Design Patterns for Dummies. The Singleton Pattern"
date: 2008-12-22
comments: true
disqus_identifier: 26
categories: [Design Patterns]
---
In continuation of my posts on Creational design patterns, I will be
discussing about the Singleton pattern in this post. You can read about
the other patterns from the following links.

1.  [Prototype
    Pattern](http://www.simplyvinay.com/Post/24/Design-Patterns-For-Dummies.-The-Prototype-Pattern.aspx)
2.  [Factory Method
    Pattern](http://www.simplyvinay.com/Post/25/Design-Patterns-For-Dummies.-The-Factory-Method-Pattern.aspx)

You can read about Structural Patterns from
[here](http://www.simplyvinay.com/Post/23/Structural-Design-Patterns.aspx).

Singleton pattern deals with ensuring that there is only one instance of
a class and providing a single global access point to that object. It
also ensures that the object is not created until its actually needed.

{% highlight csharp %}
class SingletonPattern
{
    class SingletonPerson
    {
        private static SingletonPerson instance = null;
        public string Name { get; private set; }
        public int Age { get; private set; }
        public string Address { get; private set; }
        private Random random = new Random();

        SingletonPerson()
        {
            Name = "Person" + random.Next(
                       1,
                       10);
            Age = 27;
            Address = "Address" + random.Next(
                          10,
                          20);
        }

        public static SingletonPerson Instance
        {
            get { return Nested.Instance; }
        }

        private class Nested
        {
            internal static readonly SingletonPerson Instance =
                new SingletonPerson();

            static Nested()
            {
            }
        }
    }

    static void Main()
    {
        SingletonPerson person1 = SingletonPerson.Instance;
        Console.WriteLine(
            "Name  : " + person1.Name + " Age : " + person1.Age
            + " Address : " + person1.Address);
        //Output : Name  : Person7 Age : 27 Address : Address19
        
        SingletonPerson person2 = SingletonPerson.Instance;
        Console.WriteLine(
            "Name  : " + person2.Name + " Age : " + person2.Age
            + " Address : " + person2.Address);
        //Output : Name  : Person7 Age : 27 Address : Address19
        
        // Confirm person1 and person2 are same
        if (person1 == person2)
        {
            Console.WriteLine("Same instance\n");
        }
        Console.ReadLine();
    }
}
{% endhighlight %}

As you can see from the above example the 2 instances of the
SingletonPerson object are the same and so are their properties viz.
Name, Age and Address.  We can use the singleton pattern when we need to
create only one instance of a class or when we need to provide
controlled access to an instance.

In my next post I will be writing about the Abstract Factory Pattern.

