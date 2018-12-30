---
layout: post
title: "Design Patterns for Dummies. The Interpreter Pattern"
date: 2009-03-03
comments: true
disqus_identifier: 39
categories: [Design Patterns]
redirect_from: "/Post/39/Design-Patterns-for-Dummies.-The-Interpreter-Pattern.aspx/"
---
Following up on the series on design patterns, I will be writing about
the Interpreter pattern today. Interpreter is  a behavioral pattern. You
can read about the other patterns from the following links.
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

You can read about the Structural patterns
[here](/2008/12/15/Structural-Design-Patterns/).

You can read about the Creational patterns
[here](/2009/01/12/Creational-Design-Patterns/).

**The Interpreter**

The interpreter pattern deals with interpretation of instructions
defined for some specific purpose. The instructions are given as
notations which are precise and can be defined in terms of grammar. For
the sake of example, the notation in the following example is taken to
be “PCCCCPC”. We will have to parse this expression and interpret it.

The components that make up an interpreter pattern are Context which
contains information that is used by the interpreter and Element ( Term
) which provides an interface for the structure and the default
operation for the interpreter.

*Parsing* 

The interpreter pattern itself does not specify how the objects are to
be built. For this purpose parsing is used, in which the the input is
matched against a grammar to create the object structure. Once this is
created the objects can passed to an interpreter to do its work.

{% highlight csharp %}
namespace PatternsConsole
{
    class InterpreterPattern
    {
        //Context
        class Context
        {
            public string Input { get; set; }

            public Context(
                string input)
            {
                Input = input;
            }
        }

        // Term ( Includes a Parse method to parse the input )
        abstract class Element
        {
            public abstract char GetElement();
            public abstract void Interpret();

            public void Parse(
                Context context)
            {
                if (context.Input.Length == 0)
                {
                    return;
                }

                foreach (var character in context.Input)
                {
                    if (character.CompareTo(GetElement()) == 0)
                    {
                        Interpret();
                    }
                }
            }
        }

        class Producer : Element
        {
            public override char GetElement()
            {
                return 'P';
            }

            public override void Interpret()
            {
                Console.WriteLine("Interpreted Producer");
            }
        }

        class Consumer : Element
        {
            public override char GetElement()
            {
                return 'C';
            }

            public override void Interpret()
            {
                Console.WriteLine("Interpreted Consumer");
            }
        }

        private static void Main()
        {
            var expression = "PCCCCPC";
            var context = new Context(expression);
            var tree = new List<Element>
            {
                new Producer(), new Consumer()
            };
            Console.WriteLine(
                "Exression : {0}\n",
                expression);
            foreach (var element in tree)
            {
                element.Parse(context);
            }

            // Output:
            // Exression : PCCCCPC
            // Interpreted Producer
            // Interpreted Producer
            // Interpreted Consumer
            // Interpreted Consumer
            // Interpreted Consumer
            // Interpreted Consumer
            // Interpreted Consumer
            Console.Read();
        }
    }
}
{% endhighlight %}

This pattern has performance issues and is rarely used. We can use the
Interpreter pattern when we have a grammar to be interpreted and the
grammar is fairly small and efficiency is not critical.

