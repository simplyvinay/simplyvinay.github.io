---
layout: post
title: "Design Patterns For Dummies. The Factory Method Pattern"
date: 2008-12-18
comments: true
disqus_identifier: 25
categories: [Design Patterns]
redirect_from: "/Post/25/Design-Patterns-For-Dummies.-The-Factory-Method-Pattern.aspx/"
---
In continuation of my posts on Creational design patterns, I will be
discussing about the Factory Method pattern in this post. You can read
about the other patterns from the following links.

1.  [Prototype
    Pattern](/2008/12/15/Design-Patterns-For-Dummies.-The-Prototype-Pattern/)

You can read about Structural Patterns from
[here](/2008/12/15/Structural-Design-Patterns/).

The Factory Method pattern deals with creation of objects in which the
subclasses decide which class's object has to be created. Various
subclasses may implement the same interface, but the Factory Method
creates the appropriate object based on some supplied information.

{% highlight csharp %}
public class FactoryPattern
{
    public interface IWorkLocation
    {
        string WorkingFrom();
    }

    public class Developer : Person, IWorkLocation
    {
        public Developer(
            string name,
            string designation)
        {
            this.Name = name;
            this.Designation = designation;
        }

        public string WorkingFrom()
        {
            return "Name : " + this.Name + ", Work Location :  Home";
        }
    }

    public class Tester : Person, IWorkLocation
    {
        public Tester(
            string name,
            string designation)
        {
            this.Name = name;
            this.Designation = designation;
        }

        public string WorkingFrom()
        {
            return "Name : " + this.Name + ", Work Location :  Office";
        }
    }

    public class Person : IWorkLocation
    {
        public string Designation { get; set; }
        public string Name { get; set; }

        public Person()
        {
            Name = "Base Person";
            Designation = "";
        }

        public Person(
            string name,
            string designation)
        {
            Designation = designation;
            Name = name;
        }

        public string WorkingFrom()
        {
            return "Name : " + this.Name + ", Work Location :  Not Available";
        }
    }

    //Creator
    public class Creator
    {
        public IWorkLocation FactoryMethod(
            Person per)
        {
            if (per.Designation.Equals("Developer"))
                return new Developer(
                    per.Name,
                    per.Designation);
            else if (per.Designation.Equals("Tester"))
                return new Tester(
                    per.Name,
                    per.Designation);
            else
                return new Person();
        }
    }

    static void Main()
    {
        Creator crtr = new Creator();
        var person = crtr.FactoryMethod(
            new Person(
                "Person1",
                "Developer"));
        Console.WriteLine(person.WorkingFrom());
        //Output: Name : Person1, Work Location :  Home
        
        person = crtr.FactoryMethod(
            new Person(
                "Person2",
                "Tester"));
        Console.WriteLine(person.WorkingFrom());
        //Output: Name : Person2, Work Location :  Office
        
        person = crtr.FactoryMethod(new Person());
        Console.WriteLine(person.WorkingFrom());
        //Output:  Name : Base Person, Work Location :  Not Available
        
        Console.ReadLine();
    }
}
{% endhighlight %}

As seen in the above example, the instantiation of objects is handled by
the FactoryMethod of the Creator class. In this method, the information
being passed helps the method in deciding which object to create. We
must use the Factory method pattern when we want the flexibility of
creating objects based on some criteria or the objects can be extended
in subclasses.

In my next post I will talking about the Singleton pattern.

