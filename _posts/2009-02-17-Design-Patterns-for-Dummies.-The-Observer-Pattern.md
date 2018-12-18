---
layout: post
title: "Design Patterns for Dummies. The Observer Pattern"
date: 2009-02-17
comments: true
disqus_identifier: 37
categories: [Design Patterns]
---
In continuation with the series on design pattern, I am currently going
through Behavioral Patterns. Today I will write about the Observer
Pattern. You can read about rest of the patterns from the following
links

1.  [Strategy
    Pattern](http://www.simplyvinay.com/Post/30/Design-Patterns-for-Dummies.-The-Strategy-Pattern.aspx)
2.  [State
    Pattern](http://www.simplyvinay.com/Post/31/Design-Patterns-for-Dummies.-The-State-Pattern.aspx)
3.  [Template Method
    Pattern](http://www.simplyvinay.com/Post/32/Design-Patterns-for-Dummies.-The-Template-Method-Pattern.aspx)
4.  [Chain of Responsibility
    Pattern](http://www.simplyvinay.com/Post/33/Design-Patterns-for-Dummies.-The-Chain-of-Responsibility-Pattern.aspx)
5.  [Command
    Pattern](http://www.simplyvinay.com/Post/34/Design-Patterns-for-Dummies.-The-Command-Pattern.aspx)
6.  [Iterator
    Pattern](http://www.simplyvinay.com/Post/35/Design-Patterns-for-Dummies.-The-Iterator-Pattern.aspx)
7.  [Mediator
    Pattern](http://www.simplyvinay.com/Post/36/Design-Patterns-for-Dummies.-The-Mediator-Pattern.aspx)

You can read about the Structural patterns from
[here](http://www.simplyvinay.com/Post/23/Structural-Design-Patterns.aspx).

You can read about the Creational patterns from
[here](http://www.simplyvinay.com/Post/29/Creational-Design-Patterns.aspx).

The Observer pattern deals with providing a relation between objects
such that when one objects changes its state, then all the other objects
that have a relation to it are notified of the change. This could be
achieved by using a single publisher of the state of object and many
subscribers who may want the notification for the changed state.

The Observer is usually composed of 2 class viz. a Subject whose state
may change periodically and the Observer itself who maybe notified of
the change, if they wish to receive the state change information.

The following example is based on the example from Dofactory website

{% highlight csharp %}
namespace PatternsConsole
{
    class ObserverPattern
    {
        public delegate void ChangeEventHandler<TArg1, TArg2>(
            TArg1 sender,
            TArg2 eventArgs);

        public class ChangeEventArgs : EventArgs
        {
            public string Title { get; set; }
            public string Name { get; set; }

            public ChangeEventArgs(
                string name,
                string title)
            {
                Name = name;
                Title = title;
            }

        }

        // Subject
        class Blog
        {
            public string Name { get; private set; }

            protected string title;

            // Constructor
            public Blog(
                string name,
                string title)
            {
                Name = name;
                this.title = title;
            }

            public event ChangeEventHandler<Blog, ChangeEventArgs> Change;

            public virtual void OnChange(
                ChangeEventArgs e)
            {
                if (Change != null)
                {
                    Change(
                        this,
                        e);
                }
            }

            public void Attach(
                IObserver observer)
            {
                Change += observer.Update;
            }

            public string Title
            {
                set
                {
                    title = value;
                    OnChange(
                        new ChangeEventArgs(
                            Name,
                            title));
                    Console.WriteLine("");
                }
            }
        }

        // Observer
        interface IObserver
        {
            void Update(
                Blog sender,
                ChangeEventArgs e);
        }

        // ConcreteObserver
        class Observer : IObserver
        {
            public string Name { get; private set; }

            public Observer(
                string name)
            {
                Name = name;
            }

            public void Update(
                Blog sender,
                ChangeEventArgs e)
            {
                Console.WriteLine(
                    "Notified {0} of {1}'s " + "post : {2}",
                    Name,
                    e.Name,
                    e.Title);
            }
        }

        static void Main()
        {
            var rob = new Blog(
                "Rob Conery",
                "Weke Road");
            rob.Attach(new Observer("Vinay"));
            rob.Title = "Subsonic 3";
            rob.Title = "Storefront Finis";
            rob.Title = "Oxite Refactor";
            //Output :
            // Notified Vinay of Rob Conery's post : Subsonic 3
            // Notified Vinay of Rob Conery's post : Storefront Finis
            // Notified Vinay of Rob Conery's post : Oxite Refactor
            Console.ReadLine();
        }
    }
}
{% endhighlight %}

We can use the observer pattern when changes in one object needs to be
propagated to other objects or when the object sending the changes does
not need to know about the receivers.

In my next post I will be writing about the Visitor Pattern

