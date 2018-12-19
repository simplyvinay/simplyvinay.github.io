---
layout: post
title: "Design Patterns for Dummies. The Proxy Pattern"
date: 2008-11-24
comments: true
disqus_identifier: 15
categories: [Design Patterns]
redirect_from: "/Post/15/Design-Patterns-for-Dummies.-The-Proxy-Pattern.aspx/"
---
This is the second post in a series of post on Design patterns.
Presently we are going through the structural patterns. In my previous
post I had discussed about the [Decorator
    Pattern](/2008/11/17/Design-Patterns-for-Dummies.-The-Decorator-Pattern/).
I will be writing about the proxy pattern today.


Proxy patterns is all about creating small objects which in turn are
used in creating and accessing more complex objects.

{% highlight csharp %}
public class Vehicle

{
    public string Name { get; set; }
    public string Model { get; set; }
}

public interface IGear
{
    void GearUp(
        Vehicle v);

    void GearDown(
        Vehicle v);
}

public class RunVehicle : IGear
{
    public void GearUp(
        Vehicle v)
    {
        Console.WriteLine(
            "Going one gear up : {0}",
            v.Name);
    }

    public void GearDown(
        Vehicle v)
    {
        Console.WriteLine(
            "Going one gear down : {0}",
            v.Name);

    }
}

public class VehicleProxy : IGear
{
    private RunVehicle _runVehicle;

    public VehicleProxy()
    {
        _runVehicle = new RunVehicle();
    }

    public void GearUp(
        Vehicle v)
    {
        _runVehicle.GearUp(v);
    }

    public void GearDown(
        Vehicle v)
    {
        _runVehicle.GearDown(v);
    }
}

public class Program
{
    public static void Main()
    {
        VehicleProxy proxy = new VehicleProxy();
        proxy.GearUp(new Vehicle {Name = "Vehicle1", Model = "Model1"}); //Output : Going one gear up : Vehicle1
        proxy.GearDown(
            new Vehicle {Name = "Vehicle1", Model = "Model1"}); //Output : Going one gear down : Vehicle1
        Console.ReadLine();
    }
}
{% endhighlight %}

Proxies act as frontend to classes that are complex or rather heavy in
their operation. Proxies are similar to decorators in the sense that
they both delegate requests to another object, however the proxy
relationship is well know at design time for objects where as in
decorators they are added dynamically. Proxies are to be used when,
creation of objects of any class is expensive or when we need to access
a remote object, or when we need to perform some action when an object
is called or rather creation  of objects only when an operation is
called.

In my next post I will be discussing about the Bridge pattern.

