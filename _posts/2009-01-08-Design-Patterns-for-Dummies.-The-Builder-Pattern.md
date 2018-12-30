---
layout: post
title: "Design Patterns for Dummies. The Builder Pattern"
date: 2009-01-08
comments: true
disqus_identifier: 28
categories: [Design Patterns]
redirect_from: "/Post/28/Design-Patterns-for-Dummies.-The-Builder-Pattern.aspx/"
---
In continuation of my series on Creational design patterns, I will be
discussing about the Builder pattern in this post. You can read about
the other patterns from the following links.
<!--more-->
1.  [Prototype
    Pattern](/2008/12/15/Design-Patterns-For-Dummies.-The-Prototype-Pattern/)
2.  [Factory Method
    Pattern](/2008/12/18/Design-Patterns-For-Dummies.-The-Factory-Method-Pattern/)
3.  [Singleton
    Pattern](/2008/12/22/Design-Patterns-for-Dummies.-The-Singleton-Pattern/)
4.  [Abstract Factory
    Pattern](/2009/01/05/Design-Patterns-for-Dummies.-The-Abstract-Factory-Pattern/)

You can read about Structural Patterns from
[here](/2008/12/15/Structural-Design-Patterns/).

The Builder pattern deals with separation of the specification of a
complex object from its actual construction. This construction process
can create different representations of the class.

{% highlight csharp %}
public class BuilderPattern
{
    // Interface Car
    public interface ICar
    {
        string Details { get; set; }
    }

    // All concrete Cars
    class Car : ICar
    {
        public string Details { get; set; }
    }

    // Directors
    private interface IModel
    {
        ICar CreateCar();
    }

    //Individual car model
    public class Benz : IModel
    {
        public ICar CreateCar()
        {
            var car = new Car();
            DoWork("Build chassis");
            DoWork("Fit Engine");
            DoWork("Paint");
            car.Details = "Merc";
            return car;
        }
    }

    //Individual car model
    public class BMW : IModel
    {
        public ICar CreateCar()
        {
            var car = new Car();
            DoWork("Build chassis");
            DoWork("Fit Engine");
            DoWork("Paint");
            car.Details = "BMW";
            return car;
        }
    }

    public static void DoWork(
        string workitem)
    {
        Console.Write("" + workitem + ": 0%");
        Console.Write("....25%");
        Console.Write("....50%");
        Console.WriteLine("....100%");
    }

    //Builder that creates the car
    private interface IBuilder<T> where T : IModel
    {
        ICar CreateCar();
    }

    // Concrete Builder
    class Builder<T> : IBuilder<T> where T : IModel, new()
    {
        T myModel;

        public Builder()
        {
            myModel = new T();
        }

        public ICar CreateCar()
        {
            return myModel.CreateCar();
        }
    }

    class Client<T> where T : IModel, new()
    {
        public void ClientMain()
        {
            IBuilder<T> builder = new Builder<T>();
            Console.WriteLine("Building a car");
            var car = builder.CreateCar();
            Console.WriteLine("Car Built : " + car.Details);
        }
    }

    private static void Main()
    {
        new Client<Benz>().ClientMain();
        new Client<BMW>().ClientMain();
        // Output : Building a car
        //          Build chassis: 0%....25%....50%....100%
        //          Fit Engine: 0%....25%....50%....100%  
        //          Paint: 0%....25%....50%....100%  
        //          Car Built : Merc
        //
        //          Building a car
        //          Build chassis: 0%....25%....50%....100%
        //          Fit Engine: 0%....25%....50%....100%
        //          Paint: 0%....25%....50%....100%
        //          Car Built : BMW  108
        Console.ReadLine();
    }
}
{% endhighlight %}

The Builder pattern is based on Directors and Builders. Builder classes
can confirm to an IBuilder interface, and they can be called by a
director to produce a product according to specification. Builders can
be found in applications that create complex structures. We can use the
builder pattern when The object to be assembled might have different
representations. or when you need fine control over the construction
process.

In my next post I will start with the Behavioral patterns.

