---
layout: post
title: "Design Patterns for Dummies. The Template Method Pattern"
date: 2009-01-19
comments: true
disqus_identifier: 32
categories: [Design Patterns]
---
In continuation with the series on design pattern, I am currently going
through Behavioral Patterns. Today I will write about the Template
Method Pattern. You can read about rest of the patterns from the
following links

1.  [Strategy
    Pattern](http://www.simplyvinay.com/Post/30/Design-Patterns-for-Dummies.-The-Strategy-Pattern.aspx)
2.  [State
    Pattern](http://www.simplyvinay.com/Post/31/Design-Patterns-for-Dummies.-The-State-Pattern.aspx)

You can read about the Structural patterns from
[here](http://www.simplyvinay.com/Post/23/Structural-Design-Patterns.aspx).
\
You can read about the Creational patterns from
[here](http://www.simplyvinay.com/Post/29/Creational-Design-Patterns.aspx).

The template method pattern deals with enabling algorithms to defer
certain steps. It doesn't change the structure of the algorithm but a
few of their operations are handled elsewhere.

{% highlight csharp %}
namespace PatternsConsole
{
    class TemplatePattern
    {
        //Interface for operations
        interface ISortStrategy
        {
            void Sort();
        }

        //Algorithm with template method
        class Algorithm
        {
            public void TemplateMethod(
                ISortStrategy sortStrategy)
            {
                sortStrategy.Sort();
            }
        }

        //Concrete Class
        class SortByAge : ISortStrategy
        {
            public void Sort()
            {
                PersonList.Sort(
                    (p1, p2) => p1.Age.CompareTo(p2.Age));
                Console.WriteLine("Sort By Age");
                foreach (var person in PersonList)
                {
                    Console.WriteLine("Name : " + person.Name + " Age :" + person.Age);
                }
            }
        }

        //Concrete Class
        class SortByName : ISortStrategy
        {
            public void Sort()
            {
                PersonList.Sort(
                    (p1, p2) => p1.Name.CompareTo(p2.Name));
                Console.WriteLine("\nSort By Name");
                foreach (var person in PersonList)
                {
                    Console.WriteLine("Name : " + person.Name + " Age :" + person.Age);
                }
            }
        }

        class Person
        {
            public string Name { get; set; }
            public int Age { get; set; }
        }

        private static List<Person> PersonList;

        private static void Main()
        {
            PersonList = new List<Person>
            {
                new Person {Name = "Person1", Age = 25},
                new Person {Name = "Person3", Age = 26},
                new Person {Name = "Person4", Age = 20},
                new Person {Name = "Person2", Age = 28}

            };
            var sortAlgo = new Algorithm();
            sortAlgo.TemplateMethod(new SortByAge());
            //Output :  Sort By Age
            //          Name : Person4 Age :20
            //          Name : Person1 Age :25
            //          Name : Person3 Age :26
            //          Name : Person2 Age :28

            sortAlgo.TemplateMethod(new SortByName());
            //Output :  Sort By Name
            //          Name : Person1 Age :25
            //          Name : Person2 Age :28
            //          Name : Person3 Age :26
            //          Name : Person4 Age :20

            Console.Read();
        }
    }
}
{% endhighlight %}

We can use the template method pattern when common behavior can be
outside the algorithm and the the behavior itself varies based on the
type of the subclass.

In my next post I will be writing about the Chain of Responsibility
pattern.

