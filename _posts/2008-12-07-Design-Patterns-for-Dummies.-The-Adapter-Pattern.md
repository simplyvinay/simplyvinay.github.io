---
layout: post
title: "Design Patterns for Dummies. The Adapter Pattern"
date: 2008-12-07
comments: true
disqus_identifier: 19
categories: [Design Patterns]
redirect_from: "/Post/19/Design-Patterns-for-Dummies.-The-Adapter-Pattern.aspx/"
---
This is the continuing post in a series of post on Design patterns.
Presently we are going through the structural patterns. I will be
writing about the adapter pattern today.
<!--more-->
You can read about the other patterns from the following links

1.  [Decorator
    Pattern](/2008/11/17/Design-Patterns-for-Dummies.-The-Decorator-Pattern/)
2.  [Proxy
    Pattern](/2008/11/24/Design-Patterns-for-Dummies.-The-Proxy-Pattern/)
3.  [Bridge
    Pattern](/2008/11/27/Design-Patterns-for-Dummies.-The-Bridge-pattern/)
4.  [Composite
    Pattern](/2008/12/01/Design-Patterns-for-Dummies.-The-Composite-Pattern/)
5.  [Flyweight
    Pattern](/2008/12/04/Design-Patterns-for-Dummies.-The-Flyweight-Pattern/)

Adapter pattern deals with providing a way to use an already present
interface that does not meet with the present requirements. We create an
adapter with our desired interface and make this class communicate with
the classes that implement a different interface.

{% highlight csharp %}
public class AdapterPattern
{
    public interface IPerson
    {
        string Name { get; set; }
        void MetroTravel();
    }

    public class Person : IPerson
    {
        public string Name { get; set; }

        public Person(
            string name)
        {
            Name = name;
        }

        public void MetroTravel()
        {
            Console.WriteLine("Travel by Metro rail");
        }
    }

    public interface INewPerson
    {
        string Name { get; set; }
        void CarTravel();
    }

    public class NewPerson : INewPerson
    {
        public string Name { get; set; }

        public NewPerson(
            string name)
        {
            Name = name;
        }

        public void CarTravel()
        {
            Console.WriteLine("Travel by Car");
        }
    }

    public class PersonAdapter : INewPerson
    {
        public IPerson _person;
        public string Name { get; set; }

        public PersonAdapter(
            IPerson person)
        {
            _person = person;
        }

        public void CarTravel()
        {
            _person.MetroTravel();
        }
    }

    public static void Main()
    {
        Person person = new Person("Person1");
        PersonAdapter adapter = new PersonAdapter(person);
        adapter.CarTravel();
        //Output : Travel by Metro rail
        Console.ReadLine();
    }
}
{% endhighlight %}

As seen in the example, the Person class implements the IPerson
interface which has a method MetroTravel. But we have a new interface
called INewPerson which has a method called CarTravel. So if we have to
use the new interface with the old class, we can create an adapter,
PersonAdapter and use it as shown. We have to use an adapter when we
have a class that has to connect to a mismatching interface or when we
want to create a class which might interact with other class which are
not yet built or when we have different methods for different purposes
or when have have methods which change as called.

In my next post I will be writing about the Facade pattern which would
bring us to the end of structural patterns.

