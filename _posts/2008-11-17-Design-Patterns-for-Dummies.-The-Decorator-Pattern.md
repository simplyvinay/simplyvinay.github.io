---
layout: post
title: "Design Patterns for Dummies. The Decorator Pattern"
date: 2008-11-17
comments: true
disqus_identifier: 14
categories: [Design Patterns]
---
Yes there are a lot of posting on design patterns, and you might ask why
another of these post on design patterns. I am doing this post so that I
can document the design patterns for myself for later use.

There are 23 design patterns as observed by the Gang Of Four (Erich
Gamma, Richard Helm, Ralph Johnson, and John Vlissides). And they are
categorized basically into 3 segments viz. Structural, Creational and
Behavioral patterns. I will take these in order.

To begin with I will start with the Decorator pattern.

Decorator pattern comes under Structural patterns which dwells on how
classes and objects are designed to form a much larger structure.
Usually these patterns play a major role in the post production or the
maintenance stages of the development cycle, when you need to add some
additional functionality without changing the existing classes. The
decorator comes into play when you need this, i.e. when you want to add
functionality to a class without changing it. Like the decorator, the
proxy and the bridge pattern also helps in adding dynamic functionality
which I will discuss in a later post.

OK. Lets see some code of the decorator pattern.

{% highlight csharp %}
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }

    public Person()
    {
        this.Name = "Person1";
        this.Age = 27;
    }

    public virtual void GetDetails()
    {

        Console.Write(
            "My name is {0} ",
            Name);

        Console.Write(
            "and my age is {0}\n",
            Age);

    }
}

public class Decorator
{
    public class PersonLocation : Person
    {
        Person _person;
        string _location;

        public PersonLocation(
            Person person,
            string location)
        {
            _person = person;
            _person.Name = "New Person1";
            _person.Age = 28;
            _location = location;
        }

        public override void GetDetails()
        {
            base.GetDetails();

            Console.WriteLine(
                "and i live in {0}",
                _location);
        }
    }

    public class PersonJob : Person
    {
        Person _person;
        string _jobDesc;

        public PersonJob(
            Person person,
            string jobDesc)
        {
            _person = person;
            _person.Name = "New Person2";
            _person.Age = 30;
            _jobDesc = jobDesc;
        }

        public override void GetDetails()
        {
            base.GetDetails();

            Console.WriteLine(
                "and i work as a {0}",
                _jobDesc);
        }
    }
}

public class Program
{
    public static void Main()
    {
        Person p = new Person();

        Decorator.PersonLocation pLocation = new Decorator.PersonLocation(
            p,
            "Miami");

        pLocation.GetDetails(); //Output : My name is Person1 and my age is 27
                   // and i live in Miami
        p = new Person();
        Decorator.PersonJob pJob = new Decorator.PersonJob(
            p,
            "Developer");
        pJob.GetDetails();
    }
}
{% endhighlight %}

Here the classes PersonJob and PersonLocation are decorators around the
Person class. Person class has only 2 properties viz. name and age. We
are dynamically adding 2 new properties which are job and location
through 2 decorators.

One thing to note here as you see is that even though we are changing
the person’s properties in the decorator, in the output the name and age
doesn't change. This is because the decorator pattern is based around
new objects being created with their own sets of operations.

When to use the decorator pattern : We can use the decorator pattern
when we want to, as discussed earlier, change some objects in a class
without affecting the original class, when we want some behavior for an
object dynamically.

