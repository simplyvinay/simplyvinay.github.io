---
layout: post
title: "Design Patterns for Dummies. The Composite Pattern"
date: 2008-12-01
comments: true
disqus_identifier: 17
categories: [Design Patterns]
redirect_from: "/Post/17/Design-Patterns-for-Dummies.-The-Composite-Pattern.aspx/"
---
This is the continuing post in a series of post on Design patterns.
Presently we are going through the structural patterns. I will be
writing about the composite pattern today.
<!--more-->
You can read about the other patterns from the following links

1.  [Decorator
    Pattern](/2008/11/17/Design-Patterns-for-Dummies.-The-Decorator-Pattern/)
2.  [Proxy
    Pattern](/2008/11/24/Design-Patterns-for-Dummies.-The-Proxy-Pattern/)
3.  [Bridge
    Pattern](/2008/11/27/Design-Patterns-for-Dummies.-The-Bridge-pattern/)

Composite pattern deals with providing a structure such that a single
object ( component ) or a group of objects ( composite ) can be treated
in the same way. This saves the caller of this composite from knowing
the objects individually. Lets see an example.

{% highlight csharp %}
public class CompositePattern
{
    public interface IComponent<T>
    {
        string Name { get; set; }

        void Add(
            IComponent<T> component);

        void Remove(
            IComponent<T> component);

        void Display(
            int depth);
    }

    public class Composite<T> : IComponent<T>
    {
        public string Name { get; set; }
        private List<IComponent<T>> _list;

        public Composite(
            string name)
        {
            Name = name;
            _list = new List<IComponent<T>>();
        }

        public void Add(
            IComponent<T> component)
        {
            _list.Add(component);
        }

        public void Remove(
            IComponent<T> component)
        {
            _list.Remove(component);
        }

        public void Display(
            int depth)
        {
            Console.WriteLine(
                new String(
                    '-',
                    depth) + Name + " | Length : " + _list.Count);
            foreach (IComponent<T> component in _list)
            {
                component.Display(depth + 1);
            }
        }
    }

    public class Component<T> : IComponent<T>
    {
        public string Name { get; set; }

        public Component(
            string name)
        {
            Name = name;
        }

        //A component can not add on to itself, So show a freindly message
        public void Add(
            IComponent<T> component)
        {
            throw new System.NotSupportedException("Can't add an item");
        }

        //A component can not delete from itself, So show a freindly message
        public void Remove(
            IComponent<T> component)
        {
            throw new System.NotSupportedException("Can't remvove an item");
        }

        public void Display(
            int depth)
        {
            Console.WriteLine(
                new String(
                    '-',
                    depth) + Name);
        }
    }

    //Person stub
    public class Person
    {
    }

    private static void Main()
    {
        IComponent<Person> group = new Composite<Person>("Group");
        group.Add(new Component<Person>("Person1"));
        group.Add(new Component<Person>("Person2"));
        IComponent<Person> subgroup = new Composite<Person>("SubGroup");
        subgroup.Add(new Component<Person>("Person2"));
        subgroup.Add(new Component<Person>("Person3"));
        group.Add(subgroup);
        group.Display(0);
        //Output :
        //Group | Length : 3
        //-Person1
        //-Person2
        //-SubGroup | Length : 2
        //--Person2   
        //--Person3
        Console.ReadLine();
    }
}
{% endhighlight %}

As you can see in the above example, “group” is a composite containing
components of “person” but it might as well contain another composite
which also contains components of “person”. You can use a composite
pattern when you have to treat all objects in a composite in the same
way or when the consumer is not bothered of all but the basic
differences between individual object and their composite.

In my next post I will be talking about the Flyweight pattern.

