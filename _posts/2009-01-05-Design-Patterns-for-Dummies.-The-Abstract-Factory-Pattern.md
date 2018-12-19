---
layout: post
title: "Design Patterns for Dummies. The Abstract Factory Pattern"
date: 2009-01-05
comments: true
disqus_identifier: 27
categories: [Design Patterns]
redirect_from: "/Post/27/Design-Patterns-for-Dummies.-The-Abstract-Factory-Pattern.aspx/"
---
After a brief hiatus for the holidays, I will continue my series on
design patterns. Currently I am going through Creational design
patterns, I will be discussing about the Abstract Factory pattern in
this post. You can read about the other patterns from the following
links.

1.  [Prototype
    Pattern](/2008/12/15/Design-Patterns-For-Dummies.-The-Prototype-Pattern/)
2.  [Factory Method
    Pattern](/2008/12/18/Design-Patterns-For-Dummies.-The-Factory-Method-Pattern/)
3.  [Singleton
    Pattern](/2008/12/22/Design-Patterns-for-Dummies.-The-Singleton-Pattern/)

You can read about Structural Patterns from
[here](/2008/12/15/Structural-Design-Patterns/).

Abstract factory pattern deals with creation of objects that are
designed to be produced together. Abstract factory can be defined to
create different objects of different types and in different
combinations. This pattern isolates the object definitions and their
class names so that only a factory can return an object when a client
requires it.

{% highlight csharp %}
public class AbstractFactoryPattern
{
    // An interface for all Cars
    private interface IModel
    {
        int Cost { get; }
        string Name { get; }
    }

    //Individual car model
    public class Benz : IModel
    {
        public int Cost
        {
            get { return 20000; }
        }

        public string Name
        {
            get { return "Merc"; }
        }
    }

    //Individual car model
    public class BMW : IModel
    {
        public int Cost
        {
            get { return 21000; }
        }

        public string Name
        {
            get { return "BMW"; }
        }
    }

    // Interface Car
    private interface ICar
    {
        string Name { get; }
        string Cost { get; }
    }

    // All concrete Cars
    class Car<T> : ICar where T : IModel, new()
    {
        private readonly T model;

        public Car()
        {
            model = new T();
        }

        public string Name
        {
            get { return model.Name; }
        }

        public string Cost
        {
            get { return model.Cost.ToString(); }
        }
    }

    //Factory that creates the car
    private interface IFactory
    {
        ICar CreateCar();
    }

    // Concrete Factories
    class CarFactory<T> : IFactory where T : IModel, new()
    {
        public ICar CreateCar()
        {
            return new Car<T>();
        }
    }

    class Client<T> where T : IModel, new()
    {
        public void ClientMain()
        {
            IFactory factory = new CarFactory<T>();
            var car = factory.CreateCar();
            Console.WriteLine("My car is " + car.Name + " and its cost is " + car.Cost);
        }
    }

    private static void Main()
    {
        new Client<Benz>().ClientMain();
        new Client<BMW>().ClientMain();
        //Output: My car is Merc and its cost is 20000
        //        My car is BMW and its cost is 21000
        Console.ReadLine();
    }
}
{% endhighlight %}

As we can see from the above example, the abstract factory handles
creation of objects and keeps their details hidden from the client. We
can use the Abstract Factory pattern when a system should be independent
of how its objects are created and represented or when a system can be
configured with one of many families of products or when the importance
is on revealing interfaces and not implementations.

In my next post I will writing about the Builder pattern.

