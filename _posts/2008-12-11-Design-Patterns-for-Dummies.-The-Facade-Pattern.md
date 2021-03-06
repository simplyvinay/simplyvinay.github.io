---
layout: post
title: "Design Patterns for Dummies. The Facade Pattern"
date: 2008-12-11
comments: true
disqus_identifier: 21
categories: [Design Patterns,C#,.NET]
redirect_from: "/Post/21/Design-Patterns-for-Dummies.-The-Facade-Pattern.aspx/"
---
This is the continuing post in a series of post on Design patterns.
Presently we are going through the structural patterns. I will be
writing about the Façade pattern today. This would be the last
structural pattern.
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
6.  [Adapter
    Pattern](/2008/12/07/Design-Patterns-for-Dummies.-The-Adapter-Pattern/)

Façade pattern deals with encapsulating a subsystem which means
providing a different high level view of subsystem whose details are
hidden from users.

{% highlight csharp %}
class FacadePattern
{
    public class Person
    {
        public string Position { get; set; }
        public double Salary { get; set; }

        public Person(
            string position,
            double salary)
        {
            Position = position;
            Salary = salary;
        }

    }

    //Subsystem
    internal class Bonus
    {
        internal bool IsDeveloper(
            Person person)
        {
            return (person.Position.Equals("Developer"));
        }

        internal void GetBonus(
            Person person)
        {
            if (IsDeveloper(person))
            {
                person.Salary += person.Salary * .10;
            }
            else
            {
                person.Salary += person.Salary * .12;
            }
        }
    }

    //Facade
    class BonusFacade
    {
        Bonus bonus = new Bonus();

        public void GetBonus(
            Person p)
        {
            bonus.GetBonus(p);
            Console.WriteLine(p.Salary);
        }
    }

    static void Main()
    {
        BonusFacade bonus = new BonusFacade();
        Person person1 = new Person(
            "Developer",
            5000);
        bonus.GetBonus(person1);
        //Output : 5500;

        Person person2 = new Person(
            "Tester",
            5000);
        bonus.GetBonus(person2);
        //Output : 5600;

        Console.ReadLine();
    }
}
{% endhighlight %}

As you can see from the above example, the Bonus class is a subsystem
which is encapsulated in the façade. When we call the Bonusfacade’s
GetBonus method, it internally creates an object of the subsystem and
does the necessary functionality. We have to use the façade pattern when
the system might get more complex but the present users shouldn’t be
aware of the change or when the abstraction and implementation of a
system are tightly coupled.

This is the last of the structural patterns. In my next post I will
start with the Creational patterns.

