---
layout: post
title: "Design Patterns For Dummies. The Prototype Pattern"
date: 2008-12-15
comments: true
disqus_identifier: 24
categories: [Design Patterns]
redirect_from: "/Post/24/Design-Patterns-For-Dummies.-The-Prototype-Pattern.aspx/"
---
Continuing with the series on Design patterns, I will take up Creational
Patterns from today. I have written about [Structural
Patterns](/2008/12/15/Structural-Design-Patterns/)
in my previous posts. You can get all the links for them
[here](/2008/12/15/Structural-Design-Patterns/).

**Creational Patterns**

Creational patterns deals with separation of a system in terms of how
their objects are created, what they are made of and how they must be
represented. There are 5 types of creational patterns as observed by the
GOF. I will write about the Prototype pattern today.

**The Prototype**

The prototype patterns deals with creation of new objects by cloning one
of the available prototypes. The creation of objects is much faster for
large classes. It also keeps a record of parts of a data structure so
that it can be copied without knowing the subclass from which they were
created.

{% highlight csharp %}
class PrototypePattern
{
    [Serializable]
    public abstract class ProtoType<T> where T : class
    {
        public T ShallowCopy()
        {
            return this.MemberwiseClone() as T;
        }

        public T DeepCopy()
        {
            MemoryStream stream = new MemoryStream();
            BinaryFormatter formatter = new BinaryFormatter();
            formatter.Serialize(
                stream,
                this);
            stream.Seek(
                0,
                SeekOrigin.Begin);
            T copy = formatter.Deserialize(stream) as T;
            stream.Close();
            return copy;
        }
    }

    [Serializable]
    public class Address
    {
        public string Street { get; set; }
        public string City { get; set; }
        public string Country { get; set; }

        public Address(
            string street,
            string city,
            string country)
        {
            Street = street;
            City = city;
            Country = country;
        }
    }

    [Serializable]
    public class Person : ProtoType<Person>
    {
        public string Name { get; set; }
        public int Age { get; set; }
        public Address Address { get; set; }

        public Person(
            string name,
            int age,
            string street,
            string city,
            string country)
        {
            Name = name;
            Age = age;
            Address = new Address(
                street,
                city,
                country);
        }

        public override string ToString()
        {
            return string.Format(
                "Name : {0}\nAge : {1}\nAddress : {2}, {3}, {4}",
                Name,
                Age,
                Address.Street,
                Address.City,
                Address.Country);
        }
    }


    class PersonManager : ProtoType<Person>
    {
        public Dictionary<string, Person> persons =
            new Dictionary<string, Person>()
            {
                {
                    "person1", new Person(
                        "Person1",
                        25,
                        "Street1",
                        "City1",
                        "Country1")
                },
                {
                    "person2", new Person(
                        "Person2",
                        26,
                        "Street2",
                        "City2",
                        "Country2")
                }
            };
    }

    static void Main()
    {
        PersonManager persManager = new PersonManager();
        Person person1, person2, person3, person4;
        person1 = persManager.persons["person1"];
        Console.WriteLine(person1.ToString());

        // Output:  Name : Person1
        //          Age : 25   
        //          Address : Street1, City1, Country1
        person3 = persManager.persons["person1"].ShallowCopy();
        person3.Name = "Person3";
        person3.Age = 20;
        person3.Address.Street = "Street3";
        person3.Address.City = "City3";
        person3.Address.Country = "Country3";
        Console.WriteLine(person3.ToString());
        Console.WriteLine(person1.ToString());
        // Output:  Name : Person3   
        //          Age : 20   
        //          Address : Street3, City3, Country3   
        // Output:  Name : Person1   
        //          Age : 25   94
        //          Address : Street3, City3, Country3

        person2 = persManager.persons["person2"];
        Console.WriteLine(person2.ToString());
        // Output:  Name : Person2  100
        //          Age : 26  101
        //          Address : Street2, City2, Country2

        person4 = persManager.persons["person2"].DeepCopy();
        person4.Name = "Person4";
        person4.Age = 23;
        person4.Address.Street = "Street4";
        person4.Address.City = "City4";
        person4.Address.Country = "Country4";
        Console.WriteLine(person4.ToString());
        Console.WriteLine(person2.ToString());
        // Output:  Name : Person4  
        //          Age : 23  
        //          Address : Street4, City4, Country4  
        // Output:  Name : Person2  
        //          Age : 26  
        //          Address : Street2, City2, Country2
        Console.ReadLine();
    }
}
{% endhighlight %}

As you can see from the above example, the prototype pattern is
implemented using cloning and serialization.  When we are using the
shallow copied objects, the value of reference types present inside the
class that was cloned will also be changed when we change the cloned
object, where as in deep copied object this is not the case.  We can use
the prototype pattern when we want to hide the concrete class from
client or when we want to add or delete new classes dynamically.

In my next post I will talking about the Factory Method pattern.

