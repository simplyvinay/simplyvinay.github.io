---
layout: post
title: "Design Patterns for Dummies. The Flyweight Pattern"
date: 2008-12-04
comments: true
disqus_identifier: 18
categories: [Design Patterns]
redirect_from: "/Post/18/Design-Patterns-for-Dummies.-The-Flyweight-Pattern.aspx/"
---
This is the continuing post in a series of post on Design patterns.
Presently we are going through the structural patterns. I will be
writing about the flyweight pattern today.

You can read about the other patterns from the following links

1.  [Decorator
    Pattern](/2008/11/17/Design-Patterns-for-Dummies.-The-Decorator-Pattern/)
2.  [Proxy
    Pattern](/2008/11/24/Design-Patterns-for-Dummies.-The-Proxy-Pattern/)
3.  [Bridge
    Pattern](/2008/11/27/Design-Patterns-for-Dummies.-The-Bridge-pattern/)
4.  [Composite
    Pattern](/2008/12/01/Design-Patterns-for-Dummies.-The-Composite-Pattern/)

Flyweight pattern deals with sharing of common information in smaller
objects used that are being used in large numbers. This can rescue  the
storage necessities of these objects. The flyweight pattern deals with
the internal and external state of the objects. What it helps in
accomplishing is that the internal state can be shared across objects
and external sate can be computed on the “fly”.

{% highlight csharp %}
public class FlyweightPattern
{
    //Flyweight interface
    public interface IPersonFlyweight
    {
        string FirstName { get; set; }
        string LastName { get; set; }
        string Address { get; set; }
        string Designation { get; set; }

        void Display(
            float salary);
    }

    //Flyweight
    public class Person : IPersonFlyweight
    {
        //Internal state
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public string Address { get; set; }
        public string Designation { get; set; }

        public void Display(
            float salary)
        {
            Console.WriteLine(
                "Name : " + this.FirstName + ", Designation : " +
                this.Designation + ", Salary : " + salary);
        }

        public override string ToString()
        {
            return FirstName;
        }
    }

    public class Tester : Person
    {
        public Tester()
        {
            this.FirstName = "Tester";
            this.LastName = "Tester";
            this.Address = "Tester_Address";
            this.Designation = "Tester";
        }
    }

    public class Developer : FlyweightPattern.Person
    {
        public Developer()
        {
            this.FirstName = "Developer";
            this.LastName = "Developer";
            this.Address = "Developer_Address";
            this.Designation = "Developer";
        }
    }

    public class FlyweightFactory
    {
        // Indexed list of IFlyweight objects
        Dictionary<string, FlyweightPattern.IPersonFlyweight> _flyweights =
            new Dictionary<string, FlyweightPattern.IPersonFlyweight>();

        public FlyweightFactory()
        {
            _flyweights.Clear();
        }

        public FlyweightPattern.Person this[
            string index]
        {
            get
            {
                if (!_flyweights.ContainsKey(index))
                {
                    _flyweights[index] = new FlyweightPattern.Person();
                }

                return _flyweights[index] as FlyweightPattern.Person;
            }
        }
    }

    private static void Main()
    {
        //List of person objects containing a Tester and a Developer object
        FlyweightPattern.Person[] personList = new FlyweightPattern.Person[]
        {
            new FlyweightPattern.Tester(), new Developer()
        };

        //shared state
        FlyweightFactory people = new FlyweightFactory(); //external state

        float salary = 50000;
        foreach (var person in personList)
        {
            if (person is FlyweightPattern.Tester)
            {
                FlyweightPattern.Person p = people[person.ToString()];
                person.Display(
                    salary);
                //Output : Name : Tester, Designation : Tester, Salary : 50000
            }
            else
            {
                FlyweightPattern.Person p = people[person.ToString()];
                person.Display(salary + 5000);
                //Output : Name : Developer, Designation : Developer, Salary : 55000
            }
        }

        Console.ReadLine();
    }
}
{% endhighlight %}

The flyweight pattern is used when we have many objects that may be
memory intensive “and” the sate of these objects can be computed at
runtime.

In my next post I will talking about the Adapter pattern.

