---
layout: post
title: "Design Patterns for Dummies. The Strategy Pattern"
date: 2009-01-12
comments: true
disqus_identifier: 30
categories: [Design Patterns]
redirect_from: "/Post/30/Design-Patterns-for-Dummies.-The-Strategy-Pattern.aspx/"
---
Continuing with the series on Design patterns, I will take up Behavioral
Patterns from today. I have written about [Structural
Patterns](/2008/12/15/Structural-Design-Patterns/)
and [Creational
Patterns](/2009/01/12/Creational-Design-Patterns/)
in my previous posts. You can get all the links for them
[here](/2008/12/15/Structural-Design-Patterns/)
and
[here](/2009/01/12/Creational-Design-Patterns/).

**Behavioral Patterns**

Behavioral patterns are concerned with algorithms and communication
between them. An algorithm that may have been spilt into complex classes
could be hard to maintain. Behavioral patterns helps in identifying the
basis for this split and deals with communication between these classes.

**Strategy Pattern**

The strategy pattern deals with removing algorithms from its host and
placing them separately. The client program is given a context to select
the desired algorithm in a simple way.

{% highlight csharp %}
namespace PatternsConsole
{
    class StrategyPattern
    {
        // Strategy Interface
        interface ISortStrategy
        {
            void Sort(
                List<Person> list);
        }

        class Person
        {
            public string Name { get; set; }
            public int Age { get; set; }
        }

        // Concrete Strategies
        class SelectionSort : ISortStrategy
        {
            public void Sort(
                List<Person> list)
            {
                for (var i = 0; i < list.Count - 1; i++)
                {
                    for (var j = 1 + 1; j < list.Count; j++)
                    {
                        if (list[i].Age > list[j].Age)
                        {
                            Swap(
                                i,
                                list,
                                j);
                        }
                    }
                }
            }

            private static void Swap(
                int i,
                IList<Person> list,
                int j)
            {
                var temp = list[i];
                list[i] = list[j];
                list[j] = temp;
            }
        }

        class LINQSort : ISortStrategy
        {
            public void Sort(
                List<Person> list)
            {
                list.Sort(
                    (p1, p2) => p1.Age.CompareTo(p2.Age));
            }
        }

        // Context
        class SortedList
        {
            private readonly List<Person> list = new List<Person>();
            public ISortStrategy SortStrategy { private get; set; }

            public void Add(
                Person person)
            {
                list.Add(person);
            }

            public void Sort()
            {
                SortStrategy.Sort(list);
                Console.WriteLine(SortStrategy.GetType().Name);
                foreach (var person in list)
                {
                    Console.WriteLine(" " + person.Name);
                }
                Console.WriteLine();
            }
        }

        private static void Main()
        {
            var list = new SortedList();
            list.Add(new Person {Name = "Person1", Age = 25});
            list.Add(new Person {Name = "Person2", Age = 28});
            list.Add(new Person {Name = "Person3", Age = 26});
            list.Add(new Person {Name = "Person4", Age = 20});
            list.SortStrategy = new SelectionSort();
            list.Sort();
            list.SortStrategy = new LINQSort();
            list.Sort();
            // Output : SelectionSort
            //          Person4   
            //          Person1   
            //          Person3
            //          Person2
            //LINQSort
            //          Person4
            //          Person1
            //          Person3
            //          Person2
            Console.Read();
        }
    }
}
{% endhighlight %}

As you can see in the above example, we have 2 strategies to sort a
list. There is a context class which is exposed to the client where it
can select a particular strategy. We can use the strategy pattern when
we have different classes only differing in behavior or when there are
different algorithms and we have to give the selection to the client.

In my next post I will write about the State Pattern.

