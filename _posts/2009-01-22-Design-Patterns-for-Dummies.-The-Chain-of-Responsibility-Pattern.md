---
layout: post
title: "Design Patterns for Dummies. The Chain of Responsibility Pattern"
date: 2009-01-22
comments: true
disqus_identifier: 33
categories: [Design Patterns]
---
In continuation with the series on design pattern, in which presently I
going through Behavioral Patterns, today I will write about the Chain of
Responsibility Pattern. You can read about rest of the patterns from the
following links

1.  [Strategy
    Pattern](/2009/01/12/Design-Patterns-for-Dummies.-The-Strategy-Pattern/)
2.  [State
    Pattern](/2009/01/15/Design-Patterns-for-Dummies.-The-State-Pattern/)
3.  [Template Method
    Pattern](/2009/01/19/Design-Patterns-for-Dummies.-The-Template-Method-Pattern/)

You can read about the Structural patterns
[here](/2008/12/15/Structural-Design-Patterns/).

You can read about the Creational patterns
[here](/2009/01/12/Creational-Design-Patterns/).

The Chain of Responsibility pattern deals with a list of Handler objects
that limits based on the requests that they can deal with. If one object
can not handle a request, then the request is passed to the next object
in the chain which means by the end of the chain there can be an
expected behavior or an exception. 

The following example is based on the example provided in the DoFactory
[website](http://www.dofactory.com/Patterns/PatternChain.aspx)

{% highlight csharp %}
namespace PatternsConsole
{
    class ChainOfResponsibilityPattern
    {
        // Delegate for withdrawal requests
        public delegate void WithdrawHandler<T, E>(
            T sender,
            E eventArgs);

        public class WithdrawArgs : EventArgs
        {
            public double Amount { get; set; }
            public int AccountNumber { get; set; }

        }

        // "Handler"
        abstract class Approver
        {
            public event WithdrawHandler<Approver, WithdrawArgs> Withdraw;

            public Approver Successor { protected get; set; }

            // Invoing the event
            public virtual void OnWithdraw(
                WithdrawArgs e)
            {
                if (Withdraw != null)
                {
                    Withdraw(
                        this,
                        e);
                }
            }

            public void ProcessRequest(
                WithDrawal withDrawal)
            {
                OnWithdraw(
                    new WithdrawArgs
                    {
                        AccountNumber = withDrawal.AccountNumber,
                        Amount = withDrawal.Amount
                    });
            }
        }

        // "ConcreteHandler"
        class Clerk : Approver
        {
            public Clerk()
            {
                // Hook up delegate to event
                Withdraw += ProcessByClerk;
            }

            public void ProcessByClerk(
                Approver approver,
                WithdrawArgs e)
            {
                if (e.Amount < 10000)
                {
                    Console.WriteLine(
                        "{0} approved withdrawal for account : {1}",
                        GetType().Name,
                        e.AccountNumber);

                }
                else if (Successor != null)
                {
                    Successor.OnWithdraw(e);
                }
            }
        }

        // "ConcreteHandler"
        class Officer : Approver
        {
            public Officer()
            {
            // Hook up delegate to event
                Withdraw += ProcessByOfficer;
            }

            public void ProcessByOfficer(
                Approver approver,
                WithdrawArgs e)
            {
                if (e.Amount < 100000)
                {
                    Console.WriteLine(
                        "{0} approved withdrawal for account : {1}",
                        GetType().Name,
                        e.AccountNumber);
                }
                else if (Successor != null)
                {
                    Successor.OnWithdraw(e);
                }
            }
        }

        // "ConcreteHandler"
        class Manager : Approver
        {
            public Manager()
            {
            // Hook up delegate to event
                Withdraw += ProcessByManager;
            }

            public void ProcessByManager(
                Approver approver,
                WithdrawArgs e)
            {
                if (e.Amount < 1000000)
                {
                    Console.WriteLine(
                        "{0} approved withdrawal for account : {1}",
                        GetType().Name,
                        e.AccountNumber);
                }
                else
                {
                    Console.WriteLine("Cannot be approved");
                }
            }
        }

        class WithDrawal
        {
            public double Amount { get; set; }
            public int AccountNumber { get; set; }
        }

        static void Main()
        {
        // Setup Chain of Responsibility
            var clerkAprover = new Clerk();
            var officerApprover = new Officer();
            var managerApprover = new Manager();
            clerkAprover.Successor = officerApprover;
            officerApprover.Successor = managerApprover;
            var withDrawal = new WithDrawal {AccountNumber = 1234, Amount = 1001};
            clerkAprover.ProcessRequest(withDrawal);
            withDrawal = new WithDrawal {AccountNumber = 2345, Amount = 10000};
            clerkAprover.ProcessRequest(withDrawal);
            withDrawal = new WithDrawal {AccountNumber = 3456, Amount = 100000};
            clerkAprover.ProcessRequest(withDrawal);
            //Output : Clerk approved withdrawal for account : 1234
            //         Officer approved withdrawal for account : 2345
            //         Manager approved withdrawal for account : 3456

            Console.Read();
        }
    }
}
{% endhighlight %}

As seen in the example there are 3 approvers for withdrawal as  a chain.
The request is passed to the clerk and based on the amount the
responsibility is transferred to the successive approver in the chain.
We can use the chain of responsibility patter when we have more than one
handler for a single request or we have a set of handler that vary
dynamically.

In my next post I will be writing about the Command pattern.

