---
layout: post
title: "Design Patterns for Dummies. The Memento Pattern"
date: 2009-03-11
comments: true
disqus_identifier: 40
categories: [Design Patterns]
redirect_from: "/Post/40/Design-Patterns-for-Dummies.-The-Memento-Pattern.aspx/"
---
I have been writing about the GOF design patterns for quite some time
now, and, this would be the last of the series. I will be writing about
the Memento pattern today which is  a behavioral pattern. You can read
about the other patterns from the following links.
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
6.  [Iterator
    Pattern](/2009/02/05/Design-Patterns-for-Dummies.-The-Iterator-Pattern/)
7.  [Mediator
    Pattern](/2009/02/09/Design-Patterns-for-Dummies.-The-Mediator-Pattern/)
8.  [Observer
    Pattern](/2009/02/17/Design-Patterns-for-Dummies.-The-Observer-Pattern/)
9.  [Visitor
    Pattern](/2009/02/24/Design-Patterns-for-Dummies.-The-Visitor-Pattern/)
10. [Interpreter
    Pattern](/2009/03/03/Design-Patterns-for-Dummies.-The-Interpreter-Pattern/)

You can read about the Structural patterns
[here](/2008/12/15/Structural-Design-Patterns/).

You can read about the Creational patterns
[here](/2009/01/12/Creational-Design-Patterns/).

**The Memento**

The memento pattern deals with capturing an object’s internal state and
saving it externally so that the internal state of that object can be
restored later. This pattern is usually used in computer games where the
state of the game can be saved, and can be started from the same point.
The following example illustrates the use of memento pattern.

The components that make up the memento pattern are the O**riginator**,
for which the state has to be saved, the C**aretaker** which is a class
that holds the mementos and the M**emento** which holds the state of the
originator.

{% highlight csharp %}
namespace PatternsConsole
{
    class MementoPatter
    {
        // Originator
        [Serializable]
        class PersonInfo
        {
            public string Name { get; set; }
            public float Salary { get; set; }

            public Memento SetMemento()
            {
                var memento = new Memento();
                return memento.Save(this);

            }

            public void GetMemento(
                Memento memento)
            {
                var info = memento.Restore() as PersonInfo;
                if (info != null)
                {
                    Name = info.Name;
                    Salary = info.Salary;
                }
            }
        }

        // Memento
        [Serializable]
        class Memento
        {
            readonly MemoryStream stream = new MemoryStream();
            readonly BinaryFormatter formatter = new BinaryFormatter();

            public Memento Save(
                object obj)
            {
                formatter.Serialize(
                    stream,
                    obj);
                return this;
            }

            public object Restore()
            {
                stream.Seek(
                    0,
                    SeekOrigin.Begin);
                var obj = formatter.Deserialize(stream);
                stream.Close();
                return obj;
            }
        }

        // CareTaker
        class CareTaker
        {
            public Memento Memento { get; set; }
        }

        private static void Main()
        {
            var personInfo = new PersonInfo
            {
                Name = "Person1", Salary = 5000
            };
            
            // Store internal state
            var careTaker = new CareTaker
            {
                Memento = personInfo.SetMemento()
            };
            Console.WriteLine(
                "Saved State : \nName = {0}, Salary = {1}",
                personInfo.Name,
                personInfo.Salary);
            
            // Change originator
            personInfo.Name = "Person2";
            personInfo.Salary = 10000;
            Console.WriteLine(
                "Changed Sate : \nName = {0}, Salary = {1}",
                personInfo.Name,
                personInfo.Salary);
            
            // Restore saved state
            personInfo.GetMemento(careTaker.Memento);
            Console.WriteLine(
                "Restored State : \nName = {0}, Salary = {1}",
                personInfo.Name,
                personInfo.Salary);
                // Output :
                // Saved State :
                // Name = Person1, Salary = 5000
                // Changed Sate :
                // Name = Person2, Salary = 10000
                // Restored State :
                // Name = Person1, Salary = 5000

            Console.ReadLine();
        }
    }
}
{% endhighlight %}

The memento pattern can be used when we need to save an object’s state
and retrieve it later or when we don't want to expose an objects state
directly.

