---
layout: post
title: "Design Patterns for Dummies. The Visitor Pattern"
date: 2009-02-24
comments: true
disqus_identifier: 38
categories: [Design Patterns]
---
Following on the series of posts on design patterns I will be talking
about the Visitor Pattern today. Visitor is behavioral pattern. You can
read about the other behavioral patterns from the following links.

1.  [Strategy
    Pattern](/Post/30/Design-Patterns-for-Dummies.-The-Strategy-Pattern.aspx)
2.  [State
    Pattern](/Post/31/Design-Patterns-for-Dummies.-The-State-Pattern.aspx)
3.  [Template Method
    Pattern](/Post/32/Design-Patterns-for-Dummies.-The-Template-Method-Pattern.aspx)
4.  [Chain of Responsibility
    Pattern](/Post/33/Design-Patterns-for-Dummies.-The-Chain-of-Responsibility-Pattern.aspx)
5.  [Command
    Pattern](/Post/34/Design-Patterns-for-Dummies.-The-Command-Pattern.aspx)
6.  [Iterator
    Pattern](/Post/35/Design-Patterns-for-Dummies.-The-Iterator-Pattern.aspx)
7.  [Mediator
    Pattern](/Post/36/Design-Patterns-for-Dummies.-The-Mediator-Pattern.aspx)
8.  [Observer
    Pattern](/Post/37/Design-Patterns-for-Dummies.-The-Observer-Pattern.aspx)

You can read about the Structural patterns
[here](/Post/23/Structural-Design-Patterns.aspx). 
You can read about the Creational patterns
[here](/Post/29/Creational-Design-Patterns.aspx).

**The Visitor**

Suppose you need to add a new method to a structure of classes, you
might end up damaging the design. This is where the visitor pattern
comes into play. The visitor pattern deals with performing new
operations on all elements of an existing hierarchy of classes without
altering the the classes themselves. 

The visitor pattern is mainly made up of 2 components, the classes that
make up the hierarchy and the methods that are applied to these classes.
These methods are called as visitors. In the following example there is
one visitor viz. SalaryVisitor which increments a persons salary.

The following example is based on the example shown in the Dofactory
[site](http://www.dofactory.com)

{% highlight csharp %}
namespace PatternsConsole
{
    class VisitorPattern
    {
        // Visitor
        public abstract class Visitor
        {
            public void ReflectiveVisit(
                Element element)
            {
                var types = new[] {element.GetType()};
                var methodInfo = GetType().GetMethod(
                    "Visit",
                    types);
                if (methodInfo != null)
                {
                    methodInfo.Invoke(
                        this,
                        new object[] {element});
                }
            }
        } 
        
        // Element
        public abstract class Element
        {
            public abstract void Accept(
                Visitor visitor);
        } 
        
        // ConcreteElement
        class Person : Element
        {
            public string Name { get; set; }
            public string Designation { get; set; }
            public double Salary { get; set; }

            public Person(
                string name,
                string designation,
                double salary)
            {
                Name = name;
                Designation = designation;
                Salary = salary;
            }

            public override void Accept(
                Visitor visitor)
            {
                visitor.ReflectiveVisit(this);
            }
        } 
        
        // ElementType
        class Developer : Person
        {
            public Developer(
                string name,
                double salary) : base(
                name,
                "Developer",
                salary)
            {
            }
        } 
        
        // ElementType
        class Tester : Person
        {
            public Tester(
                string name,
                double salary) : base(
                name,
                "Tester",
                salary)
            {
            }
        } 
        
        // ConcreteVisitor
        class SalaryVisitor : Visitor
        {
            public void Visit(
                Developer developer)
            {
                VisitMethod(developer);
            }

            public void Visit(
                Tester tester)
            {
                VisitMethod(tester);
            }

            private static void VisitMethod(
                Element element)
            {
                var employee = element as Person;
                if (employee != null)
                {
                    employee.Salary *= 1.50;
                    Console.WriteLine(
                        "{0} {1}'s new income: {2:C}",
                        employee.GetType().Name,
                        employee.Name,
                        employee.Salary);
                }
            }
        }

        private static void Main()
        {
            // ObjectStructure
            var people = new List<Person>();
            var developer = new Developer(
                "Foo",
                1000);
            var tester = new Tester(
                "Bar",
                1500);
            people.Add(developer);
            people.Add(tester);
            foreach (var person in people)
            {
                person.Accept(new SalaryVisitor());
            }

            //Output: Developer Foo's new income: $1,500.00
            //        Tester Bar's new income: $2,250.00
            Console.ReadLine();
        }
    }
}
{% endhighlight %}

The visitor pattern provides number of ways to modify the behavior of a
hierarchy of classes without modifying the classes. We can use the
visitor pattern when we have a hierarchy that is sealed or when there
are many operations to perform on it or when we need the flexibility of
defining new operations.

In my next post I will be writing about the Interpreter pattern.

