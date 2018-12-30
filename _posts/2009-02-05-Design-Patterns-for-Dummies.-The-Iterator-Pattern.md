---
layout: post
title: "Design Patterns for Dummies. The Iterator Pattern"
date: 2009-02-05
comments: true
disqus_identifier: 35
categories: [Design Patterns,C#,.NET]
redirect_from: "/Post/35/Design-Patterns-for-Dummies.-The-Iterator-Pattern.aspx/"
---
In continuation with the series on design pattern, I am currently going
through Behavioral Patterns. Today I will write about the Iterator
Pattern. You can read about rest of the patterns from the following
links
<!--more-->
1.  [Strategy
    Pattern](/2009/01/12/Design-Patterns-for-Dummies.-The-Strategy-Pattern/)
2.  [State
    Pattern](/2009/01/15/Design-Patterns-for-Dummies.-The-State-Pattern/)
3.  [Template Method
    Pattern](/2009/01/19/Design-Patterns-for-Dummies.-The-Template-Method-Pattern/)
4.  [Chain of Responsibility
    Pattern](/2009/01/22/Design-Patterns-for-Dummies.-The-Chain-of-Responsibility-Pattern/)
5.  [Command
    Pattern](/2009/02/02/Design-Patterns-for-Dummies.-The-Command-Pattern/)

You can read about the Structural patterns
[here](/2008/12/15/Structural-Design-Patterns/).

You can read about the Creational patterns
[here](/2009/01/12/Creational-Design-Patterns/).

The Iterator patterns deals with providing an easy way to access
elements of a collection sequentially. To implement an iterator in C#,
we can implement either IEnumerator or the IEnumerable interfaces. 
IEnumerator interface has 2 methods *GetEnumerator* and *Reset* which we
would have to implement and an *Current* object which is used to
maintain the position. The easier way of doing it is to implement the
IEnumerable interface and implement the GetEnumerator method for it. In
the following example I am using the yield return statement in the
GetEnumerator method which saves us from maintaining the state.

{% highlight csharp %}
namespace PatternsConsole
{
    class IteratorPattern
    {
        class Collection<T> : IEnumerable<T>
        {
            private readonly List<T> items = new List<T>();

            public void Add(
                T t)
            {
                items.Add(t);
            }

            public IEnumerator<T> GetEnumerator()
            {
                for (int i = 0; i < Count; i++)
                {
                    yield return items[i];
                }
            }

            public int Count
            {
                get { return items.Count; }
            }

            IEnumerator IEnumerable.GetEnumerator()
            {
                return GetEnumerator();
            }
        }

        class Person
        {
            public string Name { get; private set; }

            public Person(
                string name)
            {
                Name = name;
            }
        }

        private static void Main()
        {
            var people = new Collection<Person>
            {
                new Person("Person 1"),
                new Person("Person 2"),
                new Person("Person 3"),
                new Person("Person 4"),
                new Person("Person 5")
            };

            foreach (var person in people)
            {
                Console.WriteLine( person.Name );
            }
            // Output :
            // Person 1
            // Person 2
            // Person 3
            // Person 4
            // Person 5

            Console.ReadLine();
        }
    }
}
{% endhighlight %}

We can implement our iterator when we want iterations over a collection
or we have many ways of traversing it or when there are many collections
for traversing.

In my next post I will be writing about the Mediator Pattern.

