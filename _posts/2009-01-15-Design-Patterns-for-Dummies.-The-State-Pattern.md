---
layout: post
title: "Design Patterns for Dummies. The State Pattern"
date: 2009-01-15
comments: true
disqus_identifier: 31
categories: [Design Patterns]
---
In continuation with the series on design pattern, I am currently going
through Behavioral Patterns. Today I will write about the State Pattern.
You can read about the other patterns from the following links

1.  [Strategy
    Pattern](http://www.simplyvinay.com/Post/30/Design-Patterns-for-Dummies.-The-Strategy-Pattern.aspx)

You can read about Structural Patterns from
[here](http://www.simplyvinay.com/Post/23/Structural-Design-Patterns.aspx).

You can read about Creational Patterns from
[here](http://www.simplyvinay.com/Post/29/Creational-Design-Patterns.aspx).

State pattern in essence is a dynamic version of the strategy pattern,
meaning, when the sate inside an object is changed, it could change its
behavior.

{% highlight csharp %}
namespace PatternsConsole
{
    class StatePattern
    {
        interface IState
        {
            void Deposit(
                double amount);

            void Withdraw(
                double amount);

            double Balance { get; set; }
            Account Account { get; set; }
        }

        // State 1
        class InsufficientState : IState
        {
            public InsufficientState(
                double balance,
                Account account)
            {
                Balance = balance;
                Account = account;
            }

            public void Deposit(
                double amount)
            {
                Balance += amount;
                CheckState();
            }

            public void Withdraw(
                double amount)
            {
                Balance -= amount;
                Console.WriteLine("Insufficient fund");
            }

            private void CheckState()
            {
                if (Balance > 100)
                {
                    Account.State = new NormalState(
                        Balance,
                        Account);
                }
                else if (Balance > 300)
                {
                    Account.State = new GoldState(
                        Balance,
                        Account);
                }
            }

            public double Balance { get; set; }
            public Account Account { get; set; }

        }

        // State 2
        class NormalState : IState
        {
            public NormalState(
                double balance,
                Account account)
            {
                Balance = balance;
                Account = account;
            }

            public void Deposit(
                double amount)
            {
                Balance += amount;
                CheckState();
            }

            public void Withdraw(
                double amount)
            {
                Balance -= amount;
                CheckState();
            }

            private void CheckState()
            {
                if (Balance < 100)
                {
                    Account.State = new InsufficientState(
                        Balance,
                        Account);
                }
                else if (Balance > 300)
                {
                    Account.State = new GoldState(
                        Balance,
                        Account);
                }
            }

            public double Balance { get; set; }
            public Account Account { get; set; }
        }

        // State 3
        class GoldState : IState
        {
            public GoldState(
                double balance,
                Account account)
            {
                Balance = balance;
                Account = account;
            }

            public void Deposit(
                double amount)
            {
                Balance += amount;
                CheckState();
            }

            public void Withdraw(
                double amount)
            {
                Balance -= amount;
                CheckState();
            }

            private void CheckState()
            {
                if (Balance < 100)
                {
                    Account.State = new InsufficientState(
                        Balance,
                        Account);
                }
                else if (Balance < 300)
                {

                    Account.State = new NormalState(
                        Balance,
                        Account);
                }
            }

            public double Balance { get; set; }
            public Account Account { get; set; }
        }

        // Context
        class Account
        {
            public IState State { set; get; }

            // Construcstor
            public Account()
            {
                State = new NormalState(
                    0,
                    this);
            }

            public void Deposit(
                double amount)
            {
                State.Deposit(amount);
                Console.WriteLine(
                    "Deposited {0:C} ",
                    amount);
                Console.WriteLine(
                    " Balance = {0:C}",
                    State.Balance);
                Console.WriteLine(
                    " Status  = {0}",
                    State.GetType().Name);
                Console.WriteLine("");
            }

            public void Withdraw(
                double amount)
            {
                State.Withdraw(amount);
                Console.WriteLine(
                    "Withdrew {0:C} ",
                    amount);
                Console.WriteLine(
                    " Balance = {0:C}",
                    State.Balance);
                Console.WriteLine(
                    " Status  = {0}\n",
                    State.GetType().Name);
            }
        }

        static void Main()
        {
            var account = new Account();
            account.Deposit(100);
            account.Deposit(100);
            account.Deposit(300);
            account.Withdraw(200);
            account.Withdraw(200);
            account.Withdraw(100);
            
            // Output:
            // Deposited $100.00
            // Balance = $100.00
            // Status  = NormalState

            // Deposited $100.00
            // Balance = $200.00
            // Status  = NormalState

            // Deposited $300.00
            // Balance = $500.00
            // Status  = GoldState

            // Withdrew $200.00
            // Balance = $300.00
            // Status  = GoldState

            // Withdrew $200.00
            // Balance = $100.00
            // Status  = NormalState

            // Withdrew $100.00
            // Balance = $0.00
            // Status  = InsufficientState

            Console.Read();
        }
    }
}
{% endhighlight %}

As you can see in the example, the behavior of our code is changing
based on the state which internally is changed based on the amount
balance. We can use the state pattern when we have objects that may
change their behavior at runtime based on some context or when the
objects are becoming complex with many conditional branches.

In my next post I will be writing about the Template Method Pattern

