---
layout: post
title: "Design Patterns for Dummies. The Mediator Pattern"
date: 2009-02-09
comments: true
disqus_identifier: 36
categories: [Design Patterns]
---
In continuation with the series on design pattern, I am currently going
through Behavioral Patterns. Today I will write about the Mediator
Pattern. You can read about rest of the patterns from the following
links

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
6.  [Iterator
    Pattern](/2009/02/05/Design-Patterns-for-Dummies.-The-Iterator-Pattern/)

You can read about the Structural patterns
[here](/2008/12/15/Structural-Design-Patterns/).

You can read about the Creational patterns
[here](/2009/01/12/Creational-Design-Patterns/).

The Mediator patterns deals with providing a way for objects to
communicate with each other without knowing each others identity. The
participants involved in this pattern are a Mediator and Colleague. A
concrete colleague is used to communicate with other colleagues through
its mediator.

{% highlight csharp %}
namespace PatternsConsole
{
    class MediatorPattern
    {
        public delegate void Callback(
            string message,
            string from);

        class Mediator
        {
            Callback respond;

            public void Register(
                Callback method)
            {
                respond += method;
            }

            // Send is implemented as a broadcast
            public void Send(
                string message,
                string from)
            {
                respond(
                    message,
                    from);
                Console.WriteLine();
            }
        }

        class FooColleague
        {
            Mediator mediator;
            protected string name;

            public FooColleague(
                Mediator mediator,
                string name)
            {
                this.mediator = mediator;
                this.name = name;
                mediator.Register(Receive);
            }

            public virtual void Receive(
                string message,
                string from)
            {
                if (!string.Equals(
                    from,
                    name))
                    Console.WriteLine(name + " received from " + from + ": " + message);
            }

            public void Send(
                string message)
            {
                Console.WriteLine("Send (From " + name + "): ");
                mediator.Send(
                    message,
                    name);
            }
        }

        class BarColleague : FooColleague
        {
            public BarColleague(
                Mediator mediator,
                string name)
                : base(
                    mediator,
                    name)
            {
            }

            public override void Receive(
                string message,
                string from)
            {
                if (!string.Equals(
                    from,
                    name))
                    Console.WriteLine(name + " received from " + from + ": " + message);
            }
        }

        static void Main()
        {
            var mediator = new Mediator();
            var foo = new FooColleague(
                mediator,
                "Foo");
            var bar = new BarColleague(
                mediator,
                "Bar");
            foo.Send("Hi Bar! This is a greeting through the mediator");
            bar.Send("Hi Foo! Really!!");
            // Output :
            // Send (From Foo):
            // Bar received from Foo: Hi Bar! This is a greeting through the mediator

            // Send (From Bar):
            // Foo received from Bar: Hi Foo! Really!!

            Console.ReadLine();
        }
    }
}
{% endhighlight %}

We can use the mediator pattern when we have objects are communicating
in a complex way or when we have to protect the identities of objects
who are communicating.

In my next post I will be writing about the Observer Pattern.

