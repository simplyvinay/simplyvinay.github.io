---
layout: post
title: "Design Patterns for Dummies. The Bridge pattern"
date: 2008-11-27
comments: true
disqus_identifier: 16
categories: [Design Patterns]
redirect_from: "/Post/16/Design-Patterns-for-Dummies.-The-Bridge-pattern.aspx/"
---
This is the third post in a series of post on Design patterns. Presently
we are going through the structural patterns. I will be writing about
the bridge pattern today.

You can read about the other patterns from the following links

1.  [Decorator
    Pattern](/2008/11/17/Design-Patterns-for-Dummies.-The-Decorator-Pattern/)
2.  [Proxy
    Pattern](/2008/11/24/Design-Patterns-for-Dummies.-The-Proxy-Pattern/)

The Bridge pattern deals with removing the tight coupling of
implementation of a class from its abstraction.  It helps the code to
have backward functionality in the sense that, if there is a new version
of implementation available, then the older version should not break i.e
the older consumer of the code should not break.

{% highlight csharp %}
//abstraction
public class Person
{
    private IBridge _bridge;

    public Person(
        IBridge bridge)
    {
        _bridge = bridge;
    }

    public virtual void ShowFirstPerson()
    {
        _bridge.ShowFirst();
    }

    public virtual void ShowAllPersons()
    {
        _bridge.ShowAll();
    }
}

//bridge
public interface IBridge
{
    void ShowFirst();
    void ShowAll();
}

//new abstraction
public class PersonNewVersion : Person
{
    public PersonNewVersion() : base(new DisplayPerson())
    {
    }

    public override void ShowAllPersons()
    {
        Console.WriteLine("New version of ShowAll");
        Console.WriteLine("----------------------");
        base.ShowAllPersons();
    }
}


//bridge implementor
public class DisplayPerson : IBridge
{
    private List<string> _personList;

    public DisplayPerson()
    {
        _personList = new List<string>();
        _personList.Add("Person1");
        _personList.Add("Person2");
        _personList.Add("Person3");
        _personList.Add("Person4");
    }

    public void ShowFirst()
    {
        Console.WriteLine(_personList.First());
    }

    public void ShowAll()
    {
        foreach (var item in _personList)
        {
            Console.WriteLine(item);
        }
    }
}

public class BridgeProgram
{
    public static void Main()
    {
        PersonNewVersion person = new PersonNewVersion();
        person.ShowFirstPerson(); //Output : Person1
        person.ShowAllPersons(); 
        //Output : New version of ShowAll
        //----------------------
        //Person1
        //Person2
        //Person3
        //Person4
        Console.ReadLine();
    }
}
{% endhighlight %}

We can see from the code that we can add a new implementation over the
present implementation by a bridge and an abstraction layer and achieve
more functionality over the original design. Bridge pattern can be used
when we have to hide the implementation from the caller or when we have
to change the implementation without changing the concrete  abstraction
or when we know that certain operation might not always be implemented
the same way.

In my next post I will be discussing about the Composite pattern.

