---
layout: post
title: "Design Patterns for Dummies. The Command Pattern"
date: 2009-02-02
comments: true
disqus_identifier: 34
categories: [Design Patterns]
redirect_from: "/Post/34/Design-Patterns-for-Dummies.-The-Command-Pattern.aspx/"
---
In continuation with the series on design pattern, I am currently going
through Behavioral Patterns. Today I will write about the Command
Pattern. You can read about rest of the patterns from the following
links

1.  [Strategy
    Pattern](/2009/01/12/Design-Patterns-for-Dummies.-The-Strategy-Pattern/)
2.  [State
    Pattern](/2009/01/15/Design-Patterns-for-Dummies.-The-State-Pattern/)
3.  [Template Method
    Pattern](/2009/01/19/Design-Patterns-for-Dummies.-The-Template-Method-Pattern/)
4.  [Chain of Responsibility
    Pattern](/2009/01/22/Design-Patterns-for-Dummies.-The-Chain-of-Responsibility-Pattern/)

You can read about the Structural patterns
[here](/2008/12/15/Structural-Design-Patterns/).

You can read about the Creational patterns
[here](/2009/01/12/Creational-Design-Patterns/).

The Command pattern deals with segregation of the client that requested
an operation and the object that can perform that operation. The command
pattern can support sending requests to different receivers, queuing of
requests, redo and undo functionality.

{% highlight csharp %}
namespace PatternsConsole
{
    public class CommandPattern
    {
        // Command
        interface ICommand
        {
            void Execute();
        }

        // ConcreteCommand
        class AddCommand : ICommand
        {
            public Item Itm { get; set; }
            public Category Cat { get; set; }

            public void Execute()
            {
                Itm.Add(Cat);
                Cat.Add(Itm);
            }
        }

        // ConcreteCommand
        class DeleteCommand : ICommand
        {
            public Item Itm { get; set; }
            public Category Cat { get; set; }

            public void Execute()
            {
                Itm.Delete(Cat);
                Cat.Delete(Itm);
            }
        }

        // Invoker
        class ItemManager
        {
            public ICommand Command { set; get; }

            public void Process()
            {
                Command.Execute();
            }
        }

        // Receiver
        class Item
        {
            private IDictionary<string, Category> categories;
            public string Desc { get; set; }

            public Item(
                string itemName)
            {
                Desc = itemName;
                categories = new Dictionary<string, Category>();
            }

            public void Add(
                Category cat)
            {
                categories.Add(
                    cat.Desc,
                    cat);
            }

            public void Delete(
                Category cat)
            {
                categories.Remove(cat.Desc);
            }
        }

        // Receiver
        class Category
        {
            private IDictionary<string, Item> items;
            public string Desc { get; set; }

            public Category(
                string catName)
            {
                Desc = catName;
                items = new Dictionary<string, Item>();
            }

            public void Add(
                Item item)
            {
                items.Add(
                    item.Desc,
                    item);
                Console.WriteLine(
                    "Item '" + item.Desc + "' has been added to the '"
                    + Desc + "' Category ");
            }

            public void Delete(
                Item item)
            {
                items.Remove(item.Desc);
                Console.WriteLine(
                    "Item '" + item.Desc
                             + "' has been deleted from the '" + Desc + "' Category ");
            }
        }

        public static void Main()
        {
            // Create Receiver objects
            Item DVD = new Item("The Fountain");
            Category catDVD = new Category("DVD");

            // create the command object
            ICommand command = new AddCommand {Itm = DVD, Cat = catDVD};

            // create the invoker
            ItemManager manager = new ItemManager {Command = command};
            manager.Process();

            // Add an item to the CD category
            DVD = new Item("The Wrestler");
            catDVD = new Category("DVD");
            command = new AddCommand {Itm = DVD, Cat = catDVD};
            manager.Command = command;
            manager.Process();

            // Add an item to the Old Releases category
            DVD = new Item("Pi");
            catDVD = new Category("Old Releases");
            command = new AddCommand {Itm = DVD, Cat = catDVD};
            manager.Command = command;
            manager.Process();

            // Delete an item from the Old Releases category
            command = new DeleteCommand {Itm = DVD, Cat = catDVD};
            manager.Command = command;
            manager.Process();
            // Output:  
            // Item 'The Fountain' has been added to the 'DVD' Category
            // Item 'The Wrestler' has been added to the 'DVD' Category
            // Item 'Pi' has been added to the 'Old Releases' Category
            // Item 'Pi' has been deleted from the 'Old Releases' Category
            
            Console.ReadLine();
        }
    }
}
{% endhighlight %}

We can use the command pattern when we have different receivers and
handle each in a different way or we want to specify and execute
commands at different times or when we want to support undo
functionality.

In my next post I will be writing about the Iterator pattern.

